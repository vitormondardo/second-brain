# RabbitMQ

## O que é?

RabbitMQ é um **Message Broker (Broker de Mensagens)** responsável por armazenar mensagens em filas para que outros serviços as processem posteriormente.

Seu objetivo é desacoplar sistemas e permitir processamento assíncrono.

Em vez de executar uma tarefa imediatamente, a aplicação envia uma mensagem para uma fila e responde ao usuário rapidamente.

Posteriormente, um ou mais consumidores (Workers) processam essas mensagens.

---

# Problema que resolve

Sem RabbitMQ:

Usuário cria um chamado.

A aplicação precisa:

- Salvar no banco
- Enviar e-mail
- Enviar WhatsApp
- Gerar PDF
- Atualizar dashboard

O usuário fica esperando todas essas operações.

Se uma delas falhar, toda a requisição pode falhar.

---

# Com RabbitMQ

Fluxo:

Frontend

↓

API

↓

Banco de Dados

↓

RabbitMQ

↓

Resposta ao usuário

Depois:

Worker Email

↓

Envia e-mail

Worker WhatsApp

↓

Envia WhatsApp

Worker PDF

↓

Gera PDF

Worker IA

↓

Processa IA

Cada Worker trabalha independentemente.

---

# Conceitos principais

## Producer

Sistema que envia mensagens.

Exemplo:

API do Service Desk.

---

## Queue

Fila onde as mensagens ficam armazenadas.

Exemplo:

```
EnviarEmail
```

---

## Consumer (Worker)

Serviço que lê mensagens da fila e executa o trabalho.

---

## Exchange

Componente responsável por decidir para qual fila uma mensagem será enviada.

---

## Routing Key

Chave utilizada para determinar o destino da mensagem.

---

# Vantagens

- Processamento assíncrono
- Desacoplamento
- Escalabilidade horizontal
- Retry automático
- Balanceamento de carga
- Redução do tempo de resposta

---

# Desvantagens

- Não mantém histórico permanente dos eventos
- Após consumida, normalmente a mensagem é removida
- Não foi projetado para Event Streaming

---

# Quando utilizar

Utilize RabbitMQ quando existir uma tarefa que pode ser executada depois da resposta ao usuário.

Exemplos:

- Enviar e-mail
- Enviar WhatsApp
- Gerar PDF
- Processar IA
- Sincronizar Omie
- Atualizar SAP
- Gerar relatórios
- Executar Webhooks

---

# Exemplo no Service Desk

Quando um chamado é criado:

- Salvar chamado
- Publicar mensagem "Enviar Email"
- Publicar mensagem "Enviar WhatsApp"
- Publicar mensagem "Gerar PDF"

Cada Worker executa sua tarefa independentemente.

---

# Quando NÃO utilizar

Não é a melhor opção quando vários sistemas precisam consumir o mesmo evento ou quando é necessário manter um histórico completo dos acontecimentos.

Nesses casos, normalmente utiliza-se Kafka.

---

# Resumo

RabbitMQ é um broker de mensagens focado em filas e processamento assíncrono. Seu principal objetivo é distribuir tarefas entre consumidores, desacoplando serviços e reduzindo o tempo de resposta da aplicação.
