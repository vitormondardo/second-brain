Interoperabilidade entre aceleradores
A implementação de grandes modelos de linguagem (LLMs) frequentemente envolve um equilíbrio entre preço, desempenho e disponibilidade. Tradicionalmente, alcançar o melhor equilíbrio significava implementações separadas, gerenciamento complexo e custos mais elevados. E se pudéssemos simplificar isso e obter o melhor de todos os mundos?

Imagine um cenário em que as cargas de trabalho de LLM possam utilizar de forma inteligente e automática tanto GPUs quanto TPUs, priorizando o desempenho quando necessário e alternando para opções de melhor custo-benefício quando possível — tudo isso em uma única implementação.

Vamos explorar como sua carga de trabalho pode escalar e alternar de forma inteligente entre GPUs e TPUs. Mas, antes disso, vamos abordar a questão fundamental: GPU ou TPU para o seu modelo? Utilizaremos uma árvore de decisão simples para orientá-lo na escolha do hardware adequado. Vamos começar.

Começando por definir se o seu código é destinado a GPU ou TPU.

Caminho 1: Otimizado para TPU

Se o código do seu modelo já estiver otimizado para TPU, você seguirá o caminho à esquerda. As TPUs são especializadas para o framework TensorFlow, do Google, e são ideais para o treinamento de modelos em larga escala.

<img width="2031" height="1001" alt="T-AIHYPE_Rise-91" src="https://github.com/user-attachments/assets/8114fc73-afa8-45f3-aec8-8039dcae92e2" />

Caminho 2: Otimizado para GPU

Se o seu modelo foi projetado para GPU — o que é mais comum e oferece suporte a uma gama maior de frameworks —, você seguirá o caminho à direita.

Demonstração: Alternando entre GPU e TPU com o vLLM

Classes de computação personalizadas nos permitem definir uma ordem de prioridade para diferentes pools de nós no cluster GKE. Nesta demonstração, configuramos uma delas para priorizar nós de TPU visando o desempenho máximo, utilizando nós de GPU como alternativa para garantir custo-benefício e lidar com o aumento da demanda. O vLLM é uma biblioteca rápida e fácil de usar para inferência e disponibilização de LLMs. Para alternar entre GPU e TPU de forma transparente, utilizamos uma abordagem de dois contêineres em um único pod. Como o vLLM utiliza imagens base diferentes para GPUs e TPUs, executamos dois contêineres distintos.

Se o acelerador adequado (GPU ou TPU) estiver presente, o servidor vLLM daquele contêiner é iniciado; caso contrário, ele permanece inativo. Isso garante que apenas o servidor vLLM correto esteja ativo, com base no hardware subjacente. Vamos explorar isso mais a fundo em uma demonstração rápida.

Como podemos observar, o GKE, guiado pela classe de computação personalizada, escalará para nós TPU para lidar com a carga de trabalho inicial, aproveitando seu desempenho superior. À medida que a carga aumenta e a capacidade da TPU é atingida, o GKE escalará para nós GPU, garantindo disponibilidade contínua e otimização de custos.

Por exemplo, o primeiro pod pode ser executado em um nó TPU (já que restringimos esse pool a um único nó). Os pods subsequentes, devido a essa restrição, tentarão ser carregados em clusters GPU de camada 4.

Essa escalabilidade dinâmica e utilização inteligente de hardware, orquestrada por classes de computação personalizadas, GKE e implantação vLLM de contêiner duplo, demonstra o poder e a flexibilidade dessa solução.

Existe uma compensação inerente entre a interoperabilidade em diferentes tipos de arquitetura de hardware e o desempenho extremo obtido pela otimização do código para uma arquitetura de hardware específica. A interoperabilidade usando o compilador XLA geralmente requer alterações no código. Com o vLLM em TPU (o recurso usado para a demonstração neste módulo), essas alterações são gerenciadas pelo backend do vLLM. Outras bibliotecas de serviço podem exigir mais esforço.

Principais conclusões

O Google Kubernetes Engine (GKE) com classes de computação personalizadas e vLLM permite o escalonamento inteligente e dinâmico de cargas de trabalho LLM em TPUs e GPUs em uma única implantação.

Essa solução prioriza o desempenho, aproveitando inicialmente os nós de TPU (por exemplo) e, em seguida, escalando perfeitamente para nós de GPU com melhor custo-benefício à medida que a demanda aumenta ou a capacidade da TPU é atingida. Essa abordagem dinâmica garante disponibilidade contínua e otimiza custos, gerenciando as compensações inerentes à implantação de LLM.

