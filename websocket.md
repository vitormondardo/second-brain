WebSocket é uma tecnologia que permite comunicação bidirecional e em tempo real entre o navegador e o servidor. Diferente do HTTP tradicional, onde o cliente precisa pedir para receber dados, o WebSocket mantém uma "conexão aberta" constante, permitindo que ambos enviem dados a qualquer momento.

## Qual a utilidade?
- No modelo padrão da web (HTTP), o servidor é "mudo" e só fala quando é provocado por uma requisição do navegador. 
- O WebSocket quebra essa barreira.
- Conversa contínua: O servidor pode enviar atualizações para o usuário instantaneamente, sem que a página precise atualizar ou fazer novas requisições.
- Economia de dados: Evita o polling (ficar perguntando ao servidor "tem algo novo?" a cada 2 segundos), reduzindo drasticamente o consumo de banda e processamento.

## Quando usar?Você deve usar WebSockets sempre que o atraso na entrega da informação prejudicar a experiência do usuário.
- 💬 Chats de mensagens: Para que a mensagem do seu amigo apareça na tela no exato segundo em que ele enviou.
- 📈 Painéis financeiros e Cripto: Gráficos de ações que mudam de valor a cada milissegundo.
- 🎮 Jogos online multiplayer: Onde a posição e as ações de outros jogadores precisam ser sincronizadas instantaneamente.
- 🚗 Rastreamento em tempo real: Mapas que mostram a entrega ou o motorista de aplicativo se movendo no mapa.
- 🔔 Notificações Push: Alertas urgentes de sistemas que aparecem na tela na mesma hora em que ocorrem.

## Quando NÃO usar: 
Em sites estáticos, blogs, portais de notícias tradicionais ou sistemas onde os dados mudam pouco (como um cadastro de cliente). O HTTP padrão resolve melhor e gasta menos recursos de infraestrutura nesses casos.

## Como usar? (Exemplo prático em JavaScript)
Abaixo veja como criar um canal de comunicação usando JavaScript nativo no navegador e uma biblioteca muito comum chamada ws no Node.js.

### 1. No Servidor (Node.js)
Primeiro, instale a biblioteca de WebSocket no seu projeto rodando npm install ws. Depois, crie o servidor:

```javascript
const { WebSocketServer } = require('ws');

// Cria o servidor na porta 8080
const wss = new WebSocketServer({ port: 8080 });

wss.on('connection', (ws) => {
  console.log('Cliente conectado!');

  // Escuta mensagens vindas do navegador
  ws.on('message', (dados) => {
    console.log(`Mensagem recebida: ${dados}`);
    
    // Responde de volta para o navegador
    ws.send(`Servidor recebeu seu recado: ${dados}`);
  });
});
```

### 2. No Cliente (Navegador)
O navegador possui suporte nativo ao WebSocket, sem precisar instalar nada:

```javascript
// Conecta ao servidor que criamos
const socket = new WebSocket('ws://localhost:8080');

// Dispara quando a conexão é estabelecida
socket.onopen = () => {
  console.log('Conectado ao servidor!');
  
  // Envia uma mensagem de teste
  socket.send('Olá, Servidor!');
};

// Dispara sempre que o servidor enviar algo
socket.onmessage = (event) => {
  console.log(`Mensagem do servidor: ${event.data}`);
};
```
