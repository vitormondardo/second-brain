# Checando conexão com o database via CLI

```bash
PS C:\pgsql\bin> .\pg_isready
```
Se estiver ativo: Retornará uma mensagem parecida com localhost:5432 - accepting connections.

Se estiver inativo: Retornará no response.
```bash
PS C:\pgsql\bin> .\pg_isready
:5432 - no response
```

## Como reconectar o database
```bash
PS C:\pgsql\bin> .\pg_ctl -D "C:\pgsql\data" start
pg_ctl: another server might be running; trying to start server anyway
waiting for server to start....2026-06-23 14:55:00.612 -03 [42904] LOG:  starting PostgreSQL 16.3, compiled by Visual C++ build 1938, 64-bit
2026-06-23 14:55:00.619 -03 [42904] LOG:  listening on IPv6 address "::1", port 5432
2026-06-23 14:55:00.619 -03 [42904] LOG:  listening on IPv4 address "127.0.0.1", port 5432
2026-06-23 14:55:00.694 -03 [28984] LOG:  database system was interrupted; last known up at 2026-06-23 14:34:14 -03
......2026-06-23 14:55:08.092 -03 [28984] LOG:  database system was not properly shut down; automatic recovery in progress
2026-06-23 14:55:08.098 -03 [28984] LOG:  redo starts at 0/2AFBBC8
2026-06-23 14:55:08.098 -03 [28984] LOG:  invalid record length at 0/2AFBCB0: expected at least 24, got 0
2026-06-23 14:55:08.098 -03 [28984] LOG:  redo done at 0/2AFBC78 system usage: CPU: user: 0.00 s, system: 0.00 s, elapsed: 0.00 s
2026-06-23 14:55:08.111 -03 [31384] LOG:  checkpoint starting: end-of-recovery immediate wait
2026-06-23 14:55:08.128 -03 [31384] LOG:  checkpoint complete: wrote 2 buffers (0.0%); 0 WAL file(s) added, 0 removed, 0 recycled; write=0.002 s, sync=0.004 s, total=0.021 s; sync files=3, longest=0.002 s, average=0.002 s; distance=0 kB, estimate=0 kB; lsn=0/2AFBCB0, redo lsn=0/2AFBCB0
.2026-06-23 14:55:08.161 -03 [42904] LOG:  database system is ready to accept connections
 done
server started
```
