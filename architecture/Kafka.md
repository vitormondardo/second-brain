# Apache Kafka

## O que é?

Apache Kafka é uma plataforma de Event Streaming utilizada para armazenar e distribuir eventos entre diversos sistemas.

Diferente de uma fila tradicional, Kafka mantém um histórico dos eventos publicados.

Diversos consumidores podem ler os mesmos eventos independentemente.

---

# Problema que resolve

Imagine um chamado criado.

Vários sistemas precisam saber disso:

- Dashboard
- BI
- IA
- Aplicativo Mobile
- Auditoria
- Analytics

Sem Kafka, a API precisaria avisar cada sistema individualmente.

Com Kafka:

API

↓

Evento

↓

Kafka

↓

Todos os consumidores recebem o evento.

---

# Conceitos principais

## Producer

Sistema que publica eventos.

---

## Topic

Canal onde os eventos são armazenados.

Exemplo:

```
ticket.created
```

---

## Consumer

Sistema que lê eventos.

---

## Consumer Group

Grupo de consumidores responsáveis por dividir o processamento.

---

## Partition

Divisão de um tópico para aumentar paralelismo.

---

## Offset

Posição de leitura dentro do histórico.

Cada consumidor possui seu próprio Offset.

---

# Vantagens

- Event Streaming
- Histórico de eventos
- Escalabilidade extremamente alta
- Diversos consumidores independentes
- Reprocessamento de eventos
- Alta disponibilidade

---

# Desvantagens

- Maior complexidade
- Mais difícil de operar
- Não é a melhor escolha para filas simples

---

# Quando utilizar

Quando diversos sistemas precisam consumir os mesmos eventos.

Exemplos:

- Analytics
- Auditoria
- IA
- Dashboard
- Mobile
- Microsserviços

---

# Exemplo

Evento:

```
TicketCreated
```

Consumidores:

- Dashboard
- BI
- IA
- CRM
- Auditoria

Todos recebem exatamente o mesmo evento.

---

# Kafka x RabbitMQ

RabbitMQ:

- Distribui tarefas.

Kafka:

- Distribui eventos.

---

# Quando NÃO utilizar

Se apenas um Worker precisa executar uma tarefa assíncrona.

Nesse cenário RabbitMQ costuma ser mais simples.

---

# Resumo

Kafka é uma plataforma de Event Streaming projetada para distribuir eventos entre diversos consumidores, mantendo um histórico persistente dos acontecimentos.
