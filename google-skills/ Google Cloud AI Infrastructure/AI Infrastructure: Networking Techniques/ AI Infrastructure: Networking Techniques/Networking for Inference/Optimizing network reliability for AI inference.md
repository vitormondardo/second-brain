# Otimizando a confiabilidade da rede para inferência de IA

Para garantir tanto uma experiência de usuário fluida quanto o uso eficiente de recursos de GPU/TPU limitados e caros, a própria rede deve ser otimizada para comportamentos específicos de IA. O Google desenvolveu novos recursos de rede para otimizar o tráfego especificamente para aplicações de IA exigentes. Embora muitos desses recursos estejam integrados em plataformas como o Agent Platform, eles também estão sendo disponibilizados no Cloud Networking. Isso significa que você pode aproveitar essas técnicas avançadas de roteamento e gerenciamento de tráfego, independentemente de qual plataforma de LLM você escolha implantar.

## Cloud Load Balancer com extensões de serviço e métricas personalizadas

Olha, todos nós sabemos como isso funciona. As aplicações são desenvolvidas para gerar valor para o negócio, como MVPs ou provas de conceito. E então, com sorte, são reestruturadas para produção e escalabilidade. Mas conforme vivem em produção, funcionalidades vão sendo adicionadas, outros sistemas de negócios são integrados e as necessidades do usuário nunca param. Eventualmente, você se depara com um problema de confiabilidade.

Agora, as abordagens tradicionais de gerenciamento de infraestrutura nem sempre estão equipadas para lidar com a natureza dinâmica das aplicações de IA, que exigem escalabilidade contínua, tolerância a falhas e gerenciamento inteligente de tráfego. Mas sabemos que isso não é fácil. E por isso temos trabalhado em soluções para ajudar todos vocês, arquitetos e engenheiros de plataforma, a construírem confiabilidade desde o início.

O AI Hypercomputer é uma arquitetura de supercomputação totalmente integrada que oferece uma experiência de ponta a ponta para infraestrutura de IA, otimizando desempenho, velocidade e custo-benefício. Ele aborda os desafios de atender a cargas de trabalho de IA com confiabilidade, oferecendo escalabilidade e tolerância a falhas.

Agora é aqui que a coisa fica divertida. Essa arquitetura começa com o AI Hypercomputer rodando no Google Kubernetes Engine. Você pode fazer escolhas sobre qual hardware otimizado para desempenho usar (GPUs ou TPUs), Parallelstore ou GCS FUSE, e pode escolher seu framework, JAX ou PyTorch. Nesse caso, estamos usando TPUs com o mecanismo de aceleração de inferência JetStream usando JAX e GCS FUSE com SSDs para acelerar o carregamento de pesos de modelos.

Em seguida, adicionamos o Cloud Load Balancer com métricas personalizadas e extensões de serviço. O GKE e o Cloud Load Balancer se integram para fornecer confiabilidade super alta. O GKE automatiza a implantação, o escalonamento e o gerenciamento de aplicações de IA conteinerizadas com um SLA de uptime a nível de pod de 99,9%. Isso é bastante coisa. Enquanto o Cloud Load Balancer com métricas personalizadas garante alta disponibilidade e distribuição inteligente de tráfego — veja só —, hoje o Cloud Load Balancing suporta mais de 1 milhão de consultas por segundo (QPS).

Só isso já seria suficiente para colocar você escalando e garantindo tolerância a falhas. Mas as aplicações de IA apresentam toda uma gama de novos desafios à confiabilidade: Qual é o tamanho do prompt? Ele é multimodal? É seguro? Precisa ser reescrito para obter resultados precisos? Com nossas extensões de serviço, os clientes agora podem fazer o roteamento inteligente de prompts em qualquer lugar de suas arquiteturas multimodais, conectando os modelos certos aos prompts certos e protegendo seus backends contra os maliciosos ou inseguros.

Em termos de confiabilidade, falamos sobre o número de "noves" para a disponibilidade da sua aplicação — 99%, 99,9%, 99,99%. Esses noves importam. E soluções como esta vão colocá-lo diretamente nesses patamares elevados. O resto depende de você.

---

A obtenção de inferência de alta confiabilidade exige que a camada de rede seja dinâmica e ciente da saúde e do desempenho da aplicação. Isso é realizado por meio de dois recursos críticos de rede: **Extensões de Serviço** (manipulação avançada de tráfego) e **Métricas Personalizadas** (roteamento ciente da aplicação).

### Extensões de Serviço (Service Extensions)
As extensões de serviço fornecem o mecanismo para controlar com precisão como as solicitações recebidas são tratadas pela camada de rede (Cloud Load Balancing).

* **Lógica personalizada no caminho de dados:** Elas permitem que você insira seu próprio código (chamado de plugins) diretamente no caminho de tráfego do balanceador de carga.
* **Benefício para redes:** Esse recurso permite um gerenciamento e manipulação avançados do tráfego de rede, possibilitando decisões de roteamento complexas que vão além do simples *round-robin* ou *least-connections*, como modificar cabeçalhos de solicitação ou direcionar tráfego com base em características específicas do usuário.

Você pode implementar o **bloqueio de prompts** com as Extensões de Serviço para evitar que prompts indesejados cheguem aos modelos de backend e consumam recursos escassos de processamento de GPU e TPU. Você também pode usar as Extensões de Serviço para **rotear solicitações para modelos de backend específicos** com base em qual modelo é mais adequado para responder aos prompts. Para fazer isso, a extensão de serviço analisa as informações no cabeçalho da solicitação e determina o melhor modelo para atender à requisição.

As chamadas (*callouts*) de Extensões de Serviço são personalizáveis, podendo ser programadas de acordo com as necessidades exclusivas de suas aplicações de IA generativa.

<img width="1716" height="640" alt="Visuals for Course 6_1" src="https://github.com/user-attachments/assets/3343c522-bfd4-4a5d-8dfd-bd1bf055aa48" />

## Custom Metrics (Métricas Personalizadas)

As métricas personalizadas preenchem a lacuna entre o desempenho da aplicação e as decisões de roteamento da rede. A rede precisa saber mais do que apenas o status de "ativo" (*up*) ou "inativo" (*down*) de um servidor; ela precisa saber se o modelo está funcionando bem.

* **Transmissão de dados:** As aplicações (os processos de atendimento do modelo) enviam dados de desempenho específicos da carga de trabalho (por exemplo, a latência de atendimento do modelo) para o Cloud Load Balancing.
* **Protocolo ORCA (*Open Request Cost Aggregation*):** Essa transmissão de dados é frequentemente padronizada usando o protocolo ORCA.
* **Roteamento inteligente:** O balanceador de carga usa esses dados de desempenho em tempo real para tomar decisões inteligentes de roteamento e dimensionamento (*scaling*).

Por exemplo, o desempenho das aplicações de LLM depende fortemente de quão rapidamente elas podem processar os prompts de usuário recebidos. Como a maioria das plataformas de LLM usa filas internas para gerenciar essas solicitações, tempos de resposta consistentes e baixos para o usuário final exigem que o número de prompts pendentes nessas filas (a profundidade da fila / *queue depth*) seja mantido o mais curto possível. Para alcançar esse objetivo essencial, as solicitações de prompt devem ser distribuídas dinamicamente entre os modelos de LLM de backend com base diretamente na profundidade atual de suas filas.

* **Impacto:** O uso da profundidade da fila do LLM como a principal métrica de distribuição de tráfego pode render uma **melhoria de cinco a dez vezes na latência** para aplicações de IA generativa.

<img width="1619" height="763" alt="C6 architecture" src="https://github.com/user-attachments/assets/3617e2c9-9dd3-4fe8-9878-bf196ad86bf2" />

# GKE Inference Gateway

A experiência de um usuário em uma aplicação de IA generativa depende fortemente tanto de uma resposta inicial rápida a uma solicitação quanto de um fluxo contínuo da resposta até a sua conclusão. Com esses novos recursos, melhoramos o tempo até o primeiro token (TTFT - *time-to-first-token*) e o tempo por token de saída (TPOT - *time-per-output-token*) no AI Hypercomputer.

* **First-to-first-token (TTFT):** O TTFT é baseado na fase de pré-preenchimento (*prefill*), um processo limitado por computação (compute-bound) onde uma passagem completa pelo modelo cria um cache de chave-valor (KV).
* **Time per output token (TPOT):** O TPOT é baseado na fase de decodificação (*decode*), um processo limitado por memória (memory-bound) onde os tokens são gerados utilizando o cache KV gerado na etapa de pré-preenchimento.

Nós melhoramos ambos os aspectos de várias maneiras. Aplicações de IA generativa, como chatbots e ferramentas de geração de código, frequentemente reutilizam o mesmo prefixo em chamadas de API.

Para otimizar o TTFT e o TPOT, o **GKE Inference Gateway** agora oferece balanceamento de carga consciente de prefixo (*prefix-aware load balancing*). Isso melhora a latência de TTFT em **até 96%** com o pico de vazão (throughput) para cargas de trabalho pesadas em prefixos em comparação com outras nuvens, roteando inteligentemente solicitações com o mesmo prefixo para os mesmos aceleradores, enquanto equilibra a carga para evitar pontos de superaquecimento (*hotspots*) e picos de latência.

O GKE Inference Gateway é um balanceador de carga especializado e consciente de modelos que otimiza o atendimento de cargas de trabalho de IA generativa. Ele roteia automaticamente solicitações recebidas para os seus modelos de IA, garantindo que a solicitação certa vá para o modelo certo.

<img width="1647" height="751" alt="Inefernce_architecture" src="https://github.com/user-attachments/assets/18aca34f-b69d-4deb-ba6f-2c20b1020139" />



