O que eu ganho com isso?
Bem-vindo ao terceiro curso da trilha de aprendizado sobre Infraestrutura de IA!

Este curso é totalmente dedicado às TPUs — aqueles chips ultrarrápidos projetados para IA! Vamos explorar em que situações elas são a melhor escolha para os seus projetos. Você poderá comparar diferentes modelos de TPU, descobrir como executar sua IA de forma eficiente nelas e até aprender como TPUs e GPUs podem trabalhar juntas para criar fluxos de trabalho de aprendizado de máquina realmente flexíveis.

Visão geral das TPUs
Embora tenhamos explorado o funcionamento interno das GPUs em um curso anterior, é importante entender que as TPUs oferecem uma abordagem distinta para a aceleração de IA.

O que são Cloud TPUs?

Em linhas gerais, TPU significa *Tensor Processing Unit* (Unidade de Processamento de Tensores). Trata-se de circuitos integrados de aplicação específica (ASICs) desenvolvidos sob medida pelo Google, criados especificamente para acelerar os cálculos intensivos típicos do aprendizado de máquina. Pense nelas como motores projetados especificamente para IA.

O código projetado para ser executado em TPUs precisa ser compilado pelo compilador XLA (Accelerator Linear Algebra). O XLA atua como um compilador *just-in-time*; isso significa que ele pega o grafo computacional gerado por um framework de aprendizado de máquina e traduz as operações de álgebra linear, funções de perda e cálculos de gradiente em código de máquina específico para a TPU.

As partes restantes do seu programa serão executadas na máquina host da TPU. O próprio compilador XLA está incluído na imagem da VM da TPU que opera nessa máquina host. Compartilharemos mais detalhes nas próximas aulas.

Vamos assistir a um vídeo interessante que oferece uma visão rara e detalhada dos bastidores dos data centers do Cloud TPU.

Quando utilizar TPUs

As Cloud TPUs são a escolha ideal para tarefas de IA específicas e exigentes, nas quais sua arquitetura especializada realmente se destaca. Veja quando elas são a melhor opção:


Treinamento de modelos massivos de aprendizado profundo (*deep learning*): Se você está desenvolvendo e treinando modelos de *deep learning* grandes e complexos — especialmente grandes modelos de linguagem (LLMs) —, as Cloud TPUs são projetadas para lidar com eficiência com a enorme quantidade de cálculos matriciais envolvidos.


Modelos que utilizam *embeddings*: As Cloud TPUs contam com *SparseCores*, processadores de fluxo de dados (*dataflow*) criados especificamente para acelerar modelos que fazem uso intensivo de *embeddings*. Isso as torna ideais para aplicações como sistemas de recomendação.


IA científica e na área da saúde: As Cloud TPUs são otimizadas para cargas de trabalho específicas. Elas se destacam em áreas científicas que exigem alto poder computacional. São particularmente poderosas para casos de uso na saúde, como a modelagem de dobramento de proteínas e a descoberta de fármacos, áreas em que simulações complexas e o aprendizado profundo são fundamentais.

Em suma, se a sua carga de trabalho de IA envolve redes neurais em larga escala, operações complexas com *embeddings* ou modelagem científica avançada, as Cloud TPUs podem oferecer a velocidade e a eficiência significativas de que você precisa.

Por que escolher as Cloud TPUs?

As Cloud TPUs oferecem vantagens significativas para uma ampla gama de tarefas de IA, desde o treinamento inicial de modelos até o ajuste fino e a inferência em grande volume. Veja por que elas se destacam:

- Cost-efficient scaling
As Cloud TPUs são projetadas para oferecer desempenho excepcional e, ao mesmo tempo, excelente custo-benefício, especialmente para cargas de trabalho de IA exigentes. Isso significa que você pode escalar seus projetos sem incorrer em custos proibitivos.

- Versatile framework support
Você não fica preso a um único ecossistema. As Cloud TPUs aceleram de forma integrada cargas de trabalho nos principais frameworks de IA — incluindo PyTorch e JAX — utilizando o compilador XLA, o que lhe confere a flexibilidade de escolher a solução mais adequada para a sua equipe.

- Integrated orchestration
Para operações de IA em larga escala, as Cloud TPUs integram-se perfeitamente ao Google Kubernetes Engine (GKE). Isso permite orquestrar e gerenciar cargas de trabalho de IA complexas com eficiência. Além disso, o Dynamic Workload Scheduler garante que todos os aceleradores necessários sejam provisionados simultaneamente, melhorando significativamente a escalabilidade e evitando desperdícios.

- Simplified development with Agent Platform
Se você busca o caminho mais direto para o desenvolvimento de modelos de IA, as Cloud TPUs estão profundamente integradas à Agent Platform, a plataforma de IA totalmente gerenciada do Google Cloud. Isso proporciona uma experiência simplificada, cuidando da infraestrutura subjacente para que você possa focar exclusivamente em suas cargas de trabalho.

- Powered by reconfigurable OCS
O TPU v4 foi o primeiro supercomputador a implementar a tecnologia de comutadores de circuitos ópticos (OCS). Esses comutadores podem reconfigurar dinamicamente a topologia de interconexão entre chips, o que melhora significativamente a escalabilidade, a disponibilidade, a utilização, a modularidade, a implementação, a segurança, a eficiência energética e o desempenho. A tecnologia OCS é consideravelmente mais barata, consome menos energia e oferece maior largura de banda do que a tecnologia InfiniBand tradicional. Essa eficiência decorre da eliminação de conversões óptico-elétrico-ópticas de alto consumo energético e de comutadores de pacotes de rede complexos.

Principais destaques

As Cloud TPUs são ASICs desenvolvidos sob medida pelo Google para acelerar cargas de trabalho intensivas de aprendizado de máquina. Elas operam com o compilador XLA para traduzir grafos computacionais em código de máquina otimizado, tornando-as ideais para o treinamento de modelos massivos de aprendizado profundo, o processamento de *embeddings* e aplicações de IA científica.

A escolha das Cloud TPUs oferece benefícios como escalabilidade com bom custo-benefício, amplo suporte a *frameworks* (PyTorch, JAX), integração perfeita com o GKE e a Agent Platform, além de desempenho avançado impulsionado por comutadores de circuitos ópticos reconfiguráveis.

