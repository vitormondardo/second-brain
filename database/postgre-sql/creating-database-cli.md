# Como criar um banco de dados Postgres via linha de comando{

## 1. Logar como super usuário

.\psql -U postgres -- powershell

sudo -u postgres psql -- linux

Conecte-se ao servidor principal
```powershell
"C:\pgsql\bin\psql.exe" -U postgres
```
*O terminal deverá retornar:
```powershell
postgres=#
```
## 2. Crie o banco de dados
```powershell
CREATE DATABASE fitpro;
```
