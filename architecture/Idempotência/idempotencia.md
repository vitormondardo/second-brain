# Idempotência

> **Idempotência** é a propriedade de uma operação que produz o mesmo resultado quando executada uma ou várias vezes, desde que as condições de entrada permaneçam as as mesmas.

---

# O que é?

Uma operação **idempotente** pode ser executada repetidamente sem alterar o resultado após a primeira execução bem-sucedida.

Em outras palavras:

> Repetir a operação não gera novos efeitos colaterais.

## Exemplo simples

Imagine um botão que sempre coloca uma lâmpada em **ligada**.

- Primeira vez → lâmpada ligada.
- Segunda vez → continua ligada.
- Terceira vez → continua ligada.

O estado final é exatamente o mesmo.

Agora imagine um botão que alterna entre ligado e desligado.

- Primeiro clique → ligada.
- Segundo clique → desligada.
- Terceiro clique → ligada.

Nesse caso, **não existe idempotência**, pois cada execução modifica o estado.

---

# Exemplo em programação

Operação idempotente:

```python
usuario.ativo = True
```

Executar esse código uma ou cem vezes gera exatamente o mesmo estado.

Já esta operação não é idempotente:

```python
saldo += 100
```

Cada execução aumenta o saldo novamente.

---

# Idempotência em HTTP

Os métodos HTTP possuem comportamentos diferentes.

| Método | Idempotente? | Explicação |
|---------|--------------|------------|
| GET | ✅ Sim | Apenas consulta dados. |
| PUT | ✅ Sim | Substitui um recurso pelo mesmo estado. |
| DELETE | ✅ Sim | Após excluir uma vez, excluir novamente mantém o mesmo resultado. |
| POST | ❌ Geralmente não | Costuma criar um novo recurso a cada chamada. |

### Exemplo

```
GET /clientes/15
```

Pode ser executado mil vezes.

O servidor continua exatamente igual.

Já:

```
POST /pedidos
```

Pode criar:

```
Pedido 501
Pedido 502
Pedido 503
```

Se a requisição for enviada várias vezes.

---

# Por que a idempotência existe?

Em sistemas distribuídos é muito comum ocorrer:

- perda de conexão
- timeout
- retry automático
- clique duplo do usuário
- falha de rede
- reenvio de Webhooks
- reprocessamento de mensagens

Sem idempotência isso gera duplicidade.

Exemplos:

- dois pagamentos
- dois pedidos
- dois e-mails
- duas notas fiscais
- duas movimentações financeiras

---

# Quando utilizar

Sempre que uma operação puder ser executada mais de uma vez.

Principalmente em:

- APIs REST
- Pagamentos
- Webhooks
- Filas
- Microsserviços
- Integrações entre sistemas
- Operações críticas

---

# Exemplo prático: criação de pedidos

Cliente envia:

```
POST /pedidos
```

A API cria:

```
Pedido #501
```

A resposta não chega ao cliente.

O cliente envia novamente.

Resultado:

```
Pedido #501
Pedido #502
```

Agora existem dois pedidos iguais.

---

# Solução: Idempotency-Key

O cliente gera um identificador único.

```
POST /pedidos

Idempotency-Key: 6f3b92ab
```

Primeira chamada:

```
Pedido #501 criado.
```

Segunda chamada:

```
Pedido já existe.

Retornando Pedido #501.
```

Nenhum recurso duplicado é criado.

---

# Fluxo da Idempotency-Key

```
Cliente
    │
    │ Idempotency-Key
    ▼
Servidor
    │
    ├── A chave já existe?
    │
    ├── Sim
    │      │
    │      └── Retorna a resposta salva
    │
    └── Não
           │
           ├── Executa a operação
           ├── Salva a resposta
           └── Salva a chave
```

---

# Como implementar

Existem várias estratégias.

## 1. Tabela própria (mais utilizada)

Cria-se uma tabela exclusiva.

```sql
CREATE TABLE idempotency_keys (
    id SERIAL PRIMARY KEY,
    idempotency_key UUID UNIQUE NOT NULL,
    endpoint VARCHAR(255),
    response JSONB,
    status_code INT,
    created_at TIMESTAMP DEFAULT NOW(),
    expires_at TIMESTAMP
);
```

Fluxo:

```
Recebe requisição

↓

Consulta a chave

↓

Existe?

↓

SIM
Retorna resposta anterior

↓

NÃO

↓

Executa

↓

Salva chave

↓

Salva resposta
```

### Vantagens

- Funciona com várias instâncias.
- Funciona em Kubernetes.
- Funciona em múltiplos servidores.
- Não perde dados ao reiniciar.
- Excelente para produção.

---

## 2. Redis

Também é possível armazenar em memória compartilhada.

```
Redis

idempotency:abc123

TTL = 24 horas
```

Fluxo:

```
Existe?

↓

Sim
Retorna resposta

↓

Não

↓

Executa

↓

Salva no Redis
```

### Vantagens

- Muito rápido.
- Pouquíssimo acesso ao banco.
- Expiração automática.

### Desvantagens

- Se não houver persistência adequada, pode perder os dados.
- Indicado para períodos curtos.

---

## 3. Coluna na própria entidade

Nem sempre é necessária uma tabela exclusiva.

Exemplo:

```sql
Pedidos

id
cliente_id
valor
idempotency_key UNIQUE
```

Consulta:

```sql
SELECT *
FROM pedidos
WHERE idempotency_key = ?;
```

Se existir:

```
Retorna o pedido existente.
```

Caso contrário:

```
Cria um novo pedido.
```

Essa abordagem funciona muito bem quando cada requisição gera exatamente um recurso.

---

# Idempotência em Webhooks

Webhooks normalmente são reenviados quando o sistema de origem não recebe confirmação.

Fluxo:

```
Webhook recebido

↓

Já foi processado?

↓

SIM
Ignora

↓

NÃO

↓

Processa

↓

Salva o ID do evento
```

Tabela comum:

```sql
processed_webhooks

event_id UNIQUE
processed_at
```

Quando o mesmo webhook chegar novamente:

```
Já processado.

Ignorar.
```

---

# Idempotência em Filas

RabbitMQ, Kafka, Amazon SQS e outras filas podem entregar a mesma mensagem mais de uma vez.

Fluxo:

```
Mensagem recebida

↓

message_id existe?

↓

SIM
Ignora

↓

NÃO

↓

Processa

↓

Salva message_id
```

Tabela:

```sql
processed_messages

message_id UNIQUE
processed_at
```

---

# Idempotência em Pagamentos

Imagine uma transferência de R$ 500,00.

A API recebe:

```
Transferir R$ 500,00
```

O pagamento é realizado.

A resposta não chega ao cliente.

O aplicativo envia novamente.

Sem idempotência:

```
Transferência 1

Transferência 2
```

Resultado:

```
R$ 1.000,00 debitados.
```

Com idempotência:

```
Pagamento já processado.

Retornando comprovante anterior.
```

Nenhuma cobrança adicional acontece.

---

# Idempotência no clique do usuário

Imagine um botão:

```
Finalizar Compra
```

O usuário clica cinco vezes.

Sem idempotência:

```
Pedido 1

Pedido 2

Pedido 3

Pedido 4

Pedido 5
```

Com idempotência:

```
Pedido já criado.

Retornando o pedido existente.
```

---

# Casos em que utilizar

| Situação | Utilizar? |
|----------|-----------|
| Cadastro de usuário | ✅ Sim |
| Cadastro de pedido | ✅ Sim |
| Pagamentos | ✅ Obrigatório |
| Webhooks | ✅ Obrigatório |
| Filas | ✅ Obrigatório |
| Atualização de cadastro | ✅ Sim |
| Exclusão | ✅ Sim |
| GET | Já é idempotente |
| Login | Normalmente não |
| Logout | Geralmente sim |

---

# Benefícios

- Evita pedidos duplicados.
- Evita pagamentos duplicados.
- Evita envio duplicado de e-mails.
- Evita reprocessamento de mensagens.
- Permite retries automáticos.
- Facilita arquiteturas distribuídas.
- Melhora a confiabilidade do sistema.
- Aumenta a resiliência da aplicação.

---

# Boas práticas

- Gere uma **Idempotency-Key** utilizando UUID.
- Utilize índices `UNIQUE` no banco.
- Salve a resposta original para reutilizá-la em chamadas repetidas.
- Defina um TTL quando fizer sentido.
- Em Webhooks e filas, registre sempre o identificador do evento.
- Não utilize apenas memória local em aplicações distribuídas.
- Prefira banco de dados ou Redis para compartilhar o estado entre múltiplas instâncias.

---

# Quando escolher cada abordagem?

| Cenário | Melhor solução |
|----------|----------------|
| Pagamentos | Tabela de idempotência |
| APIs críticas | Tabela de idempotência |
| Criação de pedidos | Tabela de idempotência ou coluna na entidade |
| Webhooks | Tabela de eventos processados |
| Filas | Tabela de eventos processados |
| Cache temporário | Redis |

---

# Resumo

A idempotência garante que uma operação possa ser executada várias vezes sem produzir efeitos adicionais após a primeira execução bem-sucedida.

Ela é um dos fundamentos de sistemas distribuídos e está presente em APIs REST, microsserviços, gateways de pagamento, Webhooks, filas de mensagens e integrações entre sistemas.

Em aplicações modernas, a forma mais comum de implementá-la é através de uma **Idempotency-Key**, armazenada em uma tabela específica ou em um armazenamento compartilhado como Redis. Isso garante que requisições repetidas retornem exatamente o mesmo resultado, evitando duplicações e tornando a aplicação mais segura, resiliente e confiável.
