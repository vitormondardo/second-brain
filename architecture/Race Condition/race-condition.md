# Race Condition (Condição de Corrida)

> **Objetivo:** Entender o que é uma Race Condition, quando ela ocorre, quais problemas pode causar e como evitá-la em aplicações concorrentes e distribuídas.

---

# O que é Race Condition?

Uma **Race Condition** (Condição de Corrida) ocorre quando **duas ou mais operações acessam e modificam um mesmo recurso ao mesmo tempo**, e o resultado final depende da ordem em que essas operações são executadas.

Como essa ordem não é previsível, a aplicação pode produzir resultados incorretos ou inconsistentes.

Em outras palavras:

> Se duas operações "competem" pelo mesmo dado sem sincronização adequada, existe uma Race Condition.

---

# Exemplo simples

Imagine uma conta bancária com saldo de:

```text
R$ 1.000
```

Duas requisições chegam ao mesmo tempo para transferir R$ 800.

Sem proteção:

```text
Requisição A
↓
Lê saldo = 1000

Requisição B
↓
Lê saldo = 1000

Requisição A
↓
Atualiza saldo para 200

Requisição B
↓
Atualiza saldo para 200
```

Resultado:

- Foram enviados R$ 1.600
- A conta possuía apenas R$ 1.000

Esse é um exemplo clássico de Race Condition.

---

# Por que isso acontece?

Porque muitas operações aparentemente simples não são executadas de uma única vez.

Por exemplo:

```java
count++;
```

Na verdade, o processador executa:

```text
1. Ler o valor
2. Somar 1
3. Escrever o novo valor
```

Se outra thread fizer o mesmo processo entre essas etapas, um incremento pode ser perdido.

---

# Quando pode acontecer?

Sempre que houver concorrência.

Exemplos:

- Múltiplas threads
- Múltiplos processos
- Múltiplas requisições HTTP
- Várias instâncias da aplicação
- Dois usuários alterando o mesmo registro
- Processamento paralelo
- Microsserviços
- Workers em filas

---

# Exemplos reais

## 1. Estoque

Produto:

```text
Notebook
Quantidade: 1
```

Dois clientes compram ao mesmo tempo.

Sem sincronização:

```text
Cliente A verifica estoque → 1

Cliente B verifica estoque → 1

Cliente A compra

Cliente B compra
```

Resultado:

```text
Estoque vendido duas vezes.
```

---

## 2. Transferência bancária

Saldo:

```text
R$ 1.000
```

Duas transferências simultâneas:

```text
Transferência A → R$ 800

Transferência B → R$ 800
```

Ambas leem o mesmo saldo antes da atualização.

Resultado:

```text
Saldo inconsistente.
```

---

## 3. Botão "Salvar"

Usuário clica rapidamente:

```text
Salvar

Salvar

Salvar
```

Resultado:

```text
Pedido #101

Pedido #102

Pedido #103
```

Quando deveria existir apenas um pedido.

---

## 4. Pagamentos

O cliente clica duas vezes em "Pagar".

Sem proteção:

```text
Pagamento processado duas vezes.
```

---

## 5. Webhooks

Um sistema envia o mesmo webhook duas vezes.

Sem controle:

```text
Atualiza estoque duas vezes

Envia dois e-mails

Cria dois pedidos
```

---

# Como evitar Race Conditions?

Existem diversas técnicas.

A escolha depende do problema.

---

## 1. Locks (Mutex)

Permitem que apenas uma operação utilize um recurso por vez.

```text
Thread A

🔒 Lock

Atualiza saldo

🔓 Unlock

↓

Thread B
```

Muito usado para proteger recursos compartilhados.

---

## 2. Transações

Muito utilizadas em bancos de dados.

```sql
BEGIN;

UPDATE contas
SET saldo = saldo - 100
WHERE id = 1;

COMMIT;
```

Caso ocorra erro:

```sql
ROLLBACK;
```

Garantem consistência durante alterações críticas.

---

## 3. Controle de Concorrência Otimista (Optimistic Locking)

Cada registro possui uma versão.

Exemplo:

```text
Pedido

Versão = 5
```

Atualização:

```sql
UPDATE pedidos
SET ...
WHERE id = 1
AND versao = 5;
```

Se outra pessoa alterar antes, a versão muda e a atualização falha.

Muito usado em sistemas web.

---

## 4. Filas

Em vez de executar tudo simultaneamente:

```text
Requisições

↓

Fila

↓

Worker
```

As tarefas são processadas de forma controlada.

Exemplos:

- RabbitMQ
- Kafka
- BullMQ
- Amazon SQS

---

## 5. Idempotência

Evita executar duas vezes a mesma operação.

Exemplo:

Cliente envia:

```text
POST /pagamentos
Idempotency-Key: abc123
```

Mesmo que envie novamente:

```text
POST /pagamentos
Idempotency-Key: abc123
```

O servidor retorna o mesmo resultado sem criar um novo pagamento.

Muito utilizada em:

- Pagamentos
- PIX
- Stripe
- Mercado Pago
- Webhooks
- APIs REST

---

# Quando usar cada solução?

| Situação | Solução recomendada |
|----------|----------------------|
| Atualizar saldo bancário | Transações + Locks |
| Controle de estoque | Transações + Locks |
| Dois usuários editando o mesmo registro | Optimistic Locking |
| Processamento de pagamentos | Idempotência + Transações |
| Recebimento de Webhooks | Idempotência |
| Processamento assíncrono | Filas |
| Variáveis compartilhadas entre threads | Mutex, Lock ou estruturas atômicas |

---

# Race Condition x Idempotência

Embora estejam relacionadas, resolvem problemas diferentes.

| Race Condition | Idempotência |
|----------------|--------------|
| Problema de concorrência | Problema de repetição |
| Ocorre quando duas operações executam ao mesmo tempo | Ocorre quando a mesma operação é enviada novamente |
| Pode causar inconsistência nos dados | Evita processamento duplicado |
| Resolvida com sincronização, transações e locks | Resolvida com uma chave de idempotência |

---

# Boas práticas

- Nunca confiar apenas no frontend.
- Validar regras críticas no backend.
- Utilizar transações para operações financeiras.
- Proteger recursos compartilhados com mecanismos de sincronização.
- Implementar idempotência em APIs críticas.
- Utilizar filas para processamentos pesados.
- Testar cenários com múltiplas requisições simultâneas.

---

# Resumo

Uma **Race Condition** acontece quando duas ou mais operações tentam acessar ou modificar o mesmo recurso ao mesmo tempo, sem sincronização adequada.

Esse problema pode causar:

- Perda de dados
- Duplicidade de registros
- Inconsistência de estoque
- Pagamentos duplicados
- Corrupção de informações

As principais estratégias para evitar Race Conditions são:

- Locks (Mutex)
- Transações
- Optimistic Locking
- Filas
- Idempotência (quando o problema envolve repetição de requisições)

---

# Conceitos relacionados

- Concorrência (Concurrency)
- Paralelismo (Parallelism)
- Thread Safety
- Mutex
- Semaphore
- Locks
- Deadlock
- Optimistic Locking
- Pessimistic Locking
- ACID
- Transações
- Idempotência
- Filas de Mensagens
- Webhooks
- APIs REST
- Processamento Assíncrono

---

> **Resumo em uma frase:** Race Condition é um problema de concorrência em que o resultado de uma operação depende da ordem imprevisível de execução de múltiplas operações sobre o mesmo recurso. A prevenção envolve sincronização adequada, transações, controle de concorrência, filas e, em alguns casos, idempotência.
