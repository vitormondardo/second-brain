# Fundamentos de Engenharia de Software

> **Guia completo de qualidade, boas práticas, código limpo e arquitetura de software**

---

## A Pergunta Central

**O que faz um software ser bom?**

Um software é bom quando pode ser **mudado com confiança, rapidamente, e por qualquer pessoa do time** — não apenas por quem o escreveu.

Isso é tudo. O resto são técnicas para alcançar isso.

---

## Sumário

1. [Princípios Fundamentais](#1-princípios-fundamentais)
2. [SOLID](#2-solid)
3. [Clean Architecture](#3-clean-architecture)
4. [Design Patterns Essenciais](#4-design-patterns-essenciais)
5. [Código Limpo](#5-código-limpo)
6. [Princípios de Arquitetura de Sistema](#6-princípios-de-arquitetura-de-sistema)
7. [Qualidade de Software](#7-qualidade-de-software)
8. [Observabilidade](#8-observabilidade)
9. [Débito Técnico](#9-débito-técnico)
10. [Convenções que Evitam Discussões](#10-convenções-que-evitam-discussões)
11. [Síntese Final](#11-síntese-final)

---

## 1. Princípios Fundamentais

### 1.1 Separação de Responsabilidades (SoC)

> *"Cada parte do sistema deve fazer uma coisa, e fazer bem."*

O princípio mais antigo e mais ignorado. Quando você mistura responsabilidades — regra de negócio com acesso ao banco, com formatação de resposta, com log, com validação — você cria um emaranhado onde **mudar qualquer coisa arrisca tudo**.

```typescript
// ❌ Errado — tudo junto
function createTicket(req) {
  // valida entrada
  // conecta no banco
  // aplica regra de negócio
  // envia e-mail
  // loga no console
  // retorna HTTP 200
}

// ✅ Certo — responsabilidades separadas
// Controller       → recebe e valida a entrada HTTP
// Use Case         → orquestra a regra de negócio
// Repository       → acessa o banco
// NotificationService → envia e-mail
// Logger           → registra eventos
```

---

### 1.2 Acoplamento vs. Coesão

Dois conceitos opostos que você deve maximizar/minimizar:

| Conceito | Definição | Meta |
|---|---|---|
| **Coesão** | O quanto os elementos dentro de um módulo pertencem juntos | Maximizar (alta coesão) |
| **Acoplamento** | O quanto módulos dependem uns dos outros | Minimizar (baixo acoplamento) |

```typescript
// ❌ Baixa coesão, alto acoplamento
class TicketService {
  createTicket() {}
  sendWhatsApp() {}
  generatePDF() {}
  sendEmail() {}
  calculateCommission() {}
}

// ✅ Alta coesão, baixo acoplamento
class TicketService       { createTicket() {}; closeTicket() {} }
class WhatsAppService     { sendMessage() {} }
class PDFService          { generateReport() {} }
class NotificationService { notifyTechnician() {} }
```

---

### 1.3 DRY — Don't Repeat Yourself

> *"Todo conhecimento deve ter uma representação única, inequívoca e autoritativa no sistema."*

Não é só sobre copiar/colar código. É sobre **duplicação de conhecimento**.

Se a regra _"um ticket só pode ser fechado se tiver responsável"_ está em 3 lugares, quando essa regra mudar você vai lembrar de todos os 3?

> **Atenção:** DRY não significa _"nunca escreva código parecido"_. Significa não duplicar **conceitos de negócio**. Às vezes dois trechos parecidos representam coisas diferentes.

---

### 1.4 YAGNI — You Aren't Gonna Need It

> *"Não implemente funcionalidades que você não precisa agora."*

O maior assassino de projetos é a complexidade prematura. Engenheiros adoram resolver problemas que não existem ainda.

```
❌ "Vou já preparar para multi-tenant, multicloud, event sourcing..."
✅ "Vou resolver o problema de hoje, preparando para o amanhã."
```

---

### 1.5 KISS — Keep It Simple, Stupid

Complexidade é o inimigo. Toda linha de código é uma linha que pode ter bugs.

```
❌ 200 linhas de código genérico e abstrato
✅ 20 linhas específicas e legíveis
```

---

## 2. SOLID

### S — Single Responsibility Principle

Uma classe/módulo deve ter **um único motivo para mudar**.

```typescript
// ❌ Errado — dois motivos para mudar
class TicketService {
  createTicket(data) { /* lógica de negócio */ }
  formatTicketForEmail(ticket) { /* formatação */ }
}

// ✅ Certo — um motivo cada
class TicketService       { createTicket(data) {} }
class TicketEmailFormatter { format(ticket) {} }
```

---

### O — Open/Closed Principle

Aberto para extensão, fechado para modificação. Você deve conseguir adicionar comportamento **sem modificar código existente**.

```typescript
// ❌ Errado — novo modo exige mexer na classe
class Router {
  route(ticket) {
    if (mode === 'round-robin') { /* ... */ }
    if (mode === 'fixed') { /* ... */ }
    // novo modo exige modificar aqui
  }
}

// ✅ Certo — novo modo = nova classe, zero modificação no Router
interface IRoutingStrategy {
  selectTechnician(technicians: Technician[]): Technician
}
class RoundRobinStrategy  implements IRoutingStrategy { /* ... */ }
class FixedStrategy       implements IRoutingStrategy { /* ... */ }
class LeastQueueStrategy  implements IRoutingStrategy { /* ... */ }
```

---

### L — Liskov Substitution Principle

Subtipos devem ser substituíveis por seus tipos base **sem alterar o comportamento correto do sistema**.

```typescript
// ❌ Violação — Square quebra o contrato de Rectangle
class Rectangle { setWidth(w) {}; setHeight(h) {} }
class Square extends Rectangle {
  setWidth(w) {
    super.setWidth(w)
    super.setHeight(w) // quebra o contrato
  }
}

// ✅ Na prática: se IWhatsAppProvider.sendMessage() retorna void,
// toda implementação deve honrar isso.
// EvolutionAPIProvider não pode lançar exceções não documentadas na interface.
```

---

### I — Interface Segregation Principle

Nenhum cliente deve ser forçado a depender de métodos que não usa.

```typescript
// ❌ Interface gorda — obriga dependência de tudo
interface IRepository<T> {
  find(): Promise<T>
  findAll(): Promise<T[]>
  save(entity: T): Promise<T>
  update(entity: T): Promise<T>
  delete(id: string): Promise<void>
  paginate(): Promise<T[]>
  count(): Promise<number>
  bulkInsert(entities: T[]): Promise<void>
}

// ✅ Interfaces específicas — depende só do que usa
interface ITicketReader { findById(id: string): Promise<Ticket | null> }
interface ITicketWriter { save(ticket: Ticket): Promise<Ticket> }
// Use case de leitura só depende de ITicketReader
```

---

### D — Dependency Inversion Principle

> *"Dependa de abstrações, não de implementações."*

Este é o mais poderoso. É a base de toda arquitetura testável.

```typescript
// ❌ Errado — depende da implementação concreta
class CreateTicketUseCase {
  constructor(private repo: TypeORMTicketRepository) {}
  // Trocar TypeORM por Prisma exige mudar aqui
}

// ✅ Certo — depende da abstração
class CreateTicketUseCase {
  constructor(
    @Inject(TICKET_REPOSITORY)
    private repo: ITicketRepository  // interface, não classe
  ) {}
  // Pode trocar TypeORM, Prisma, MongoDB — o use case não sabe
}
```

---

## 3. Clean Architecture

### A ideia central

As camadas internas **nunca conhecem** as camadas externas.

```
┌─────────────────────────────────────────┐
│         Frameworks & Drivers            │  ← NestJS, TypeORM, Evolution API
├─────────────────────────────────────────┤
│           Interface Adapters            │  ← Controllers, Repositories concretos
├─────────────────────────────────────────┤
│          Application Use Cases          │  ← Orquestra regras de negócio
├─────────────────────────────────────────┤
│             Domain / Entities           │  ← Regras de negócio puras
└─────────────────────────────────────────┘
              ↑ dependência sempre aponta para dentro
```

**O que isso compra:**
- Você pode trocar PostgreSQL por MongoDB sem tocar nos use cases
- Você pode trocar NestJS por Express sem tocar no domínio
- Você pode testar use cases sem banco de dados real

### A Regra de Dependência

| Camada | Pode importar |
|---|---|
| `domain` | Nada externo |
| `application` | Apenas `domain` |
| `infrastructure` | `domain` + frameworks (TypeORM, etc.) |
| `presentation` | `application` + NestJS |

> Violar isso (ex: `domain` importando TypeORM) é o **erro arquitetural mais grave**.

### O que fica em cada camada

**`domain/`** — O coração do negócio. Zero dependências externas.
```
entities/       → classes que representam conceitos do negócio
interfaces/     → contratos dos repositórios (ITicketRepository)
enums/          → TicketStatus, TicketPriority, RoutingMode
value-objects/  → objetos imutáveis com regras
```

**`application/`** — Orquestração. Diz o que fazer, sem saber como.
```
use-cases/   → cada arquivo = um caso de uso = uma ação do sistema
dtos/        → formatos de entrada/saída dos use cases
```

**`infrastructure/`** — Implementações concretas. Sabe SQL, HTTP, bcrypt.
```
entities/       → classes TypeORM (@Entity, @Column)
repositories/   → implementam interfaces do domain usando TypeORM
providers/      → clientes de serviços externos (Evolution API)
```

**`presentation/`** — Interface com o mundo externo.
```
controllers/   → endpoints HTTP, validação de entrada
```

---

## 4. Design Patterns Essenciais

### Repository Pattern

Abstrai o acesso a dados atrás de uma interface. Use cases falam com `ITicketRepository`, não com SQL.

```typescript
interface ITicketRepository {
  findById(id: string): Promise<Ticket | null>
  save(ticket: Ticket): Promise<Ticket>
  findByCustomer(customerId: string): Promise<Ticket[]>
}
```

**Por que importa:** Você pode testar toda a lógica de negócio com um repositório em memória, sem banco.

---

### Factory Pattern

Centraliza a criação de objetos complexos.

```typescript
class TicketFactory {
  static create(customer: Customer, department: Department): Ticket {
    return new Ticket({
      id: uuid(),
      protocol: generateProtocol(),
      status: TicketStatus.OPEN,
      openedAt: new Date(),
      customer,
      department,
    })
  }
}
```

---

### Strategy Pattern

Encapsula algoritmos intercambiáveis. Perfeito para modos de roteamento.

```typescript
interface IRoutingStrategy {
  selectTechnician(availableTechnicians: Technician[]): Technician
}

class RoundRobinStrategy  implements IRoutingStrategy { /* ... */ }
class LeastQueueStrategy  implements IRoutingStrategy { /* ... */ }
class FixedStrategy       implements IRoutingStrategy { /* ... */ }
```

---

### Observer / Event-Driven

Desacopla ações que acontecem como consequência de um evento.

```typescript
// Quando ticket é criado, vários handlers reagem de forma independente
ticketCreated → AuditHandler        (registra auditoria)
ticketCreated → RoutingHandler      (sugere técnico)
ticketCreated → NotificationHandler (envia WhatsApp)

// Cada handler não sabe dos outros.
// Adicionar um novo handler não muda o fluxo principal.
```

---

### Unit of Work

Agrupa múltiplas operações em uma transação atômica.

```typescript
// ❌ Risco de inconsistência
await ticketRepo.save(ticket)
await historyRepo.save(history) // se falhar aqui, ticket existe sem histórico

// ✅ Unit of Work — tudo ou nada
await dataSource.transaction(async (manager) => {
  await manager.save(ticket)
  await manager.save(history)
})
```

---

## 5. Código Limpo

### Nomes que revelam intenção

```typescript
// ❌ Opaco
const d = 86400
function proc(t, u) { /* ... */ }
const arr = tickets.filter(t => t.s === 1)

// ✅ Autoexplicativo
const SECONDS_IN_A_DAY = 86400
function processIncomingMessage(ticket: Ticket, user: User) { /* ... */ }
const openTickets = tickets.filter(ticket => ticket.status === TicketStatus.OPEN)
```

---

### Funções pequenas e focadas

```typescript
// ❌ Uma função faz tudo (200 linhas)
async function handleMessage(message) {
  // validação + negócio + banco + whatsapp tudo aqui
}

// ✅ Composição de funções pequenas
async function handleMessage(message: IncomingMessage) {
  const contact = await this.findOrCreateContact(message.phone)
  const ticket  = await this.findOrCreateTicket(contact)
  await this.persistMessage(message, ticket)
  await this.advanceFlow(contact, ticket, message)
}
```

---

### Comentários: qualidade sobre quantidade

```typescript
// ❌ Comentário que explica o óbvio
// Incrementa o contador de tickets
ticketCount++

// ✅ Código que se explica sozinho
this.metrics.incrementActiveTickets()

// ✅ Comentário que explica o POR QUÊ (não o O QUÊ)
// Evolution API tem limite de 1 req/segundo por instância.
// Sem esse delay, mensagens em burst são rejeitadas com 429.
await sleep(1000)
```

---

### Tratamento de erros como cidadão de primeira classe

```typescript
// ❌ Ignora erros — pode ser null
const ticket = await repo.findById(id)
return ticket

// ✅ Erros explícitos e semânticos
const ticket = await repo.findById(id)
if (!ticket) {
  throw new NotFoundException(`Ticket ${id} não encontrado`)
}
return ticket
```

---

### Lei de Demeter — "Não fale com estranhos"

```typescript
// ❌ Navega por objetos internos de outros objetos
const city = customer.address.city.name

// ✅ Encapsula o acesso
const city = customer.getCity()
```

---

## 6. Princípios de Arquitetura de Sistema

### 6.1 Fail Fast

Valide entradas o mais cedo possível. Não deixe dados inválidos entrarem fundo no sistema.

```typescript
// ❌ Descobre o erro no banco, 10 camadas depois
createTicket({ customerId: null })

// ✅ Valida na entrada HTTP — falha imediata
class CreateTicketDto {
  @IsUUID()    customerId: string
  @IsNotEmpty() description: string
}
```

---

### 6.2 Idempotência

A mesma operação executada múltiplas vezes produz o mesmo resultado.

```typescript
// Webhook pode chegar duplicado (Evolution API pode re-enviar)

// ❌ Cria mensagem duplicada em cada chamada
await messageRepo.save(message)

// ✅ Idempotente via messageId externo
await messageRepo.upsert(message, { conflictPaths: ['external_id'] })
```

---

### 6.3 Defesa em Profundidade

Não confie em uma única camada de segurança.

```
Camada 1: Rate limiting no nginx
Camada 2: JWT validation no guard NestJS
Camada 3: RBAC no use case
Camada 4: Row-level constraints no banco
```

---

### 6.4 Tolerância a Falhas

O sistema deve **degradar graciosamente**, não explodir.

```typescript
// Se Evolution API estiver fora, o ticket ainda é criado.
// A notificação WhatsApp entra numa fila de retry.
try {
  await whatsapp.sendMessage(notification)
} catch (error) {
  this.logger.error('WhatsApp indisponível, enfileirando retry', { error })
  await this.retryQueue.add('whatsapp_notification', payload)
}
```

---

### 6.5 Bounded Contexts (DDD)

Cada contexto de negócio tem seu próprio modelo. O mesmo termo pode significar coisas diferentes.

```
Contexto Tickets:    "Cliente" → protocolo, histórico de chamados
Contexto Financeiro: "Cliente" → CNPJ, limite de crédito, inadimplência
Contexto Comercial:  "Cliente" → segmento, potencial, últimas compras
```

> Forçar um único modelo para os três contextos é um erro clássico de design.

---

## 7. Qualidade de Software

### 7.1 Pirâmide de Testes

```
           /\
          /E2E\           ← Poucos, lentos, caros — testam o sistema inteiro
         /------\
        /Integração\      ← Médios — testam colaboração entre módulos
       /------------\
      /   Unitários  \    ← Muitos, rápidos, baratos — testam unidades isoladas
     /________________\
```

**Unitários** — testam um use case com repositórios mockados.

```typescript
it('deve lançar NotFoundException quando ticket não existe', async () => {
  mockRepo.findById.mockResolvedValue(null)

  await expect(useCase.execute({ id: 'inexistente' }))
    .rejects.toThrow(NotFoundException)
})
```

**Integração** — testam o use case com banco real (test database).

**E2E** — testam a API do início ao fim com HTTP real.

---

### 7.2 TDD — Test Driven Development

Escreve o teste **antes** do código.

```
1. Escreve teste que falha  (Red)
2. Escreve código mínimo para passar  (Green)
3. Refatora mantendo o teste passando  (Refactor)
```

> **Benefício principal:** força interfaces limpas. Se é difícil testar, o design está errado.

---

### 7.3 Definition of Done

Um código só está "pronto" quando:

- [ ] Funciona conforme especificado
- [ ] Tem testes cobrindo casos felizes e erros
- [ ] Foi revisado por outro engenheiro
- [ ] Está documentado adequadamente
- [ ] Não introduz débito técnico não gerenciado

---

## 8. Observabilidade

### Os três pilares

| Pilar | Responde | Exemplo |
|---|---|---|
| **Logs** | O que aconteceu | `Ticket criado: #1042, cliente: João` |
| **Métricas** | Quantas vezes / quanto tempo | `tickets_created_total = 142` |
| **Traces** | Por onde passou | `Controller (2ms) → UseCase (15ms) → DB (45ms)` |

```typescript
// Log estruturado — filtrável, pesquisável
this.logger.info('Ticket criado', {
  correlationId,
  ticketId:   ticket.id,
  customerId: customer.id,
  department: department.name,
  durationMs: Date.now() - startedAt,
})
```

> Sem observabilidade, você está voando às cegas em produção.

---

### Correlation ID

Cada requisição recebe um ID único que acompanha **todos os logs** daquela operação. Permite reconstruir o que aconteceu em uma requisição específica entre centenas de logs simultâneos.

```
[req-9f3ef9ef] → Controller recebeu webhook
[req-9f3ef9ef] → Contact identificado: João Silva
[req-9f3ef9ef] → Ticket #1042 criado
[req-9f3ef9ef] → Técnico sugerido: Carlos
[req-9f3ef9ef] → WhatsApp notificado → sucesso
```

---

## 9. Débito Técnico

É o custo futuro de decisões passadas tomadas por conveniência.

| Tipo | Definição | Risco |
|---|---|---|
| **Intencional** | Decisão consciente e documentada | Baixo — está visível |
| **Acidental** | Ninguém percebeu que virou dívida | Alto — está escondido |

```
✅ Débito intencional aceitável:
"Vamos fazer sem cache agora, adicionamos Redis na v2."
→ Registrado em ticket, planejado.

❌ Débito acidental perigoso:
"Alguém escreveu lógica de negócio no controller
e ninguém percebeu faz 6 meses."
→ Espalhado, invisível, crescendo.
```

> **Regra:** Toda dívida técnica deve ser **registrada**, não apenas tolerada.

---

## 10. Convenções que Evitam Discussões

### Convenções de código

```
Linting:         ESLint + Prettier — automático, sem debate
Commits:         feat: | fix: | refactor: | docs: | chore:
Idioma:          inglês para código, português para documentação
Banco:           snake_case nas tabelas e colunas
TypeScript:      camelCase nas variáveis e métodos
PKs:             UUID v4 gerado pelo banco
Timestamps:      created_at e updated_at em toda tabela
```

---

### Architecture Decision Records (ADRs)

Documente **por que** decisões foram tomadas, não apenas **o que** foi decidido.

```markdown
# ADR-001: Usar TypeORM em vez de Prisma

## Contexto
Precisamos de um ORM para PostgreSQL com NestJS.

## Decisão
TypeORM.

## Justificativa
- Integração nativa com NestJS (decorators, módulo oficial)
- Migrations programáticas em TypeScript
- Equipe já tem experiência

## Consequências
- Mais verboso que Prisma
- Aceita-se esse trade-off pela integração e familiaridade
```

---

## 11. Síntese Final

| Dimensão | Princípio | O que resolve |
|---|---|---|
| **Mudança** | SRP + SoC | Mudar uma coisa não quebra outra |
| **Extensão** | OCP + Strategy | Adicionar comportamento sem modificar existente |
| **Teste** | DIP + Repository Pattern | Testar sem dependências externas reais |
| **Compreensão** | Clean Code + nomes | Qualquer engenheiro entende em minutos |
| **Confiança** | Pirâmide de testes | Mudar sem medo de regressão |
| **Rastreabilidade** | Logs + Correlation ID | Saber o que aconteceu em produção |
| **Evolução** | Clean Architecture | Trocar frameworks sem reescrever o negócio |
| **Segurança** | Defense in depth | Múltiplas camadas de proteção |

---

## A Métrica Final

> **Quanto tempo um engenheiro novo leva para entender o sistema e fazer uma mudança com confiança?**
>
> | Tempo | Qualidade da arquitetura |
> |---|---|
> | Dias | Ruim |
> | Horas | Boa |
> | Minutos | Excelente |

---

*Documento gerado para o projeto WhatsApp Service Desk — Weber Food Technology*
