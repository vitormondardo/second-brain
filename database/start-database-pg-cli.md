# Como iniciar o servidor PostgreSQL no Windows via Terminal

## 1. Salva o arquivo de instalação na sua máquina

## 2. Encontre e salve o local de instalação do arquivo em sua máquina

Exemplo: C:\pgsql\bin\pg_ctl.exe

## 3. Inicie o banco de dados

```powershell
.\pg_ctl.exe start -D C:\pgsql\data
