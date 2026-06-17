qual a função do redis nesse caso?

2:47 PM
Na Evolution API, o Redis serve como cache de sessão do WhatsApp.

Quando uma mensagem chega, a Evolution API precisa verificar rapidamente o estado da conexão, dados da instância e mensagens recentes sem ir ao banco toda vez. O Redis guarda essas informações em memória com acesso em microssegundos.

Sem Redis, ela cai para o cache local (CACHE_LOCAL_ENABLED=true), que faz a mesma coisa mas dentro do próprio processo Node.js — sem persistência entre reinicializações e sem suporte a múltiplas instâncias rodando em paralelo.

Para o MVP com uma única instância WhatsApp rodando localmente, a diferença é imperceptível. O Redis só se torna relevante quando você tiver múltiplos processos da Evolution API rodando atrás de um load balancer, porque aí o cache local de cada processo seria isolado — eles não enxergariam o estado um do outro. Com Redis centralizado, todos compartilham o mesmo cache.

