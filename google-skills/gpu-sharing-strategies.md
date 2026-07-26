# GPU sharing strategies

Como especialistas em infraestrutura, vocês sabem que as GPUs são recursos valiosos e de alto custo. A chave para alcançar escalabilidade e eficiência de custos em aprendizado de máquina e computação de alto desempenho é maximizar a utilização do seu hardware. Vamos explorar como fazer isso no Google Kubernetes Engine (GKE) utilizando estratégias inteligentes de compartilhamento de GPU. Nosso objetivo é garantir que suas cargas de trabalho operem sempre com eficiência, evitando a subutilização de hardware dispendioso.

## Otimizando a utilização de GPU

Por padrão, quando um Pod do Kubernetes solicita uma GPU, a GPU física inteira é alocada para esse Pod, mesmo que o contêiner precise apenas de uma fração dos recursos da GPU. Isso é determinado pela forma como o agendador do Kubernetes gerencia o tipo de recurso `nvidia.com/gpu`.

Esse comportamento é ineficiente e pode ser custoso, especialmente para cargas de trabalho que não exigem a capacidade total de uma GPU. Exemplos incluem:

- Tarefas de inferência de IA rápidas e iterativas.
- Atendimento a solicitações de inferência de baixo volume.
- Ambientes de prototipagem e desenvolvimento.

As estratégias de compartilhamento de GPU visam solucionar essa ineficiência permitindo que múltiplos contêineres utilizem uma única GPU de forma eficiente, melhorando assim a utilização de recursos e reduzindo custos.

Se um único Pod estiver utilizando apenas 10% da capacidade de processamento de uma GPU, qual é a utilização da memória da GPU?

Resposta:

Por padrão, a memória da GPU também é totalmente alocada para esse Pod, resultando em um desperdício significativo de capacidade de memória.

As três estratégias principais

O GKE oferece três estratégias distintas para o compartilhamento de GPUs, cada uma com características e casos de uso específicos.

<img width="1921" height="841" alt="GPU sharing strategies@2x" src="https://github.com/user-attachments/assets/d69fa4e1-170e-4985-b72b-a3e0c409be23" />

Multi-instance GPU (MIG)
O MIG é um recurso em nível de hardware (disponível em GPUs NVIDIA compatíveis, como a A100). Ele divide uma única GPU física em até sete fatias isoladas. Cada fatia funciona como uma GPU distinta, com seus próprios recursos dedicados de computação, memória e largura de banda.

Isolamento: Cada partição é isolada em nível de hardware, evitando a disputa por recursos entre cargas de trabalho. Uma carga de trabalho em execução em uma partição não afetará o desempenho de outra carga de trabalho em uma partição diferente. Isso proporciona uma qualidade de serviço (QoS) consistente e previsível.

Ideal para cargas de trabalho que exigem desempenho garantido e isolamento, como consultas paralelas de inferência de IA, nas quais a latência e a vazão (throughput) são críticas.
---
Compartilhamento de tempo da GPU (GPU time-sharing)
O compartilhamento de tempo da GPU é uma estratégia em nível de software que utiliza os recursos nativos de compartilhamento de tempo das GPUs NVIDIA (a partir da arquitetura Pascal). O escalonador da GPU realiza trocas rápidas de contexto entre múltiplos processos (seus Pods) em execução na mesma GPU física.

Isolamento: O isolamento ocorre em nível de software. Embora cada processo possua seu próprio espaço de endereçamento, não há uma imposição rígida de limites de recursos. O desempenho pode ser afetado por "vizinhos barulhentos" (noisy neighbors), já que uma carga de trabalho pode saturar os recursos compartilhados e impactar as outras.

Ideal para cargas de trabalho interativas com períodos de ociosidade, como prototipagem, notebooks interativos ou pequenas tarefas, nas quais flutuações ocasionais de desempenho são aceitáveis ​​em prol de uma melhor utilização dos recursos.
---
NVIDIA MPS

O NVIDIA MPS é uma implementação alternativa da API CUDA que permite a execução simultânea de múltiplos processos CUDA em uma única GPU. Ele foi projetado para maximizar a vazão e o uso simultâneo da GPU.

Isolamento: Assim como no compartilhamento de tempo, o isolamento ocorre em nível de software e oferece isolamento limitado de recursos (por exemplo, controle sobre threads ativas e memória de dispositivo fixada/pinned).

Ideal para processamento em lote (batch) e cargas de trabalho cooperativas de múltiplos processos, como tarefas MPI, onde o objetivo é saturar totalmente a GPU com trabalho proveniente de múltiplos processos menores executados simultaneamente.
---
Aqui está um resumo rápido para ajudar você a decidir qual estratégia é a melhor para sua carga de trabalho.

Recurso

MIG

Compartilhamento de tempo (Time-sharing)

NVIDIA MPS

Nível de isolamento

Hardware (melhor)

Software (bom)

Software (bom)

Melhor carga de trabalho

Inferência, aplicações sensíveis a QoS

Cargas intermitentes ou interativas, desenvolvimento/teste

Tarefas em lote (batch), cargas de trabalho cooperativas

Desempenho

Previsível, consistente

Pode variar (devido à contenção)

Alta vazão, bom para concorrência

Para obter eficiência máxima, você pode combinar o MIG com *time-sharing* ou MPS. Por exemplo, é possível criar uma partição MIG e executar vários contêineres menores dentro dela, compartilhando seus recursos por meio de uma das estratégias baseadas em software. Isso proporciona isolamento em nível de hardware para a partição, ao mesmo tempo que permite uma utilização eficiente de múltiplos processos em seu interior.

O *cluster autoscaler* e o provisionamento automático de nós do GKE provisionarão, então, os *node pools* adequados para atender aos requisitos da sua carga de trabalho.

Dica: Solicitar mais de uma GPU (`nvidia.com/gpu: 2`) com *time-sharing* ou MPS fará com que o GKE rejeite seu Pod. Isso ocorre porque essas estratégias foram projetadas para compartilhar uma única GPU física entre várias cargas de trabalho.

Demonstração: Particionamento de GPUs para uso no GKE

Vamos colocar o conceito em prática por meio de uma demonstração rápida.

Principais pontos

Agora você tem uma compreensão sólida das três principais estratégias de compartilhamento de GPU no GKE:

MIG: Oferece isolamento de hardware e desempenho previsível.

Time-Sharing: Ideal para cargas de trabalho interativas e com picos de demanda.

NVIDIA MPS: Ideal para trabalhos em lote (batch jobs) cooperativos e de alto throughput.

O próximo passo é explorar como implementar essas estratégias em seus próprios clusters GKE.

check
1 . Um engenheiro está implementando um cluster GKE para executar um trabalho de treinamento em vários hosts para um modelo grande. Esse trabalho exige que as GPUs se comuniquem entre si com latência mínima. Qual recurso do Cluster Director é crucial para otimizar essa comunicação?

Colocação densa com uma estrutura de rede de aprendizado de máquina dinâmica.


2 . Um novo serviço de IA para um chatbot em tempo real está apresentando tempos de resposta inconsistentes, especialmente durante picos de tráfego. A equipe de desenvolvimento suspeita que vários modelos implantados no mesmo cluster estejam competindo por recursos de GPU. Qual ferramenta do GKE foi projetada para rotear solicitações de forma inteligente para a instância do modelo menos carregada e melhorar o desempenho?

Portal de Inferência GKE


3 . Uma empresa está usando um cluster GKE Standard e deseja garantir que um novo pool de nós com GPUs de camada 4 seja criado automaticamente se um Pod solicitar recursos que não estejam disponíveis no momento. Qual recurso do GKE deve ser habilitado para atingir esse objetivo?

Provisionamento automático de nós (NAP)
