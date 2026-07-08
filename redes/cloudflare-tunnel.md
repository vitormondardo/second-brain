# Expondo a aplicação local na Internet utilizando Cloudflare Tunnel (sem permissão de administrador)

## Objetivo

Expor o backend local (`localhost:3000`) para a Internet utilizando o **Cloudflare Tunnel**, permitindo que serviços externos (como a WhatsApp Cloud API da Meta) consigam acessar a aplicação durante o desenvolvimento.

---

# Pré-requisitos

* Backend NestJS rodando na porta **3000**
* Windows
* PowerShell
* Sem necessidade de permissões de administrador

---

# 1. Verificar se o backend está funcionando

Inicie o backend normalmente.

Exemplo:

```powershell
npm run start:dev
```

Verifique se ele responde localmente:

```powershell
curl http://localhost:3000
```

Resposta esperada:

```json
{
  "statusCode":404,
  "message":"Cannot GET /"
}
```

Essa resposta indica apenas que não existe uma rota `GET /`, mas confirma que o servidor está funcionando.

---

# 2. Baixar o Cloudflare Tunnel

Criar uma pasta:

```powershell
mkdir $HOME\cloudflared
cd $HOME\cloudflared
```

Baixar o executável:

```powershell
Invoke-WebRequest `
  -Uri https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-windows-amd64.exe `
  -OutFile cloudflared.exe
```

Verificar instalação:

```powershell
.\cloudflared.exe --version
```

Exemplo:

```text
cloudflared version 2026.6.1
```

---

# 3. Criar o túnel

Como a rede apresentou problemas com QUIC/IPv6, foi necessário utilizar **HTTP/2** e **IPv4**.

Executar:

```powershell
.\cloudflared.exe tunnel --url http://localhost:3000 --protocol http2 --edge-ip-version 4
```

---

# 4. Confirmar que o túnel foi criado

Será exibida uma URL semelhante a:

```text
https://prototype-weddings-bones-serves.trycloudflare.com
```

No final do log deve aparecer:

```text
SUMMARY: Environment is healthy.
```

e

```text
Registered tunnel connection
```

Essas mensagens confirmam que o túnel foi estabelecido com sucesso.

---

# 5. Testar a URL pública

Abrir no navegador:

```
https://SEU-TUNEL.trycloudflare.com
```

Resposta esperada:

```json
{
  "statusCode":404,
  "message":"Cannot GET /",
  "error":"Not Found"
}
```

Essa resposta confirma que:

```
Internet
        ↓
Cloudflare Tunnel
        ↓
localhost:3000
        ↓
NestJS
```

está funcionando corretamente.

---

# 6. Manter o túnel ativo

O terminal onde o Cloudflare Tunnel está rodando **não pode ser fechado**.

Se ele for encerrado:

```text
CTRL + C
```

o túnel será destruído e uma nova URL será gerada na próxima execução.

---

# Estrutura do ambiente

Durante o desenvolvimento, manter três terminais abertos.

## Terminal 1

Backend

```powershell
npm run start:dev
```

---

## Terminal 2

Cloudflare Tunnel

```powershell
cd $HOME\cloudflared

.\cloudflared.exe tunnel --url http://localhost:3000 --protocol http2 --edge-ip-version 4
```

---

## Terminal 3

Frontend

```powershell
npm run dev
```

---

# Utilizando no WhatsApp Cloud API

A URL gerada pelo Cloudflare será utilizada como base para os webhooks.

Exemplo:

```
https://prototype-weddings-bones-serves.trycloudflare.com
```

Se o endpoint do backend for:

```
POST /webhook
```

a URL pública será:

```
https://prototype-weddings-bones-serves.trycloudflare.com/webhook
```

Essa é a URL que deve ser cadastrada no painel da Meta para receber notificações do WhatsApp.

---

# Observações

* A URL do Quick Tunnel é temporária e muda sempre que o túnel é recriado.
* Não é necessário instalar o Cloudflare Tunnel como serviço do Windows.
* Não são necessárias permissões de administrador para utilizar essa abordagem.
* O backend deve permanecer em execução para que o túnel continue respondendo às requisições externas.
