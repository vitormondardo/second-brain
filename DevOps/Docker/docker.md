# Docker: conceitos fundamentais e boas práticas

## Antes de tudo: o que o Docker realmente faz?

Imagine que você tem uma VM Linux na Oracle Cloud.

Sem Docker, todos os serviços rodam diretamente no sistema operacional:

- Node.js
- PostgreSQL
- Evolution API
- Nginx

Todos compartilham:

- sistema de arquivos;
- portas;
- bibliotecas;
- dependências.

Se um serviço precisa atualizar uma biblioteca, outro pode quebrar. Se um processo consumir todos os recursos da máquina, os demais sofrem junto.

É um ambiente compartilhado.

### Sem Docker

```text
VM Linux
├── Node.js 20
├── PostgreSQL 16
├── Evolution API
└── Tudo misturado
```

---

O Docker resolve esse problema criando **containers**.

Cada container possui:

- seu próprio sistema de arquivos;
- suas próprias dependências;
- sua própria versão dos programas;
- isolamento dos demais containers.

Eles só se comunicam quando você autoriza.

### Com Docker

```text
VM Linux
├── Container: backend
│     └── Node.js 20
│
├── Container: postgres
│     └── PostgreSQL 16
│
├── Container: evolution-api
│
└── Container: nginx
```

Cada serviço fica dentro da sua própria "caixa".

---

# Por que existe o docker-compose.yml?

Seria possível iniciar cada container manualmente usando `docker run`.

Por exemplo:

```bash
docker run ...
docker run ...
docker run ...
docker run ...
```

Além de trabalhoso, você teria que lembrar:

- portas;
- volumes;
- redes;
- variáveis de ambiente;
- ordem correta de inicialização.

O **docker-compose.yml** resolve isso.

Ele funciona como uma receita que descreve toda a infraestrutura.

Depois basta executar:

```bash
docker compose up
```

E todos os containers são criados corretamente.

---

# Ensinamento 1 — Por que o PostgreSQL não deve expor ports?

No arquivo original havia:

```yaml
postgres:
  ports:
    - "5432:5432"
```

## O que significa ports?

Quando você escreve:

```yaml
5432:5432
```

está dizendo:

> Pegue a porta 5432 do container e publique na VM.

Resultado:

```text
Internet
      │
      ▼
VM:5432
      │
      ▼
PostgreSQL
```

Agora qualquer pessoa que descubra o IP da sua VM consegue tentar acessar o banco.

Mas quem realmente precisa conversar com o PostgreSQL?

Apenas o backend.

Como backend e banco estão na mesma rede Docker, eles se comunicam internamente.

O backend acessa:

```text
postgres:5432
```

e não precisa da porta publicada.

### Com ports

```text
Internet
     │
VM:5432
     │
Banco
```

### Sem ports

```text
Internet
    │
    ✗

Backend
    │
postgres:5432
    │
Banco
```

## Regra geral

> Só exponha uma porta quando algo **fora do Docker** realmente precisar acessá-la.

---

# Ensinamento 2 — Por que usar 127.0.0.1:3000:3000?

O backend precisa de uma porta publicada.

Mas não para a internet.

Somente para o Nginx.

Em vez de:

```yaml
ports:
  - "3000:3000"
```

utilizamos:

```yaml
ports:
  - "127.0.0.1:3000:3000"
```

O endereço `127.0.0.1` representa o **loopback**.

Significa:

> Aceite conexões apenas originadas da própria máquina.

Assim:

```text
Internet
     │
     ✗

Servidor
     │
Nginx
     │
Backend
```

É como uma porta interna de um prédio que só pode ser aberta por quem já está dentro.

---

# Ensinamento 3 — O que é uma rede Docker?

No Compose:

```yaml
networks:
  wsd-network:
    driver: bridge
```

O driver `bridge` cria uma rede privada.

Todos os containers conectados nela conseguem conversar usando o nome do serviço.

Por exemplo:

```text
backend
    │
    ▼
postgres:5432
```

O Docker resolve automaticamente:

```text
postgres
```

para o IP correto do container.

Isso evita:

- IPs fixos;
- configurações manuais;
- dependência de endereços que mudam.

Além disso, aumenta o isolamento entre aplicações.

---

# Ensinamento 4 — Por que usar restart: unless-stopped?

Containers podem parar por vários motivos:

- erro da aplicação;
- falta de memória;
- reinicialização da VM;
- falhas inesperadas.

Com:

```yaml
restart: unless-stopped
```

o Docker tenta reiniciar automaticamente o container.

Exceto quando você mesmo executa:

```bash
docker compose stop
```

Sem isso:

```text
Erro às 03:00

↓

Sistema parado

↓

Alguém precisa entrar na VM para religar
```

Com `restart`:

```text
Erro às 03:00

↓

Docker reinicia automaticamente

↓

Sistema continua funcionando
```

---

# Ensinamento 5 — O que é healthcheck?

O comando:

```yaml
depends_on:
```

garante apenas que o container foi iniciado.

Ele **não garante** que o serviço esteja pronto.

Exemplo:

O PostgreSQL pode levar alguns segundos para aceitar conexões.

Durante esse tempo:

- o container está rodando;
- o banco ainda não responde.

O backend tenta conectar e falha.

Por isso usamos:

```yaml
healthcheck:
  test:
    - CMD-SHELL
    - pg_isready -U ${DB_USER} -d ${DB_NAME}
  interval: 10s
  timeout: 5s
  retries: 5
```

O comando:

```bash
pg_isready
```

é fornecido pelo PostgreSQL.

Ele responde sucesso apenas quando o banco realmente está aceitando conexões.

Depois usamos:

```yaml
depends_on:
  postgres:
    condition: service_healthy
```

Agora o backend só inicia quando o banco estiver funcional.

---

# Ensinamento 6 — Por que limitar memória e CPU?

Exemplo:

```yaml
deploy:
  resources:
    limits:
      memory: 1g
      cpus: "1.0"
```

Na Oracle Free Tier os recursos são limitados.

Sem limites:

```text
Backend
↓

Consome toda RAM

↓

PostgreSQL morre

↓

Sistema inteiro para
```

Com limites:

Cada serviço possui sua parcela de recursos.

Se um container apresentar vazamento de memória, ele será encerrado sem comprometer os demais.

É como dividir uma pizza em fatias antes de servir.

---

# Ensinamento 7 — Por que usar Nginx?

O NestJS consegue responder requisições HTTP sozinho.

Mas o Nginx adiciona diversas funcionalidades importantes.

## SSL

O Nginx recebe conexões HTTPS.

Internamente conversa com o backend usando HTTP.

Assim o NestJS não precisa lidar diretamente com certificados.

---

## Porta 443

A porta 443 normalmente exige privilégios elevados.

O Nginx pode assumir essa responsabilidade.

O Node.js continua rodando com permissões reduzidas.

---

## Proxy reverso

O Nginx recebe todas as requisições.

Depois decide para onde enviá-las.

Exemplo:

```text
/api/*
```

↓

Backend

Enquanto:

```text
/
```

↓

Frontend

---

## Headers de segurança

O Nginx adiciona automaticamente headers como:

- Strict-Transport-Security
- X-Frame-Options
- X-Content-Type-Options

Sem precisar configurar tudo dentro do NestJS.

---

Pense no Nginx como a recepção de um prédio.

Você não entra diretamente nas salas.

Primeiro passa pela recepção.

Ela verifica para onde você deve ir.

---

# Ensinamento 8 — O que é um proxy reverso?

Fluxo de uma requisição:

```text
Usuário

↓

Nginx (443)

↓

Remove HTTPS

↓

Analisa URL

↓

Encaminha

↓

Backend

↓

Resposta

↓

Nginx

↓

HTTPS

↓

Usuário
```

O cliente nunca conversa diretamente com o backend.

Isso permite:

- centralizar SSL;
- esconder a aplicação;
- adicionar vários servidores futuramente;
- balancear carga.

---

# Ensinamento 9 — Entendendo o nginx.conf

## server_tokens off;

Por padrão o Nginx informa sua versão:

```text
nginx/1.24.0
```

Isso facilita ataques direcionados.

Com:

```nginx
server_tokens off;
```

essa informação deixa de ser enviada.

---

## Strict-Transport-Security

```nginx
add_header Strict-Transport-Security "max-age=31536000" always;
```

Instrui o navegador:

> Durante um ano utilize apenas HTTPS.

Mesmo que o usuário digite:

```text
http://
```

o navegador converte automaticamente para:

```text
https://
```

---

## ACME Challenge

```nginx
location /.well-known/acme-challenge/ {
    root /var/www/certbot;
}
```

Essa rota é utilizada pelo Let's Encrypt.

Ela comprova que você controla o domínio antes de emitir certificados.

---

## X-Real-IP

```nginx
proxy_set_header X-Real-IP $remote_addr;
```

Quando o Nginx encaminha a requisição, o backend enxerga apenas o IP interno do Nginx.

Esse header preserva o IP verdadeiro do usuário.

Muito importante para:

- logs;
- auditoria;
- rate limiting.

---

# Ensinamento 10 — Por que usar pg_dump?

Backup recomendado:

```bash
docker exec wsd-postgres \
pg_dump -U wsd_user wsd_db \
| gzip > backup.sql.gz
```

Muita gente tenta copiar diretamente:

```text
/var/lib/postgresql/data
```

Isso é perigoso.

O PostgreSQL mantém:

- WAL;
- índices;
- páginas internas;
- buffers.

Copiar esses arquivos durante o funcionamento pode gerar um backup inconsistente.

O `pg_dump` conversa com o PostgreSQL e exporta um estado consistente do banco.

O resultado é SQL puro.

Pode ser restaurado usando:

```bash
psql < backup.sql
```

Além disso:

```bash
gzip
```

reduz bastante o tamanho do arquivo.

Um banco de aproximadamente 1 GB normalmente gera um backup compactado entre 100 MB e 200 MB.

---

# O princípio que une todas as decisões

Todas essas escolhas seguem exatamente a mesma filosofia.

> **Expor o mínimo necessário, ao menor número possível de componentes, pelo menor tempo possível.**

Aplicando esse princípio:

- PostgreSQL não precisa falar com a internet → não expõe portas.
- Backend não precisa ser público → fica acessível apenas internamente.
- Nginx é o único ponto de entrada → somente ele publica portas.
- Containers possuem limites de recursos → um serviço não derruba os demais.
- Healthchecks garantem que serviços só iniciem quando seus dependentes estiverem realmente prontos.

Quando esse raciocínio se torna natural, você deixa de apenas copiar arquivos `docker-compose.yml` e passa a entender **por que cada configuração existe**, conseguindo analisar qualquer infraestrutura Docker de forma crítica e segura.
