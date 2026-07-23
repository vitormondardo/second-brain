## O Ciclo de Agência
A melhor forma de explicar o Claude Code é por meio do ciclo de agência:

1. Você insere um prompt no Claude Code.
2. O Claude reúne o contexto necessário interagindo com o modelo, que retorna texto ou uma chamada de ferramenta que o Claude Code pode executar.
3. Ele realiza uma ação — por exemplo, editar um arquivo ou executar um comando.
4. Ele verifica os resultados e determina se eles atendem ao objetivo definido pelo seu prompt.
5. Se atenderem, o Claude conclui a tarefa e aguarda o próximo prompt. Caso contrário, ele reinicia o ciclo e tenta novamente até que os resultados estejam completos e verificáveis.

Ao longo desse ciclo, você pode adicionar contexto, interromper a operação ou direcionar o modelo para ajudá-lo a alcançar seu objetivo.

<img width="3840" height="2160" alt="instructor_8lsy243ftffjjy1cx9lm3o2bw_public_1775686365_agenticloop 1775686365141" src="https://github.com/user-attachments/assets/098e3d5e-fe07-4e01-9ab1-91e4e7c3988c" />

## Contexto
O Claude possui uma janela de contexto que determina quanto da sua conversa, do conteúdo de arquivos, das saídas de comandos e de outros elementos ele consegue armazenar e consultar. Ao atingir esse limite, o Claude Code compacta a conversa — determinando automaticamente o que pode ser removido ou resumido para reduzir a janela de contexto a um tamanho utilizável.

## Ferramentas
As ferramentas são a base da forma como os agentes operam. A maioria dos assistentes de IA simplesmente recebe texto como entrada e devolve texto como saída. As ferramentas permitem que o Claude Code determine quando executar código para avançar na conclusão de uma tarefa. Isso pode envolver uma ferramenta de leitura de arquivos, uma ferramenta de busca na web ou diversas outras funcionalidades. O Claude Code utiliza compreensão semântica para decidir quando acionar uma ferramenta e como utilizar o resultado obtido.

## Permissões
O Claude Code possui vários modos de permissão:

Comportamento padrão: O Claude solicita permissão explícita antes de editar um arquivo ou executar um comando de shell.
Aceitação automática: Arquivos são editados sem solicitação prévia, mas comandos ainda exigem aprovação.
Modo de planejamento: Utiliza ferramentas de leitura para elaborar um plano de ação antes de iniciar qualquer trabalho.

<img width="3840" height="2160" alt="instructor_8lsy243ftffjjy1cx9lm3o2bw_public_1775686376_video2ask 1775686376586" src="https://github.com/user-attachments/assets/18289f50-0e1c-4d69-a674-9707785d0cd7" />

## Resumo
O Claude Code combina vários conceitos de agentes: um ciclo de atuação autônoma, uma janela de contexto gerenciada, ferramentas e permissões configuráveis ​​— tudo isso dentro do seu terminal. Ele consegue ler sua base de código, realizar ações e verificar o próprio trabalho. É isso que o torna fundamentalmente diferente de uma janela de chat.

## O fluxo de trabalho Explorar → Planejar → Codificar → Confirmar
Se você levar apenas um ensinamento deste curso, que seja este fluxo de trabalho: Explorar, Planejar, Codificar e Confirmar. Sem ele, a maioria das pessoas pula direto para pedir ao Claude que escreva o código — o que resulta em mais ajustes de rota mais adiante.

## Explorar e Planejar
- A maneira mais rápida de lidar com essas duas primeiras etapas é com o Modo de Planejamento. No modo de planejamento, Claude não pode editar arquivos — ele apenas lê os arquivos para coletar informações sobre como abordará a implementação.
- Para entrar no modo de planejamento, pressione Shift + Tab até ver "Modo de Planejamento" abaixo da entrada de texto. Em seguida, digite um comando como:

  <img width="3840" height="2160" alt="instructor_8lsy243ftffjjy1cx9lm3o2bw_public_1775686390_video5planmodeshifttab 1775686390450" src="https://github.com/user-attachments/assets/57ade868-aca6-4b0f-9576-550eb68e2abd" />


  ```bash
  Preciso adicionar a conversão para WebP ao nosso pipeline de upload de imagens. É preciso definir em que ponto do pipeline isso deve ocorrer, se precisamos de novas dependências e qual será a abordagem adotada.
  ```
  
Este é o melhor momento para corrigir o rumo, pois isso ocorre antes de qualquer código ser escrito. Você também pode executar o subagente de exploração sem estar no modo de planejamento, caso queira apenas um resumo geral da sua base de código, sem a intenção de fazer alterações posteriormente.

## Código
Assim que o plano parecer adequado, selecione "aprovar" para aceitá-lo e permitir que o Claude execute os itens da lista. Você pode escolher se o Claude deve aceitar automaticamente as edições de arquivos ou solicitar sua confirmação a cada etapa.

O Claude fará o possível para solucionar problemas antes de considerar o plano "concluído", mas, às vezes, será necessário intervir. Essa é a vantagem de trabalhar com o Modo de Planejamento (Plan Mode): após a execução, você também tem o contexto de como os resultados foram alcançados, o que ajuda a orientar as próximas decisões do Claude.

Algumas dicas para tornar a fase de codificação mais fluida:

- Defina critérios de sucesso. Para que o Claude tenha confiança em seus resultados, ele precisa ter clareza sobre o que define uma execução "correta". Deixe isso explícito ao elaborar o plano.
- Adicione ferramentas. Ferramentas que auxiliam o Claude a atingir seus objetivos reduzem significativamente a necessidade de idas e vindas. Por exemplo, se você estiver desenvolvendo interfaces web (UIs), instale a extensão do Claude para Chrome; assim, o Claude Code poderá controlar uma aba do navegador e testar a interface diretamente.
- Inclua uma suíte de testes. Forneça ao Claude uma suíte de testes que ele possa utilizar para validação contínua. O Claude pode até mesmo escrever testes para você. Antes de finalizar, certifique-se de que os testes sejam uma fonte confiável de verdade para evitar falsos positivos.

Dica rápida: Se você perceber que o Claude continua enfrentando os mesmos problemas, peça a ele que salve a solução no arquivo CLAUDE.md.

## Commit
Depois de testar as alterações por conta própria e ficar satisfeito com os resultados, é hora de enviar (*push*) o seu código. Antes de fazer o *commit*, execute um subagente de revisão de código para analisar o seu trabalho. O subagente oferece um novo olhar sobre a base de código — ele não carrega o viés que o agente principal pode ter adquirido durante a sessão.

<img width="3840" height="2160" alt="instructor_8lsy243ftffjjy1cx9lm3o2bw_public_1775686388_video5claudesubagentcodereviewer 1775686387773" src="https://github.com/user-attachments/assets/e9cc393c-35da-45d2-a063-50103dca62e1" />

## Resumo
Para usar o Claude Code com eficiência, siga o fluxo de trabalho: Explorar, Planejar, Codificar e Confirmar (Commit):

- Explorar fornece ao Claude o contexto relevante de que ele precisa para o seu projeto.
- Planejar cria um plano de ação que o Claude utiliza para avaliar o sucesso.
- Codificar é a interação de ida e volta entre você e o Claude antes de chegar ao resultado final.
- Confirmar ajuda você a revisar e enviar (push) seu código, permitindo que você comece a trabalhar na próxima funcionalidade.
---
# Gerenciamento de Contexto
O contexto é a memória de trabalho do Claude. Cada arquivo que ele lê, cada comando que executa, cada mensagem que você envia — tudo isso ocupa espaço na janela de contexto.

## O que é a janela de contexto?
Pense na janela de contexto como a quantidade de espaço que o Claude consegue manter em sua memória. Sempre que você insere um prompt, o Claude lê um arquivo, executa uma chamada de ferramenta ou recebe o resultado de uma chamada de ferramenta, tudo isso é adicionado à janela de contexto. Como o espaço é limitado, é importante otimizar a forma como você o utiliza.

<img width="3840" height="2160" alt="instructor_8lsy243ftffjjy1cx9lm3o2bw_public_1775686395_video6contextwindowdiagram 1775686395676" src="https://github.com/user-attachments/assets/1746f05d-2cdc-4593-9bdf-51552cd7e169" />

## O que acontece quando o contexto fica cheio
Ao se aproximar do limite, a janela de contexto é compactada automaticamente. A compactação resume detalhes importantes e remove resultados desnecessários de chamadas de ferramentas para liberar espaço. Observe que esse processo pode resultar na perda de detalhes.

<img width="3840" height="2160" alt="instructor_8lsy243ftffjjy1cx9lm3o2bw_public_1775686393_video6compactingcontext 1775686393619" src="https://github.com/user-attachments/assets/c0336d57-c643-4d72-b848-b2c01a88d611" />

## Comandos
Você pode executar a compactação manualmente com o comando `/compact`. Isso compacta tudo até aquele ponto. É útil quando você deseja liberar espaço de contexto e, ao mesmo tempo, manter um registro do que trabalhou anteriormente.

**If you want to completely start from scratch with no memory of the previous session, run /clear. This removes everything.

Para verificar o estado do seu contexto, execute o comando `/context`. Você obterá uma visão geral do tamanho do seu contexto, das categorias que ocupam mais espaço e um gráfico visual mostrando o detalhamento.

<img width="3840" height="2160" alt="instructor_8lsy243ftffjjy1cx9lm3o2bw_public_1775686364_Video_2_01_01_11_11 1775686364258" src="https://github.com/user-attachments/assets/6a6bb831-e1d6-41de-9412-495af85916aa" />

Quando usar cada um
Uma regra prática geral:

- Use `/compact` quando estiver trabalhando em uma funcionalidade específica e atingindo o limite de contexto, mas precisar continuar. É importante manter o contexto relevante para a funcionalidade atual.
- Use `/clear` quando quiser começar uma nova funcionalidade. Você não quer que a conversa anterior introduza vieses em algo novo. Para informações que você deseja que o Claude lembre entre sessões, coloque-as no arquivo `CLAUDE.md`; assim, ele não precisará redescobrir coisas do zero.

<img width="3840" height="2160" alt="instructor_8lsy243ftffjjy1cx9lm3o2bw_public_1775686391_video6claudemdfile 1775686391669" src="https://github.com/user-attachments/assets/26bcd49c-4904-4b6f-ae36-4a6488f46565" />

## Dicas para economizar espaço de contexto
- Seja específico. Um prompt vago pode parecer menor, mas, na verdade, consome mais espaço de contexto a longo prazo. Sem instruções claras, o Claude é forçado a explorar mais a sua base de código e a realizar seu próprio raciocínio — o que ocupa muito mais espaço de contexto do que um prompt detalhado ocuparia.

- Gerencie seus servidores MCP. Por padrão, os servidores MCP carregam todas as suas ferramentas disponíveis no contexto, mesmo quando você não as está utilizando. Se você tiver servidores configurados para tarefas não relacionadas ao projeto atual, considere desativá-los. Você também pode experimentar as "Skills" (Habilidades), que funcionam de forma semelhante aos servidores MCP, mas não carregam tudo para o contexto de imediato.

- Use subagentes. Os subagentes são executados em paralelo com o seu agente principal, mas possuem uma janela de contexto totalmente separada. Para tarefas em que você precisa apenas da resposta — como "onde estão localizados os endpoints de autenticação?" — um subagente realiza o trabalho e retorna apenas um resumo para o seu agente principal, mantendo o contexto primário limpo.
---
# Code Review

O Claude Code possui alguns recursos integrados que tornam seu fluxo de trabalho com o Git mais rápido. Vamos conhecê-los.

## Revisão com um subagente
Antes de enviar um Pull Request (PR), peça ao Claude para usar um subagente para revisar suas alterações. O subagente opera em sua própria janela de contexto e com um olhar renovado — ele não carrega o viés do agente principal, que acabou de passar a sessão escrevendo o código.

Ao criar um subagente revisor de código, restrinja-o a ferramentas de apenas leitura. Um revisor deve apontar problemas, não editar arquivos. Inclua a configuração do subagente no seu repositório para que toda a sua equipe utilize o mesmo revisor.

## A Skill `/commit-push-pr`
A skill `/commit-push-pr` realiza o commit, o push e a criação do PR em uma única etapa. Em vez de executar cada ação manualmente, basta acionar a skill e o Claude cuida de tudo.

Se você tiver um servidor MCP do Slack configurado com canais listados no seu arquivo `CLAUDE.md`, o link do PR será publicado automaticamente no canal da sua equipe.

## Vinculação de Sessão com `--from-pr`
Quando o Claude cria um PR usando o comando `gh pr create`, a sessão é vinculada automaticamente a esse PR. Se precisar retomar o trabalho mais tarde — talvez para responder a comentários de revisão ou corrigir uma falha no build —, execute:

`claude --from-pr <NÚMERO_DO_PR>`
Isso permite continuar exatamente de onde você parou.

## Resumo
Use um subagente para uma revisão de código imparcial antes de fazer o push. Utilize o `/commit-push-pr` para realizar todo o fluxo, do commit à criação do PR, em um único passo. E use o `--from-pr` para retomar o trabalho em um PR posteriormente. São recursos simples, mas que eliminam muita fricção do seu fluxo de trabalho diário.

---
#Customizando o Claude Code

## O arquivo CLAUDE.md
Um dos recursos mais úteis do Claude Code é o arquivo CLAUDE.md. Ele fornece ao Claude Code uma memória persistente sobre o seu projeto.

O problema que ele resolve
Quando você inicia o Claude Code sem um arquivo CLAUDE.md, ele começa do zero todas as vezes. Ele precisa explorar novamente a base de código, identificar as dependências necessárias e entender quais funcionalidades já estão implementadas. Às vezes, ele faz suposições, o que torna mais difícil direcionar o Claude para o caminho certo.

O CLAUDE.md resolve isso. É um arquivo Markdown que você adiciona à raiz do projeto, e o Claude Code o lê automaticamente sempre que você inicia uma sessão. Pense nele como um roteiro de integração (*onboarding*) para a sua base de código. O conteúdo do arquivo CLAUDE.md é anexado ao seu prompt.

Um exemplo
Veja como é um arquivo CLAUDE.md típico:
```
# Projeto

Este é um aplicativo Next.js 15 que utiliza App Router, Tailwind e Drizzle ORM.

# Comandos
- Servidor de desenvolvimento: `pnpm dev`
- Executar testes: `pnpm test`
- Lint: `pnpm lint`

# Estilo de código
- Use indentação de 2 espaços
- Prefira exportações nomeadas (*named exports*)
- Todas as rotas de API ficam em app/api/
- Use *server actions* em vez de rotas de API sempre que possível
```

É simples e direto. Agora, se você pedir ao Claude Code para criar um componente React, ele já saberá usar Tailwind para estilização e seguir suas convenções de código.

Um arquivo CLAUDE.md aberto no VS Code mostrando informações do projeto, comandos e regras de estilo de código

## O CLAUDE.md é para equipes
Você pode (e deve) fazer o *commit* do seu CLAUDE.md no controle de versão para que sua equipe também se beneficie dele. Na verdade, existe uma hierarquia de arquivos de memória, dependendo de a quem eles se destinam:

O CLAUDE.md em nível de projeto fica no diretório raiz do projeto. É compartilhado com a equipe.
O CLAUDE.md em nível de usuário fica na sua pasta de configuração. Este é exclusivo para você e se aplica a todos os seus projetos. Coloque aqui as suas preferências pessoais.

## Dicas
Salve correções na memória. Se você se pegar corrigindo o Claude repetidamente — como ao instruí-lo a sempre usar *server actions* em vez de rotas de API —, peça explicitamente para ele salvar essa regra na memória. Na próxima vez que você abrir o projeto, ele já saberá disso.

Pedindo ao Claude para salvar uma regra no arquivo CLAUDE.md — sempre usar *server actions* em vez de rotas de API.

<img width="3840" height="2160" alt="instructor_8lsy243ftffjjy1cx9lm3o2bw_public_1775686399_video8Askingclaudetoputinmemory 1775686399417" src="https://github.com/user-attachments/assets/0755819d-b841-4e50-bf05-4b2cd07be479" />

Referencie a documentação do projeto. Se houver documentação no projeto que você queira que o Claude consulte, use o símbolo `@` seguido do caminho do arquivo:


```
## README.md
Leia isto se precisar de mais informações: @README.md
```

Comece sem ele. Recomendamos iniciar o projeto sem um arquivo CLAUDE.md para que você possa identificar em quais pontos precisa corrigir constantemente a atuação do modelo. Isso mantém seu CLAUDE.md conciso e focado apenas nas informações necessárias. Quando estiver pronto, execute `/init` para que o Claude gere um arquivo para você.


## Resumo
A diferença entre uma sessão frustrante e uma produtiva no Claude Code geralmente reside no contexto — e o arquivo CLAUDE.md é a maneira de fornecer esse contexto. Comece definindo sua *stack* (pilha tecnológica), suas preferências e seus comandos, e vá expandindo essas definições conforme necessário.

---
# Subagentes
O Claude pode delegar tarefas a subagentes que as subdividem e executam tarefas componentes em paralelo, melhorando o gerenciamento de contexto. Cada subagente opera em sua própria janela de contexto isolada.

## Como funciona
Gerenciar o contexto no Claude Code é importante. Grande parte da janela de contexto é consumida por ações como chamadas de ferramentas para explorar sua base de código ou a realização de pesquisas na web. O que o Claude descobre durante essa exploração nem sempre é relevante para a funcionalidade principal que você está desenvolvendo.

É aí que entram os subagentes. O Claude cria um subagente para lidar com uma tarefa como "explore esta base de código para mim". O subagente opera em paralelo, utilizando sua própria janela de contexto, realiza todo o trabalho de exploração e, ao concluir, resume as descobertas e envia esse resumo de volta ao Claude.

O resultado: você obtém a resposta que procurava, sem que todo o processo necessário para chegar a ela sobrecarregue seu contexto principal.

## Criando seu próprio subagente
Os subagentes são definidos em arquivos Markdown com *frontmatter* em YAML. A maneira mais fácil de começar é deixar o Claude gerar um para você. Execute:

```
/agents
```
Em seguida, selecione "Create new agent" (Criar novo agente). Você passará por etapas que incluem escolher o escopo do agente, definir seu objetivo, selecionar as ferramentas às quais ele terá acesso e até mesmo escolher uma cor para ele.

O Claude gerará um nome, uma descrição e um *prompt* para o subagente. Isso também indica ao Claude quando acionar o subagente, com base nos *prompts* que você fornecer.

## Personalização adicional

Os subagentes podem ser personalizados ainda mais. Aqui estão alguns destaques:

- A memória persistente permite que o subagente retenha informações entre conversas. Isso é excelente se você o utiliza de forma consistente nos mesmos projetos.
- Pré-carregue habilidades nos subagentes adicionando a chave da habilidade e listando as habilidades pelo nome. Observe que, diferentemente das habilidades na sua conversa principal, aqui a habilidade completa é carregada no contexto.

## Resumo
Manter a janela de contexto organizada é uma das melhores formas de manter a produtividade com o Claude Code. Com subagentes, você pode executar um agente em segundo plano para realizar as tarefas mais pesadas e retornar apenas a resposta para a sua janela de contexto principal.

Quer se aprofundar no assunto? Confira nosso curso dedicado: Introdução aos subagentes.

---
# Skills

---
# MCP
O Model Context Protocol (MCP) é um padrão aberto que permite ao Claude Code conectar-se a ferramentas e fontes de dados externas. Ao fazer uma pergunta, o Claude identifica automaticamente quando deve utilizar essas ferramentas para processar melhor a sua solicitação.

Grande parte do seu contexto reside fora da base de código — em bancos de dados, aplicativos de produtividade ou repositórios públicos. O MCP preenche essa lacuna.

## O que você pode fazer com isso?
Primeiro, é importante entender o conceito de "ferramentas" na IA agentiva. As ferramentas conferem a agentes como o Claude Code a capacidade de realizar ações que os ajudam a concluir tarefas de forma mais eficaz. Isso difere da IA ​​convencional, na qual você recebe apenas uma resposta em texto.

Por exemplo, se a sua equipe utiliza o Linear para gerenciamento de projetos, você pode adicionar um servidor MCP do Linear para importar os detalhes das suas demandas específicas. Se precisar de documentação atualizada sobre uma dependência, um servidor MCP de documentação — como o Context7 — pode fornecer essas informações ao Claude Code.

## Adicionando um servidor MCP
Você pode adicionar servidores MCP com o comando `claude mcp add`. Existem dois tipos principais:


- Servidores HTTP destinam-se a serviços remotos. Eles são hospedados pelo provedor de serviços e realizam a conexão pela rede.
- Servidores Stdio destinam-se a processos locais executados na sua máquina.

<img width="3840" height="2160" alt="instructor_8lsy243ftffjjy1cx9lm3o2bw_public_1775686371_video10claudemcpaddcommand 1775686370957" src="https://github.com/user-attachments/assets/121de090-0ff0-48d8-b42b-98ed07e41811" />

- Você pode gerenciar seus servidores com o comando `/mcp` dentro de uma sessão do Claude Code para ver o que está conectado, verificar o status e desativar servidores de que não precisa.

<img width="3840" height="2160" alt="instructor_8lsy243ftffjjy1cx9lm3o2bw_public_1775686374_video10stdioservers 1775686374586" src="https://github.com/user-attachments/assets/b018e5e3-1654-4500-892b-62aca3f5f96a" />

<img width="3840" height="2160" alt="instructor_8lsy243ftffjjy1cx9lm3o2bw_public_1775686374_video10slashmcpcommand 1775686373865" src="https://github.com/user-attachments/assets/b92135d5-a3b3-46da-931d-3b1165c1e59e" />

## Escopo de Servidores MCP
Servidores MCP podem ter seu escopo definido de três maneiras:

1. Local — disponível apenas no projeto atual, somente para você.
2. Usuário — disponível em todos os seus projetos.
3. Projeto — utiliza um arquivo `.mcp.json` que você inclui no controle de versão, garantindo que qualquer pessoa que trabalhe na base de código tenha acesso automático exatamente aos mesmos servidores.

## Custos de Contexto
Servidores MCP adicionam definições de ferramentas à sua janela de contexto — mesmo quando você não as está utilizando ativamente. Se você tiver muitos servidores configurados, isso consome o contexto disponível. Execute `/mcp` para ver o que está conectado e desative qualquer servidor que não esteja sendo utilizado no momento.

<img width="3840" height="2160" alt="instructor_8lsy243ftffjjy1cx9lm3o2bw_public_1775686372_video10disablingmcpservers 1775686372522" src="https://github.com/user-attachments/assets/7bf3d162-5798-4bd2-bd76-4768847421f7" />

Se uma ferramenta tiver um equivalente via CLI (como o `gh` para o GitHub ou o `aws` para a AWS), a CLI é mais eficiente em termos de uso de contexto, pois não adiciona definições de ferramentas persistentes.

Você também pode optar por usar uma *Skill* (habilidade). Uma *Skill* tem seu nome e descrição carregados no contexto, e o Claude só carrega o conteúdo completo da ferramenta quando determina que precisa utilizá-la.

Se as suas ferramentas MCP excederem 10% da janela de contexto, o Claude Code alterna automaticamente para o modo de busca de ferramentas, que identifica as ferramentas adequadas sob demanda — embora essa abordagem possa não ser tão confiável.

## Resumo
O MCP conecta o Claude Code às suas ferramentas e fontes de dados externas. Adicione servidores usando o comando `claude mcp add`. Defina o escopo deles para o seu projeto com o arquivo `.mcp.json`, para que sua equipe os receba automaticamente. Além disso, monitore o uso de contexto desativando servidores que você não esteja utilizando ativamente.

---
# Hooks
Os *hooks* permitem executar comandos em pontos específicos do ciclo de vida do Claude Code. A principal diferença entre os *hooks* e tudo o mais abordado neste curso é que eles são determinísticos: são executados sempre.

Por que usar hooks
Você pode instruir o Claude, por meio do arquivo `CLAUDE.md`, a executar o Prettier após cada edição de arquivo. Na maioria das vezes, ele o fará. Mas, às vezes, não. Um hook garante que isso aconteça sempre, sem exceções.

Casos de uso comuns incluem:

1. Formatação automática após edições de arquivo
2. Registro (log) de todos os comandos executados para fins de conformidade
3. Bloqueio de operações perigosas, como a modificação de arquivos em produção
4. Envio de notificações para você quando o Claude concluir uma tarefa

Como funcionam
Os *hooks* são configurados no seu arquivo `settings.json`. Você seleciona um evento, define opcionalmente um filtro (*matcher*) para especificar a quais ferramentas ele se aplica e fornece um comando a ser executado. Os eventos disponíveis são:

- PreToolUse — executado antes de uma chamada de ferramenta
- PostToolUse — executado após a conclusão de uma chamada de ferramenta
- UserPromptSubmit — executado quando você envia um *prompt*, antes de o Claude processá-lo
- Stop — executado quando o Claude termina de responder
- Notification — executado quando o Claude envia uma notificação

Você pode configurá-los por meio do comando `/hooks` no Claude Code ou editando diretamente o arquivo `settings.json`.

<img width="3840" height="2160" alt="instructor_8lsy243ftffjjy1cx9lm3o2bw_public_1775686375_video11settingsjsonfile 1775686375582" src="https://github.com/user-attachments/assets/3a81cdc9-5ca8-4bc2-a479-13d0a9a7df23" />

## Um exemplo prático
O *hook* mais comum: formatação automática após edições. Configure um *hook* do tipo `PostToolUse` com um filtro (*matcher*) definido como "Edit|MultiEdit|Write" para que ele seja acionado sempre que o Claude modificar um arquivo. O comando verifica a extensão do arquivo e executa o formatador apropriado — Prettier para TypeScript, gofmt para Go ou qualquer ferramenta que seu projeto utilize.

## Bloqueio com PreToolUse
Os *hooks* do tipo `PreToolUse` podem bloquear chamadas de ferramentas antes que elas sejam executadas. Seu *hook* recebe o nome da ferramenta e os dados de entrada (input) no formato JSON via `stdin`. O código de saída (*exit code*) determina o comportamento:

- Código de saída 0 — prosseguir normalmente.
- Código de saída 2 — bloquear a ação. A mensagem enviada para o `stderr` é repassada ao Claude como *feedback*, permitindo que ele saiba o motivo do bloqueio e faça os ajustes necessários.
- 
Qualquer outro código de saída — um erro que não bloqueia a execução; ele é exibido para você, mas não interrompe o processo.

É assim que você impõe regras rígidas. Bloqueie gravações em diretórios de configuração de produção. Bloqueie comandos bash que contenham `rm -rf`. Bloqueie *commits* na *branch* principal (*main*). Bloqueie qualquer coisa que sua equipe precise garantir que seja cumprida, em vez de apenas sugerida.

<img width="3840" height="2160" alt="instructor_8lsy243ftffjjy1cx9lm3o2bw_public_1775686375_video11hooksblock 1775686375002" src="https://github.com/user-attachments/assets/171188f8-5041-495e-a6cf-ad970ec7b4c9" />

## Compartilhando Hooks com sua equipe
Os hooks configurados em `.claude/settings.json` operam em nível de projeto e podem ser incluídos no seu repositório. Isso significa que toda a sua equipe recebe os mesmos hooks automaticamente. Utilize a variável de ambiente `CLAUDE_PROJECT_DIR` em seus comandos para referenciar scripts armazenados no projeto; assim, eles funcionarão independentemente do diretório de trabalho atual do Claude.

## Resumo
Os hooks oferecem controle determinístico sobre o comportamento do Claude Code. Use `PostToolUse` para formatação automática e registro de logs. Use `PreToolUse` para bloquear operações arriscadas. Configure-os via `/hooks` ou no arquivo `settings.json` e inclua-os no repositório para que sua equipe também os utilize.

Se algo precisa acontecer sempre, sem falhas, não coloque isso em um prompt. Coloque em um hook.


