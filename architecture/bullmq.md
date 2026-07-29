# BullMQ

## Introdução

BullMQ é uma biblioteca de filas para Node.js construída sobre Redis.

Seu objetivo é permitir que aplicações executem tarefas em segundo plano de forma confiável, controlada e escalável.

Em vez de executar uma operação pesada durante uma requisição HTTP, a aplicação cria um job e um worker executa posteriormente.

---

# Problema que BullMQ resolve

Sem fila:

```
Usuário solicita operação

        |

API executa tudo

        |

Resposta demora
```

Com BullMQ:

```
Usuário solicita operação

        |

Cria job

        |

Resposta rápida

        |

Worker processa depois
```

---

# Conceitos principais

## Queue

É a fila onde os jobs ficam armazenados.

Exemplo:

```
emailQueue
```

Ela recebe tarefas que precisam ser executadas.

---

## Job

É uma tarefa individual.

Exemplo:

```json
{
  "type": "send-email",
  "recipient": "compras@empresa.com"
}
```

---

## Worker

É o processo responsável por executar os jobs.

Fluxo:

```
Queue

   |

Worker

   |

Processamento
```

---

## Redis

BullMQ utiliza Redis como armazenamento.

O Redis mantém:

- jobs pendentes;
- estados;
- tentativas;
- delays;
- informações de processamento.

---

# Principais funcionalidades

## 1. Processamento assíncrono

Exemplo:

```
Pedido aprovado

        |

Job:

send-email

        |

Worker envia e-mail
```

---

## 2. Retry automático

Caso uma tarefa falhe:

```
Tentativa 1

Falhou

        |

Aguardar

        |

Tentativa 2

Falhou

        |

Tentativa 3

Sucesso
```

---

## 3. Delayed Jobs

Permite executar tarefas no futuro.

Exemplo:

```
Criar chamado

        |

Esperar 24 horas

        |

Verificar SLA
```

---

## 4. Jobs recorrentes

Permite tarefas periódicas.

Exemplo:

```
Todo dia às 08:00

        |

Gerar relatório
```

---

## 5. Prioridades

Permite definir quais tarefas têm preferência.

Exemplo:

```
Alta prioridade:

Reset de senha


Baixa prioridade:

Relatório mensal
```

---

## 6. Controle de concorrência

Permite limitar quantos jobs um worker executa simultaneamente.

Exemplo:

```
Worker

Concorrência = 5

Processa 5 tarefas ao mesmo tempo
```

---

# Arquitetura básica

```
Aplicação

   |

   v

BullMQ Queue

   |

   v

Redis

   |

   v

Worker

   |

   v

Processamento
```

---

# Exemplos de uso

## Envio de e-mails

```
Usuário cadastra conta

        |

Job:

welcome-email

        |

Worker

        |

Envia e-mail
```

---

## Relatórios

```
Usuário solicita relatório

        |

Job

        |

Worker gera arquivo
```

---

## Processamento de imagens

```
Upload

        |

Job

        |

Worker redimensiona imagem
```

---

## Integrações externas

```
Sincronização ERP

        |

Job

        |

Worker consulta API
```

---

# BullMQ vs RabbitMQ

BullMQ:

```
"Preciso executar esta tarefa depois."
```

RabbitMQ:

```
"Preciso comunicar que algo aconteceu."
```

---

# Quando usar BullMQ?

Use BullMQ quando:

- a aplicação é principalmente Node.js;
- precisa de jobs em background;
- precisa de agendamento;
- precisa controlar tarefas;
- já utiliza Redis.

---

# Quando evitar BullMQ?

Evite quando:

- já possui RabbitMQ resolvendo o problema;
- precisa comunicar múltiplos serviços;
- quer um barramento de eventos;
- possui arquitetura distribuída.

---

# Exemplos no Service Desk

Possíveis jobs:

```
send-email

generate-report

sync-omie

send-notification

check-sla
```

Cada job possui um worker responsável.

---

# Conclusão

BullMQ é uma ferramenta poderosa para gerenciamento de tarefas assíncronas, principalmente em aplicações Node.js.

Ele não substitui completamente o RabbitMQ.

A escolha depende do problema:

- Comunicação entre sistemas → RabbitMQ.
- Gerenciamento de tarefas internas e agendadas → BullMQ.
