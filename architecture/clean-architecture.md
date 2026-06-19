#Clean Architecture. A regra central é uma só:

## As dependências sempre apontam para dentro.

Visualize como cebola:
```powershell
┌─────────────────────────────────────────┐
│           presentation                  │  ← sabe do mundo externo
│   ┌─────────────────────────────────┐   │
│   │         infrastructure          │   │  ← sabe de banco, HTTP
│   │   ┌─────────────────────────┐   │   │
│   │   │       application       │   │   │  ← orquestra
│   │   │   ┌─────────────────┐   │   │   │
│   │   │   │     domain      │   │   │   │  ← não sabe de nada externo
│   │   │   └─────────────────┘   │   │   │
│   │   └─────────────────────────┘   │   │
│   └─────────────────────────────────┘   │
└─────────────────────────────────────────┘
```

- domain não importa nada de fora. infrastructure pode importar domain. presentation pode importar tudo. Nunca o contrário.

- Isso significa que quando você vai adicionar uma feature, você sempre começa pelo centro e vai para fora. Nunca comece pelo controller.

A estrutura de um módulo
Cada funcionalidade do sistema vive em src/modules/<nome>/ com exatamente essa estrutura:
```powershell
modules/tickets/
│
├── domain/
│   ├── entities/           ← o que é um Ticket (regras puras)
│   ├── interfaces/         ← ITicketRepository (contrato do repositório)
│   └── enums/              ← TicketStatus, TicketPriority
│
├── application/
│   ├── use-cases/          ← CreateTicketUseCase, CloseTicketUseCase
│   └── dtos/               ← CreateTicketDto (formato de entrada/saída)
│
├── infrastructure/
│   ├── entities/           ← TicketOrmEntity (@Entity do TypeORM)
│   └── repositories/       ← TicketRepository (implementa ITicketRepository)
│
├── presentation/
│   └── controllers/        ← TicketsController (endpoints HTTP)
│
└── tickets.module.ts       ← cola tudo com DI
```
Agora, o que cada camada realmente faz e por que você precisa dela:

## As 5 camadas — o que cada arquivo faz e por que existe
### Camada 1 — domain/entities/
O que é: A definição pura do conceito de negócio. Sem NestJS, sem TypeORM, sem banco.

Por que existe: Para que as regras de negócio não fiquem espalhadas em controllers ou repositórios. Se a regra mudar, você muda aqui — e só aqui.

Quando você mexe: Quando o conceito em si muda. Ex: "um ticket agora tem prioridade" → você adiciona priority na entidade Ticket.

Sinal de que está errado: Se você vê import { Column } from 'typeorm' aqui, está na camada errada.

### Camada 2 — domain/interfaces/
O que é: O contrato do repositório. Uma interface TypeScript que diz "quem quiser guardar/buscar tickets precisa implementar esses métodos".

Por que existe: Para que os use cases não saibam se os dados estão no PostgreSQL, MongoDB ou num arquivo de texto. Eles só conhecem a interface.

Quando você mexe: Quando você precisa de um novo método de busca ou persistência. Ex: "preciso buscar tickets pelo número de protocolo" → você adiciona findByProtocol(protocol: string): Promise<Ticket | null> na interface.

Regra: Primeiro a interface, depois a implementação. Nunca o contrário.

### Camada 3 — application/use-cases/
O que é: A orquestração. Cada arquivo é uma ação do sistema ("criar ticket", "fechar ticket", "atribuir técnico"). O use case não sabe SQL, não sabe HTTP — ele só coordena.

Por que existe: Para que a lógica de negócio não fique no controller (que pode mudar) nem no repositório (que é infraestrutura). O use case é o coração testável do sistema.

Quando você mexe: Toda nova ação do sistema vira um use case. Ex: "quando um ticket é fechado, enviar mensagem ao cliente" → você cria CloseTicketUseCase.

Sinal de que está errado: Se você vê @Controller ou @Entity aqui, está na camada errada.

### Camada 4 — infrastructure/repositories/
O que é: A implementação concreta da interface do domain. Aqui você escreve SQL via TypeORM.

Por que existe: Para isolar tudo que é "mecânica de banco de dados" num lugar só. Se você trocar PostgreSQL por MongoDB, você só muda aqui.

Quando você mexe: Quando você adicionar um método na interface do domain, você precisa implementá-lo aqui.

Regra: O repositório implementa a interface. Sem interface definida antes, não existe repositório.

### Camada 5 — presentation/controllers/
O que é: O ponto de entrada HTTP. Recebe a requisição, valida os dados com o DTO, chama o use case, retorna a resposta.

Por que existe: Para separar "como o mundo exterior chama o sistema" de "o que o sistema realmente faz". O controller não tem lógica de negócio — ele só delega.

Quando você mexe: Quando você precisa de um novo endpoint ou mudar a forma de entrada/saída de um existente.

Sinal de que está errado: Se você vê regras de negócio aqui (condicionais sobre status, cálculos, validações de negócio), extraia para um use case.

O arquivo que cola tudo — *.module.ts
O que é: O arquivo de configuração do módulo NestJS. Define quais providers existem, quais controllers existem, e faz os bindings de injeção de dependência.

Por que existe: Sem ele, o NestJS não sabe que ITicketRepository deve ser resolvido com TicketRepository. É aqui que você registra o símbolo e a classe concreta.

Quando você mexe: Sempre que você criar um novo provider (use case, repositório, client HTTP) ou um novo controller. Se você criar um arquivo e não registrar aqui, o NestJS vai ignorar.

typescript
// O padrão de binding que você vai ver repetido em todo módulo:
{ provide: TICKET_REPOSITORY, useClass: TicketRepository }
//  ↑ símbolo (contrato)          ↑ implementação concreta
O procedimento — o que percorrer em ordem
Toda feature nova segue esse roteiro. Nunca pule etapas.
```powershell
NOVA FEATURE
     │
     ▼
① DOMAIN — o que muda no conceito de negócio?
     │  → entidade precisa de campo novo?      → domain/entities/
     │  → repositório precisa de método novo?  → domain/interfaces/
     │
     ▼
② APPLICATION — qual ação nova o sistema precisa executar?
     │  → criar novo use case                  → application/use-cases/
     │  → criar/ajustar DTO                    → application/dtos/
     │
     ▼
③ INFRASTRUCTURE — como persistir/buscar esses dados?
     │  → implementar método novo no repositório → infrastructure/repositories/
     │  → atualizar ORM entity se mudou schema   → infrastructure/entities/
     │  → gerar migration se mudou tabela
     │
     ▼
④ PRESENTATION — como o mundo externo acessa essa ação?
     │  → criar ou ajustar endpoint no controller → presentation/controllers/
     │
     ▼
⑤ MODULE — registrar tudo que foi criado
          → tickets.module.ts
``` 

Por que essa ordem importa: Se você começa pelo controller, você vai inventar estruturas de dados "na hora" que vão inconsistir com o domínio. Se você começa pelo domain, tudo que vem depois é consequência natural.

Como identificar qual módulo mexer
Quando chegar uma feature, a primeira pergunta é: "essa mudança afeta qual entidade de negócio?"

A feature envolve...	Módulo
Login, token, senha	auth
Clientes do WhatsApp	customers
Técnicos e disponibilidade	technicians
Departamentos e setores	departments
Chamados e status	tickets
Mensagens e webhook	messages
Para qual técnico vai	routing
Enviar/receber WhatsApp	whatsapp
Log de ações críticas	audit
Se a feature afeta dois módulos (ex: "fechar ticket e enviar mensagem"), o use case fica no módulo "dono" da ação principal (tickets) e ele recebe o outro módulo como dependência injetada.

Para o frontend — o procedimento paralelo
O frontend tem uma estrutura mais simples:

```powershell
src/
├── api/           ← funções fetch (uma por recurso)
├── types/         ← interfaces TypeScript dos dados da API
├── pages/         ← uma página por rota
├── components/    ← componentes reutilizáveis
└── hooks/         ← lógica compartilhada entre páginas
```
O procedimento para uma nova página é sempre:

① types/index.ts     → definir a interface TypeScript do dado que virá da API
② api/<recurso>.ts   → criar a função que faz o fetch
③ pages/<Nome>.tsx   → criar a página que usa o hook e renderiza
④ App.tsx            → registrar a rota
⑤ Sidebar.tsx        → adicionar o item de navegação
Por que começar pelos types: Se você não sabe o formato do dado que a API retorna, você não consegue escrever o componente sem inventar. O type é o contrato entre frontend e backend. Você olha o api.md e cria o type correspondente antes de qualquer componente.

## O checklist mental — 3 perguntas antes de escrever qualquer linha
### 1. Em qual camada isso pertence?
Regra de negócio → use case. Busca no banco → repositório. Entrada HTTP → controller. Se você hesitar, escolha a camada mais interna que fizer sentido.

### 2. Existe uma interface que precisa mudar primeiro?
Se a feature precisa de um novo método de busca ou persistência, a interface no domain precisa ser atualizada antes da implementação no repositório.

### 3. O módulo .ts está registrando tudo?
Criou um use case novo? Registre em providers. Criou um controller novo? Registre em controllers. Criou um repositório novo? Registre o binding { provide: SYMBOL, useClass: Implementation }.

Exemplo aplicado — a feature do QR Code que fizemos
Para você ver o raciocínio em ação:
```powershell
① DOMAIN — mudou algum conceito?
   Não. QR Code não é uma entidade de negócio nova.
   Não precisou mexer em nada aqui.

② APPLICATION — nova ação do sistema?
   Sim, mas simples demais para virar use case separado.
   O client HTTP já é o suficiente como abstração.

③ INFRASTRUCTURE — mudou como buscar dados?
   Sim. evolution-api.client.ts ganhou getQrCode() e getConnectionState()
   porque são chamadas HTTP para a Evolution API.

④ PRESENTATION — novo endpoint?
   Sim. whatsapp.controller.ts ganhou GET /qrcode e GET /status

⑤ MODULE — precisou registrar algo novo?
   Não. O client já estava registrado. Só os métodos novos.

FRONTEND:
① types → nenhum type novo (resposta simples)
② api   → poderia ter criado whatsapp.api.ts, mas fetch direto na página
           é aceitável para MVP
③ page  → WhatsAppPage.tsx
④ rota  → App.tsx
⑤ menu  → Sidebar.tsx
```
Quando você internalizar esse fluxo, a pergunta deixa de ser "onde eu mexo?" e passa a ser "o que precisa mudar no conceito de negócio?" — e o resto segue naturalmente.








