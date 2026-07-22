# Prisma
## O que é?
Um ORM moderno para Node.js e TypeScript.

## Onde se aplica?
Quando sua aplicação precisa ler, criar, atualizar e remover dados em bancos como PostgreSQL, MySQL, SQLite ou SQL Server.

## Função principal
Traduz operações em TypeScript para SQL.

## Prisma
### O que é?
Prisma é um ORM (Object-Relational Mapper). Ele permite que você manipule o banco usando código TypeScript, sem precisar escrever SQL para operações comuns.

### Onde se aplica?
- Backends em Node.js / TypeScript.
- APIs com NestJS, Express, Fastify.
- Bancos relacionais: PostgreSQL, MySQL, SQLite, SQL Server.

### Função
- Buscar registros.
- Criar registros.
- Atualizar registros.
- Excluir registros.
- Gerar tipos TypeScript automaticamente.
- Criar migrações do banco.

### Exemplo
```typescript
const user = await prisma.user.findUnique({
  where: { id: 1 },
});

// O que ele gera internamente
SELECT * FROM users WHERE id = 1;
```

## Resumo do Prisma
| Problema que resolve | Acesso ao banco |
| :--- | :---: |
| Camada | Infraestrutura / Persistência |
| Substitui SQL? | Parcialmente |
| Usado com | PostgreSQL, MYSQL |

## Em qual momento decide-se usar Prisma?
O Prisma é um ORM (Object-Relational Mapper) moderno para Node.js e TypeScript. Você costuma optar por ele em cenários específicos:

- Quando você usa TypeScript: É o maior ponto forte do Prisma. Ele gera tipos estáticos automaticamente a partir do seu banco de dados. Se você alterar uma coluna no banco, o código acusa o erro em tempo de compilação, sem surpresas em produção.

- Projetos novos (Greenfield): O Prisma facilita muito o início de um projeto porque você define o esquema no arquivo schema.prisma e ele cuida das migrações e da criação dos tipos.

- APIs em Node.js / Next.js / Express / NestJS: Quando você precisa realizar operações de CRUD (criar, ler, atualizar, deletar) e relacionamentos sem precisar escrever queries SQL manuais em texto solto.

- Equipes que valorizam produtividade: Ele oferece autocomplete inteligente no editor (VS Code), autocompletando relacionamentos, campos e filtros enquanto você digita.

- Projetos que usam PostgreSQL, MySQL, SQLite, SQL Server, CockroachDB ou MongoDB.

## Quando NÃO usar o Prisma?
- Projetos com queries SQL extremamente complexas: Se você precisa de muitas subqueries aninhadas, CTEs complexas ou otimizações de performance muito finas no nível do banco, o Prisma pode se tornar uma limitação (ou exigir o uso de prisma.$queryRaw).
- Microserviços Serverless com conexões limitadas (sem Data Proxy): Como cada instância abre um pool de conexões, é preciso atenção na configuração ao usar em funções AWS Lambda ou Vercel (embora ferramentas como Prisma Postgres / Accelerate ajudem a mitigar isso).

## Quais são as outras opções?
O mercado de bancos de dados para JavaScript e TypeScript evoluiu bastante e hoje é dividido em três categorias principais:

### A. ORMs Modernos e Type-Safe (Concorrentes diretos)
#### Drizzle ORM:
- O que é: O principal concorrente do Prisma atualmente.
- Diferencial: Muito mais leve, sem "magic code" por trás, e traduz quase 1:1 para SQL puro. Oferece performance superior e executa perfeitamente em environments edge/serverless (Cloudflare Workers, Vercel).

#### TypeORM:
- O que é: Um dos ORMs mais tradicionais do Node.js, baseado no padrão Data Mapper e Active Record (similar ao Hibernate do Java ou Entity Framework do .NET).
- Diferencial: Muito utilizado junto com o NestJS. Usa decorators (@Entity(), @Column()) diretamente nas classes TypeScript.

### B. Query Builders (Mais controle sobre o SQL)
Se você não quer a abstração pesada de um ORM, mas quer evitar concatenar strings SQL na mão:

#### Kysely:
- O que é: Um query builder end-to-end type-safe.
- Diferencial: Escreve-se algo muito próximo do SQL puro, mas com autocomplete e verificação de tipos completa em TypeScript.

#### Knex.js:
- O que é: O query builder mais antigo e consolidado do ecossistema Node.js.
- Diferencial: Muito estável e flexível, embora o suporte a TypeScript não seja tão nativo/avançado quanto o do Kysely.
