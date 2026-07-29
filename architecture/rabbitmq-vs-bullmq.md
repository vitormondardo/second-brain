# RabbitMQ vs BullMQ

## Introdução

RabbitMQ e BullMQ são tecnologias utilizadas para processamento assíncrono, filas e execução de tarefas em segundo plano.

Apesar de ambos trabalharem com filas, eles possuem objetivos diferentes.

A principal diferença é:

- **RabbitMQ:** sistema de mensageria para comunicação entre componentes e sistemas.
- **BullMQ:** biblioteca de gerenciamento de jobs baseada em Redis, focada em tarefas em background dentro de aplicações.

---

# RabbitMQ

## O que é?

RabbitMQ é um **message broker**.

Ele funciona como intermediário entre produtores e consumidores de mensagens.

Sua principal função é permitir que uma aplicação publique uma mensagem e outra aplicação processe posteriormente.

Exemplo:

```
Aplicação A

Pedido aprovado

        |
        v

RabbitMQ

        |
        v

Aplicação B

Envia e-mail
```

---

## Principais usos

RabbitMQ é utilizado principalmente para:

- comunicação entre microsserviços;
- processamento distribuído;
- integração entre sistemas;
- eventos de domínio;
- desacoplamento de componentes.

Exemplos de eventos:

```
whatsapp.message.received

quotation.approved

invoice.created

customer.updated
```

Cada evento pode possuir um consumidor responsável.

---

# BullMQ

## O que é?

BullMQ é uma biblioteca de filas construída sobre Redis.

Ela permite criar e controlar tarefas que serão executadas posteriormente por workers.

Exemplo:

```
Usuário solicita relatório

        |
        v

BullMQ

        |
        v

Worker gera relatório
```

---

# Por que os dois existem?

Porque eles resolvem problemas parecidos, mas com focos diferentes.

## RabbitMQ pensa em mensagens

Pergunta principal:

> "Como um componente comunica que algo aconteceu para outro componente?"

Exemplo:

```
Service Desk

quotation.approved

        |

RabbitMQ

        |

Email Worker
```

O foco é comunicação.

---

## BullMQ pensa em tarefas

Pergunta principal:

> "Como minha aplicação controla tarefas que precisam executar depois?"

Exemplo:

```
Gerar relatório

Executar amanhã às 08:00

        |

BullMQ

        |

Worker
```

O foco é gerenciamento de jobs.

---

# Comparação

| Característica | RabbitMQ | BullMQ |
|---|---|---|
| Categoria | Message Broker | Job Queue |
| Base | Próprio broker | Redis |
| Comunicação entre serviços | Excelente | Não é o foco |
| Microsserviços | Excelente | Limitado |
| Jobs agendados | Não é o foco | Excelente |
| Cron jobs | Precisa de scheduler externo | Nativo |
| Retry | Sim | Sim |
| Delay | Sim | Sim |
| Eventos de domínio | Excelente | Não é o foco |
| Ecossistema Node.js | Bom | Excelente |

---

# Cron Jobs

RabbitMQ não é um scheduler.

Ele não sabe que uma tarefa deve executar todo dia às 08:00.

Normalmente utiliza-se um scheduler externo:

```
node-cron

        |

        v

RabbitMQ

        |

        v

Worker
```

O scheduler cria o evento e o RabbitMQ distribui.

---

BullMQ possui suporte próprio para jobs recorrentes:

```
BullMQ

Todo dia 08:00

        |

Worker executa
```

---

# Exemplos práticos

## RabbitMQ

Cenário:

O técnico aprova uma cotação.

```
Pedido aprovado

        |

Evento:

quotation.approved

        |

RabbitMQ

        |

Email Worker

        |

Resend envia e-mail
```

O objetivo é comunicar que algo aconteceu.

---

## BullMQ

Cenário:

A aplicação precisa enviar um e-mail.

```
Pedido aprovado

        |

Criar job:

send-email

        |

BullMQ

        |

Worker

        |

Resend envia e-mail
```

O objetivo é controlar uma tarefa.

---

# Podem coexistir?

Sim.

Exemplo:

```
RabbitMQ

Eventos do sistema

        |

        v

BullMQ

Jobs internos da aplicação
```

Porém, adicionar os dois sem necessidade aumenta a complexidade operacional.

---

# Quando escolher RabbitMQ?

Use RabbitMQ quando:

- existem vários serviços se comunicando;
- deseja trabalhar com eventos;
- precisa integrar sistemas;
- possui arquitetura distribuída;
- deseja desacoplar componentes.

---

# Quando escolher BullMQ?

Use BullMQ quando:

- possui uma aplicação Node.js;
- precisa executar tarefas em background;
- precisa de muitos jobs agendados;
- precisa de delays;
- precisa controlar tarefas internas.

---

# Cenário do Service Desk

Como o sistema já utiliza RabbitMQ para comunicação com a Meta API, faz sentido reutilizar a infraestrutura:

```
RabbitMQ

├── whatsapp.messages
├── quotation.email
├── notifications
└── integrations
```

BullMQ somente seria necessário caso exista uma grande necessidade de gerenciamento de jobs temporizados ou tarefas internas específicas.

---

# Resumo

**RabbitMQ:**

> Algo aconteceu. Avise quem precisa saber.

**BullMQ:**

> Existe uma tarefa que precisa ser executada em algum momento.
