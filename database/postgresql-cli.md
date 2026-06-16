# Como Instalar o PostgreSQL no Windows via Terminal

Anotações da instalação raiz usando binários e o PowerShell (sem instalador gráfico).

## 1. Download e Extração
Baixa o arquivo zipado direto para o disco C e extrai a pasta `pgsql`.

```powershell
Invoke-WebRequest -Uri "URL_DO_DOWNLOAD" -OutFile "C:\psql.zip"
Expand-Archive -Path "C:\psql.zip" -DestinationPath "C:\"
2. Criar a Pasta de Dados
Onde os bancos serão fisicamente armazenados no disco.

PowerShell
New-Item -ItemType Directory -Path "C:\pgsql\data"
3. Inicializar o Motor (initdb)
Cria as regras do banco e a senha do superusuário (postgres). A flag -W força o pedido da senha.

PowerShell
C:\pgsql\bin\initdb.exe -D "C:\pgsql\data" -U postgres -W
4. Ligar o Servidor (pg_ctl)
Inicia o processo em background e salva os logs em um arquivo de texto.

PowerShell
C:\pgsql\bin\pg_ctl.exe -D "C:\pgsql\data" -l "C:\pgsql\data\logfile.log" start
5. Conectar no Terminal Interativo (psql)
Para executar comandos SQL no servidor que acabou de ser ligado.

PowerShell
C:\pgsql\bin\psql.exe -U postgres
