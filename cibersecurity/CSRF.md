## CSRF (Cross-Site Request Forgery): O Falsário
Enquanto o XSS rouba a chave da casa, o CSRF engana o sistema para abrir a porta para o invasor, aproveitando-se de uma conveniência dos navegadores: o envio automático de cookies.

## Como funciona na prática?
Imagine que você está logado no painel do seu sistema (seusite.com) e o token está em um cookie httpOnly.

1. Você abre uma nova aba e acessa um site de humor inofensivo, mas que foi comprometido por um invasor (sitemalicioso.com).

2. O código HTML do sitemalicioso.com tem um formulário invisível ou uma requisição JavaScript programada para fazer um POST para https://seusite.com/api/usuarios/deletar-conta.

3. Assim que você entra no site de humor, essa requisição é disparada por baixo dos panos.

4. O pulo do gato: Como a requisição está indo para seusite.com, o seu navegador diz: "Opa, eu tenho um cookie de sessão para esse domínio!" e o anexa automaticamente à requisição maliciosa.

5. O backend recebe a requisição, vê o cookie válido, valida o JWT e deleta a conta.

## A Relação com o Cookie httpOnly:
Ao mover o JWT para um cookie httpOnly, você blindou o token contra o XSS (o JS não consegue lê-lo). Mas você terceirizou a entrega do token para o navegador. O backend não tem como saber facilmente se a requisição de deleção de conta partiu de um clique intencional no seu painel ou de um script rodando em outra aba no site do invasor.
