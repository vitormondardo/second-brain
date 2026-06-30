## Parte 2 — Camadas adicionais que fecham as lacunas que sobram
Mesmo com tudo acima, ainda restam vetores que cookie+CSRF não cobrem. Para "minimizar ao máximo":

1. Content-Security-Policy (CSP) restritivo no frontend, bloqueando scripts inline e origens não whitelisted — isso reduz drasticamente a chance de um XSS conseguir executar algo útil, mesmo que exista uma falha de sanitização em algum componente React.
2. Helmet no NestJS (npm install helmet) — seta automaticamente vários headers de segurança (X-Content-Type-Options, X-Frame-Options, Strict-Transport-Security, etc.) que mitigam clickjacking, MIME sniffing e downgrade de HTTPS.
3. Rotação de JWT_SECRET com suporte a múltiplas chaves — permite invalidar todos os tokens emitidos sem derrubar sessões válidas instantaneamente (usando kid no header do JWT e aceitando duas chaves durante a transição).
4. Expiração curta de accessToken (você já tem 8h — para um painel administrativo interno isso é razoável, mas para reduzir ainda mais a janela de um token roubado, considerar algo como 15-30 min com refresh silencioso é o padrão de mercado mais agressivo).
5. Logout invalida o refreshToken no banco, não só limpa o cookie — senão um token roubado antes do logout continua válido até expirar.
