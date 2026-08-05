# Redes para Tipos de Máquina com GPU

Vamos analisar detalhadamente as arquiteturas de rede das instâncias de GPU otimizadas para aceleradores do Google Compute Engine (GCE), focando em como você pode aproveitar suas larguras de banda massivas. Para esta lição, focaremos nas arquiteturas A4X e A4/A3 Ultra como um exemplo chave dessas redes de alto desempenho.

## Arquiteturas A4X e A4/A3 Ultra

Os tipos de máquina **A4 (GPUs NVIDIA B200)** e **A3 Ultra (GPUs NVIDIA H200)** são projetados para maximizar a velocidade de comunicação para cargas de trabalho distribuídas em larga escala. Eles atingem **3.200 Gbps** via RoCE e **400 Gbps** via 2 placas de rede gVNIC de 200 Gbps, agregando uma largura de banda máxima de **3.600 Gbps**.

A arquitetura se divide em dois componentes principais:

### 1. Comunicação de GPU de alta velocidade (CX-7)
O núcleo do desempenho da máquina depende das oito placas de rede (NICs) NVIDIA CX-7.

*   **Velocidade dedicada:** Estas oito NICs CX-7 entregam coletivamente uma impressionante largura de banda total de rede de 3.200 Gbps.
*   **Uso exclusivo:** Esta imensa largura de banda é dedicada exclusivamente à comunicação GPU-para-GPU de alta velocidade e baixa latência — algo essencial para cargas de trabalho de treinamento distribuído.
*   **Sem tráfego geral:** De forma crítica, as NICs CX-7 *não podem* ser usadas para necessidades gerais de rede, como acesso à internet pública ou acesso a armazenamento externo.
*   **Otimização NUMA (Non-Uniform Memory Access):** Cada NIC CX-7 é alinhada fisicamente com uma GPU específica. Este design otimiza o NUMA, garantindo o caminho mais curto e rápido possível entre o hardware de rede e a GPU que ele atende.
*   **Link de GPU "All-to-all" (Todos-para-todos):** Todas as oito GPUs dentro da máquina podem se comunicar rapidamente entre si usando a ponte NVLink, que as conecta em uma topologia onde todas se falam diretamente para uma troca de dados interna com desempenho máximo.

### 2. Rede de uso geral (gVNIC)
Esta arquitetura de sistema duplo ("Dual Plane") garante que o pipeline crítico e de alto volume da comunicação entre as GPUs permaneça desobstruído. Ao mesmo tempo, o sistema fornece uma conectividade robusta por meio das **2 gVNICs (400 Gbps)** para todas as outras necessidades do sistema (como tráfego de host, armazenamento e internet).

---
*Para mais detalhes sobre outros tipos de máquinas com GPU e suas arquiteturas de rede, consulte o curso Cloud GPUs.*
*Para mais detalhes sobre os tipos de máquina TPU e suas arquiteturas de rede, consulte o curso Cloud TPUs.*
---

## Realizando uma configuração e teste simples do NCCL em GPUs no GCE

Frameworks modernos de machine learning frequentemente usam a **NVIDIA Collective Communications Library (NCCL)** para realizar tarefas essenciais de comunicação entre GPUs.

A **NCCL** (pronuncia-se "Nickel") é uma biblioteca especializada que fornece rotinas altamente otimizadas para comunicação entre GPUs. Ela suporta um conjunto abrangente de operações coletivas, como *all-reduce*, *all-gather* e *broadcast*, bem como padrões de envio/recebimento ponto a ponto (*point-to-point*).

A NCCL é projetada para entregar a largura de banda máxima em várias plataformas. Ela aproveita as interconexões de alta velocidade dentro de um único nó (como PCIe, NVLink e NVswitch) e entre vários nós (usando InfiniBand ou TCP/IP). Isso permite que ela escale eficientemente para um número arbitrário de GPUs, tanto em aplicações de processo único quanto em aplicações multiprocesso (por exemplo, baseadas em MPI).

---

## Teste seus conhecimentos

Vamos testar sua compreensão sobre essa arquitetura de rede:

**Pergunta:** Você está configurando uma VM A3 Ultra para um trabalho de treinamento distribuído em larga escala. Você precisa planejar a ingestão de dados do Cloud Storage. Qual interface de rede lida com esse tráfego e qual é sua capacidade máxima?

*   [ ] As 8 NICs NVIDIA ConnectX-7 (3.200 Gbps)
*   [ ] A Ponte NVLink (Velocidade interna)
*   **[X] As 2 Google gVNICs (400 Gbps) -> RESPOSTA CORRETA**
*   [ ] A largura de banda agregada combinada de todas as NICs (3.600 Gbps)

> **Justificativa:** Como visto na seção sobre CX-7 e gVNIC, o tráfego de armazenamento externo e dados em geral (como acesso ao Cloud Storage para ingestão de dados de treinamento) é roteado pela rede de uso geral (as 2 placas gVNIC, somando 400 Gbps). As placas CX-7 são restritas exclusivamente à comunicação entre as GPUs (GPU-to-GPU).
