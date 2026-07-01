# Como rodar a aplicação localmente em duas máquinas
*Nota: antes de qualquer coisa, ambas as máquinas devem estar conectadas na mesma rede

Passo 0: encontrar endereço IP
```bash
ipconfig
```
O endereço para a outra máquina será: http://SEU_IP_AQUI:3001 (Exemplo: http://192.168.1.15:3001)

Algumas linguagens gera automaticamente, como Next.js. Mas linguagens como note é necessário ativar.

1. Navegue para a pasta correta
No seu terminal (PowerShell), certifique-se de entrar na pasta exata onde esse package.json está salvo. Você pode usar o comando cd (Change Directory) para isso.

2. Rode o comando com a flag de Host
```bash
npm run dev -- --host
```

3. Acesse no celular
Seu package.json está configurado para forçar a porta 3001 ("dev": "vite --port 3001").

Assim que o comando rodar com sucesso no terminal, o Vite vai mostrar algumas URLs. Procure pela URL da rede (geralmente chamada de Network).

Pegue o seu celular (conectado no mesmo Wi-Fi do PC) e digite o IP do seu computador seguido da porta 3001:

http://SEU_IP_AQUI:3001 (Exemplo: http://192.168.1.15:3001)

Se a página ficar carregando infinitamente, lembre-se da dica do passo anterior: o Firewall do Windows pode estar bloqueando o Node.js/Vite de receber conexões externas. Caso isso aconteça, basta liberar a permissão para ele nas configurações do Firewall.
