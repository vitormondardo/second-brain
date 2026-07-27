# GKE inference reference architecture

Nesta aula, exploraremos a arquitetura de referência de inferência no GKE — uma estrutura robusta e escalável para implantar e gerenciar cargas de trabalho de inferência no Google Kubernetes Engine (GKE). Pense nela como um modelo para criar serviços de IA de alto desempenho, resilientes e com boa relação custo-benefício.

Padronize as implantações
Crie um processo consistente para implantar modelos, o que é fundamental para reduzir erros e promover a automação.

Otimize desempenho e custos
Aproveite recursos do GKE, como escalonamento automático e aceleração de hardware (GPUs, TPUs), para lidar com eficiência com cargas de trabalho de alta vazão e baixa latência.

Viabilize a escalabilidade
Escalone cargas de trabalho automaticamente para atender à demanda em tempo real, lidando de forma fluida com picos repentinos de tráfego.

Promova as melhores práticas de operações
Integre versionamento de modelos, CI/CD, monitoramento, registro de logs e segurança robusta ao ciclo de vida dos seus modelos.

Acelere a implementação
Ofereça um caminho claro e prático para obter uma carga de trabalho de inferência funcional.

Vamos analisar a infraestrutura essencial e as estratégias em nível de modelo para atender a cargas de trabalho de inferência em escala no Google Kubernetes Engine (GKE). Essa arquitetura oferece uma metodologia padronizada e reproduzível para a implantação de cargas de trabalho de inferência, ajudando a reduzir a complexidade operacional e a adotar princípios como o GitOps.

<img width="1641" height="970" alt="GKEarch1" src="https://github.com/user-attachments/assets/c0fac6ee-ea47-4776-be69-623141f9bc18" />

Aqui estão as principais considerações para configurar seu ambiente GKE:

Selecione cada aba para saber mais.


Capacidade de aceleradores e escolha do cluster
Antes de qualquer coisa, certifique-se de ter cota suficiente para as GPUs ou TPUs necessárias na região de destino. Caso contrário, você deve solicitar um aumento com bastante antecedência.

Após garantir a cota, você deve escolher um modo de cluster GKE:

Autopilot: Este modo totalmente gerenciado automatiza o provisionamento, o escalonamento e a manutenção de nós. O GKE cuida de tudo, desde o dimensionamento dos nós até o *bin-packing* (alocação eficiente de recursos), para que você pague apenas pelos recursos que seus Pods realmente utilizam. É a opção mais simples e econômica para a maioria das cargas de trabalho.

Standard: Este modo oferece controle granular sobre os nós. Escolha o modo Standard se precisar instalar softwares específicos nos nós, utilizar uma imagem de máquina personalizada ou necessitar de acesso SSH direto para depuração avançada. Embora ofereça flexibilidade, ele implica maior sobrecarga operacional.


Configuração de cluster e pool de nós
Para garantir alta disponibilidade e escalonamento robusto, configure seus clusters seguindo estas práticas recomendadas:

Clusters regionais: Para cargas de trabalho em produção, utilize clusters regionais para distribuir seus Pods de inferência entre múltiplas zonas. Isso proporciona tolerância a falhas e maior disponibilidade.

Autoprovisionamento de nós (NAP): Habilite o NAP em clusters Standard. Ele trabalha em conjunto com o *autoscaler* do cluster para criar automaticamente novos pools de nós com os tipos de máquina e aceleradores adequados, sempre que seus Pods precisarem de recursos indisponíveis.

Classes de computação personalizadas (CCC): Este recurso do GKE permite definir pools de nós especializados com lógica de *fallback* (alternativa). Por exemplo, você pode instruir o GKE a tentar agendar um Pod primeiramente em um nó com GPU NVIDIA A100 e, caso não esteja disponível, recorrer a uma GPU L4 menos potente, garantindo que seus Pods sejam sempre agendados.

Otimização em nível de modelo
A infraestrutura é apenas metade da equação. Você também deve otimizar seus modelos para inferência, a fim de atingir as metas de desempenho e custo.

Quantização: É o processo de redução da precisão dos pesos e ativações do modelo (por exemplo, de ponto flutuante de 32 bits para números inteiros de 8 bits). Isso reduz drasticamente o tamanho do modelo e acelera a inferência, mas exige uma avaliação cuidadosa para evitar perdas na precisão. Paralelismo de tensores: Para LLMs massivos que não cabem em uma única GPU, essa técnica divide os tensores do modelo entre vários aceleradores. Cada acelerador processa uma parte do tensor, permitindo executar modelos que, de outra forma, seriam inviáveis ​​em uma única máquina.

Paged attention e flash attention: São técnicas especializadas para otimizar o mecanismo de atenção dos LLMs, que consome muita memória. Elas reduzem significativamente o uso de memória e aumentam a vazão (throughput), fatores que representam gargalos críticos para sequências longas e grandes tamanhos de lote (batch sizes).


Monitoramento contínuo
Após a implantação, o monitoramento contínuo é fundamental para manter o desempenho e a eficiência de custos.

Métricas: Utilize o Cloud Monitoring para acompanhar métricas importantes de inferência, como consultas por segundo (QPS), latência (p99) e utilização de GPU/TPU. Para um escalonamento mais inteligente, use um adaptador de métricas personalizadas para permitir que o escalonador automático horizontal de pods (HPA) realize o escalonamento com base em métricas do servidor de modelos, como `requests_per_second` ou `model_latency_ms`.

Registro de logs (Logging): Centralize os logs da aplicação e do sistema no Cloud Logging. Implemente logs estruturados para facilitar a consulta de erros específicos ou problemas de desempenho.

Rastreamento (Tracing): Integre com o Cloud Trace ou OpenTelemetry para rastrear o caminho de uma solicitação por todo o pipeline de inferência. Isso ajuda a identificar gargalos e depurar problemas em microsserviços distribuídos.

Uma equipe de MLOps está se preparando para implantar um modelo pequeno e personalizado para inferência em tempo real. Eles preveem um tráfego de base baixo, mas precisam lidar com picos de tráfego imprevisíveis de forma eficiente. O objetivo principal é alcançar a implantação mais econômica possível.

Qual combinação de modo de cluster do GKE e estratégia de otimização de modelo representa a solução mais econômica para essa carga de trabalho?

Modo Autopilot do GKE combinado com quantização de modelo.

Correto. O Autopilot é econômico devido à sua cobrança por uso e escalonamento automático para tráfego com picos. A quantização é a melhor otimização de modelo, reduzindo o tamanho do modelo para ser executado em aceleradores menores e mais baratos.

