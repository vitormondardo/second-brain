# CQRS (Command Query Responsibility Segregation)

> **CQRS** é um padrão arquitetural que separa completamente as operações de **escrita (Commands)** das operações de **leitura (Queries)**, permitindo que cada uma evolua e seja otimizada de forma independente.

---

# O problema que o CQRS resolve

Em aplicações tradicionais, a mesma camada normalmente é responsável por:

- Criar registros
- Atualizar registros
- Excluir registros
- Consultar dados
- Realizar buscas
- Aplicar regras de negócio

Isso faz com que uma única classe ou serviço acumule diversas responsabilidades, tornando o sistema mais difícil de manter e evoluir.

Exemplo:

```text
UserService

├── CreateUser()
├── UpdateUser()
├── DeleteUser()
├── GetUser()
├── ListUsers()
└── SearchUsers()
```

Conforme o sistema cresce, essa classe tende a ficar cada vez maior e mais complexa.

---

# O conceito do CQRS

CQRS divide essas responsabilidades em dois fluxos independentes.

```text
                 Cliente
                    │
        ┌───────────┴───────────┐
        │                       │
        ▼                       ▼
   Commands                 Queries
(Escrevem dados)       (Consultam dados)
        │                       │
        ▼                       ▼
 Banco de Escrita        Banco de Leitura
```

A principal ideia é:

- **Commands modificam dados**
- **Queries apenas consultam dados**

---

# Commands

Um **Command** representa uma intenção de alterar o estado da aplicação.

Exemplos:

- Criar usuário
- Atualizar cliente
- Cancelar pedido
- Aprovar pagamento
- Excluir produto

Um Command **nunca retorna dados da entidade**.

Normalmente retorna apenas:

- sucesso
- erro
- ID criado
- código de status

Exemplo:

```csharp
public record CreateUserCommand(
    string Name,
    string Email
);
```

Handler:

```csharp
public class CreateUserHandler
{
    public async Task<Guid> Handle(CreateUserCommand command)
    {
        // Validações

        // Regras de negócio

        // Salvar no banco

        return user.Id;
    }
}
```

---

# Queries

Uma Query representa apenas uma consulta.

Ela **não altera absolutamente nada**.

Exemplos:

- Buscar usuário
- Listar pedidos
- Buscar produtos
- Consultar estoque

Exemplo:

```csharp
public record GetUserByIdQuery(Guid Id);
```

Handler:

```csharp
public class GetUserByIdHandler
{
    public async Task<UserDto> Handle(GetUserByIdQuery query)
    {
        // Apenas consulta

        return dto;
    }
}
```

Observe que normalmente retornamos um **DTO**, e não a entidade completa.

---

# Fluxo completo

## Escrita

```text
Cliente

   │

   ▼

Controller

   │

   ▼

Command

   │

   ▼

Command Handler

   │

   ▼

Domínio

   │

   ▼

Repository

   │

   ▼

Banco de Escrita
```

---

## Leitura

```text
Cliente

   │

   ▼

Controller

   │

   ▼

Query

   │

   ▼

Query Handler

   │

   ▼

Banco de Leitura

   │

   ▼

DTO
```

---

# Bancos diferentes?

Uma dúvida muito comum é pensar que CQRS significa obrigatoriamente utilizar dois bancos de dados.

**Não.**

CQRS separa responsabilidades.

Os bancos podem ser:

## Mesmo banco

```text
PostgreSQL

├── Escrita
└── Leitura
```

Mais comum em sistemas pequenos.

---

## Bancos separados

```text
PostgreSQL
      │
      ▼
Replica

Escrita      Leitura
```

Muito comum em sistemas de alta escala.

---

## Bancos especializados

```text
Commands

PostgreSQL

Queries

ElasticSearch
Redis
MongoDB
```

Cada tecnologia é utilizada para aquilo em que possui melhor desempenho.

---

# Organização de pastas

Uma estrutura bastante utilizada é:

```text
Application

├── Commands
│
│   ├── CreateUser
│   │
│   ├── CreateUserCommand.cs
│   ├── CreateUserHandler.cs
│   └── CreateUserValidator.cs
│
├── Queries
│
│   ├── GetUserById
│   │
│   ├── GetUserByIdQuery.cs
│   ├── GetUserByIdHandler.cs
│   └── UserDto.cs
│
└── Common
```

Essa organização facilita encontrar rapidamente tudo relacionado a uma funcionalidade.

---

# CQRS com MediatR

Em aplicações ASP.NET é muito comum utilizar **MediatR**.

Controller:

```csharp
public async Task<IActionResult> Get(Guid id)
{
    var user = await _mediator.Send(
        new GetUserByIdQuery(id)
    );

    return Ok(user);
}
```

O Controller não conhece nenhuma regra de negócio.

Ele apenas envia a requisição.

O MediatR encontra automaticamente o Handler responsável.

Fluxo:

```text
Controller

      │

      ▼

Mediator

      │

      ▼

Query Handler

      │

      ▼

Banco
```

O mesmo acontece para Commands.

---

# Benefícios

## Código mais organizado

Cada classe possui apenas uma responsabilidade.

---

## Escalabilidade

Leitura e escrita podem crescer independentemente.

---

## Performance

As consultas podem utilizar:

- índices específicos
- cache
- banco otimizado
- projeções

sem afetar a escrita.

---

## Facilidade para testes

Commands e Queries são independentes.

Cada Handler pode ser testado isoladamente.

---

## Melhor separação de responsabilidades

A lógica de negócio fica concentrada apenas nos Commands.

As Queries apenas consultam dados.

---

# Desvantagens

- Maior quantidade de classes.
- Estrutura inicial mais complexa.
- Mais arquivos para manter.
- Pode haver consistência eventual quando leitura e escrita são separadas.

---

# Quando utilizar?

CQRS faz muito sentido quando:

- Sistemas grandes
- SaaS
- ERP
- CRM
- Sistemas financeiros
- Microsserviços
- APIs com muitas consultas
- Aplicações com regras de negócio complexas

---

# Quando evitar?

Para aplicações simples como:

- CRUD básico
- Projetos pequenos
- MVP
- Sistemas internos simples

o custo da complexidade normalmente não compensa.

---

# Exemplo prático

Imagine um e-commerce.

## Criar pedido

```text
CreateOrderCommand

↓

CreateOrderHandler

↓

Pedido criado
```

---

## Consultar pedido

```text
GetOrderQuery

↓

GetOrderHandler

↓

OrderDto
```

Perceba que consultar um pedido nunca passa pelas regras de criação do pedido.

Cada fluxo possui sua própria responsabilidade.

---

# CQRS + Clean Architecture

CQRS combina muito bem com Clean Architecture.

```text
Presentation

↓

Application

├── Commands
├── Queries

↓

Domain

↓

Infrastructure
```

A camada **Application** torna-se responsável por orquestrar todas as operações da aplicação, separando claramente leitura e escrita.

---

# Resumo

| Commands | Queries |
|----------|---------|
| Alteram dados | Apenas consultam |
| Executam regras de negócio | Não executam regras de negócio |
| Escrevem no banco | Leem do banco |
| Retornam sucesso, erro ou IDs | Retornam DTOs |
| Podem disparar eventos | Não modificam estado |

---

# Conclusão

CQRS (**Command Query Responsibility Segregation**) é um padrão arquitetural que promove uma clara separação entre operações de leitura e escrita.

Essa separação melhora a organização do código, facilita a escalabilidade, otimiza a performance e torna a manutenção mais simples em sistemas de médio e grande porte.

Embora introduza uma estrutura mais complexa, ele é uma excelente escolha para aplicações com regras de negócio robustas, alto volume de consultas ou arquiteturas baseadas em microsserviços e Clean Architecture.
