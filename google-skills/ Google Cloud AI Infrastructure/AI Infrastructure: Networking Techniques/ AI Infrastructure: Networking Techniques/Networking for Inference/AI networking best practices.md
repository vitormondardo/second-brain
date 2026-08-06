# A arquitetura de offloads moderna do Titanium

O Google adota uma abordagem otimizada para cargas de trabalho na construção de sua infraestrutura, empregando uma combinação de hardware dedicado e componentes de software para atender às demandas crescentes de suas cargas de trabalho. Na base dessa infraestrutura está o Titanium, um sistema de silício personalizado e construído especificamente para esse fim, além de múltiplas camadas de offloads de escala horizontal (scale-out), que juntos impulsionam melhorias no desempenho, na confiabilidade e na segurança das cargas de trabalho dos clientes.

## Sem offloads (abordagem tradicional)
Neste estágio inicial, a VM (host) executa tanto as cargas de trabalho do usuário (suas aplicações) quanto todos os serviços principais de infraestrutura (segurança, rede e armazenamento).
* **Desvantagem:** Os núcleos de CPU alocados para a sua carga de trabalho precisam gastar ciclos gerenciando pacotes de rede, aplicando políticas de segurança e lidando com E/S de armazenamento. Essa sobrecarga consome recursos diretamente das suas aplicações, limitando o desempenho e a taxa de transferência (throughput).

## Geração 1: Com offloads no host
Para liberar a CPU principal, um hardware especializado — a Placa de Interface de Rede Inteligente (SmartNIC) — é introduzido diretamente na máquina host. Os serviços de infraestrutura (segurança, rede e armazenamento) são movidos (ou seja, sofreram *offload*) da CPU principal para essa SmartNIC dedicada.
* **Vantagem:** Isso melhora significativamente o desempenho. As cargas de trabalho do usuário obtêm o benefício da capacidade total da CPU principal, já que a SmartNIC lida com o trabalho pesado das tarefas de infraestrutura.

## Geração 2: Offloads no host + offloads de escala horizontal (scale-out)
Esta é a arquitetura Titanium, que leva o processo de *offload* um passo além, de forma crítica. Ela combina a SmartNIC no host (para tarefas básicas e de baixa latência) com uma camada de servidores de escala horizontal fora do host (frequentemente rodando em uma plataforma como o Google Borg).
* **O diferencial principal do Titanium:** Ao contrário de abordagens concorrentes que utilizam apenas offloads e aceleradores no host, o Titanium combina de forma única aceleradores inteligentes no host com uma camada de servidores de escala horizontal fora do host.

---

## O poder dos offloads de escala horizontal (scale-out)

Esta abordagem híbrida exclusiva oferece vantagens significativas:

* **Escalabilidade massiva:** Ao distribuir tarefas de infraestrutura complexas e intensivas em recursos (como determinados processamentos de rede ou gerenciamento de armazenamento em larga escala) para clusters dedicados e escaláveis (Google Borg), podemos dimensionar o desempenho muito além do que uma única máquina host consegue suportar.
* **Preço/desempenho líderes de mercado:** Descarregar tarefas para hardware centralizado e especializado que pode ser compartilhado de forma eficiente entre muitos usuários melhora drasticamente a utilização de recursos. Essa eficiência traduz-se diretamente em uma relação preço/desempenho líder de mercado para produtos construídos sobre esta arquitetura, como o Hyperdisk.
* **Segurança e confiabilidade aprimoradas:** Centralizar e isolar funções essenciais de infraestrutura (segurança, rede e armazenamento) fornece um limite de segurança mais forte e gerenciável, além de melhorar a confiabilidade geral do sistema.

Em suma, a arquitetura de Geração 2 do Titanium é a mudança fundamental que nos permite entregar serviços de nuvem superiores, mais rápidos e mais econômicos, tratando as funções de infraestrutura como um serviço externo e escalável, em vez de uma limitação fixa do host.

---

## Redes para inferência de Inteligência Artificial Generativa

Quando as empresas implantam LLMs e outras aplicações de IA generativa, elas encontram desafios de rede muito diferentes dos das aplicações web tradicionais. Por quê? Porque o comportamento da inferência de IA (fornecimento de previsões aos usuários) muda fundamentalmente a forma como a rede é utilizada.

### Tráfego de IA Generativa vs. Tráfego web tradicional
As aplicações web tradicionais normalmente possuem padrões de tráfego previsíveis. As solicitações e respostas são pequenas, e os tempos de processamento são rápidos (milissegundos). No entanto, as aplicações de IA generativa são únicas e apresentam diversas dificuldades de rede.

### O principal desafio de rede
Como uma única consulta de LLM pode ocupar 100% de uma GPU ou TPU custosa por um tempo significativo, as antigas formas de roteamento de tráfego — como o simples *round-robin* ou técnicas básicas baseadas em utilização — geralmente são ineficientes e podem levar a uma experiência ruim para o usuário.

---

| Característica | Tráfego web tradicional | Tráfego de inferência de IA Generativa |
| :--- | :--- | :--- |
| **Tamanho da requisição/resposta** | Pequeno e simples (ex.: texto e imagem pequena) | Respostas dinâmicas e com picos de tráfego (bursty), por vezes imprevisíveis, dependendo de quão ocupados estão os pontos de extremidade. A rajada de tráfego é explicada por comprimentos de resposta variáveis (imagens grandes, geração de vídeo e modelos que fornecem respostas com capacidades de raciocínio). |
| **Paralelismo de computação** | Muitas consultas podem ser processadas em paralelo em um único servidor. | Uma única consulta de LLM pode consumir 100% do tempo de computação de uma GPU/TPU. |
| **Disponibilidade de recursos** | As requisições são frequentemente processadas assim que chegam. | As requisições frequentemente aguardam até que os recursos computacionais (GPU/TPU), que são custosos e limitados, fiquem disponíveis. |
| **Latência/tempo de processamento** | Medido em milissegundos (ms). | Altamente variável, variando de segundos a minutos devido ao custo computacional. |
| **Cache** | Requisições similares frequentemente podem ser atendidas a partir de um cache rápido. | As requisições frequentemente geram conteúdo novo e exclusivo, tornando o uso de cache menos eficaz. |
| **Gerenciamento de custos** | O custo do tráfego é gerenciado dentro de um backend padronizado. | O tráfego pode ser inteligentemente direcionado para um modelo mais barato ou mais caro, dependendo da requisição específica do usuário. |

---

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

---

## Custom Metrics (Métricas Personalizadas)

As métricas personalizadas preenchem a lacuna entre o desempenho da aplicação e as decisões de roteamento da rede. A rede precisa saber mais do que apenas o status de "ativo" (*up*) ou "inativo" (*down*) de um servidor; ela precisa saber se o modelo está funcionando bem.

* **Transmissão de dados:** As aplicações (os processos de atendimento do modelo) enviam dados de desempenho específicos da carga de trabalho (por exemplo, a latência de atendimento do modelo) para o Cloud Load Balancing.
* **Protocolo ORCA (*Open Request Cost Aggregation*):** Essa transmissão de dados é frequentemente padronizada usando o protocolo ORCA.
* **Roteamento inteligente:** O balanceador de carga usa esses dados de desempenho em tempo real para tomar decisões inteligentes de roteamento e dimensionamento (*scaling*).

Por exemplo, o desempenho das aplicações de LLM depende fortemente de quão rapidamente elas podem processar os prompts de usuário recebidos. Como a maioria das plataformas de LLM usa filas internas para gerenciar essas solicitações, tempos de resposta consistentes e baixos para o usuário final exigem que o número de prompts pendentes nessas filas (a profundidade da fila / *queue depth*) seja mantido o mais curto possível. Para alcançar esse objetivo essencial, as solicitações de prompt devem ser distribuídas dinamicamente entre os modelos de LLM de backend com base diretamente na profundidade atual de suas filas.

* **Impacto:** O uso da profundidade da fila do LLM como a principal métrica de distribuição de tráfego pode render uma **melhoria de cinco a dez vezes na latência** para aplicações de IA generativa.

---

# GKE Inference Gateway

O GKE Inference Gateway foi projetado para otimizar o atendimento de grandes modelos de linguagem (LLMs) e outras cargas de trabalho de IA generativa. Ao contrário dos balanceadores de carga tradicionais, que não são adequados para a natureza complexa e variável da inferência de IA, o GKE Inference Gateway utiliza um sistema de roteamento inteligente e consciente do modelo para melhorar o desempenho, reduzir custos e simplificar as operações.

A experiência de um usuário em uma aplicação de IA generativa depende fortemente tanto de uma resposta inicial rápida a uma solicitação quanto de um fluxo contínuo da resposta até a sua conclusão. Com esses novos recursos, melhoramos o tempo até o primeiro token (TTFT - *time-to-first-token*) e o tempo por token de saída (TPOT - *time-per-output-token*) no AI Hypercomputer.

* **First-to-first-token (TTFT):** O TTFT é baseado na fase de pré-preenchimento (*prefill*), um processo limitado por computação (compute-bound) onde uma passagem completa pelo modelo cria um cache de chave-valor (KV).
* **Time per output token (TPOT):** O TPOT é baseado na fase de decodificação (*decode*), um processo limitado por memória (memory-bound) onde os tokens são gerados utilizando o cache KV gerado na etapa de pré-preenchimento.

Nós melhoramos ambos os aspectos de várias maneiras. Aplicações de IA generativa, como chatbots e ferramentas de geração de código, frequentemente reutilizam o mesmo prefixo em chamadas de API.

Para otimizar o TTFT e o TPOT, o **GKE Inference Gateway** agora oferece balanceamento de carga consciente de prefixo (*prefix-aware load balancing*). Isso melhora a latência de TTFT em **até 96%** com o pico de vazão (throughput) para cargas de trabalho pesadas em prefixos em comparação com outras nuvens, roteando inteligentemente solicitações com o mesmo prefixo para os mesmos aceleradores, enquanto equilibra a carga para evitar pontos de superaquecimento (*hotspots*) e picos de latência.

O GKE Inference Gateway é um balanceador de carga especializado e consciente de modelos que otimiza o atendimento de cargas de trabalho de IA generativa. Ele roteia automaticamente solicitações recebidas para os seus modelos de IA, garantindo que a solicitação certa vá para o modelo certo.

---

## Como o GKE Inference Gateway Funciona

O GKE Inference Gateway monitora a carga em seus servidores de modelo usando métricas específicas de IA, como o tamanho da fila de solicitações pendentes e a utilização do KVCache. Em seguida, ele roteia inteligentemente as solicitações recebidas para a GPU ou TPU menos sobrecarregada, garantindo uma distribuição uniforme do trabalho em toda a sua infraestrutura.

* **Passo 1:** Solicitação do cliente `GET / completions`
* **Passo 2:** Seleção do `InferencePool` como um serviço do Kubernetes (K8s) usando o nome do modelo (especificação da API da OpenAI).
* **Passo 3:** Escolha da réplica de modelo menos sobrecarregada que possui o adaptador LoRA em memória.
* **Passo 4:** Roteamento para o `InferencePool` e para a réplica de modelo ideal por prioridade.

---

O GKE Inference Gateway atua como um intermediário inteligente entre a solicitação de uma cliente e uma instância de modelo. Quando um cliente envia uma solicitação, o GKE Inference Gateway a processa por meio de uma série de extensões especializadas:

* **Roteamento baseado no corpo da requisição (*Body-based routing*):** Esta extensão extrai o identificador do modelo diretamente do corpo da solicitação. O GKE Inference Gateway usa essas informações para rotear a solicitação para o modelo correto com base em regras definidas por você.
* **Segurança (*Security*):** Esta extensão aplica políticas de segurança específicas de modelo usando o Model Armor ou soluções de terceiros.
* **Seletor de endpoint (*Endpoint picker*):** Este é o núcleo da inteligência do GKE Inference Gateway. Ele monitora continuamente métricas importantes dos seus servidores de modelo, como solicitações pendentes e a utilização do cache de chave-valor (KV-cache).

Esse roteamento inteligente oferece benefícios significativos de desempenho:
* **Maior vazão (*throughput*):** Obtenha um **aumento de até 40%** na vazão utilizando seus recursos de GPU e TPU de forma mais eficiente.
* **Menor latência:** Reduza os tempos de resposta em **até 60%**, proporcionando uma experiência de usuário muito superior.

---

## Demonstração: Instalação, configuração e teste do GKE Inference Gateway

Olá a todos! Neste vídeo, mostramos como o GKE Inference Gateway otimiza o atendimento de seu Modelo de Grande Linguagem (LLM) em escala. A demonstração cobre a instalação via Cloud Shell Editor (`get gateway`, `get pods`, `get services`) e compara o balanceamento tradicional *round-robin* com o balanceamento inteligente otimizado por inferência.

* **Resultados com balanceamento tradicional:** Variação alta no uso do KV cache, saturação de servidores, novas solicitações enfileiradas e picos de latência (TTFT).
* **Resultados com GKE Inference Gateway:** Carga distribuída de forma uniforme, ausência de servidores saturados, zero enfileiramento de solicitações, latência constante sem picos e menor tempo médio por token de saída (TPOT).

---

## Principais conclusões do GKE Inference Gateway

O GKE AI Inference Gateway é um balanceador de carga especializado e consciente do modelo, projetado para otimizar o atendimento de IA generativa e LLMs. Ao usar métricas específicas de IA (como fila de solicitações e utilização do KVCache) para rotear inteligentemente as solicitações, ele garante o uso eficiente dos recursos, resultando em uma vazão significativamente maior e menor latência em comparação com os balanceadores de carga tradicionais.

---

# Melhores práticas de rede para IA

Vamos analisar a seguir a criação de um ambiente de rede seguro e resiliente para as suas cargas de trabalho no AI Hypercomputer.

## Estabelecendo um Gerenciamento de Identidade e Acesso (IAM) seguro
Configurar o IAM corretamente é fundamental para a segurança e o sucesso das suas implantações de IA. 

### Verificação: Verificar permissões antes da implantação
Antes de iniciar qualquer recurso, verifique sempre se a sua conta de serviço possui as funções necessárias utilizando o comando `gcloud`:

```bash
gcloud projects get-iam-policy PROJECT_ID \
  --flatten="bindings[].members" \
  --format='table(bindings.role)' \
  --filter="bindings.members:serviceAccount:SERVICE_ACCOUNT_EMAIL"
