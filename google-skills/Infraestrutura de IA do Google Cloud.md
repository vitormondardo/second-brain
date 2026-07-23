# Infraestrutura de IA do Google Cloud 
Este programa de aprendizagem, desenvolvido para alunos com conhecimentos técnicos de nível intermediário a avançado, oferece cursos sob demanda para especialização em Infraestrutura de IA no Google Cloud. Você adquirirá o conhecimento e as habilidades necessárias para projetar e implementar soluções de IA/ML de alto desempenho usando o hipercomputador de IA do Google Cloud, GPUs, TPUs, computação e o Google Kubernetes Engine. Otimize cada camada das suas soluções de Infraestrutura de IA, do armazenamento e rede à orquestração e melhores práticas, para redefinir o que é possível com a Infraestrutura de IA do Google.

## Introducing the AI Hypercomputer
O AI Hypercomputer é um sistema de supercomputação otimizado para dar suporte às suas cargas de trabalho de inteligência artificial (IA) e aprendizado de máquina (ML). Trata-se de um sistema integrado que combina hardware otimizado para desempenho, software aberto, frameworks de ML e modelos de consumo flexíveis.

A ideia central de uma arquitetura baseada em sistemas é que ela é projetada e otimizada como um todo integrado e completo, em vez de consistir apenas na montagem de componentes individuais de alto desempenho. O Google não está oferecendo apenas TPUs, GPUs ou redes rápidas de forma isolada.

A empresa oferece um sistema no qual esses componentes são especificamente selecionados, interconectados e ajustados para colaborar de maneira ideal nas tarefas mais exigentes de IA e aprendizado de máquina.

## Ponto-chave
O AI Hypercomputer é a solução holística do Google Cloud projetada para superar os obstáculos comuns ao escalar inovações de IA para aplicações no mundo real.

Ele integra hardware otimizado para desempenho, software aberto e modelos de consumo flexíveis, oferecendo um sistema unificado desenvolvido para cargas de trabalho exigentes de IA e aprendizado de máquina.

Para mostrar como o supercomputador de IA ganha vida em um data center, vamos pensar em como configuramos uma tarefa de treinamento de aprendizado de máquina.

É aqui que tudo começa: com a infraestrutura de desempenho otimizado que está no coração do supercomputador de IA. Um agendador ou orquestrador inicia uma tarefa de treinamento que cria um cluster de computação.

Para cargas de trabalho de IA, essas instâncias utilizam aceleradores como GPUs da Nvidia ou TPUs do Google Cloud. Este é um rack de servidores equipado com VMs A3, impulsionadas pelas GPUs H100 com Tensor Cores da Nvidia. Nossas unidades de processamento de inteligência (IPUs) personalizadas, com capacidade de 200 GB por segundo, criam um canal de dados separado para que as GPUs se comuniquem entre si, contornando a CPU host.

Uma TPU é uma Unidade de Processamento de Tensores; cada chip possui núcleos de tensores com quatro unidades de multiplicação de matrizes (MXUs) que realizam os cálculos aritméticos propriamente ditos. As TPUs são desenvolvidas internamente e projetadas especificamente para inteligência artificial — tudo está integrado nelas. Em uma única máquina, as TPUs podem contar com um, quatro ou oito chips, e cargas de trabalho em múltiplos hosts podem utilizar centenas ou até milhares dessas TPUs em conjunto, formando um "pod" ou uma "fatia de pod" (pod slice).

Tarefas de treinamento que abrangem múltiplas fatias (multi-slice) podem utilizar dezenas de milhares de chips. Tudo isso é incrível; a escala aqui é tão vasta que chega a ser difícil de assimilar. Além disso, ao adotarmos o OCS para rede, criamos a oportunidade de reduzir o consumo de energia em até 40%.

Essa infraestrutura de alto desempenho, eficiência energética e alta densidade de potência oferece um caminho para uma TI mais sustentável. Esperamos que todos tenham gostado de conhecer os elementos que tornam o supercomputador de IA uma realidade.

O sistema de hipercomputador de IA possui três camadas principais: hardware otimizado para desempenho, software aberto e consumo flexível.

## <img width="1474" height="738" alt="Rise - AI Hypercomputer Architecture_arrows (2)" src="https://github.com/user-attachments/assets/20865dac-3bd1-4d3f-9f9c-47915ca2281f" />

##Camada 1: Infraestrutura dedicada e otimizada para desempenho

Essa camada fundamental fornece o poder computacional bruto necessário para tarefas de IA exigentes. Ela engloba recursos de aceleração, rede de alta velocidade e infraestrutura de armazenamento eficiente, tudo meticulosamente otimizado para desempenho em escala.

### Compute
No cerne da infraestrutura estão as unidades de processamento projetadas para aceleração de IA. Isso inclui hardware de última geração, como as TPUs (Tensor Processing Units) do Google Cloud e poderosas GPUs (unidades de processamento gráfico) em nuvem, complementadas por CPUs onde necessário.

Esses componentes fornecem as capacidades fundamentais de processamento para o treinamento de modelos complexos e para a inferência.

### Storage
Lidar com os enormes conjuntos de dados comuns em IA exige soluções de armazenamento especializadas. O AI Hypercomputer oferece suporte a diversas opções, incluindo armazenamento em bloco, arquivo e objeto. Por exemplo, o Hyperdisk ML fornece armazenamento em bloco otimizado especificamente para inferência e disponibilização de modelos de IA, reduzindo significativamente os tempos de carregamento dos modelos e melhorando a eficiência de custos.

Além disso, os recursos de cache do Cloud Storage FUSE e do Parallelstore aumentam a vazão e reduzem a latência tanto no treinamento quanto na inferência. Esse armazenamento otimizado é fundamental em todo o pipeline de dados de IA — desde a preparação e o treinamento até a inferência, a entrega e a proteção de dados. Um sistema de arquivos paralelo de alto desempenho e totalmente gerenciado, como o Google Cloud Managed Lustre, oferece escalabilidade na casa dos petabytes e alta vazão. Essas soluções são otimizadas para aplicações de IA e HPC.

### Nerworking
Conectar esses aceleradores de alto desempenho de maneira eficiente é fundamental. O Google Cloud utiliza tecnologias de rede avançadas, como a *fabric* de rede Jupiter e a comutação de circuitos ópticos (OCS), para criar redes de data center altamente escaláveis. Essa infraestrutura oferece largura de banda na escala de petabits, essencial para tarefas de treinamento distribuído em larga escala. Por exemplo, as VMs A3 Mega utilizam essa *fabric* Jupiter.

Além disso, uma infraestrutura de VPC especializada e otimizada para conectividade direta entre GPUs oferece capacidade de até 3,2 Tbps por VM e suporta RDMA para transferência de dados com latência ultrabaixa, aspectos vitais para cargas de trabalho de IA Generativa e HPC.

## Camada 2: Software aberto

Com base em seu hardware robusto, o AI Hypercomputer utiliza uma pilha de software aberta. Essa camada conta com versões otimizadas de frameworks, bibliotecas, compiladores, projetos de referência, ferramentas de orquestração e sistemas operacionais populares, todos operando de forma integrada e fluida. O objetivo principal é simplificar o acesso às poderosas TPUs e GPUs subjacentes, aumentando significativamente o desempenho e a produtividade, além de aprimorar a usabilidade geral para tarefas de IA.

### Orchestration
Para implantar e gerenciar um grande número de aceleradores como uma unidade única, você também pode usar o Cluster Director para Google Kubernetes Engine, o Cluster Director para Slurm ou interagir diretamente com as APIs do Compute Engine. O GKE é uma excelente escolha para atender às necessidades de orquestração, sendo selecionado por sua base no Kubernetes (de código aberto), que oferece portabilidade, capacidade de personalização, desempenho e escalabilidade.

No contexto do AI Hypercomputer, o GKE inclui recursos especializados e otimizados para IA. Exemplos disso são o escalonamento com reconhecimento de IA generativa, o pré-carregamento de contêineres para inicializações mais rápidas, o acesso otimizado a dados por meio de ferramentas como o Google Cloud Storage Fuse (com cache) e o Parallelstore, além do gerenciamento eficiente de jobs utilizando o sistema de filas Kueue.

<img width="552" height="223" alt="Rise - AI Hypercomputer Architecture-3" src="https://github.com/user-attachments/assets/31a5556c-b49b-407f-ba24-dddbea53d00f" />

### ML Frameworks
Para o desenvolvimento de modelos, a stack oferece suporte otimizado a frameworks de aprendizado de máquina open-source fundamentais, como Jax, PyTorch e Keras, aproveitando o poderoso compilador XLA para garantir desempenho máximo no hardware do Google. Para agilizar os fluxos de trabalho, ela inclui bibliotecas úteis como Optimum TPU e PyTorch/XLA, que simplificam a execução eficiente de modelos, além de ferramentas especializadas como JetStream para inferência de LLMs, MaxText como referência para LLMs densos e MaxDiffusion para modelos de difusão.

O suporte a frameworks distribuídos, como o Ray no GKE, e a utilização de blueprints de cluster para implantações replicáveis ​​asseguram um ambiente abrangente para a criação e o escalonamento de aplicações de IA de ponta.

## Camada 3: Consumo flexível

O Google Cloud oferece uma gama versátil de modelos de consumo para seu sistema AI Hypercomputer, projetados para otimizar custos, flexibilidade e disponibilidade de recursos para diversas cargas de trabalho de IA/ML.

### Standard Options
- Preços sob demanda, que oferecem máxima flexibilidade para picos de demanda ou necessidades imprevisíveis, com pagamento conforme o uso e sem interrupção forçada (preempção).
- Para tarefas tolerantes a falhas, como processamento em lote ou experimentação, as VMs Spot oferecem o menor custo ao utilizar capacidade ociosa, embora estejam sujeitas à interrupção forçada.
- Cargas de trabalho previsíveis e de regime estável são as que mais se beneficiam dos descontos por uso comprometido (CUDs), que proporcionam economias significativas (30% a 55%) mediante compromissos de uso de recursos por um ou três anos. No entanto, os CUDs, por si só, não garantem a disponibilidade de capacidade.

### Guaranteed / Garantia
Para cenários que exigem disponibilidade garantida de recursos, é possível fazer reservas para tipos de máquina e zonas específicos, assegurando que a capacidade seja mantida para cargas de trabalho críticas. Essas reservas podem ser combinadas com CUDs (Compromissos de Uso Contínuo) para reduzir os custos dos recursos reservados.

Atendendo a essa necessidade de acesso garantido, o Google Cloud oferece o Dynamic Workload Scheduler (DWS). O DWS melhora o acesso a GPUs e TPUs de alta demanda para cargas de trabalho de IA com picos de utilização, sem exigir necessariamente compromissos de longo prazo. Ele também pode ser utilizado para trabalhos em lote (batch jobs) com o modo Flex Start. A ferramenta integra-se a plataformas como GKE e Vertex AI e opera em dois modos: DWS Calendar Mode e DWS Flex Start Mode.


## Principais pontos

O Hipercomputador de IA é um sistema integrado projetado para escalar e implantar aplicações de IA de forma eficiente.

Camada 1: Hardware de IA de alto desempenho (TPUs/GPUs), rede de alta velocidade e armazenamento otimizado para cargas de trabalho de IA exigentes.

Camada 2: Software aberto (PyTorch, GKE, Kueue) que simplifica fluxos de trabalho de IA e aumenta a produtividade.

Camada 3: Modelos de consumo flexíveis (sob demanda, spot, CUDs, reservas, DWS) para diversas cargas de trabalho de IA.

---
## Principais cargas de trabalho

Agora que você entende a arquitetura por trás do AI Hypercomputer, vamos explorar o que ele possibilita na prática. As principais cargas de trabalho nas quais o AI Hypercomputer pode ajudar são:

1 - Viabilizar o treinamento de IA em larga escala

2 - Servir modelos de forma eficiente e em escala

3 - Desenvolver aplicações de IA com frameworks abertos

### Impulsionando o treinamento de IA em larga escala

O AI Hypercomputer atua como o sistema de supercomputação fundamental para praticamente todas as cargas de trabalho de IA executadas no Google Cloud. O sistema foi projetado especificamente para escalar com eficiência, tornando-o ideal para atender às demandas rigorosas do treinamento de modelos em larga escala.

Para atender a diversos objetivos de treinamento, o Google Cloud oferece opções de hardware otimizadas, incluindo as poderosas TPUs e GPUs do Google Cloud.

Vamos ver isso na prática com clientes como a Assembly AI.

Somos a Assembly AI. Somos uma plataforma de IA especializada em comunicação humana. Especificamente, trabalhamos com conversão de fala em texto e compreensão de fala. Atualmente, realizamos dois milhões de transcrições e 25 milhões de chamadas de inferência por dia, além de termos mais de 85 serviços de modelos distintos em produção. Nossos clientes utilizam alguns desses serviços para potencializar seus próprios produtos. Portanto, atuamos no modelo B2B: dependemos de outras empresas que criam produtos incríveis impulsionados por IA, utilizando nossos serviços.

O grande diferencial que o Google Cloud nos proporcionou foi a capacidade de superar a limitação de treinar modelos em uma única máquina equipada com 8 GPUs — algo que fazíamos anteriormente com um volume de 60.000 horas de áudio. Com o Google Cloud, conseguimos escalar para mais de 1.000.000 de horas de áudio, utilizando 32 GPUs trabalhando em conjunto. Esse avanço só foi possível graças ao uso do Google Cloud.

Na verdade, realizamos três atividades distintas no Google Cloud: treinamento de modelos de *deep learning*, pipelines de dados em larga escala e inferência. Para a inferência, utilizamos TPUs. Teremos uma palestra hoje mais tarde sobre as TPUs v5e, abordando sua excelente relação custo-benefício. Para pipelines de dados, utilizamos BigQuery, Dataproc e outros produtos do Google Cloud baseados no modelo MapReduce. Por fim, para o treinamento, utilizamos Vertex AI, serviços de armazenamento, Filestore e outros recursos de IA da plataforma. Ou seja, utilizamos uma ampla gama de serviços do Google Cloud.

Escalabilidade é a especialidade do Google, certo? É possível obter grandes quantidades de recursos computacionais, GPUs e capacidade de processamento de dados no Google de forma muito eficiente. Quanto ao impacto para nossos clientes, isso nos permite treinar modelos maiores e melhores com um bom custo-benefício e realizar inferências a um custo acessível, capacitando-os a aprimorar seus produtos.

A escala do Google nos permite treinar modelos cada vez maiores. Atualmente, nosso foco está em modelos com bilhões de parâmetros e petabytes de dados, que exigem *pods* de TPUs. Isso só é possível graças ao Google Cloud. Isso só é possível graças aos modelos treinados no Google. Basicamente, ao avançar na hierarquia tecnológica, viabilizamos recursos poderosos que tornam o mundo um lugar melhor. E isso é possível porque todos trabalhamos juntos — sabe? — e construímos algo novo a partir do que foi realizado por quem veio antes de nós.

### Servindo modelos de forma eficiente e em escala

Além do treinamento, o AI Hypercomputer foi projetado para implantar modelos e atender usuários com eficiência de custos. Ele permite que as implantações alcancem escalas líderes no setor, maximizando a relação custo-benefício para a disponibilização de modelos de IA.

- Isso inclui oferecer opções de aceleradores voltados para inferência, como o Ironwood, que proporciona 5 vezes mais capacidade de computação de pico e 6 vezes mais capacidade de memória de alta largura de banda (HBM) em comparação com a geração anterior, o Trillium.

- As instâncias Cloud TPU v5e e G2 VM (equipadas com GPUs NVIDIA L4), que oferecem excelente custo-benefício. Essas opções entregam alto desempenho para uma ampla gama de cargas de trabalho de IA, lidando com eficiência com a inferência para os mais recentes modelos de linguagem de grande porte e modelos de IA generativa.

## Desenvolvimento de aplicações de IA com frameworks abertos

Em última análise, o objetivo é oferecer aplicações de IA de alto impacto. O AI Hypercomputer apoia diretamente essa meta ao adotar software e padrões abertos.

Notebook exibindo um exemplo de framework, acompanhado de um post-it e outros elementos decorativos.

Seu ecossistema de software aberto e otimizado garante suporte robusto a ferramentas e bibliotecas poderosas e amplamente utilizadas pelos desenvolvedores, como PyTorch e JAX. Esse compromisso permite que os clientes criem soluções utilizando frameworks familiares, ao mesmo tempo em que se beneficiam da eficiência da arquitetura subjacente.

O Google Cloud trabalha ativamente para garantir a integração perfeita desses frameworks abertos, viabilizando aplicações inovadoras — como a implementação eficiente de sistemas sofisticados de RAG (Retrieval-Augmented Generation) no GKE.

A Cymbal Auto Manufacturing enfrentou um desafio significativo ao escalar suas iniciativas de IA em suas linhas de produção.

Apesar de desenvolver modelos de IA promissores em laboratório, integrá-los às operações diárias reais mostrou-se complexo e frequentemente resultava em gargalos computacionais, ineficiências nos pipelines de dados e limitações de infraestrutura.

Tarefas manuais e trabalhosas consumiam milhares de horas anualmente, impedindo que os trabalhadores da fábrica se concentrassem em atividades de maior valor. A infraestrutura existente tinha dificuldades em se adaptar à crescente demanda dos usuários e à necessidade de criação rápida de modelos, impactando a eficiência geral da manufatura e a otimização dos processos.

A Cymbal recorreu ao AI Hypercomputer do Google Cloud para construir uma plataforma de IA inovadora. O objetivo era criar um sistema que capacitasse os funcionários da linha de produção — independentemente de sua experiência em IA — a desenvolver e implantar modelos de aprendizado de máquina com facilidade. Essa plataforma utilizou uma abordagem de nuvem híbrida, empregando recursos locais (*on-premises*) para operações normais e escalando para a nuvem durante picos de demanda, a fim de otimizar custos e desempenho no uso de GPUs.

Os principais componentes do Google Cloud incluíram o Google Kubernetes Engine (GKE), com Autopilot e Image Streaming para flexibilidade e velocidade, e o Dynamic Workload Scheduler para o gerenciamento eficiente de recursos de GPU.

A plataforma também integrou o Cloud Build para gatilhos de CI (Integração Contínua), o Artifact Registry e o Container Analysis para verificação de vulnerabilidades em imagens, além do Binary Authorization para garantir um ambiente seguro.

Os modelos de treinamento foram criados em VMs N1 com GPUs NVIDIA T4 e VMs A2 com GPUs NVIDIA A100, implantadas por meio de aplicações web no GKE. Uma pequena equipe de seis desenvolvedores construiu e implantou a plataforma em aproximadamente metade do tempo de um projeto padrão de desenvolvimento de sistemas.

Estimou-se que a nova plataforma economizaria até 10.000 horas de trabalho rotineiro por ano, graças à maior eficiência na manufatura e à otimização de processos. O recurso de Image Streaming do GKE agilizou a inicialização de *pods* e acelerou o aprendizado, resultando em uma redução de 20% no tempo de criação de modelos de aprendizado e melhorando significativamente a experiência do usuário.

A plataforma de IA está agora em uso nas dez fábricas da Cymbal Car Manufacturing, com aplicações que vão além da inspeção de peças acabadas para incluir o monitoramento em tempo real do processo de fabricação, a inspeção da aplicação de adesivos e a detecção de anomalias em máquinas de moldagem por injeção. Isso levou a uma maior adoção pelos usuários e a um salto significativo na produtividade.

Este caso de uso demonstra como o AI Hypercomputer no Google Cloud ajuda a superar desafios reais da manufatura, permitindo que as organizações alcancem eficiências operacionais significativas e acelerem a adoção de IA.

Principais destaques

Este conjunto tecnológico de alto desempenho foi projetado de forma integrada para oferecer a melhor relação inteligência-custo em tarefas intensivas de IA, viabilizando aplicações do mundo real, como:

Treinamento de modelos de IA em larga escala: aproveitando seu poder para o desenvolvimento de modelos complexos.

Serviço de modelos eficiente em escala: oferecendo uma relação custo-benefício ideal para a implementação generalizada de IA.

Desenvolvimento de aplicações de IA: impulsionando a inovação por meio do uso de frameworks abertos.

## Deployment options

<img width="1920" height="664" alt="T-AIHYPE_Rise-12" src="https://github.com/user-attachments/assets/c8c01380-0e94-43bc-b27d-3147cc8aa1e6" />

A escolha da opção de implantação adequada é fundamental, e o Google Cloud oferece uma variedade de abordagens. Elas variam desde opções altamente manuais e autogerenciadas, que oferecem controle máximo, até soluções mais automatizadas e totalmente gerenciadas, projetadas para facilitar o uso. Nesta aula, você analisará quatro opções principais de implantação — Direta, Fundamental, Open Frameworks e Totalmente Gerenciada — para ajudar a decidir qual caminho melhor atende às necessidades da sua organização, à experiência da sua equipe e às tarefas específicas de IA.

Lembre-se: a configuração, como TPUs, GPUs e armazenamento, é definida pelo seu caso de uso; a estratégia é como você a orquestra.

## Gerenciamento direto usando o Google Compute Engine (GCE)

O gerenciamento direto usando o Google Compute Engine (GCE) representa a abordagem mais manual e prática. Essa opção de autogerenciamento interage diretamente com os recursos de computação fundamentais, como máquinas virtuais com Cloud TPU ou Cloud GPU. Existem várias formas de provisionar esses recursos: criando VMs individuais, utilizando a Bulk API para conjuntos maiores ou empregando Managed Instance Groups (MIGs) para obter certo nível de escalabilidade automatizada e resiliência.

<img width="819" height="222" alt="T-AIHYPE_Deployment options_direct 2" src="https://github.com/user-attachments/assets/6def7b91-964a-4f13-bf69-1ec10cce60ef" />

- Controle: controle máximo absoluto sobre todos os aspectos do seu ambiente
- Expertise: Você precisará de expertise significativa em engenharia de infraestrutura em sua equipe para lidar com tudo: instalação, configuração, rede, instalação de software, agendamento de tarefas, manutenção e resolução de problemas.
- Tempo: Embora poderoso, esse método direto pode aumentar o tempo necessário para colocar seus modelos em operação, devido à configuração envolvida, e acrescenta uma carga operacional contínua considerável.
- Quando escolher: Geralmente, é a opção escolhida quando o controle absoluto e granular é fundamental para necessidades altamente específicas ou experimentais.

## Abordagem de base com o Google Kubernetes Engine (GKE)

Avançando para uma maior estruturação, a abordagem de base utiliza o Google Kubernetes Engine (GKE) como uma plataforma unificada e nativa da nuvem, otimizada para o treinamento e a disponibilização de modelos de IA/ML.

<img width="813" height="223" alt="T-AIHYPE_Deployment options_foundational 2" src="https://github.com/user-attachments/assets/54ceb9b5-aa1f-4c87-82e5-b6ba2b68207f" />

No âmbito do AI Hypercomputer, o GKE oferece recursos poderosos para aceleração de cargas de trabalho, otimização de recursos e facilitação da gestão de equipes. Ele gerencia dezenas de milhares de TPUs ou nós e integra escalonamento automático, alocação inteligente de tarefas, provisionamento eficiente e sistemas sofisticados de enfileiramento e agendamento de tarefas (frequentemente utilizando ferramentas como o Kueue).

- Controle: Embora ofereça autogerenciamento em relação à configuração de clusters e à implantação de cargas de trabalho, o GKE automatiza muitas tarefas de infraestrutura subjacente em comparação com o GCE.
- Expertise: Considerações importantes são a necessidade de conhecimento em Kubernetes e a responsabilidade pelo gerenciamento de clusters GKE.
- Quando escolher: Essa abordagem é adequada para organizações com expertise em Kubernetes que buscam um equilíbrio entre controle e automação para treinamento distribuído em larga escala ou pipelines de inferência complexos.

## Cluster Director

Ao escalar aceleradores, é necessário um meio de implantá-los em grupos — idealmente, próximos fisicamente uns dos outros — e, em seguida, controlar com precisão onde as cargas de trabalho serão executadas neles. É exatamente isso que um Cluster Director faz.

<img width="960" height="496" alt="Cluster Director test" src="https://github.com/user-attachments/assets/26898966-ee65-46e8-85d9-b7dcaa847e40" />

## Frameworks abertos integrados via toolkits

Para equipes que buscam aproveitar padrões estabelecidos e melhores práticas — especialmente em computação de alto desempenho ou frameworks de ML específicos —, a próxima estratégia envolve frameworks abertos integrados via toolkits.

<img width="1920" height="553" alt="ray" src="https://github.com/user-attachments/assets/d18236d3-8184-4d3d-8d0d-33e31481453f" />

Um excelente exemplo disso é o uso do Cluster Toolkit. Trata-se de um kit de ferramentas modular, baseado em Terraform, projetado para implantar ambientes de HPC e IA/ML prontos para uso e replicáveis, seguindo as melhores práticas do Google Cloud. O kit oferece modelos (*blueprints*) capazes de configurar automaticamente ambientes com gerenciadores de tarefas conhecidos, como o Slurm, ou de estruturar fluxos de trabalho de ML no GKE utilizando o Kueue. Essencialmente, ele encapsula conhecimento especializado em modelos prontos para implantação, simplificando a configuração de ambientes complexos estruturados em torno de frameworks como Ray ou Slurm.

- Controle: Essa abordagem ainda oferece considerável flexibilidade e aproveita ferramentas de código aberto, mas reduz parte da carga de configuração manual em comparação com a criação de tudo do zero no GKE ou no GCE.
- Expertise: É um excelente meio-termo para equipes que buscam arquiteturas baseadas em melhores práticas sem precisar projetar cada componente do zero, embora a familiaridade com o framework escolhido (Slurm, Ray) — e, potencialmente, com o Terraform — seja vantajosa.

## Abordagem totalmente gerenciada utilizando Vertex AI e Cloud Run

Por fim, na extremidade de maior automação do espectro, temos a abordagem totalmente gerenciada utilizando Vertex AI e Cloud Run. Nossos LLMs mais capazes, incluindo os modelos Gemini, são treinados em uma infraestrutura poderosa. É por isso que o Gemini Flash 2.0, impulsionado pelo AI Hypercomputer, oferece uma inteligência significativamente maior por dólar investido.

<img width="1920" height="795" alt="AI Hypercomputer_Rise build_AG-33@2x" src="https://github.com/user-attachments/assets/2c792754-71f3-4c16-a315-5887432922a3" />

<img width="522" height="532" alt="Rise - AI Hypercomputer Architecture_Vertex AI" src="https://github.com/user-attachments/assets/9c1b161e-913b-4544-ac83-89f17ca6ceff" />

Ao utilizar o Vertex AI, nossa plataforma de desenvolvimento de IA totalmente gerenciada, o AI Hypercomputer é integrado de forma transparente. Isso frequentemente oferece o caminho mais simples e rápido para criar e implantar soluções de IA. Em uma arquitetura mais ampla, o Vertex AI geralmente atua como uma camada sobre a infraestrutura central do AI Hypercomputer. Ele oferece um conjunto abrangente de serviços gerenciados — abrangendo desde a preparação de dados e o treinamento de modelos (Vertex AI Training) até a implantação (Vertex AI Endpoints), a orquestração de MLOps (Vertex AI Pipelines) e o acesso a soluções prontas e modelos de base por meio do Model Garden.

Com o Vertex AI, o Google gerencia a grande maioria da infraestrutura subjacente e da complexidade operacional. Sua equipe interage com APIs e interfaces de nível superior, concentrando-se principalmente nos aspectos de aprendizado de máquina do projeto.

- Controle: A contrapartida é, geralmente, um controle menos granular em comparação com as opções autogerenciadas, uma vez que você opera dentro da estrutura fornecida pelo serviço gerenciado.
- Expertise: Isso reduz significativamente a barreira de entrada, acelera a obtenção de valor e diminui a necessidade de conhecimento profundo em infraestrutura..
- Quando escolher: Essa abordagem é excelente para equipes que priorizam velocidade, facilidade de uso e o aproveitamento de uma plataforma de MLOps totalmente integrada.

## Cloud Run

Agora, vamos falar sobre o Cloud Run. O Cloud Run permite executar seu código diretamente na infraestrutura escalável do Google, combinando a flexibilidade dos containers com a simplicidade do modelo *serverless* para aumentar sua produtividade.

Qualquer carga de trabalho que realize inferência de IA — especialmente aplicações que exigem processamento em tempo real — necessita de aceleração por GPU para oferecer uma experiência responsiva ao usuário. Com suporte para GPUs NVIDIA, o Cloud Run pode realizar inferência de IA online sob demanda, utilizando os LLMs de sua escolha em questão de segundos. Com 24 GB de vRAM, você obtém altas taxas de processamento de tokens para modelos com até 9 bilhões de parâmetros, incluindo Llama 3.1 (8B), Mistral (7B) e Gemma 2 (9B). Quando sua aplicação não está em uso, o serviço reduz automaticamente a escala para zero, evitando cobranças desnecessárias.

1 - Controle: Com o Cloud Run, você pode executar serviços de *frontend* e *backend* e tarefas em lote (*batch jobs*), implantar sites e aplicações e processar cargas de trabalho baseadas em filas. Tudo isso sem precisar gerenciar a infraestrutura subjacente.

2 - Quando escolher: O Cloud Run é ideal para cargas de trabalho de inferência de IA em tempo real e orientadas a eventos, nas quais a eficiência de custos e o escalonamento automático são fatores decisivos.

3 - Especialização: Sua natureza *serverless* reduz a carga operacional, permitindo que sua equipe foque na lógica da aplicação em vez de no gerenciamento da infraestrutura.

**A escolha ideal depende muito das necessidades específicas da sua organização: equilibrar o desejo de controle e flexibilidade com a necessidade de facilidade de uso e velocidade, levando em conta a experiência que sua equipe já possui em gerenciamento de infraestrutura, Kubernetes, Cluster Toolkit ou plataformas de MLOps.

Árvore de decisão para escolher a plataforma de ML ideal no Google Cloud

Esta árvore serve como um guia útil para escolher sua plataforma de ML, considerando sua experiência e o nível de controle desejado, a fim de encontrar o serviço do GCP mais adequado para suas cargas de trabalho de ML.

O Google Cloud oferece uma estrutura de decisão organizada para ajudar você a selecionar a plataforma de ML ideal no GCP. Essa estrutura orienta suas escolhas com base no nível de gerenciamento desejado e na experiência da equipe, direcionando você tanto para serviços totalmente gerenciados — como Vertex AI e BQML — quanto para opções de infraestrutura totalmente configuráveis, como GKE, Cloud Run, GCE e Cloud Batch.

<img width="840" height="414" alt="T-AIHYPE_Decision tree ML workloads" src="https://github.com/user-attachments/assets/82cd0bab-649c-400b-8350-ec1c4833d96f" />


### Qual opção de implantação de hipercomputador de IA se caracteriza por oferecer o controle mais direto sobre a infraestrutura subjacente, mas também implica maior sobrecarga operacional?

R: Google Compute Engine

## Principais pontos

Existem quatro opções principais de implantação para cargas de trabalho de IA Hypercomputer no Google Cloud:

Gerenciamento direto (GCE): Controle máximo, alta sobrecarga operacional e necessidade de profundo conhecimento em infraestrutura.

Fundamental (GKE): Equilibra controle e automação; ideal para especialistas em Kubernetes.

Frameworks abertos via Toolkits: Aproveita as melhores práticas e simplifica configurações complexas (ex.: Cluster Toolkit).

Totalmente gerenciado (Vertex AI): Mais fácil de usar; o Google cuida da infraestrutura, com controle menos granular.

A escolha da estratégia ideal depende das necessidades de controle, da expertise da equipe e dos objetivos do projeto.

Resumo das Opções de Consumo no AI HypercomputerAs opções de consumo são os modelos de provisionamento e contratação que definem como você acessa e paga pelos recursos de computação no Google Cloud AI Hypercomputer (como GPUs e TPUs). O objetivo principal é balancear custo, disponibilidade e tolerância a interrupções de acordo com a sua necessidade.1. Perguntas-chave para Escolha do ModeloCritérioPergunta de DecisãoDuração do TrabalhoPreciso dos recursos por horas, dias ou meses contínuos?Sensibilidade ao CustoO foco é o menor preço possível ou garantir que a tarefa rode sem atrasos?DisponibilidadePreciso de acesso imediato/garantido ou posso aguardar capacidade ociosa?Tolerância a FalhasA carga de trabalho pode ser interrompida bruscamente e reiniciada?PrevisibilidadeO uso é constante (regime estável) ou esporádico/em picos?2. Comparativo das Duas Principais OpçõesA. Reservas Futuras (Future Reservations)O que é: Garantia de capacidade reservada para uma data/hora específica no futuro.Funcionamento: Alocação densa de hardware (minimiza latência de rede) atrelada a um modelo de reserva (reservation-bound).Prerupção: Não. O recurso é dedicado e não pode ser interrompido inesperadamente.Preço: Descontos de até 53% (vCPUs e GPUs).Ideal para: Treinamento de grandes modelos de fundação, projetos críticos de longa duração e cargas de trabalho que não toleram interrupções.  B. Instâncias Spot (Spot VMs)O que é: Uso de capacidade ociosa dos data centers do Google Cloud.Funcionamento: Provisionamento flexível sem garantia de disponibilidade.Prerupção: Sim. O Google pode interromper a VM a qualquer momento se precisar da capacidade.Preço: Altíssimo desconto (60% a 91% de economia).Ideal para: Processamento em lote (batch jobs), testes, experimentos e tarefas altamente tolerantes a falhas que possuem pontos de checagem (checkpoints).  3. Síntese dos Cenários de UsoCenário 1 — Treinamento de Longa Duração Sem Interrupções:Escolha: Reservas Futuras (Future Reservations).Motivo: Prioriza a confiabilidade e disponibilidade garantida para treinar modelos massivos durante meses sem risco de parada.  Cenário 2 — Processamento em Lote com Foco em Custo Mínimo:Escolha: Instâncias Spot (Spot VMs).Motivo: Prioriza o menor custo financeiro possível em uma carga de trabalho que consegue reiniciar facilmente se houver interrupção.

1 .

Which of the following Google Cloud deployment options offers the least amount of direct control over the underlying infrastructure?
Direct management using Google Compute Engine (GCE)
check
Fully managed approach using Vertex AI
Foundational approach using Google Kubernetes Engine (GKE)
Open frameworks integrated via Toolkits (e.g., Cluster Toolkit)
Correct. The fully managed approach using Vertex AI handles the majority of the underlying infrastructure and operational complexity, offering users the least amount of direct control as they interact with higher-level APIs and services.
check
2 .

What are the key components and characteristics of the performance-optimized, purpose-built infrastructure layer within an application on AI Hypercomputer?
Primarily software tools for model development and deployment.
Focuses on user interface design and workflow management.
Mainly deals with cost optimization and resource allocation strategies.
check
Encompasses AI accelerators (like TPUs and GPUs), high-bandwidth networking, and optimized storage solutions for demanding AI tasks.
Correct. This option accurately describes the key components of the performance-optimized, purpose-built infrastructure layer, emphasizing the hardware and fundamental systems designed for AI workloads.
check
3 .

Beyond raw hardware performance, how does the open software layer within the AI Hypercomputer system (e.g. GKE, optimized ML frameworks, Kueue) contribute to enhancing productivity in the AI lifecycle?
By directly reducing the cost of TPU and GPU instances.
By automating hardware manufacturing and supply chain.
By offering physical colocation services for AI hardware.
check
By simplifying workload orchestration, providing efficient resource management, and enabling faster iteration with familiar ML tools.
Correct. This accurately describes how the open software layer (through components like GKE, optimized frameworks, and Kueue) enhances productivity by making it easier to manage and utilize the hardware for AI tasks.
check
4 .

What best describes the system-based architecture philosophy of Google Cloud's AI Hypercomputer?
check
Designing and optimizing hardware, software, ML frameworks, and consumption models as an integrated, collaborative whole for AI workloads.
Focusing solely on providing managed services to abstract all infrastructure complexity.
Assembling the most powerful individual hardware components available on the market.
Prioritizing open-source software exclusively, regardless of hardware integration.
Correct. This accurately captures the essence of a system-based architecture, the deliberate design and tuning of all layers to function optimally together for AI and ML tasks.
