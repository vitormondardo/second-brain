# Soluções para Dual-Write

Quando uma operação precisa atualizar mais de um sistema, existem diversos padrões arquiteturais para garantir consistência.

---

# 1. Retry

## Como funciona

Se uma operação falhar, o sistema tenta novamente.

Exemplo:

Banco

↓

Kafka

↓

Falhou

↓

Retry

↓

Sucesso

### Vantagens

- Simples
- Fácil implementação

### Desvantagens

- Não evita perda de mensagens
- Não garante consistência

### Quando utilizar

Falhas temporárias.

---

# 2. Transactional Outbox

## Como funciona

A aplicação salva:

- Pedido
- Evento Outbox

Na mesma transação do banco.

Depois um Worker envia os eventos.

Fluxo:

Pedido

↓

Outbox

↓

Commit

↓

Worker

↓

Kafka

### Vantagens

- Resolve Dual-write
- Muito utilizada
- Alta confiabilidade

### Desvantagens

- Necessita Worker
- Maior complexidade

### Quando utilizar

Banco + RabbitMQ

Banco + Kafka

Banco + Omie

Banco + SAP

É a solução mais comum em sistemas modernos.

---

# 3. CDC (Change Data Capture)

## Como funciona

A aplicação apenas grava no banco.

Ferramentas como Debezium monitoram o log do banco e publicam os eventos.

Fluxo:

Banco

↓

Transaction Log

↓

Debezium

↓

Kafka

### Vantagens

- Total desacoplamento
- Eventos automáticos

### Desvantagens

- Infraestrutura adicional
- Configuração mais complexa

### Quando utilizar

Grandes arquiteturas baseadas em eventos.

---

# 4. Saga

## Como funciona

Caso uma etapa falhe, operações anteriores são compensadas.

Exemplo:

Criar pedido

↓

Reservar estoque

↓

Cobrar cartão

↓

Falhou

↓

Cancelar estoque

↓

Cancelar pedido

### Vantagens

- Mantém consistência entre microsserviços

### Desvantagens

- Complexidade elevada

### Quando utilizar

Fluxos longos envolvendo diversos sistemas.

---

# 5. Two-Phase Commit (2PC)

## Como funciona

Todos os sistemas confirmam que conseguem executar a operação.

Somente depois ocorre o Commit.

### Vantagens

- Consistência forte

### Desvantagens

- Baixa disponibilidade
- Alto custo
- Complexidade

### Quando utilizar

Sistemas legados que exigem transações distribuídas.

É pouco utilizado em arquiteturas modernas.

---

# Comparação

| Solução | Complexidade | Resolve Dual-write | Uso comum |
|----------|-------------|--------------------|-----------|
| Retry | ⭐ | Parcial | Falhas temporárias |
| Transactional Outbox | ⭐⭐⭐ | Sim | Microsserviços |
| CDC | ⭐⭐⭐⭐ | Sim | Event Streaming |
| Saga | ⭐⭐⭐⭐⭐ | Sim | Processos distribuídos |
| Two-Phase Commit | ⭐⭐⭐⭐⭐ | Sim | Sistemas legados |

---

# Qual aprender primeiro?

1. RabbitMQ
2. Kafka
3. Transactional Outbox
4. Saga
5. CDC
6. Two-Phase Commit

---

# Resumo

A solução mais utilizada atualmente para evitar Dual-write é o padrão **Transactional Outbox**, geralmente combinado com **RabbitMQ** ou **Kafka**. Em arquiteturas maiores, é comum utilizar **CDC** para publicação automática de eventos e **Saga** para coordenar processos distribuídos entre múltiplos serviços.
