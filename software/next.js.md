```bash
▲ Next.js 16.2.6 (Turbopack)

- Local:         http://localhost:3000

- Network:       http://10.238.1.101:3000

- Environments: .env.local
```
Quando você roda um projeto Next.js (ou React, Node, etc.), ele levanta o servidor de desenvolvimento e te dá essas duas rotas. Ambas apontam para o mesmo projeto na sua máquina, mas servem para propósitos diferentes.

1. Local (http://localhost:3000)
O que é: É o endereço de loopback da sua própria máquina (também conhecido como 127.0.0.1).

Para que serve: Serve para você acessar o projeto no navegador do mesmo computador onde o código está rodando.

Acesso: Nenhuma outra pessoa ou dispositivo consegue acessar o seu projeto usando esse link. Ele é estritamente interno.

2. Network (http://10.238.1.101:3000)
O que é: Esse é o endereço IP local do seu computador na rede Wi-Fi ou cabeada em que você está conectado no momento (sua LAN).

Para que serve: Ele permite que outros dispositivos conectados à mesma rede de internet acessem o seu projeto em desenvolvimento.

Caso de uso: É perfeito para testar como o seu site está ficando na tela do celular. Basta conectar o celular no mesmo Wi-Fi do computador, abrir o navegador do celular e digitar 10.238.1.101:3000.

Por que o Network diz "Not Secure" (Não Seguro)?
Esse aviso no navegador é completamente normal e inofensivo durante o desenvolvimento local. Ele acontece por causa de como os navegadores lidam com o protocolo HTTP:

Falta de Certificado SSL (HTTPS): O seu servidor local está rodando em http:// (texto plano) e não em https:// (criptografado). Para ter o "cadeadinho" de segurança, é necessário um certificado SSL válido.

A exceção do localhost: Os navegadores modernos (como Chrome, Edge, Firefox) têm uma regra embutida que confia no localhost automaticamente. Eles sabem que os dados não estão viajando pela rede, então não mostram o aviso de "Não Seguro", mesmo sendo HTTP.

A regra do IP: Quando você acessa via IP (10.238.1.101), o navegador trata isso como um acesso de rede real. Como a conexão é HTTP sem certificado, ele cumpre o papel dele e avisa: "Ei, essa conexão não é criptografada".

Resumo: Não se preocupe com o aviso de "not secure" enquanto estiver desenvolvendo. Quando você fizer o deploy do seu site para produção em um servidor real (como a Vercel, por exemplo), o serviço aplicará automaticamente o HTTPS e o aviso desaparecerá para os seus usuários finais.
