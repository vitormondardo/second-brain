# Introduction to Model Context Protocol
O Model Context Protocol (MCP) é uma camada de comunicação que fornece contexto e ferramentas ao Claude, sem exigir que você escreva uma grande quantidade de código de integração trabalhoso. Pense nele como uma forma de transferir a responsabilidade pela definição e execução de ferramentas do seu servidor para servidores MCP especializados.

<img width="1920" height="1080" alt="instructor_a46l9irobhg0f5webscixp0bs_public_1749849216_09_-_001_-_Introducing_MCP_01 1749849216723" src="https://github.com/user-attachments/assets/341bb578-afc6-4ae2-bd4e-cea7ac3a8df2" />

Ao conhecer o MCP, você verá diagramas que mostram a arquitetura básica: um Cliente MCP (seu servidor) conectando-se a Servidores MCP que contêm ferramentas, prompts e recursos. Cada Servidor MCP atua como uma interface para algum serviço externo.

## O problema que o MCP resolve

Digamos que você esteja criando uma interface de chat onde os usuários possam perguntar ao Claude sobre seus dados do GitHub. Um usuário poderia perguntar: "Quais pull requests estão abertos em todos os meus repositórios?" Para lidar com isso, o Claude precisa de ferramentas para acessar a API do GitHub.
<img width="1920" height="1080" alt="instructor_a46l9irobhg0f5webscixp0bs_public_1749849217_09_-_001_-_Introducing_MCP_03 1749849217761" src="https://github.com/user-attachments/assets/aad70423-56b8-4dba-902f-e863ccb2b272" />

O GitHub possui uma funcionalidade imensa — repositórios, *pull requests*, *issues*, projetos e muito mais. Sem o MCP, você precisaria criar uma quantidade enorme de esquemas de ferramentas e funções para lidar com todos os recursos do GitHub.

<img width="1920" height="1080" alt="instructor_a46l9irobhg0f5webscixp0bs_public_1749849218_09_-_001_-_Introducing_MCP_05 1749849218279" src="https://github.com/user-attachments/assets/6f22ae3d-5a1d-4ee3-b1c1-19273b387851" />

Isso significa escrever, testar e manter todo esse código de integração por conta própria. Isso representa um grande esforço e uma carga contínua de manutenção.

## Como o MCP funciona
O MCP transfere essa carga ao deslocar as definições de ferramentas e a execução do seu servidor para servidores MCP dedicados. Em vez de você criar todas essas ferramentas para o GitHub, um servidor MCP para o GitHub encarrega-se disso.

<img width="1920" height="1080" alt="instructor_a46l9irobhg0f5webscixp0bs_public_1749849219_09_-_001_-_Introducing_MCP_08 1749849219623" src="https://github.com/user-attachments/assets/510c6a74-c841-4195-a8b6-9db8f33dde6a" />

## Explicação sobre Servidores MCP
Os servidores MCP fornecem acesso a dados ou funcionalidades implementados por serviços externos. Eles atuam como interfaces especializadas que disponibilizam ferramentas, prompts e recursos de maneira padronizada.

<img width="1920" height="1080" alt="instructor_a46l9irobhg0f5webscixp0bs_public_1749849221_09_-_001_-_Introducing_MCP_10 1749849221341" src="https://github.com/user-attachments/assets/dced935f-dae2-4207-8055-acdb46e416a4" />

No nosso exemplo do GitHub, o servidor MCP para o GitHub contém ferramentas como `get_repos()` e conecta-se diretamente à API do GitHub. Seu servidor comunica-se com o servidor MCP, que cuida de todos os detalhes de implementação específicos do GitHub.

## Perguntas Frequentes
### Quem cria servidores MCP?
Qualquer pessoa pode criar uma implementação de servidor MCP. Frequentemente, os próprios provedores de serviços criam suas implementações oficiais de MCP. Por exemplo, a AWS pode lançar um servidor MCP oficial com ferramentas para seus diversos serviços.

### Qual é a diferença em relação a chamar APIs diretamente?
Os servidores MCP fornecem esquemas de ferramentas e funções já definidos para você. Se você quiser chamar uma API diretamente, terá que criar essas definições de ferramentas por conta própria. O MCP poupa você desse trabalho de implementação.

### O MCP não é a mesma coisa que o uso de ferramentas?
Esse é um equívoco comum. Servidores MCP e o uso de ferramentas são conceitos complementares, porém distintos. Os servidores MCP fornecem esquemas de ferramentas e funções já definidos para você, enquanto o uso de ferramentas refere-se à forma como o Claude realmente chama essas ferramentas. A principal diferença está em quem realiza o trabalho: com o MCP, outra pessoa já implementou as ferramentas para você.

** A vantagem é clara: em vez de manter você mesmo um conjunto complexo de integrações, você pode aproveitar servidores MCP que lidam com a parte mais trabalhosa da conexão com serviços externos.
