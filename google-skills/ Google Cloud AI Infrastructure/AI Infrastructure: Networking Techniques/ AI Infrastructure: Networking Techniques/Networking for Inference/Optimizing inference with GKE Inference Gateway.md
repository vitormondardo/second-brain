# Otimizando a inferência com o GKE Inference Gateway

O GKE Inference Gateway foi projetado para otimizar o atendimento de grandes modelos de linguagem (LLMs) e outras cargas de trabalho de IA generativa. Ao contrário dos balanceadores de carga tradicionais, que não são adequados para a natureza complexa e variável da inferência de IA, o GKE Inference Gateway utiliza um sistema de roteamento inteligente e consciente do modelo para melhorar o desempenho, reduzir custos e simplificar as operações.

<img width="1920" height="1080" alt="KVcache Utilization" src="https://github.com/user-attachments/assets/f93066da-1052-4b9d-b158-c6622b4489f8" />

<img width="1920" height="1080" alt="average QUEUE SIZE" src="https://github.com/user-attachments/assets/4545bb97-c460-4c09-a25f-b7987ac3c47d" />

# Como o GKE Inference Gateway Funciona

O GKE Inference Gateway monitora a carga em seus servidores de modelo usando métricas específicas de IA, como o tamanho da fila de solicitações pendentes e a utilização do KVCache. Em seguida, ele roteia inteligentemente as solicitações recebidas para a GPU ou TPU menos sobrecarregada, garantindo uma distribuição uniforme do trabalho em toda a sua infraestrutura.

* **Passo 1:** Solicitação do cliente `GET / completions`
* **Passo 2:** Seleção do `InferencePool` como um serviço do Kubernetes (K8s) usando o nome do modelo (especificação da API da OpenAI).
* **Passo 3:** Escolha da réplica de modelo menos sobrecarregada que possui o adaptador LoRA em memória.
* **Passo 4:** Roteamento para o `InferencePool` e para a réplica de modelo ideal por prioridade.

<img width="1156" height="912" alt="Visuals for Course 4 (1)" src="https://github.com/user-attachments/assets/eb1880d9-f060-426c-bdc2-0d9d314e4a19" />

O GKE Inference Gateway atua como um intermediário inteligente entre a solicitação de um cliente e uma instância de modelo. Quando um cliente envia uma solicitação, o GKE Inference Gateway a processa por meio de uma série de extensões especializadas:

* **Roteamento baseado no corpo da requisição (*Body-based routing*):** Esta extensão extrai o identificador do modelo diretamente do corpo da solicitação. O GKE Inference Gateway usa essas informações para rotear a solicitação para o modelo correto com base em regras definidas por você. Isso é especialmente útil ao executar vários modelos em um único cluster.
* **Segurança (*Security*):** Esta extensão aplica políticas de segurança específicas de modelo usando o Model Armor ou soluções de terceiros. Ela pode realizar filtragem de conteúdo, detecção de ameaças e higienização tanto nas solicitações recebidas quanto nas respostas enviadas.
* **Seletor de endpoint (*Endpoint picker*):** Este é o núcleo da inteligência do GKE Inference Gateway. Ele monitora continuamente métricas importantes dos seus servidores de modelo, como solicitações pendentes e a utilização do cache de chave-valor (KV-cache). Em seguida, ele roteia a solicitação para a réplica de modelo ideal e menos sobrecarregada, garantindo baixa latência e alta vazão (*throughput*).

Esse roteamento inteligente oferece benefícios significativos de desempenho:
* **Maior vazão (*throughput*):** Obtenha um **aumento de até 40%** na vazão utilizando seus recursos de GPU e TPU de forma mais eficiente.
* **Menor latência:** Reduza os tempos de resposta em **até 60%**, proporcionando uma experiência de usuário muito superior.

Ao priorizar solicitações sensíveis à latência e gerenciar eficientemente a capacidade de seus aceleradores, o GKE Inference Gateway garante que você possa atender de forma justa a vários casos de uso de IA a partir de um único cluster, sem impactar a experiência do usuário.

---

## Demonstração: Instalação, configuração e teste do GKE Inference Gateway

Olá a todos! Neste vídeo, vamos mostrar como o GKE Inference Gateway otimiza o atendimento de seu Modelo de Grande Linguagem (LLM) em escala.

O que veremos: Primeiro, usaremos a CLI do Cloud Shell Editor para mostrar como instalar e configurar o GKE Inference Gateway em um cluster GKE. Em seguida, demonstraremos um teste de carga que compara o balanceamento de carga tradicional com o balanceamento de carga inteligente e otimizado para inferência do GKE Inference Gateway.

Vamos começar explorando nosso cluster GKE no Cloud Shell Editor.
Executamos o comando `get gateway`. A saída do comando confirma que o gateway está configurado.
Em seguida, executamos o comando `get pods`. Isso confirma que os pods estão em execução.
E, finalmente, executamos o comando `get services` para confirmar a qual serviço os pods estão expostos. Neste exemplo, eles estão expostos ao serviço VLM `llama-2-7b`.

Agora, vamos ver o GKE Inference Gateway em ação.
Para nosso exemplo, executamos um teste de carga usando um conjunto de dados GPD compartilhado e disponível publicamente.
Testamos nosso modelo contra dois cenários diferentes:
Primeiro, um gateway GKE tradicional usando balanceamento de carga padrão *round-robin*. E depois, o GKE Inference Gateway usando seu balanceamento de carga otimizado para inferência.

Para o nosso cenário, vamos comparar os resultados de ambas as execuções, começando pela **utilização do KV cache**.
Quando olhamos para os resultados do balanceamento de carga tradicional *round-robin*, note que há muita variação na utilização do KV cache entre os servidores de modelo. Em alguns casos, ele até satura alguns dos servidores de modelo completamente.
Em contraste, quando examinamos o balanceamento de carga otimizado para inferência — que roteia solicitações com base na utilização do KV cache —, a carga é distribuída de maneira muito mais uniforme entre todos os diferentes servidores de modelo.
O resultado? Nenhum dos servidores ficou saturado durante o nosso teste.

Então, o que isso significa em termos de desempenho?
No caso do balanceamento de carga tradicional, o KV cache pode ficar saturado. Aqui percebemos que novas solicitações começam a acumular na fila (*queued up*), o que leva a latências mais altas para os usuários.
No entanto, no caso do balanceamento de carga otimizado para inferência, **não houve enfileiramento de solicitações**. Isso tem um impacto direto na experiência do usuário.

Quando as solicitações entram na fila, vemos um pico na **latência até o primeiro token (TTFT)**. Esta é uma métrica crucial, pois é o tempo que os usuários precisam esperar antes de obter uma resposta às suas solicitações.
Por outro lado, ao usar o GKE Inference Gateway, a latência permanece constante e não há picos inesperados.
De fato, nosso teste mostrou que o **tempo médio por token de saída (TPOT)** foi realmente menor com o GKE Inference Gateway.
Isso significa que você obtém um desempenho geral de atendimento superior, o que se traduz em uma experiência muito melhor para os seus usuários.

E isso nos traz ao final da nossa curta demonstração. Você pôde examinar como o GKE Inference Gateway fornece uma distribuição de carga mais uniforme, reduz o enfileiramento de solicitações e mantém a baixa latência para uma melhor experiência do usuário. Obrigado por assistir!

---

## Principais conclusões

O GKE AI Inference Gateway é um balanceador de carga especializado e consciente do modelo, projetado para otimizar o atendimento de IA generativa e LLMs. Ao usar métricas específicas de IA (como fila de solicitações e utilização do KVCache) para rotear inteligentemente as solicitações, ele garante o uso eficiente dos recursos, resultando em uma vazão significativamente maior e menor latência em comparação com os balanceadores de carga tradicionais.
