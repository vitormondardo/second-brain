# Dual-Write

## O que é?

Dual-write é o problema de gravar a mesma operação em dois sistemas diferentes sem garantia de que ambos serão atualizados com sucesso.

---

# Exemplo

Criar Pedido

↓

Banco

↓

Kafka

Se:

Banco ✅

Kafka ❌

Os sistemas ficam inconsistentes.

---

# Outro exemplo

Banco

↓

Omie

Banco salva.

Omie falha.

Agora existe um pedido apenas localmente.

---

# Onde acontece

- Banco + RabbitMQ
- Banco + Kafka
- Banco + Redis
- Banco + Elasticsearch
- Banco + SAP
- Banco + Omie
- Banco + API Externa

---

# Por que acontece?

Porque não existe uma transação única entre sistemas diferentes.

---

# Problemas

- Dados inconsistentes
- Eventos perdidos
- Integrações quebradas
- Difícil recuperação

---

# Como identificar

Sempre pergunte:

"E se a primeira gravação funcionar e a segunda falhar?"

Se a resposta for "os dados ficam inconsistentes", existe um problema de Dual-write.

---

# Soluções

- Retry
- Transactional Outbox
- CDC
- Saga
- Two-Phase Commit

---

# Resumo

Dual-write é um problema clássico de arquiteturas distribuídas que ocorre quando uma operação grava em dois sistemas diferentes sem garantia de consistência entre eles.
