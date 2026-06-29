# Guia: Recriar banco PostgreSQL de WIN1252 para UTF-8 sem perder dados

> **Contexto:** PostgreSQL instalado no Windows herda o encoding do sistema operacional (WIN1252).
> Este guia documenta como migrar para UTF-8 preservando todos os dados existentes,
> e como manter o projeto organizado com uma migration consolidada.

---

## Índice

1. [Por que isso acontece](#1-por-que-isso-acontece)
2. [Pré-requisitos](#2-pré-requisitos)
3. [Etapa 1 — Fazer backup do banco antigo](#3-etapa-1--fazer-backup-do-banco-antigo)
4. [Etapa 2 — Criar o banco novo em UTF-8](#4-etapa-2--criar-o-banco-novo-em-utf-8)
5. [Etapa 3 — Consolidar as migrations](#5-etapa-3--consolidar-as-migrations)
6. [Etapa 4 — Restaurar os dados](#6-etapa-4--restaurar-os-dados)
7. [Etapa 5 — Corrigir a tabela de migrations](#7-etapa-5--corrigir-a-tabela-de-migrations)
8. [Etapa 6 — Verificações finais](#8-etapa-6--verificações-finais)
9. [Etapa 7 — Subir o backend](#9-etapa-7--subir-o-backend)
10. [Erros comuns e soluções](#10-erros-comuns-e-soluções)
11. [Referência rápida de comandos](#11-referência-rápida-de-comandos)

---

## 1. Por que isso acontece

Quando o PostgreSQL é instalado no Windows, o `initdb` (processo que inicializa o cluster) lê
o locale do sistema operacional. No Windows em português ou inglês americano, o locale padrão
é `English_United States.1252`, que mapeia para o encoding **WIN1252**.

O resultado é que **todos** os bancos do cluster herdam esse encoding por padrão:

```
template0  →  WIN1252  (template imutável, criado pelo initdb)
template1  →  WIN1252  (cópia do template0, usado por novos bancos)
postgres   →  WIN1252
```

**Consequência prática:** qualquer mensagem de WhatsApp com emoji (🔧, ✅, 🎫) tenta ser
salva no banco e falha silenciosamente — o emoji não tem representação em WIN1252.

**A solução** é criar o banco explicitamente com `TEMPLATE template0 ENCODING 'UTF8'`.
O `template0` é somente leitura e permite encoding diferente do cluster.

No Linux (e na imagem Docker `postgres:16-alpine`) isso não ocorre porque o locale padrão
já é `en_US.UTF-8`, então o cluster nasce em UTF-8 automaticamente.

---

## 2. Pré-requisitos

| O que | Onde verificar |
|---|---|
| PostgreSQL 16 instalado | `psql --version` no terminal |
| Variável PATH com `C:\pgsql\bin` | `where psql` deve retornar o caminho |
| Acesso ao usuário `postgres` (superusuário) | `psql -U postgres` sem erro |
| Arquivo `.env` do projeto configurado | `apps/backend/.env` ou `infrastructure/.env` |

---

## 3. Etapa 1 — Fazer backup do banco antigo

> **Onde executar:** qualquer pasta — o importante é ter `pg_dump` no PATH.
> Recomendado: `C:\pgsql\bin\` ou a raiz do projeto.

### Verificar o encoding atual

```powershell
# Conectar ao psql como superusuário
psql -U postgres

# Ver encoding de todos os bancos
\l

# Sair
\q
```

A coluna `Encoding` deve mostrar `WIN1252` se o problema existir.

### Gerar o backup

```powershell
# Substitua wsd_dev pelo nome real do seu banco
# Substitua o caminho de destino conforme sua máquina
pg_dump -U postgres -d wsd_dev -F p -f C:\backup\wsd_db_backup.sql
```

**O que cada flag faz:**

| Flag | Significado |
|---|---|
| `-U postgres` | Conecta como superusuário |
| `-d wsd_dev` | Nome do banco de origem |
| `-F p` | Formato plain text (arquivo .sql legível) |
| `-f C:\backup\...` | Caminho de destino do arquivo |

> **Importante:** o `pg_dump` automaticamente inclui a linha `SET client_encoding = 'WIN1252'`
> no arquivo gerado. Essa linha é a chave da migração — o PostgreSQL usa ela para converter
> os dados automaticamente durante a restauração.

### Verificar o backup

```powershell
# Confirmar que o arquivo foi criado e tem tamanho razoável
ls C:\backup\wsd_db_backup.sql

# Ver as primeiras linhas para confirmar o encoding declarado
Get-Content C:\backup\wsd_db_backup.sql -TotalCount 10
```

Deve aparecer algo como:
```sql
SET client_encoding = 'WIN1252';
```

---

## 4. Etapa 2 — Criar o banco novo em UTF-8

> **Onde executar:** dentro do `psql` conectado como `postgres`.

```powershell
# Conectar como superusuário
psql -U postgres
```

### Testar se o cluster aceita UTF-8

```sql
-- Teste antes de criar o banco definitivo
CREATE DATABASE wsd_teste_utf8
  WITH ENCODING 'UTF8'
       LC_COLLATE 'C'
       LC_CTYPE 'C'
       TEMPLATE template0
       OWNER wsd_user;
```

**Se retornar `CREATE DATABASE`:** o cluster aceita UTF-8. Continue.

**Se retornar erro** `encoding UTF8 does not match locale`: o `initdb` foi feito com locale
fixo e não permite override. Neste caso, é necessário reinicializar o cluster inteiro
(ver seção [Erros comuns](#10-erros-comuns-e-soluções)).

### Criar o banco definitivo

```sql
-- Remover o banco de teste
DROP DATABASE wsd_teste_utf8;

-- Criar o banco de desenvolvimento
CREATE DATABASE wsd_dev
  WITH ENCODING 'UTF8'
       LC_COLLATE 'C'
       LC_CTYPE 'C'
       TEMPLATE template0
       OWNER wsd_user;

-- Criar o banco de produção (se aplicável)
CREATE DATABASE wsd_db
  WITH ENCODING 'UTF8'
       LC_COLLATE 'C'
       LC_CTYPE 'C'
       TEMPLATE template0
       OWNER wsd_user;
```

**Por que `LC_COLLATE 'C'` e `LC_CTYPE 'C'`?**

O locale `C` é sempre disponível em qualquer sistema e é compatível com UTF-8 para ordenação.
Usar `C` evita dependência de locales do sistema operacional que podem não estar instalados,
especialmente em containers Linux.

### Confirmar o encoding

```sql
-- Dentro do psql
\l wsd_dev
```

A coluna `Encoding` deve mostrar `UTF8`.

```sql
\q
```

---

## 5. Etapa 3 — Consolidar as migrations

> **Onde executar:** no terminal, dentro da pasta do projeto.

Migrations acumuladas que se sobrepõem tornam o schema imprevisível em um servidor limpo.
A consolidação cria uma única migration que representa o estado definitivo e atual do banco.

### Arquivar as migrations antigas

```powershell
# Navegue até a pasta de migrations do backend
cd C:\Dev\Repositories\chat-bot\whatsapp-service-desk\apps\backend\src\database\migrations

# Criar pasta de arquivo
mkdir _archive

# Mover todas as migrations antigas (manter apenas a nova)
Move-Item *.ts _archive\
```

### Criar a migration consolidada

Crie o arquivo `1788000000000-InitialSchema.ts` dentro de:
```
apps/backend/src/database/migrations/
```

> O arquivo deve conter o schema completo em uma única migration `up/down`.
> Consulte o arquivo gerado anteriormente neste projeto como referência.

**Convenção de nomenclatura:**

```
{timestamp}-{NomePascalCase}.ts
```

O timestamp deve ser **maior** que todos os anteriores para garantir que o TypeORM
o reconheça como o mais recente. Usar `1788000000000` (Unix epoch futuro) funciona bem.

---

## 6. Etapa 4 — Restaurar os dados

> **Onde executar:** `C:\pgsql\bin\` ou qualquer pasta com `psql` no PATH.

### Como a conversão de encoding funciona

O arquivo `.sql` gerado pelo `pg_dump` contém na primeira linha:
```sql
SET client_encoding = 'WIN1252';
```

Quando o `psql` lê esse comando durante a restauração, ele avisa ao PostgreSQL:
*"os dados que estou enviando estão em WIN1252"*. O servidor, que agora é UTF-8,
converte cada caractere automaticamente na hora de gravar.

Isso significa que **não é necessário nenhuma flag especial** — a conversão é automática.

### Executar a restauração

```powershell
psql -U postgres -d wsd_dev -f "C:\backup\wsd_db_backup.sql"
```

**O que esperar na saída:**

```
SET
SET
CREATE TYPE
...
COPY 7      ← 7 linhas inseridas na tabela customers
COPY 19     ← 19 técnicos inseridos
COPY 16     ← 16 tickets inseridos
...
CREATE INDEX
ALTER TABLE
```

Erros esperados que podem ser ignorados com segurança:
- `already exists` em tipos ENUM (se o banco já tinha alguma estrutura)

Erros que indicam problema real:
- `could not connect to server` — banco não existe ou credenciais erradas
- `permission denied` — usuário sem privilégios suficientes
- `invalid byte sequence for encoding "UTF8"` — o arquivo de backup está corrompido

---

## 7. Etapa 5 — Corrigir a tabela de migrations

> **Onde executar:** `C:\pgsql\bin\` no PowerShell.

Após a restauração, a tabela `migrations` contém o histórico antigo (20 entradas).
O TypeORM vai tentar rodar as migrations antigas que não existem mais como arquivos,
causando erros na inicialização do backend.

A solução é substituir o histórico pela entrada única da migration consolidada.

### Limpar e registrar a migration consolidada

```powershell
psql -U postgres -d wsd_dev -c "TRUNCATE public.migrations RESTART IDENTITY; INSERT INTO public.migrations (id, timestamp, name) VALUES (1, 1788000000000, 'InitialSchema1788000000000'); SELECT id, timestamp, name FROM public.migrations;"
```

**O que cada comando faz:**

| Comando | Efeito |
|---|---|
| `TRUNCATE public.migrations RESTART IDENTITY` | Remove todas as linhas e reseta a sequência para 1 |
| `INSERT INTO public.migrations ...` | Registra a migration consolidada como "já executada" |
| `SELECT ...` | Confirma o resultado |

**Saída esperada:**

```
 id |   timestamp   |            name
----+---------------+----------------------------
  1 | 1788000000000 | InitialSchema1788000000000
(1 row)
```

> **Por que não usar `DELETE` + `setval(0)`?**
> `setval` não aceita 0 como valor (o mínimo é 1). `TRUNCATE ... RESTART IDENTITY`
> é equivalente a deletar tudo e resetar a sequência em um único comando atômico.

---

## 8. Etapa 6 — Verificações finais

> **Onde executar:** `C:\pgsql\bin\` no PowerShell.

### Verificar encoding do banco

```powershell
psql -U postgres -d wsd_dev -c "SELECT pg_catalog.pg_encoding_to_char(encoding) AS encoding FROM pg_catalog.pg_database WHERE datname = 'wsd_dev';"
```

Resultado esperado: `UTF8`

### Verificar contagem de dados

```powershell
psql -U postgres -d wsd_dev -c "
SELECT
  (SELECT COUNT(*) FROM tickets)      AS tickets,
  (SELECT COUNT(*) FROM technicians)  AS tecnicos,
  (SELECT COUNT(*) FROM departments)  AS departamentos,
  (SELECT COUNT(*) FROM customers)    AS clientes;
"
```

### Verificar que o UTF-8 está correto nos dados

```powershell
psql -U postgres -d wsd_dev -c "
SELECT
  name,
  display_name,
  length(display_name)        AS chars,
  octet_length(display_name)  AS bytes
FROM departments
WHERE name = 'After Sales';
"
```

**Como interpretar:**
- `chars` = número de caracteres
- `bytes` = número de bytes
- Se `bytes > chars`: há caracteres multibyte → UTF-8 real ✅
- Se `bytes = chars`: apenas ASCII → dado pode estar sem acentos ⚠️

Exemplo: `Pós-venda` tem 9 chars e 10 bytes (o `ó` ocupa 2 bytes em UTF-8).

### Nota sobre o terminal Windows

O PowerShell no Windows pode exibir acentos corrompidos mesmo com o banco em UTF-8.
Isso é problema de **exibição do terminal**, não do banco.

Para forçar UTF-8 no terminal:

```powershell
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8
chcp 65001
psql -U postgres -d wsd_dev -c "SELECT name, display_name FROM departments;"
```

Para confirmar que o banco está correto independente do terminal, use o teste
`chars vs bytes` acima.

### Verificar a tabela de migrations

```powershell
psql -U postgres -d wsd_dev -c "SELECT id, timestamp, name FROM public.migrations;"
```

Deve retornar apenas 1 linha com `InitialSchema1788000000000`.

---

## 9. Etapa 7 — Subir o backend

> **Onde executar:** pasta `apps/backend/` do projeto.

```powershell
cd C:\Dev\Repositories\chat-bot\whatsapp-service-desk\apps\backend
npm run start:dev
```

**O que o TypeORM faz na inicialização:**

1. Lê todos os arquivos `.ts` em `src/database/migrations/`
2. Compara com o que está na tabela `public.migrations` do banco
3. Se `InitialSchema1788000000000` já está na tabela → não executa nada ✅
4. Se encontrar migrations sem correspondência no banco → executa na ordem do timestamp

Como a migration consolidada está registrada na tabela, o TypeORM não vai rodar nada
e o backend sobe normalmente.

**Logs esperados na inicialização:**

```
[TypeORM] No migrations are pending
[NestJS] Application is running on: http://localhost:3000
```

---

## 10. Erros comuns e soluções

### `encoding UTF8 does not match locale English_United States.1252`

**Causa:** o `initdb` foi executado com locale fixo e o `template0` não permite override.

**Solução:** reinicializar o cluster inteiro.

```powershell
# 1. Fazer backup de TODOS os bancos antes
pg_dump -U postgres -d wsd_dev  -f C:\backup\wsd_dev.sql
pg_dump -U postgres -d evolution_api -f C:\backup\evolution_api.sql

# 2. Parar o serviço PostgreSQL
Stop-Service postgresql-x64-16

# 3. Renomear o data directory (não deletar ainda)
Rename-Item "C:\Program Files\PostgreSQL\16\data" "C:\Program Files\PostgreSQL\16\data_win1252_bkp"

# 4. Criar novo cluster em UTF-8
& "C:\Program Files\PostgreSQL\16\bin\initdb.exe" `
  -D "C:\Program Files\PostgreSQL\16\data" `
  --encoding=UTF8 `
  --locale=C `
  -U postgres

# 5. Copiar configurações do cluster antigo
Copy-Item "C:\Program Files\PostgreSQL\16\data_win1252_bkp\postgresql.conf" "C:\Program Files\PostgreSQL\16\data\"
Copy-Item "C:\Program Files\PostgreSQL\16\data_win1252_bkp\pg_hba.conf"    "C:\Program Files\PostgreSQL\16\data\"

# 6. Subir o serviço
Start-Service postgresql-x64-16

# 7. Recriar usuário e bancos
psql -U postgres -c "CREATE USER wsd_user WITH PASSWORD 'sua_senha';"
psql -U postgres -c "CREATE DATABASE wsd_dev WITH ENCODING 'UTF8' LC_COLLATE 'C' LC_CTYPE 'C' TEMPLATE template0 OWNER wsd_user;"

# 8. Restaurar dados
psql -U postgres -d wsd_dev -f C:\backup\wsd_dev.sql
```

---

### `permission denied to create database`

**Causa:** o usuário `wsd_user` não tem privilégio `CREATEDB`.

**Solução:** criar o banco como `postgres` (superusuário) e transferir ownership.

```sql
-- Conectado como postgres
CREATE DATABASE wsd_dev
  WITH ENCODING 'UTF8'
       LC_COLLATE 'C'
       LC_CTYPE 'C'
       TEMPLATE template0
       OWNER wsd_user;
```

Se quiser dar o privilégio permanentemente:
```sql
ALTER USER wsd_user CREATEDB;
```

---

### `duplicate key value violates unique constraint "PK_migrations"`

**Causa:** o `DELETE FROM migrations` falhou ou não foi confirmado, e a tentativa de `INSERT`
com `id=1` encontrou uma linha existente com esse id.

**Solução:** usar `TRUNCATE` em vez de `DELETE`:

```powershell
psql -U postgres -d wsd_dev -c "TRUNCATE public.migrations RESTART IDENTITY;"
```

---

### `setval: value 0 is out of bounds for sequence`

**Causa:** sequências PostgreSQL têm valor mínimo 1, não 0.

**Solução:** não usar `setval(0)`. Usar `TRUNCATE ... RESTART IDENTITY` que reseta
corretamente, ou `setval('seq', 1)`.

---

### Acentos corrompidos no terminal (`P≤s-venda`, `TI / Inform°tica`)

**Causa:** o PowerShell usa codepage 850 ou 1252 por padrão e não renderiza UTF-8 corretamente.
O **banco está correto** — é só exibição.

**Solução para a sessão atual:**

```powershell
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8
chcp 65001
```

**Confirmação definitiva que o banco está correto** (não depende do terminal):

```sql
SELECT length(display_name), octet_length(display_name)
FROM departments WHERE name = 'After Sales';
-- chars < bytes = UTF-8 multibyte = correto
```

---

### TypeORM tenta rodar migrations antigas que não existem mais

**Causa:** a tabela `migrations` ainda tem as entradas antigas, mas os arquivos `.ts`
foram movidos para `_archive/`.

**Solução:** executar o passo 5 deste guia (TRUNCATE + INSERT da migration consolidada).

---

### `invalid byte sequence for encoding "UTF8"` durante restauração

**Causa:** o arquivo `.sql` foi editado ou transferido por uma ferramenta que corrompeu
os bytes WIN1252, ou o arquivo foi gerado de uma fonte já corrompida.

**Solução:** gerar um novo backup direto do banco original, sem abrir o arquivo em editores
de texto que possam re-encodar o conteúdo.

---

## 11. Referência rápida de comandos

### Verificar encoding de todos os bancos
```powershell
psql -U postgres -c "\l"
```

### Criar banco UTF-8
```powershell
psql -U postgres -c "CREATE DATABASE nome_db WITH ENCODING 'UTF8' LC_COLLATE 'C' LC_CTYPE 'C' TEMPLATE template0 OWNER wsd_user;"
```

### Fazer backup
```powershell
pg_dump -U postgres -d nome_db -F p -f C:\backup\nome_db.sql
```

### Restaurar backup (com conversão automática de encoding)
```powershell
psql -U postgres -d nome_db -f C:\backup\nome_db.sql
```

### Limpar e registrar migration consolidada
```powershell
psql -U postgres -d nome_db -c "TRUNCATE public.migrations RESTART IDENTITY; INSERT INTO public.migrations (id, timestamp, name) VALUES (1, 1788000000000, 'InitialSchema1788000000000'); SELECT id, timestamp, name FROM public.migrations;"
```

### Verificar encoding real dos dados
```powershell
psql -U postgres -d nome_db -c "SELECT name, length(name) AS chars, octet_length(name) AS bytes FROM departments;"
```

### Forçar UTF-8 no terminal PowerShell
```powershell
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8; chcp 65001
```

### Arquivar migrations antigas
```powershell
cd apps\backend\src\database\migrations
mkdir _archive
Move-Item *.ts _archive\
```

---

## Estrutura de pastas relevante

```
whatsapp-service-desk/
├── apps/
│   └── backend/
│       └── src/
│           └── database/
│               ├── migrations/
│               │   ├── _archive/              ← migrations antigas arquivadas
│               │   │   ├── 1700000000000-InitialSchema.ts
│               │   │   ├── 1700000000001-UpdatedSchema.ts
│               │   │   └── ... (demais migrations antigas)
│               │   └── 1788000000000-InitialSchema.ts  ← migration consolidada ativa
│               └── seed.ts
└── infrastructure/
    └── .env                                   ← variáveis de ambiente do projeto
```

---

*Última atualização: Junho 2026*
*Contexto: migração de WIN1252 para UTF-8 no projeto WhatsApp Service Desk — Weber Food Technology*
