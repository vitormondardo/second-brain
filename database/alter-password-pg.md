# Alteração de senha do banco postgres
```bash
C:\pgsql\bin> .\psql -U postgres -d wsd_dev

psql (16.3)
WARNING: Console code page (437) differs from Windows code page (1252)
         8-bit characters might not work correctly. See psql reference
         page "Notes for Windows users" for details.
Type "help" for help.

wsd_dev=# ALTER USER wsd_user WITH PASSWORD 'cole aqui sua senha';
```
Se tudo certo, retornará:
```bash
ALTER ROLE
wsd_dev=# 
``` 
