# Conceitos fundamentais
## Webhook

Um webhook é uma notificação automática que um sistema envia para outro quando algo acontece.

A diferença entre webhook e API tradicional é a direção da comunicação:

API tradicional — você pergunta:
```
Seu sistema → "tem mensagem nova?" → Evolution API
Seu sistema → "tem mensagem nova?" → Evolution API  (repetindo)
Seu sistema → "tem mensagem nova?" → Evolution API  (repetindo)
```
Webhook — eles avisam:
```
Evolution API → "chegou mensagem!" → Seu backend  (só quando acontece)
```
No projeto, você configurou o webhook assim: "quando chegar mensagem no WhatsApp, faça POST para http://localhost:3000/api/v1/webhooks/whatsapp". A Evolution API cumpre esse contrato — cada mensagem recebida dispara automaticamente uma chamada para o backend.
