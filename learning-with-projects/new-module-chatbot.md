Como Criar um Módulo do Zero

  Vou te ensinar usando o próprio projeto como laboratório. A ideia é que depois você consiga fazer qualquer módulo novo sem precisar de mim.

  ---
  1. O Modelo Mental: Por que essa estrutura existe?

  O projeto usa Clean Architecture. O principio central é um só:
```bash
  ▎ Dependências sempre apontam para dentro.

  domain  ←  application  ←  infrastructure
                                ↑
                            presentation
```
  Por que isso importa na prática?

  Imagine que você quer trocar o PostgreSQL por MongoDB amanhã. Se o seu use case importa o TypeORM diretamente, você vai ter que mudar tudo. Se ele importa apenas uma interface
  (ICustomerRepository), você só troca a implementação, e o resto do sistema nem sabe que mudou.

  Cada camada tem uma responsabilidade única:
```bash
  ┌────────────────┬──────────────────────────────────────┬───────────────────────────────────┐
  │     Camada     │      Pergunta que ela responde       │         Exemplo concreto          │
  ├────────────────┼──────────────────────────────────────┼───────────────────────────────────┤
  │ domain         │ O que é esse conceito no negócio?    │ "Um cliente tem telefone e nome"  │
  ├────────────────┼──────────────────────────────────────┼───────────────────────────────────┤
  │ application    │ O que posso fazer com esse conceito? │ "Listar clientes, criar cliente"  │
  ├────────────────┼──────────────────────────────────────┼───────────────────────────────────┤
  │ infrastructure │ Como isso se conecta ao mundo real?  │ "Busca no PostgreSQL via TypeORM" │
  ├────────────────┼──────────────────────────────────────┼───────────────────────────────────┤
  │ presentation   │ Como o mundo externo acessa isso?    │ "Endpoint GET /customers"         │
  └────────────────┴──────────────────────────────────────┴───────────────────────────────────┘
```
  ---
  2. Backend — Construindo Camada por Camada

  Vou usar um módulo hipotético omie como exemplo. A ordem importa: você sempre vai de dentro para fora.

  ---
  Passo 1 — Domain: defina o que existe

  Crie a pasta src/modules/omie/domain/.
```bash
  domain/entities/omie-order.entity.ts
```
  // Classe pura. Zero imports externos. É TypeScript vanilla.
  ```bash
  export class OmieOrder {
    id: string
    externalId: string    // ID que vem da API do Omie
    customerName: string
    total: number
    status: 'pending' | 'invoiced' | 'cancelled'
    createdAt: Date

    constructor(data: Partial<OmieOrder>) {
      Object.assign(this, data)
    }

    // Comportamento de negócio fica aqui
    isCancellable(): boolean {
      return this.status === 'pending'
    }
  }
```
  Por que sem imports? Porque o domain é o núcleo. Ele não pode depender de nada externo — nem NestJS, nem TypeORM, nem Axios. Isso garante que você pode testar essa classe sem subir
  nenhuma infraestrutura.

  ---
  ```bash
  domain/interfaces/omie-order-repository.interface.ts

  import { OmieOrder } from '../entities/omie-order.entity'
```
  // O "contrato". Define o QUE pode ser feito, sem dizer COMO.
  export const OMIE_ORDER_REPOSITORY = Symbol('IOmieOrderRepository')
```bash
  export interface IOmieOrderRepository {
    findAll(page: number, limit: number): Promise<{ data: OmieOrder[]; total: number }>
    findById(id: string): Promise<OmieOrder | null>
    save(order: Partial<OmieOrder>): Promise<OmieOrder>
  }
```
  O Symbol: é usado como token de injeção de dependência. Funciona como um "identificador único" que o NestJS usa para saber qual classe injetar quando alguém pedir IOmieOrderRepository.
  Sem o Symbol, você teria que usar a classe concreta — e aí quebraria a arquitetura.

  ---
  Passo 2 — Application: defina o que pode ser feito

  Crie src/modules/omie/application/.
```bash
  application/dtos/list-omie-orders.dto.ts

  import { IsOptional, IsInt, Min } from 'class-validator'
  import { Type } from 'class-transformer'

  // DTO = Data Transfer Object. Define o "formato" do input do use case.
  export class ListOmieOrdersDto {
    @IsOptional()
    @IsInt()
    @Min(1)
    @Type(() => Number)
    page?: number = 1

    @IsOptional()
    @IsInt()
    @Min(1)
    @Type(() => Number)
    limit?: number = 20
  }

  application/use-cases/list-omie-orders.use-case.ts

  import { Injectable, Inject } from '@nestjs/common'
  import { IOmieOrderRepository, OMIE_ORDER_REPOSITORY } from '../../domain/interfaces/omie-order-repository.interface'
  import { ListOmieOrdersDto } from '../dtos/list-omie-orders.dto'

  @Injectable()
  export class ListOmieOrdersUseCase {
    constructor(
      // Injeta a INTERFACE, não a classe concreta
      @Inject(OMIE_ORDER_REPOSITORY)
      private readonly repo: IOmieOrderRepository,
    ) {}

    execute(dto: ListOmieOrdersDto) {
      return this.repo.findAll(dto.page ?? 1, dto.limit ?? 20)
    }
  }
```
  Regra de ouro dos use cases:
  - Um arquivo = uma ação
  - Um método = execute()
  - Não sabe nada de HTTP, banco, ou qualquer tecnologia
  - Testável com new ListOmieOrdersUseCase(mockRepo) direto, sem NestJS

  ---
  Passo 3 — Infrastructure: conecte ao mundo real

  Aqui você tem dois tipos de dependências externas: banco de dados e APIs externas.
```bash
  infrastructure/entities/omie-order.orm-entity.ts

  import { Entity, Column, PrimaryGeneratedColumn, CreateDateColumn, UpdateDateColumn } from 'typeorm'

  // Só decorators TypeORM. Zero lógica de negócio.
  @Entity('omie_orders')
  export class OmieOrderOrmEntity {
    @PrimaryGeneratedColumn('uuid')
    id: string

    @Column({ name: 'external_id', unique: true })
    externalId: string

    @Column({ name: 'customer_name' })
    customerName: string

    @Column({ type: 'decimal', precision: 10, scale: 2 })
    total: number

    @Column({ type: 'enum', enum: ['pending', 'invoiced', 'cancelled'], default: 'pending' })
    status: string

    @CreateDateColumn({ name: 'created_at' })
    createdAt: Date

    @UpdateDateColumn({ name: 'updated_at' })
    updatedAt: Date
  }

  infrastructure/repositories/omie-order.repository.ts

  import { Injectable, NotFoundException } from '@nestjs/common'
  import { InjectRepository } from '@nestjs/typeorm'
  import { Repository } from 'typeorm'
  import { IOmieOrderRepository } from '../../domain/interfaces/omie-order-repository.interface'
  import { OmieOrder } from '../../domain/entities/omie-order.entity'
  import { OmieOrderOrmEntity } from '../entities/omie-order.orm-entity'

  @Injectable()
  export class OmieOrderRepository implements IOmieOrderRepository {
    constructor(
      @InjectRepository(OmieOrderOrmEntity)
      private readonly repo: Repository<OmieOrderOrmEntity>,
    ) {}

    async findAll(page = 1, limit = 20) {
      const [entities, total] = await this.repo.findAndCount({
        skip: (page - 1) * limit,
        take: limit,
        order: { createdAt: 'DESC' },
      })
      return { data: entities.map(this.toDomain), total }
    }

    async findById(id: string) {
      const entity = await this.repo.findOne({ where: { id } })
      return entity ? this.toDomain(entity) : null
    }

    async save(order: Partial<OmieOrder>) {
      const entity = this.repo.create(order as any)
      const saved = await this.repo.save(entity)
      return this.toDomain(saved)
    }

    // Único lugar que conhece os dois mundos (domain + ORM)
    private toDomain(entity: OmieOrderOrmEntity): OmieOrder {
      return new OmieOrder({
        id: entity.id,
        externalId: entity.externalId,
        customerName: entity.customerName,
        total: Number(entity.total),
        status: entity.status as OmieOrder['status'],
        createdAt: entity.createdAt,
      })
    }
  }

  infrastructure/providers/omie-api.provider.ts (para a API externa)

  import { Injectable, Logger } from '@nestjs/common'
  import axios from 'axios'

  // Provider = qualquer cliente externo (HTTP, S3, SMS, etc.)
  @Injectable()
  export class OmieApiProvider {
    private readonly logger = new Logger(OmieApiProvider.name)

    private readonly client = axios.create({
      baseURL: 'https://app.omie.com.br/api/v1',
      headers: { 'Content-Type': 'application/json' },
    })

    async listOrders(): Promise<any[]> {
      try {
        const { data } = await this.client.post('/produtos/pedidos/', {
          call: 'ListarPedidos',
          app_key: process.env.OMIE_APP_KEY,
          app_secret: process.env.OMIE_APP_SECRET,
          param: [{ pagina: 1, registros_por_pagina: 50 }],
        })
        return data.pedido_venda_produto ?? []
      } catch (err) {
        // Falhas externas sempre são warn, nunca 500
        this.logger.warn('Falha ao buscar pedidos do Omie', { error: err.message })
        return []
      }
    }
  }
```
  ---
  Passo 4 — Presentation: exponha para o HTTP

  presentation/controllers/omie.controller.ts

  import { Controller, Get, Query } from '@nestjs/common'
  import { ListOmieOrdersUseCase } from '../../application/use-cases/list-omie-orders.use-case'
  import { ListOmieOrdersDto } from '../../application/dtos/list-omie-orders.dto'

  // Controller só recebe, valida, e delega. Zero lógica.
  @Controller('omie')
  export class OmieController {
    constructor(private readonly listOrders: ListOmieOrdersUseCase) {}

    @Get('orders')
    findAll(@Query() dto: ListOmieOrdersDto) {
      return this.listOrders.execute(dto)
    }
  }

  ---
  Passo 5 — Module: registre tudo no NestJS
```bash
  omie.module.ts

  import { Module } from '@nestjs/common'
  import { TypeOrmModule } from '@nestjs/typeorm'
  import { OmieOrderOrmEntity } from './infrastructure/entities/omie-order.orm-entity'
  import { OmieOrderRepository } from './infrastructure/repositories/omie-order.repository'
  import { OmieApiProvider } from './infrastructure/providers/omie-api.provider'
  import { ListOmieOrdersUseCase } from './application/use-cases/list-omie-orders.use-case'
  import { OmieController } from './presentation/controllers/omie.controller'
  import { OMIE_ORDER_REPOSITORY } from './domain/interfaces/omie-order-repository.interface'

  @Module({
    imports: [TypeOrmModule.forFeature([OmieOrderOrmEntity])],
    controllers: [OmieController],
    providers: [
      // Aqui você "liga" a interface à implementação concreta
      { provide: OMIE_ORDER_REPOSITORY, useClass: OmieOrderRepository },
      OmieApiProvider,
      ListOmieOrdersUseCase,
    ],
  })
  export class OmieModule {}
```
  E registre em app.module.ts:
```bash
  import { OmieModule } from './modules/omie/omie.module'

  @Module({
    imports: [
      // ... outros módulos
      OmieModule,
    ],
  })
  export class AppModule {}
```
  ---
  Passo 6 — Migration: crie a tabela no banco
```bash
  docker exec wsd-backend-dev npm run migration:generate -- src/database/migrations/AddOmieOrders
```
  Depois revise o arquivo gerado e rode:
```bash
  docker exec wsd-backend-dev npm run migration:run
```
  Por que nunca usar synchronize: true? Porque em produção o TypeORM poderia apagar uma coluna que ele "não reconhece mais" — destruindo dados reais.

  ---
  3. Frontend — Conectando ao Módulo

  Passo 7 — Tipos TypeScript

  Em src/types/index.ts, adicione:
```bash
  export type OmieOrderStatus = 'pending' | 'invoiced' | 'cancelled'

  export interface OmieOrder {
    id: string
    externalId: string
    customerName: string
    total: number
    status: OmieOrderStatus
    createdAt: string
  }
```
  ---
  Passo 8 — API Client

  Crie src/api/omie.ts:
```bash
  import api from './axios'
  import type { OmieOrder, Paginated } from '../types'

  export async function getOmieOrders(params: { page?: number; limit?: number } = {}): Promise<Paginated<OmieOrder>> {
    const { data } = await api.get<Paginated<OmieOrder>>('/omie/orders', { params })
    return data
  }
```
  Por que um arquivo separado por recurso? Cada arquivo é um "módulo" de funções relacionadas. Quando o endpoint muda, você sabe exatamente onde ir. Veja como todos os outros fazem:
  customers.ts, tickets.ts, technicians.ts — mesmo padrão.

  ---
  Passo 9 — A Página

  Crie src/pages/OmiePage.tsx. Siga sempre este esqueleto:
```bash
  import { useState } from 'react'
  import { useQuery } from '@tanstack/react-query'
  import { getOmieOrders } from '../api/omie'
  import Card from '../components/ui/Card'
  import Spinner from '../components/ui/Spinner'

  const PAGE_SIZE = 20

  export default function OmiePage() {
    const [page, setPage] = useState(1)

    // useQuery: busca dados, gerencia loading/error/cache automaticamente
    const { data, isLoading } = useQuery({
      queryKey: ['omie-orders', { page }],  // cache key — muda quando page muda
      queryFn: () => getOmieOrders({ page, limit: PAGE_SIZE }),
    })

    const orders = data?.data ?? []
    const total = data?.total ?? 0

    if (isLoading) return <Spinner />

    return (
      <div className="space-y-4">
        <h1 className="text-2xl font-bold text-slate-800">Omie — Pedidos</h1>
        <Card>
          <table className="w-full text-sm">
            {/* ... */}
          </table>
        </Card>
      </div>
    )
  }
```
  Por que queryKey é importante? O React Query usa ele como chave de cache. ['omie-orders', { page: 1 }] e ['omie-orders', { page: 2 }] são caches separados — trocar de página não reinicia
   o spinner desnecessariamente.

  ---
  Passo 10 — Sidebar e Rota

  Sidebar.tsx — adicione ao array links:
  ```bash
  import { Database } from 'lucide-react'

  { to: '/omie', label: 'Omie', icon: Database }

  App.tsx — adicione dentro do <PrivateRoute>:
  import OmiePage from './pages/OmiePage'

  <Route path="/omie" element={<OmiePage />} />
```
  ---
  4. O Mapa Mental Completo
```bash
  REQUISITO: "Quero listar pedidos do Omie"
           │
           ▼
  [1] domain/entities/          → Crie a classe OmieOrder (o que é?)
  [2] domain/interfaces/        → Crie IOmieOrderRepository (o que posso fazer?)
  [3] application/dtos/         → Crie ListOmieOrdersDto (qual o input?)
  [4] application/use-cases/    → Crie ListOmieOrdersUseCase (como orquestrar?)
  [5] infrastructure/entities/  → Crie OmieOrderOrmEntity (como salva no banco?)
  [6] infrastructure/repos/     → Implemente IOmieOrderRepository com TypeORM
  [7] infrastructure/providers/ → Crie OmieApiProvider (como chama a API externa?)
  [8] presentation/controllers/ → Crie o endpoint HTTP
  [9] omie.module.ts            → Registre tudo
  [10] app.module.ts            → Importe o módulo
  [11] migration                → Crie a tabela no banco
           │
           ▼ (frontend)
  [12] types/index.ts           → Tipos TypeScript
  [13] api/omie.ts              → Funções de fetch
  [14] pages/OmiePage.tsx       → Componente React com useQuery
  [15] Sidebar.tsx              → Link de navegação
  [16] App.tsx                  → Rota
```
  ---
  5. Os 3 Erros Mais Comuns (e por que acontecem)
```bash
  Erro 1: Injetar a classe concreta no use case
  // ❌ Errado
  constructor(private readonly repo: OmieOrderRepository) {}

  // ✅ Certo
  constructor(@Inject(OMIE_ORDER_REPOSITORY) private readonly repo: IOmieOrderRepository) {}
  Por que errar aqui importa? Você nunca vai conseguir testar o use case sem subir o banco.

  ---
  Erro 2: Colocar lógica no controller
  // ❌ Errado
  @Get('orders')
  async findAll() {
    const orders = await this.repo.findAll()
    return orders.filter(o => o.status !== 'cancelled') // lógica de negócio aqui!
  }

  // ✅ Certo — controller só delega
  @Get('orders')
  findAll(@Query() dto: ListOmieOrdersDto) {
    return this.listOrders.execute(dto)
  }

  ---
  Erro 3: @Entity dentro de domain/
  // ❌ Errado — misturou ORM com domain
  // domain/entities/omie-order.entity.ts
  Por que errar aqui importa? Você nunca vai conseguir testar o use case sem subir o banco.

  ---
  Erro 2: Colocar lógica no controller
  // ❌ Errado
  @Get('orders')
  async findAll() {
    const orders = await this.repo.findAll()
    return orders.filter(o => o.status !== 'cancelled') // lógica de negócio aqui!
  }

  // ✅ Certo — controller só delega
  @Get('orders')
  findAll(@Query() dto: ListOmieOrdersDto) {
    return this.listOrders.execute(dto)
  }

  ---
  Erro 3: @Entity dentro de domain/
  // ❌ Errado — misturou ORM com domain
  // domain/entities/omie-order.entity.ts
  @Entity('omie_orders')  // ← isso é TypeORM, não pertence aqui
  export class OmieOrder { ... }
  Se você fizer isso, o domain passa a depender do TypeORM. Se amanhã trocar o ORM, você quebra o coração do sistema.
```` 
  ---
  6. Como Estudar Isso na Prática

  A melhor forma de consolidar é ler o código existente com essa lente:

  1. Abra customers.module.ts e trace o caminho completo: domain → application → infrastructure → presentation
  2. Para cada arquivo, se pergunte: "Qual é a única responsabilidade deste arquivo?"
  3. Crie um módulo de teste (pode ser um notes ou tags) do zero, mesmo que seja simples
  4. Quebre uma regra de propósito (coloque lógica no controller, por exemplo) e veja o que fica difícil de testar

  O sinal de que você entendeu a arquitetura é quando consegue responder: "Se eu precisar trocar o banco de dados, quais arquivos eu preciso mudar?" — a resposta deve ser: apenas os de
  infrastructure/.
