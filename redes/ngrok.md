# Como instalar Ngrok na máquina via terminal
## O que é:
O Ngrok é uma ferramenta de linha de comando (CLI) que cria um túnel seguro entre a sua máquina local e a internet. Ele permite que você exponha um servidor rodando no seu computador (como um localhost) para o mundo exterior, fornecendo uma URL pública e temporária, sem precisar abrir portas no roteador ou configurar firewalls

## 1 - instalar pacote
```bash
winget install ngrok.ngrok
```
Esperado:
```bash
Found Ngrok [Ngrok.Ngrok] Version 3.3.1
This application is licensed to you by its owner.
Microsoft is not responsible for, nor does it grant any licenses to, third-party packages.
Downloading https://bin.equinox.io/a/cJk8dzafvmN/ngrok-v3-3.3.1-windows-amd64.zip
  ██████████████████████████████  8.62 MB / 8.62 MB
Successfully verified installer hash
Extracting archive...
Successfully extracted archive
Starting package install...
Path environment variable modified; restart your shell to use the new value.
Command line alias added: "ngrok"
Successfully installed
```

## 2 - Crie sua conta em: https://ngrok.com/

## 3 - Configure e adicione seu AuthToken à máquina
```bash
ngrok config add-authtoken ...
```

## 4 - Exponha o local que você deseja
```bash
ngrok http --url=giddily-kooky-pouncing.ngrok-free.dev 30xx
```
