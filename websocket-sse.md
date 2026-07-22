# Qual é a diferença do WebSocket para o SSE?

A principal diferença é a direção dos dados. O WebSocket é bidirecional (mão dupla), enquanto o SSE (Server-Sent Events) é unidirecional (mão única do servidor para o cliente).

Tabela Comparativa

| Característica | WebSocket | SSE (Server-Sent Events) |
| :--- | :---: | ---: |
| Fluxo de dados| Bidirecional (Cliente ⇄ Servidor) | Unidirecional (Servidor → Cliente)
| Protocolo | Próprio (ws:// ou wss://) | HTTP padrão (http://) | 
| Facilidade | Mais complexo de configurar | Muito simples de implementar| 
| Reconexão | Precisa ser feita manualmente no código | Automática nativamente pelo navegador | 
| Formato de dados | Texto e Binários (Imagens, Arquivos) | Apenas Texto (geralmente JSON) | 
| Limite de conexões | Limitado pela memória do servidor | Limitado a 6 conexões por domínio no HTTP/1 | 

## Quando escolher cada um?
### ⚡ Escolha o WebSocket quando:
- O cliente e o servidor precisam conversar intensamente ao mesmo tempo.
- Exemplos: Chats em tempo real, jogos multiplayer online, ferramentas colaborativas (como Google Docs).

### 📡 Escolha o SSE quando:
Apenas o servidor precisa enviar atualizações e o cliente só assiste.Se o cliente precisar enviar algo, ele pode usar requisições HTTP (POST/FETCH) normais.
Exemplos: Placar de jogos de futebol, feed de redes sociais, notificações de sistema, gráficos de ações.Se o seu sistema apenas mostra dados atualizados na tela e o usuário não interage enviando comandos rápidos, o SSE é mais leve e fácil de manter!
