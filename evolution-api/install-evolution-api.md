# Instalação da Evolution API (Windows + PostgreSQL)
Antes de qualquer coisa, verifique o banco de dados - Possível erro, em caso de banco não criado:
```powershell
PS C:\evolution-api> npm.cmd run build

> evolution-api@2.3.7 build
> tsc --noEmit && tsup

src/api/controllers/chat.controller.ts:20:10 - error TS2305: Module '"@prisma/client"' has no exported member 'Contact'.

20 import { Contact, Message, MessageUpdate } from '@prisma/client';

(...)
```

*O erro é direto: o cliente Prisma não foi gerado. A Evolution API v2 usa Prisma como ORM, e o npm install não roda o prisma generate automaticamente — sem isso, o @prisma/client existe mas está vazio, sem os tipos das tabelas.

A Evolution API precisa de um banco próprio — não compartilha com outros criados.
## 1. Criar banco PostgreSQL
Conectar como postgres:
```powershell
C:\pgsql\bin\psql.exe -U postgres
```
Criar banco:

```powershell
CREATE DATABASE evolution_api OWNER wsd_user;
```

Se não houver usuário, crie usuário:
```powershell
CREATE USER wsd_user WITH PASSWORD 'dev_password';
```

Conceder permissões:
```powershell
GRANT ALL PRIVILEGES ON DATABASE evolution_api TO wsd_user;
```

Sair:
```powershell
/q
```
## 2. Clonar o repositório

```powershell
cd D:\
git clone https://github.com/EvolutionAPI/evolution-api.git
cd evolution-api
npm.cmd install
```

## 3. Copie o arquivo de configuração
```powershell
copy .env.example .env
```
## 4. Configurar .env
```powershell
DATABASE_PROVIDER=postgresql
DATABASE_CONNECTION_URI=postgresql://YOUR_USER:YOUR_PASSWORD@localhost:5432/evolution_api?schema=evolution_api
```

## 5. Gerar Prisma Client
A Evolution API utiliza schemas dinâmicos.
```powershell
npm.cmd run db:generate
```
Resultado esperado:
```powershell
✔ Generated Prisma Client
```

## 6. Aplicar migrations
Primeira execução
```powershell
npm.cmd run db:deploy:win
```
Quando perguntar:
```powershell
Overwrite ... migration_lock.toml?
```
Responder:
```powershell
A
```
Verificação manual - Caso necessário:
```powershell
npx.cmd prisma migrate deploy --schema prisma/postgresql-schema.prisma

*Resultado esperado:
No pending migrations to apply.
```
## 7. Compile a build
```powershell
npm.cmd run build
```

## 8. Iniciar
```powershell
npm.cmd run start:prod
*Resultado esperado:
HTTP Server ON: localhost:8080
```

*Caso dê erro no REDIS, siga a instalação em [Instalação Redis](installation-redis.md)

## Depois de ja ter realizado todas as configurações, será apenas um comando diário até a produção em VM:
```powershell
npm.cmd run start:prod
```
