# Redes de hipercomputadores de IA para cargas de trabalho de ML

A base para implantar e gerenciar suas cargas de trabalho com eficiência é um sólido entendimento dos serviços de rede de máquinas com GPUs. Um objetivo crucial na configuração da sua rede é maximizar o bom desempenho, ou "taxa de transferência efetiva".

- O bom desempenho mede o progresso efetivo que um sistema faz em uma tarefa de treinamento de aprendizado de máquina.

- Ele oferece insights mais precisos sobre o desempenho do que a taxa de transferência de dados bruta, pois rastreia especificamente quanto tempo as GPUs gastam computando em comparação com o tempo gasto esperando por dados.

## A arquitetura de rede hierárquica de GPUs

Famílias recentes de VMs com GPU (incluindo A3-ultra, A4-high e A4X) utilizam uma arquitetura de rede hierárquica e alinhada por trilhos (*rail-aligned*) para oferecer conectividade previsível e de alto desempenho. Esse design minimiza a sobrecarga de comunicação, permitindo que as GPUs se concentrem na computação e, assim, melhorem o *goodput*. Essa estrutura alinhada por trilhos consiste em três componentes principais:

<img width="1921" height="644" alt="Blocks" src="https://github.com/user-attachments/assets/b290dfc2-87c6-4ca1-9dcb-c628fb156785" />

1. Sub-blocos
Os sub-blocos constituem a unidade fundamental: um grupo de hosts fisicamente alocados em um único rack. Eles proporcionam comunicação extremamente eficiente de salto único (*single-hop*) entre quaisquer duas GPUs dentro do rack, viabilizada por um switch *top-of-rack* (ToR). Os hosts de um sub-bloco utilizam RDMA (*Remote Direct Memory Access*) sobre Ethernet Convergente (RoCE) para comunicação direta.

2. Blocos
Os blocos são compostos por múltiplos sub-blocos interconectados. Qualquer GPU dentro de um bloco pode ser acessada em, no máximo, dois saltos de rede por meio de uma *fabric* sem bloqueio (*non-blocking fabric*), proporcionando uma interconexão de alta largura de banda.

3. Clusters
Os clusters são formados por múltiplos blocos interconectados, escalando para milhares de GPUs para treinamento em larga escala. A comunicação entre blocos adiciona apenas um salto extra, mantendo alto desempenho e previsibilidade em escala massiva.

* Os metadados de sub-blocos, blocos e clusters são disponibilizados aos seus orquestradores (como Slurm ou Google Kubernetes Engine [GKE]) para permitir a alocação ideal de jobs em toda a hierarquia.

## Arquitetura de rede de GPU de alto desempenho

Para garantir que o tráfego crítico entre GPUs não sofra atrasos devido ao tráfego geral do sistema, todos os tipos de máquinas com GPU utilizam uma arquitetura de plano de rede duplo.

<img width="1920" height="1014" alt="HIgh level" src="https://github.com/user-attachments/assets/50061336-3eec-4b24-9cae-3738ba9f6726" />

- Tráfego de uso geral: Um caminho de rede separado gerencia todo o tráfego que não seja comunicação direta entre GPUs. As NICs Google Titanium lidam com todo o tráfego do host e de armazenamento, incluindo acesso ao Cloud Storage, gerenciamento em nível de host e comunicação com serviços do Google Cloud.

- Comunicação dedicada entre GPUs: Isso é viabilizado por tecnologias que oferecem tráfego de alto desempenho e baixa latência, essencial para cargas de trabalho de ML. Essas tecnologias incluem RDMA over Converged Ethernet (RoCE), NICs da NVIDIA e a topologia de rede "rail-aligned" do Google, implementada em todo o data center.

## Tecnologias de comunicação GPU-para-GPU

A comunicação de alto desempenho e baixa latência entre GPUs é viabilizada por uma combinação de tecnologias especializadas. Vamos explorar cada uma dessas tecnologias e suas finalidades.

<img width="1920" height="794" alt="Network architecture for A4X" src="https://github.com/user-attachments/assets/8cdba76c-640b-4bc1-b1e4-ba9a31e1c843" />

1. Google Titanium NIC
As placas de rede (NICs) Titanium são fundamentais: elas descarregam tarefas de processamento de rede da CPU, liberando-a para focar inteiramente em suas cargas de trabalho de ML. Essa separação em interfaces físicas distintas evita que o tráfego dispute recursos.

2. NVIDIA NVSwitch
Desenvolvido pela NVIDIA, o NVSwitch atua como uma *fabric* de alto desempenho que complementa o NVLink. Seu objetivo é gerenciar e viabilizar, de forma eficiente, a comunicação entre múltiplas GPUs dentro de um servidor ou de um cluster completo.

Ao utilizar o A4X, o NVSwitch permite a comunicação entre 72 chips — organizados em quatro chips por nó em um único rack — que compartilham o mesmo NVSwitch.

3. NVIDIA NVLink
Cria caminhos de dados diretos e de altíssima velocidade entre as GPUs NVIDIA na mesma máquina.

- RoCE
Com os tipos de máquina A3, A4 e A4X, o RDMA permite uma comunicação RDMA eficiente entre GPUs localizadas em máquinas diferentes.


- Topologia "rail-aligned" do Google
A estrutura de rede física de todo o data center, otimizada para transferência de dados previsível e de baixa latência.


- NCCL aprimorado
Otimizado para a topologia "rail-aligned" do Google, visando realizar operações de comunicação coletiva (como *all-reduce*) de forma eficiente em ambientes com múltiplas GPUs e múltiplos nós.

## Entendendo as pilhas de rede de GPU

A pilha de rede é o conjunto de protocolos de software e drivers que implementa a comunicação entre GPUs. A pilha específica utilizada é determinada pelo tipo de máquina com GPU escolhido:

# Pilha de Rede de GPU no Google Cloud

| Pilha de Rede (*Networking stack*) | Descrição | Tipos de Máquinas com GPU Suportados |
| :--- | :--- | :--- |
| **GPUDirect RDMA** | Cria um caminho direto para troca de dados entre uma GPU e um dispositivo par (como outra GPU ou uma placa NIC), ignorando a CPU. Utiliza RoCE para ler/escrever diretamente na memória da GPU. | A4X (com GB200), A4 (com B200) e A3 Ultra (com H200) |
| **GPUDirect-TCPXO** | Melhora o GPUDirect-TCPX fazendo o *offload* (descarregamento) do protocolo TCP para a interface de rede, dobrando a largura de banda de rede em relação ao GPUDirect-TCPX. | A3 Mega (com H100) |
| **GPUDirect-TCPX** | Aumenta o desempenho da rede permitindo que os *payloads* dos pacotes de dados sejam transferidos diretamente da memória da GPU para a interface de rede. | A3 High (com H100) |


# Requisito de Ambiente Multi-VPC para Máquinas Aceleradoras

Um ambiente com múltiplas VPCs (Multi-VPC) é necessário para que máquinas aceleradoras de alto desempenho suportem esse design de hardware especializado. A configuração específica depende do tipo de máquina com GPU e de sua pilha de rede (*networking stack*):

---

## 1. A4X, A4 e A3 Ultra (GPUDirect RDMA)

Requer **três VPCs**:

* **VPC Padrão (*Default VPC*)**
* **Uma VPC adicional** para tráfego do host (*host traffic*)
* **Uma VPC Compartilhada (*Shared VPC*)** para todo o tráfego de GPU para GPU (com o perfil de rede RDMA ativado)

---

## 2. A3 Mega (GPUDirect-TCPXO)

* Requer **oito VPCs separadas** dedicadas às placas de rede (NICs) da GPU para comunicação de alta largura de banda.

---

## 3. A3 High (GPUDirect-TCPX)

* Requer **quatro VPCs separadas** dedicadas às placas de rede (NICs) da GPU para comunicação de alta largura de banda.

## Bibliotecas e componentes de rede

Para maximizar a largura de banda da rede e aproveitar a pilha de rede do Google, você trabalhará com estes componentes principais:

- Google Virtual NIC (gVNIC)
A interface de rede virtual recomendada para o Compute Engine. Ela melhora o desempenho e a consistência, além de reduzir problemas de "vizinho barulhento" (noisy neighbor) na comunicação entre hosts.

- NCCL
Fornece primitivas otimizadas para operações de comunicação coletiva, projetadas para ambientes com múltiplas GPUs e múltiplos nós. Essencial para verificar o desempenho do cluster.

- Multirrede no GKE
Suporte a múltiplas interfaces de rede em nós e pods de um cluster GKE, permitindo o uso do plano de rede especializado para GPU em ambientes conteinerizados.

## Ponto-chave

Em resumo, aqui estão alguns componentes e configurações de rede fundamentais que você pode considerar para garantir que sua arquitetura esteja totalmente otimizada para o treinamento de ML de alto desempenho.

# Conceitos-Chave de Rede para IA no Google Cloud

| Conceito (*Concept*) | Principais Pontos de Aprendizado (*Key takeaways*) |
| :--- | :--- |
| **Goodput** | A métrica para o sucesso do treinamento de ML; a rede deve ser otimizada para isso. |
| **Arquitetura (*Architecture*)** | Um design **hierárquico e alinhado por trilhos (*rail-aligned*)** garante uma comunicação previsível e de baixa latência. |
| **Plano Duplo (*Dual plane*)** | O tráfego é separado: tráfego **GPU para GPU** versus tráfego de **Host/Armazenamento (*Storage*)**. |
| **Hardware** | **Placas NIC Titanium** lidam com o tráfego do host; **NVSwitch**, **NVLink** e **RoCE** lidam com o tráfego de alta velocidade GPU para GPU. |
| **VPCs** | Uma configuração **multi-VPC** é necessária para acomodar o hardware de rede especializado. |

