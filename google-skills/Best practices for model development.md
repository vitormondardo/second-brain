Nesta aula, você vai se aprofundar nos detalhes de como projetar e criar modelos de aprendizado de máquina que realmente se destacam nas Tensor Processing Units (TPUs) do Google. As TPUs são aceleradores poderosos, mas extrair o máximo delas exige a compreensão de alguns princípios fundamentais.

Entendendo o núcleo da TPU: a MXU

No coração de cada Cloud TPU está a Matrix Multiplier Unit (MXU). Esse hardware especializado foi projetado para realizar operações de matrizes de grande porte com eficiência incrível. Pense nela como uma calculadora ultrarrápida, criada especificamente para o tipo de matemática que impulsiona o aprendizado profundo (*deep learning*).

No entanto, se as computações do seu programa forem dominadas por operações que não envolvem matrizes — como somas simples, redimensionamento de tensores ou concatenação — é provável que você não esteja aproveitando ao máximo a potência da MXU. É como ter um carro esportivo de alto desempenho, mas dirigi-lo apenas em trânsito intenso, com constantes paradas e arranques.

Para garantir que você esteja utilizando todo o imenso potencial da MXU e evitando que operações não matriciais limitem seu desempenho, vamos explorar os princípios fundamentais para o desenvolvimento de modelos altamente otimizados em TPUs.

Princípio 1: Layout voltado para a eficiência

O compilador XLA é o melhor aliado do seu modelo ao executar em TPUs. Ele é responsável por transformar seu código para que seja executado de forma eficiente na MXU, incluindo uma etapa crucial chamada *tiling* (ou particionamento em blocos). O *tiling* divide grandes multiplicações de matrizes em blocos menores e mais gerenciáveis.

O hardware da MXU consiste em uma matriz sistólica de 256x256 (as TPUs anteriores à Trillium utilizavam 128x128), e o subsistema de memória da TPU privilegia dimensões que sejam múltiplas de 8. O compilador XLA tira proveito dessas características para realizar o *tiling* (divisão em blocos) de forma otimizada.

Layouts favoráveis: Certas formas de organizar os dados dos tensores (chamadas de "layouts") adequam-se mais naturalmente a esse processo de divisão em blocos.

Operações de *reshape* (redimensionamento): Outros layouts podem exigir que o compilador execute operações de *reshape* antes que a divisão em blocos possa ser feita de maneira eficaz. Essas operações de *reshape* são frequentemente limitadas pela largura de banda da memória na Cloud TPU; isso significa que elas tornam a computação mais lenta, pois demandam mais acesso à memória do que processamento propriamente dito.

<img width="1441" height="1081" alt="T-AIHYPE_Rise_M3_82" src="https://github.com/user-attachments/assets/8762e228-30d8-4468-8a65-ad6e8f0b2171" />

Pense em como seus dados estão estruturados. Você consegue projetar seu modelo para minimizar a necessidade de operações complexas de remodelagem, especialmente antes das multiplicações de matrizes principais?

Princípio 2: Formas fixas para desempenho previsível

O compilador XLA compila seu grafo de aprendizado de máquina no momento exato do primeiro lote de dados. Essa compilação just-in-time (JIT) é altamente otimizada para desempenho.

Aqui está o ponto crucial: se algum lote de dados subsequente tiver formas diferentes do primeiro lote, seu modelo não funcionará corretamente. Por quê? Porque recompilar todo o grafo sempre que uma forma muda é muito lento para as velocidades para as quais as TPUs foram projetadas.

<img width="1441" height="1081" alt="T-AIHYPE_Rise_M3_83_2" src="https://github.com/user-attachments/assets/10896bd9-b84c-4c38-81b2-20bbab8fd1fb" />

Qualquer modelo que possua tensores com formatos dinâmicos (onde o tamanho de um tensor pode mudar de uma etapa para a seguinte) geralmente não é adequado para TPUs. Projete seu modelo para usar formatos de tensores fixos para uma execução consistente e eficiente.

Princípio 3: Evitando preenchimento desnecessário

Um programa Cloud TPU de alto desempenho é aquele em que seus cálculos densos podem ser divididos eficientemente em blocos de 128x128 para preencher perfeitamente a MXU.

Quando um cálculo matricial não consegue ocupar totalmente uma MXU inteira (por exemplo, se uma dimensão não for um múltiplo de 128), o compilador XLA preenche automaticamente os tensores com zeros. Embora isso garanta que o cálculo possa prosseguir, apresenta duas desvantagens significativas:

Subutilização: Tensores preenchidos com zeros não utilizam totalmente o núcleo da TPU. Você está realizando cálculos em dados "vazios", o que desperdiça poder de processamento valioso.

Aumento de memória: O preenchimento aumenta a quantidade de armazenamento de memória on-chip necessária para um tensor. Em casos extremos, isso pode até levar a um erro de falta de memória.

- Automático: O preenchimento (*padding*) é gerenciado automaticamente pelo compilador XLA.
- Detecção: Você pode usar a ferramenta op_profile para determinar exatamente quanto preenchimento (padding) está sendo realizado no seu modelo.
- Prevenção: A melhor maneira de evitar o *padding* é escolher dimensões de tensor adequadas para TPUs. Especificamente, dimensões que sejam múltiplos de 8 ou, idealmente, múltiplos de 128 para operações de matriz.

Ao projetar um modelo de IA para desempenho ideal em Cloud TPUs, qual das seguintes práticas é crucial para evitar recompilações dispendiosas e maximizar a velocidade de execução?

- Projetar o modelo para usar formatos de tensores fixos em diferentes lotes.

Principais pontos

• Dimensões inteligentes: Escolha dimensões de tensor que sejam múltiplos de 8 e, idealmente, múltiplos de 128 para cálculos matriciais, a fim de minimizar o *padding* (preenchimento).

• Mantenha suas TPUs ocupadas tornando seu programa limitado pelo processamento (*compute-bound*). Isso significa que o programa deve passar a maior parte do tempo realizando cálculos na própria TPU, em vez de aguardar a movimentação de dados (ou seja, evitando limitações de memória ou de E/S).

• Evite recompilações custosas causadas por fatores como alterações frequentes no formato (*shape*) dos dados, instruções de controle que dependem de valores de tensores ou acesso prematuro aos dados a partir do host (CPU). Esses fatores podem comprometer o grafo computacional otimizado e reduzir significativamente a velocidade da sua carga de trabalho.

• Priorize operações matriciais: Certifique-se de que seus cálculos principais dependam fortemente de multiplicações de matrizes.

• Otimize layouts: Projete seu modelo para minimizar operações custosas de redimensionamento (*reshape*), especialmente antes de cálculos importantes.

• Formatos de tensor fixos: Evite formatos de tensor dinâmicos. Busque consistência nas dimensões entre os lotes (*batches*).

Casos de uso e estudos de caso
As TPUs são ideais para aplicações que envolvem grandes volumes de dados e redes neurais complexas.

Aqui estão alguns exemplos de como e por quem as TPUs são utilizadas:

Treinamento e ajuste fino de modelos de base (IA generativa)

Large AI research labs, tech companies (including Google for its Gemini models), and even startups developing cutting-edge AI, these organizations leverage large clusters of TPUs (known as "TPU Pods") to train colossal foundation models like Large Language Models (LLMs) or generative image models from scratch.

<img width="1921" height="1081" alt="T-AIHYPE_Rise_M3_86" src="https://github.com/user-attachments/assets/a21f46da-c332-40ed-ae3f-e8ccc0b9213f" />

A arquitetura especializada das TPUs permite que bilhões ou até trilhões de parâmetros sejam atualizados de forma eficiente em centenas ou milhares de chips, em paralelo, reduzindo significativamente o tempo e o custo envolvidos nessas tarefas computacionalmente intensivas. Elas também são utilizadas para o ajuste fino (*fine-tuning*) desses modelos pré-treinados em conjuntos de dados menores e específicos para cada tarefa, adaptando-os a casos de uso particulares (por exemplo, a criação de um chatbot especializado em atendimento ao cliente a partir de um LLM de uso geral).

Aceleração de sistemas de recomendação



Outro caso de uso de destaque é a aceleração de sistemas de recomendação. Plataformas de comércio eletrônico, serviços de streaming e empresas de redes sociais utilizam mecanismos de recomendação. Esses mecanismos frequentemente dependem da análise de grandes volumes de dados de interação dos usuários para prever preferências. Isso envolve trabalhar com matrizes esparsas de alta dimensionalidade (nas quais a maioria dos valores é zero).

<img width="1441" height="721" alt="T-AIHYPE_Rise_m3_mw-17 copy 7@2x" src="https://github.com/user-attachments/assets/404e59f4-23f9-417a-b674-f5cb2a23bdc3" />

As TPUs são equipadas com unidades especiais chamadas "SparseCores", que são particularmente eficientes no processamento de operações com matrizes esparsas — comuns em modelos de recomendação baseados em *embeddings* —, permitindo que essas plataformas processem rapidamente bilhões de interações de usuários e forneçam recomendações personalizadas em tempo real.

Pesquisa científica e análise de dados complexos



As TPUs também desempenham um papel fundamental na pesquisa científica e na análise de dados complexos. Áreas como a descoberta de fármacos, a modelagem do dobramento de proteínas e as simulações climáticas frequentemente envolvem cálculos numéricos complexos que podem ser estruturados como operações matriciais em larga escala. Por exemplo, simular interações moleculares ou analisar grandes conjuntos de dados genômicos exige um imenso poder de processamento paralelo.

Graças ao seu foco na multiplicação de matrizes de alto rendimento, as TPUs permitem que pesquisadores executem essas simulações e análises com muito mais rapidez, acelerando descobertas e ampliando as fronteiras do conhecimento científico.

Principais pontos

Desde o treinamento de modelos massivos até a inferência eficiente e soluções de plataforma integradas, as TPUs são projetadas para atender às demandas da IA ​​de ponta atual. Compreender esses casos de uso ajudará você a identificar como as Cloud TPUs podem atender melhor às suas necessidades específicas de desenvolvimento e implantação de IA.

A tabela a seguir descreve as principais arquiteturas de escalonamento para os aceleradores de ML do Google, detalhando suas capacidades de paralelismo, principais casos de uso e propostas de valor fundamentais.

Arquitetura de escalonamento

Casos de uso

Proposta de valor

v5e

Paralelismo 2D em Pods de 16x16 (256 chips/pod); escalonamento Multislice para centenas de pods v5e (50 mil em benchmarks) a 6,4 Tbps por pod

Inferência, ajuste fino (fine-tuning) e treinamento de ML para uso geral e modelos grandes

Escalonamento viabilizado pela tecnologia Multislice da Cloud TPU

Melhor relação desempenho/TCO para inferência de LLMs

v5p

Paralelismo 3D, 8.960 chips por pod; escalonamento Multislice para 345,6 Tbps em DCN

Treinamento de ML para uso geral (visão computacional, geração de mídia, sistemas de recomendação, LLMs com alta demanda de comunicação)

Escalonamento simplificado

Maior utilização de FLOPS

Maior desempenho de treinamento

Menor latência de serviço (inferência)

Trillium

Paralelismo 2D em Pods de 16x16 (256 chips/pod); escalonamento Multislice em cluster para 497 pods Trillium (127 mil chips) a 25,6 Tbps por pod

Treinamento e inferência de LLMs.
Suporte a SparseCore para embeddings (e flexibilidade no offload)

Escalabilidade massiva por meio do design de cluster

Melhor relação desempenho/TCO para treinamento de LLMs

Parabéns e próximos passos
Agora você tem uma compreensão abrangente das Tensor Processing Units (TPUs) do Google Cloud, com foco específico em sua arquitetura, integração com a nuvem e utilização ideal.

1
Você explorou como aproveitar efetivamente as Cloud TPUs para acelerar o treinamento e a inferência de IA, incluindo a seleção de opções adequadas e a maximização de seu potencial.

2
Você conheceu a variedade de aceleradores TPU disponíveis no Google Cloud, desde modelos fundamentais como v2 e v3 até as inovações mais recentes, como Ironwood (v7e) e Trillium (v6e), bem como as diversas configurações de escalonamento, incluindo TPU pods, slices e multislice.

3
Além disso, você obteve insights sobre como frameworks de IA, como PyTorch e JAX, são projetados para otimizar a utilização de TPUs por meio de alocação automática de dispositivos, gerenciamento eficiente de memória e estratégias de treinamento distribuído.

Você adquiriu conhecimento sobre a otimização do uso de TPUs aplicando princípios como a priorização de operações matriciais, formatos de tensor fixos e dimensões inteligentes para minimizar o *padding* e garantir um desempenho limitado pela capacidade de computação (*compute-bound*).

QUIZ

1 .

No contexto da escalabilidade de cargas de trabalho LLM em TPUs e GPUs usando o Google Kubernetes Engine (GKE) e o vLLM, qual é o principal benefício da interoperabilidade GPU/TPU?
Permite o aproveitamento inteligente e automático de GPUs e TPUs para otimizar o desempenho e o custo com base na demanda.
Isso elimina a necessidade de qualquer hardware subjacente, executando LLMs puramente em software.
Ele fornece uma alocação estática e predeterminada de GPUs ou TPUs para toda a carga de trabalho.
Permite a troca manual entre nós de GPU e TPU com base na preferência do desenvolvedor.
2 .

Qual versão do Cloud TPU é especificamente destacada por sua mudança estratégica em direção à aceleração de cargas de trabalho de inferência e por ser projetada para suportar a transição para agentes de IA mais proativos?
Nuvem TPU Trillium
Nuvem TPU
Nuvem TPU Madeira de Ferro
Cloud TPU v4
3 .

Uma empresa precisa treinar um modelo de IA crítico e de longa duração que exige capacidade computacional garantida e oferece economia significativa de custos para uso prolongado. Qual opção de consumo de hipercomputador de IA seria a mais adequada?
TPUs sob demanda
Gerenciamento direto usando o Compute Engine
reservas de longo prazo
TPUs Spot
4 .

Qual é a principal desvantagem de "preencher" tensores com zeros em cálculos do Cloud TPU?
É necessária a intervenção manual do desenvolvedor para remover os zeros adicionados.
Isso leva à subutilização do núcleo da TPU e ao aumento do consumo de memória no chip.
Isso impede que o compilador XLA processe os tensores.
Isso reduz significativamente a precisão geral do modelo.

--- 
1. Resposta: Opção 1
Permite o aproveitamento inteligente e automático de GPUs e TPUs para otimizar o desempenho e o custo com base na demanda.

Por que esta opção? Em arquiteturas modernas no GKE utilizando orquestradores de inferência como o vLLM, a interoperabilidade GPU/TPU serve para alternar dinamicamente e flexibilizar o processamento. Isso evita gargalos de disponibilidade e reduz custos ao alocar o chip mais adequado e disponível para o volume de requisições do momento.

2. Resposta: Cloud TPU Trillium (Opção 1)

Cloud TPU Ironwood

3. Resposta: reservas de longo prazo (Opção 3)
reservas de longo prazo (Long-term reservations)

Por que esta opção? Treinamentos críticos e duradouros exigem capacidade garantida (para evitar que o treinamento seja interrompido por falta de nós) e previsibilidade financeira. As reservas de longo prazo oferecem descontos significativos em troca do compromisso contínuo, ao contrário dos nós Spot (que podem ser interrompidos a qualquer momento) ou Sob Demanda (que custam mais caro).

4. Resposta: Opção 2
Isso leva à subutilização do núcleo da TPU e ao aumento do consumo de memória no chip.

Por que esta opção? A arquitetura do Cloud TPU (e do compilador XLA) opera de forma mais eficiente com tensores cujas dimensões são múltiplos exatos da sua Matrix Multiply Unit (MXU, tipicamente 128x128). Quando tensores menores recebem padding (preenchimento) de zeros para se ajustarem à matriz rígida do hardware, a TPU continua executando cálculos matemáticos e alocando memória para esses zeros "inúteis", desperdiçando ciclos de computação e espaço em memória HBM/SRAM.





