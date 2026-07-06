# Proteção de limite de tentativas de login

## 

ThrottlerGuard
100 requisições
60 segundos

Isso significa que qualquer IP pode fazer no máximo:

100 requisições por minuto

Não importa se é:

login
cadastro
listar usuários
buscar produtos
etc.

Tudo entra nessa mesma conta.

Visualmente:
```bash

                IP do usuário

                     │
                     ▼

        100 requisições / minuto
```
Não importa se é para: 
- login
- produtos
- clientes
- dashboard
- perfil

Ou seja, o sistema limita o uso geral da API.

Isso é bom.

Mas...

Não resolve o problema específico do login.
