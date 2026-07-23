# Opções de provisionamento de clusters de GPU
O Google Cloud oferece várias plataformas para o provisionamento de clusters acelerados pore controle e necessidades de escalabilidade. Nesta aula, você explorará cinco das plataformas mais utilizadas e aprenderá a adequá-las aos requisitos das suas cargas de trabalho de IA e ML.

 <img width="1090" height="498" alt="cluster provisioning" src="https://github.com/user-attachments/assets/076eaaa3-aff9-4d1d-b2b3-50912671805f" />

## Agent Platform: A plataforma de ML gerenciada
A Agent Platform é uma plataforma abrangente e totalmente gerenciada, projetada para criar, treinar e implantar modelos de aprendizado de máquina e aplicações de IA. Na Agent Platform, VMs equipadas com GPU podem aumentar significativamente o desempenho de várias maneiras:

### Treinamento personalizado acelerado
Utilize VMs com suporte a GPU em ambientes de treinamento personalizados, aproveitando os pools de nós de trabalho do Google Kubernetes Engine (GKE) para computação escalável.

### Modelos fundamentais integrados
Utilize de forma integrada grandes modelos de linguagem (LLMs) abertos e outros modelos de base diretamente do Model Garden da Agent Platform.

### Latência de previsão reduzida
Aumente a velocidade de geração de previsões com seus modelos treinados, resultando em inferência em tempo real mais rápida.

### Ambientes de desenvolvimento aprimorados
Aumente a velocidade de execução do código em notebooks do Agent Platform Workbench e em ambientes de execução do Colab Enterprise, acelerando a experimentação e o desenvolvimento.

---
# Frameworks de aceleração

Em aprendizado de máquina, alcançar o desempenho ideal é crucial, seja para um único acelerador de alto desempenho ou para uma implementação em uma combinação heterogênea de dispositivos.

Nesta lição, você explorará como as principais estruturas de aprendizado de máquina, especificamente PyTorch e JAX, utilizam tecnologias especializadas como kernels CUDA, compiladores XLA e suas respectivas integrações XLA (PyTorch/XLA, JAX/XLA) para superar esse desafio.

## Maximizando o desempenho em hardware específico

Ao trabalhar com um dispositivo de computação de alto desempenho específico, como uma GPU, o objetivo principal é aproveitar ao máximo seus pontos fortes arquitetônicos exclusivos. Isso envolve minimizar ciclos ociosos, maximizar a vazão computacional e otimizar a movimentação de dados. Vamos explorar os frameworks de ML que desbloqueiam esse potencial de eficiência.

Frameworks como TensorFlow, PyTorch e CUDA abstraíram grande parte da complexidade envolvida na programação de GPUs, permitindo que pesquisadores e desenvolvedores de IA se concentrem na criação e no treinamento de modelos, em vez de gerenciarem as complexidades do hardware.

### CUDA Kernels
CUDA (Compute Unified Device Architecture) é a plataforma de computação paralela e o modelo de programação da NVIDIA. Trata-se da interface de programação de nível mais baixo para GPUs da NVIDIA. Quando um framework de aprendizado profundo (deep learning) realiza uma operação como multiplicação de matrizes (torch.matmul) ou convolução, ele não executa uma instrução genérica de CPU; em vez disso, ele envia para a GPU um kernel CUDA pré-compilado e altamente otimizado.

Como o CUDA potencializa as GPUs:

Os kernels CUDA são projetados para aproveitar os Tensor Cores e a hierarquia de memória da GPU (por exemplo, memória compartilhada e registradores).

Eles garantem o acesso coalescido à memória (acesso alinhado às capacidades do hardware para obter largura de banda máxima) e uma execução paralela eficiente.

Isso se traduz diretamente em uma velocidade computacional superior em comparação com a execução em CPU para tarefas passíveis de paralelização.

Enquanto um núcleo de CPU pode lidar com uma ou duas threads por vez, as GPUs são projetadas para um paralelismo muito maior. O CUDA facilita o uso conjunto de CPUs (com vários núcleos) e GPUs (com centenas de núcleos) para aproveitar os pontos fortes de cada processador.

<img width="2031" height="971" alt="T-AIHYPE_CUDA Kernels" src="https://github.com/user-attachments/assets/2eaee674-2ad1-4f98-bfc8-5c07140e0e99" />

### XLA Compilers
O Accelerated Linear Algebra (XLA) é um compilador de domínio específico desenvolvido originalmente pelo Google. Embora inicialmente focado nas TPUs do Google, seus princípios são amplamente aplicáveis ​​a outros aceleradores, incluindo GPUs. Ao contrário da execução imediata (*eager execution* — na qual cada operação é executada instantaneamente), o XLA opera com base em um grafo de computação. Quando uma série de operações é definida (por exemplo, uma camada de rede neural), o XLA captura esse grafo. Em seguida, ele realiza otimizações em todo o grafo antes da compilação.

As principais otimizações do XLA incluem:

- Fusão de operadores: Combinação de várias operações pequenas em um único kernel CUDA maior, o que reduz drasticamente a sobrecarga de inicialização de kernels e as transferências intermediárias de memória.

- Otimização de memória: Análise do grafo para minimizar alocações e desalocações de memória.

- Otimização de layout: Organização dos dados na memória para melhor se adequar aos padrões de acesso do hardware. Esse processo é análogo ao de um compilador *just-in-time* (JIT), mas voltado especificamente para cálculos de álgebra linear, resultando em código de máquina altamente eficiente e otimizado para o acelerador de destino.

### Pytorch/XLA and JAX/XLA
Trata-se de integrações especializadas que combinam as interfaces intuitivas de seus respectivos frameworks com as otimizações poderosas do compilador XLA.

PyTorch/XLA: Essa integração permite que modelos PyTorch aproveitem as otimizações do XLA. Você define seu modelo usando o PyTorch padrão, e o `torch_xla` converte de forma transparente o grafo computacional para a representação intermediária do XLA, visando à compilação e execução. A integração também oferece recursos como *gradient checkpointing* para eficiência de memória e capacidades aprimoradas de treinamento distribuído (por exemplo, com suporte a SPMD), aspectos cruciais para modelos de grande escala.

JAX/XLA: O JAX foi projetado desde a sua concepção tendo o XLA como seu *backend* central de compilação. Seu paradigma de programação funcional e suas capacidades de rastreamento (*tracing*) tornam-no particularmente adequado para a construção e otimização de grafos computacionais para o XLA.

Ambas as integrações facilitam a obtenção de alto desempenho. Os desenvolvedores podem manter seu fluxo de trabalho Pythonico preferido enquanto se beneficiam automaticamente de otimizações profundas em nível de compilador. Isso abstrai grande parte da complexidade do ajuste fino de hardware de baixo nível, resultando em melhorias de vazão (*throughput* — por exemplo, tokens/segundo para grandes modelos de linguagem) e na redução de custos operacionais.

---
## Alcançando desempenho em diversos hardwares

O desafio representado por ambientes de hardware heterogêneos (GPU, TPU, CPU) consiste em garantir a portabilidade do código e uma execução eficiente, sem exigir extensas reescritas para cada plataforma de destino. Frameworks de ML, compiladores XLA e suas integrações simplificam esse processo, viabilizando uma execução eficiente em diversas cargas de trabalho de IA.

Ao trabalhar com um dispositivo específico de computação de alto desempenho, como uma GPU, o objetivo principal é aproveitar ao máximo seus pontos fortes arquitetônicos exclusivos. Isso envolve minimizar ciclos ociosos, maximizar a vazão computacional e otimizar a movimentação de dados.

Vamos explorar os frameworks de ML que desbloqueiam esse potencial para uma eficiência inigualável.

### Frameworks de ML (PyTorch, JAX): Abstração de hardware
Frameworks de aprendizado de máquina como PyTorch e JAX fornecem uma camada crucial de abstração de hardware.

Eles oferecem APIs de alto nível para definir tensores, operações e arquiteturas de redes neurais. Você descreve quais cálculos realizar, em vez de como realizá-los em um hardware específico.

Isso permite que os desenvolvedores escrevam o código do modelo uma única vez e, com modificações mínimas, o implementem em diferentes tipos de aceleradores. O framework atua como o interpretador, traduzindo a descrição de alto nível em instruções específicas para cada dispositivo.

### Compiladores XLA: O otimizador universal
O design do XLA vai além de um único tipo de hardware, tornando-o um componente fundamental para o desempenho em múltiplas plataformas.

O XLA, por si só, é agnóstico em relação ao hardware. Ele recebe o grafo computacional e, dependendo do dispositivo de destino (GPU, TPU ou CPU), utiliza um *backend* específico. Cada *backend* contém regras de compilação otimizadas e estratégias de geração de código para o seu respectivo hardware.

Isso significa que o mesmo grafo computacional pode ser processado pelo XLA, que gerará código executável altamente otimizado para uma GPU NVIDIA, uma TPU do Google Cloud ou uma CPU padrão, garantindo que os pontos fortes de cada arquitetura sejam aproveitados.

### PyTorch/XLA e JAX/XLA: Os viabilizadores multiplataforma
Essas integrações são os mecanismos práticos que cumprem a promessa de desempenho em hardware diversificado.

Ao usar o torch_xla (para PyTorch) ou a integração nativa do XLA no JAX, seu código pode direcionar dinamicamente diferentes aceleradores. Por exemplo, em PyTorch/XLA, especificar `device='xla'` permite que o mesmo código seja executado com eficiência em uma TPU e, com suporte estendido a SPMD, potencialmente em uma GPU, aproveitando os benefícios do XLA.

Elas fornecem a "ponte" necessária entre um modelo de programação unificado e diferentes backends de hardware. Isso reduz significativamente a sobrecarga de engenharia associada às otimizações específicas de hardware, permitindo que as equipes se concentrem no desenvolvimento do modelo, confiando na pilha subjacente para maximizar o desempenho no hardware escolhido. Por exemplo, o suporte a SPMD do PyTorch/XLA para GPUs simplifica o escalonamento de modelos grandes, espelhando seu sucesso em TPUs.
---
# Optimizing GPU usage

O treinamento de IA moderna exige recursos imensos. Mesmo pequenas ineficiências podem elevar drasticamente os custos e retardar o progresso. Esta aula apresentará o *ML Productivity Goodput*, uma métrica projetada para medir a eficiência real do treinamento de sua IA. Você explorará seus componentes, aprofundará-se em técnicas para ajustar o uso da GPU e aprenderá a otimizar o *goodput* e o desempenho por dólar em seus projetos de IA mais exigentes.

O *goodput* era originalmente uma métrica de redes que mede a taxa de transferência em nível de aplicação de uma comunicação.

O que é Goodput?

Em termos simples, *goodput* é uma "vazão útil" (*good throughput*). No contexto do treinamento de modelos de aprendizado de máquina (ML), não se trata apenas de quanto dado suas GPUs estão processando, mas de quanto progresso efetivo de treinamento está sendo realizado.

