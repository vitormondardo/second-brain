# Erro 22P05
Erro 22P05 (código SQLSTATE para untranslatable character) 

Exemplo:
```bash
(\nError occurred during query execution:\nConnectorError(ConnectorError { user_facing_error: None, kind: QueryError(PostgresError { code: \"22P05\", message: \"character with byte sequence 0xf0 0x9f 0x8f 0xa2 in encoding \\\"UTF8\\\" has no equivalent in encoding \\\"WIN1252\\\"\", severity: \"ERROR\", detail: None, column: None, hint: None }), transient: false })"]}} {"req":{"id":"ecd3d8b2-3bcb-47a0-872b-a6c084905c5d","method":"POST","url":"/api/v1/webhooks/whatsapp","query":{},"params":{"0":"webhooks/whatsapp"},"headers":{"accept":"application/json, text/plain, */*","content-type":"application/json","user-agent":"axios/1.13.2","content-length":"1312","accept-encoding":"gzip, compress, deflate, br","host":"localhost:3000","connection":"keep-alive","x-correlation-id":"ecd3d8b2-3bcb-47a0-872b-a6c084905c5d"},"remoteAddress":"::1","remotePort":55918},"correlationId":"ecd3d8b2-3bcb-47a0-872b-a6c084905c5d","context":"EvolutionApiClient"}
```
