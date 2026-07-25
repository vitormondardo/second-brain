# Containerizing an AI application

<img width="1654" height="689" alt="T-KUBGKE- I _ Full Slide Deck _ Getting Started with Google Kubernetes Engine _ v2 0 8" src="https://github.com/user-attachments/assets/0add6f62-40b1-44a7-807a-b6d810d72936" />
Colocar um modelo em produção é um processo complexo. A maneira mais eficaz de lidar com os desafios de reprodutibilidade e implantação é por meio da conteinerização. Pense em um container como um pacote leve e autônomo para sua aplicação. Ele reúne tudo o que seu código precisa para ser executado — como bibliotecas, frameworks e arquivos de configuração — em um único executável isolado. Isso garante que sua aplicação de IA funcione da mesma maneira, independentemente de onde for implantada, seja no seu laptop ou na nuvem.

## Modos de operação do GKE

O GKE é um componente fundamental da plataforma AI Hypercomputer. Ele oferece orquestração, automação e recursos específicos necessários para executar cargas de trabalho de IA/ML em escala. Sua primeira grande decisão é escolher um modo do GKE: Standard ou Autopilot. Trata-se de uma escolha entre um ambiente de alto controle, gerenciado pelo próprio usuário (DIY), e uma experiência totalmente gerenciada, que dispensa intervenção manual.

<img width="1920" height="738" alt="Modes of operations - Google Kubernetes Engine (GKE)" src="https://github.com/user-attachments/assets/e7c7249f-6dd7-47b2-90ff-cc7644b06740" />

### Modo Padrão

Este modo oferece controle granular sobre todos os aspectos do seu cluster e dos nós. Você é responsável pelo gerenciamento de pools de nós, pelo escalonamento e pela manutenção.

### Modo Autopilot (Recomendado)

Este é o modo serverless do GKE. O Google gerencia toda a infraestrutura subjacente, incluindo o provisionamento de nós, o escalonamento automático e as atualizações. Você paga apenas pelos recursos que seus Pods realmente utilizam.

## Arquitetura do GKE

Aqui está um diagrama de arquitetura de referência para uma plataforma de aprendizado de máquina criada no Google Kubernetes Engine (GKE). Ele mostra como o GKE serve como uma base robusta, permitindo que várias equipes criem, treinem e implantem modelos de IA de forma eficiente, utilizando uma ampla gama de ferramentas e frameworks padrão do setor.

<img width="1920" height="952" alt="Build Train Deploy" src="https://github.com/user-attachments/assets/3ab06b85-1eb7-49be-93e7-b048b6e911dc" />

### Escalonamento automático, posicionamento e provisionamento
- O GKE escala recursos automaticamente, aloca cargas de trabalho no hardware mais adequado e provisiona novos nós conforme necessário.

- Recursos como o provisionamento automático de nós (NAP) e as classes de computação personalizadas (CCC) garantem que você tenha os recursos certos no momento certo. Por exemplo, as CCC permitem definir uma lógica de fallback (como agendar a execução em uma GPU A100 de alto desempenho, mas recorrer a uma L4 menos potente caso a A100 não esteja disponível).

- O *multislicing* é um recurso fundamental para o treinamento distribuído; ele permite que uma única tarefa de treinamento utilize um bloco grande e contíguo de TPUs ou GPUs fisicamente interconectadas por uma rede de alta velocidade.

### Agendador de Cargas de Trabalho Dinâmicas (Kueue)
Um sistema de enfileiramento de jobs nativo do Kubernetes que permite a múltiplas equipes compartilhar um grande cluster de IA de forma justa, gerenciando cotas, prioridades e a alocação de recursos.

### Frameworks de computação distribuída
Frameworks como Ray e NCCL são essenciais para distribuir tarefas de aprendizado de máquina em larga escala entre múltiplas máquinas.

### Fluxo de trabalho e processamento de dados
Ferramentas como Kubeflow, Spark e Airflow são utilizadas para gerenciar pipelines de dados e orquestrar fluxos de trabalho complexos de IA/ML.

### Ferramentas e bibliotecas
A camada superior inclui ferramentas e bibliotecas de código aberto populares utilizadas para criar, treinar e implantar modelos, como Jupyter, TensorFlow, PyTorch e Dask.

Na parte inferior do diagrama, a camada do GKE representa a infraestrutura central, fornecendo recursos computacionais fundamentais (processamento, GPU, TPU, armazenamento, rede) e funcionalidades-chave que otimizam o desempenho. As camadas acima do GKE representam o software e os frameworks utilizados pelas equipes de ciência de dados e engenharia.

## Imagens de nó do GKE

Ao implantar um cluster no Google Kubernetes Engine (GKE), o GKE cria seus nós de trabalho usando uma imagem de nó específica. Essas imagens são, essencialmente, os sistemas operacionais dos seus nós e são fundamentais para dar suporte a diferentes cargas de trabalho. O GKE oferece imagens de nó baseadas em sistemas operacionais populares, como Container-Optimized OS (COS), Ubuntu e Windows Server.

Para cargas de trabalho de IA e aprendizado de máquina (ML) de alto desempenho, especialmente ao executar clusters do GKE Autopilot, você deve usar a imagem de nó Container-Optimized OS com containerd (cos_containerd).

Essa imagem específica é a escolha recomendada — e muitas vezes obrigatória — porque inclui:

• Containerd: Um runtime de contêiner leve e padrão do setor, mais eficiente do que runtimes mais antigos.

• Otimizações de IA/ML: A imagem é ajustada especificamente para oferecer suporte ao hardware subjacente (como GPUs) e à rede de alta velocidade necessária para tarefas exigentes de IA/ML.

O uso dessa imagem otimizada garante que seus nós do GKE Autopilot estejam prontos para executar com eficiência suas aplicações intensivas de IA e ML.

Precisa de mais detalhes? Para uma comparação completa de todas as imagens de nó disponíveis e seus recursos, consulte a documentação do GKE sobre imagens de nó (abre em uma nova guia).
Uma equipe de ciência de dados precisa implantar um modelo de IA conteinerizado e requer um ambiente totalmente gerenciado que não exija intervenção manual. A equipe quer que o Google cuide do escalonamento e da manutenção dos nós, pagando apenas pelos recursos de computação que os Pods da aplicação realmente consomem.

Qual modo de operação do GKE é a melhor recomendação para essa equipe?

- Autopilot

---
# Redes para implantações de clusters GKE

<img width="1145" height="443" alt="networkConfig" src="https://github.com/user-attachments/assets/56aa6dc2-e69d-4dd4-b9ad-d08302780c53" />

Ao executar cargas de trabalho de IA/ML exigentes no Google Kubernetes Engine (GKE), especialmente aquelas que utilizam aceleradores especializados, a configuração de rede é crucial para o desempenho. Você tem duas abordagens principais: usar a configuração padrão fornecida pelo Cluster Toolkit ou definir uma configuração personalizada.

## Abordagem 1: Configuração de rede padrão (blueprint do Cluster Toolkit)

Ao implantar um cluster GKE otimizado para IA usando o blueprint do Cluster Toolkit (para um tipo de máquina como o A4), a rede é configurada automaticamente para desempenho e isolamento ideais.

O blueprint implementa um ambiente com múltiplas VPCs para garantir o isolamento da rede e a comunicação de alta velocidade:

## VPC 1:
VPC padrão
Usada para os componentes principais do cluster GKE.

## VPC 2:
Segunda NIC do host
Uma VPC separada para uma segunda placa de interface de rede (NIC) do host, isolando ainda mais o tráfego geral.

## VPC 3:
Tráfego RDMA entre GPUs

Uma VPC dedicada para RDMA (Remote Direct Memory Access), que permite comunicação extremamente rápida e de baixa latência diretamente entre GPUs em nós diferentes, utilizando o protocolo GPUDirect RDMA.

O blueprint gerencia automaticamente várias configurações detalhadas:

Selecione cada botão para expandir os quatro itens e saber mais.


### Endereçamento IP
Define o espaço de endereços IP privados para os nós do GKE e configura intervalos de IP secundários para seus Pods e Services. Utiliza o recurso de alias de endereços IP para evitar conflitos de IP.


### Perfil otimizado para RDMA
Aplica um perfil de rede gerenciado pelo Google à VPC de RDMA, ajustando-o automaticamente para atender às necessidades de baixa latência e alta velocidade do RDMA.


### Sub-redes automatizadas
Para obter o desempenho máximo de RDMA, o blueprint cria automaticamente oito sub-redes dedicadas dentro da VPC de RDMA — uma para cada uma das oito placas de rede (NICs) RDMA em uma VM com acelerador.


### Regras de firewall
Configura regras de firewall para:

Permitir tráfego TCP, UDP e ICMP irrestrito entre os nós do cluster.

Proteger o plano de controle do GKE, limitando o acesso a um intervalo CIDR (Classless Inter-Domain Routing) autorizado.

## Abordagem 2: Configuração de rede personalizada

Se a sua carga de trabalho exigir mais controle do que o blueprint padrão oferece, você pode configurar manualmente os objetos de rede para o seu cluster GKE otimizado para IA. A configuração dependerá do tipo de carga de trabalho:

Selecione cada botão para expandir os dois itens e saber mais.


### Cargas de trabalho não distribuídas
Objetivo: Executar tarefas de IA/ML que não exigem comunicação de alta velocidade entre nós (por exemplo, treinamento em nó único, inferência).

Rede: Crie um cluster GKE sem o GPUDirect RDMA ativado. Essa configuração utiliza uma rede VPC única e mais simples para toda a comunicação.


### Cargas de trabalho distribuídas
Objetivo: Executar tarefas de treinamento em larga escala que exigem desempenho ideal em escala (por exemplo, treinamento distribuído).

Rede: Crie um cluster GKE com o GPUDirect RDMA ativado. Isso é essencial para o escalonamento com baixa latência e requer um ambiente com múltiplas VPCs para separar o tráfego geral do caminho de comunicação GPU-a-GPU de alta largura de banda.

Para obter instruções passo a passo sobre como criar um cluster GKE personalizado e otimizado para IA para ambos os cenários, consulte a documentação sobre a criação de um cluster GKE personalizado e otimizado para IA.

## Demonstração: Disponibilizando um modelo de IA generativa de código aberto com o GKE

Se você já fez o curso de GPUs na nuvem (Cloud GPUs), esta demonstração pode lhe parecer familiar. Caso contrário, ela mostrará como o Google Kubernetes Engine (GKE) pode ser utilizado para disponibilizar um modelo de IA generativa de código aberto. Você verá como o GKE é fundamental para simplificar o processo complexo de implantação e disponibilização desses modelos de grande porte.

Um arquiteto de nuvem está configurando um cluster GKE para treinamento de IA distribuído em larga escala. Para obter o máximo desempenho e escalabilidade de baixa latência, o arquiteto deve isolar o caminho de comunicação de alta velocidade entre os aceleradores em nós diferentes.

Qual configuração de rede é essencial para habilitar essa comunicação dedicada de baixa latência entre GPUs?

- Uma VPC dedicada com GPUDirect RDMA habilitado.

Isso mesmo. Para cargas de trabalho distribuídas, uma VPC dedicada com GPUDirect RDMA habilitado é a configuração crucial que cria o caminho de comunicação de baixa latência e alta velocidade diretamente entre os aceleradores.
---
# Otimizando uma carga de trabalho de IA
Uma única GPU, por mais poderosa que seja, simplesmente não é suficiente. O processo de treinamento pode levar semanas ou até meses. A solução é o treinamento distribuído, um método para acelerar o tempo de treinamento ao distribuir a carga de trabalho entre vários nós de um cluster.

Imagine a construção de um arranha-céu gigantesco. Você poderia ter um único arquiteto altamente qualificado trabalhando em todo o projeto, mas isso levaria muito tempo. O treinamento distribuído é como mobilizar um exército de arquitetos, engenheiros e operários, cada um focando em uma parte diferente da edificação para concluí-la muito mais rapidamente.

## Estratégias de treinamento distribuído

O treinamento distribuído é um termo amplo, mas baseia-se principalmente em duas estratégias fundamentais: paralelismo de dados e paralelismo de modelo. Essas duas estratégias também podem ser combinadas para lidar com as cargas de trabalho mais massivas.

<img width="1669" height="890" alt="Distributed training strategies" src="https://github.com/user-attachments/assets/315a93ab-de10-4ae3-9bd2-d14b87532c7a" />


