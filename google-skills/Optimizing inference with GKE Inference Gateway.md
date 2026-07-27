# Otimizando a inferência com o GKE Inference Gateway

O GKE Inference Gateway foi projetado para otimizar a disponibilização de grandes modelos de linguagem (LLMs) e outras cargas de trabalho de IA generativa. Ao contrário dos balanceadores de carga tradicionais — que não são adequados para a natureza complexa e variável da inferência de IA —, o GKE Inference Gateway utiliza um sistema de roteamento inteligente e ciente dos modelos para melhorar o desempenho, reduzir custos e simplificar as operações.

<img width="1920" height="1080" alt="KVcache Utilization" src="https://github.com/user-attachments/assets/f99ceb0c-1ac7-4f0d-ab00-4d8b6bf52e9f" />

A utilização do servidor de modelos é mais uniforme, o que significa que as solicitações recebidas nunca entram em fila.

<img width="1920" height="1080" alt="average QUEUE SIZE" src="https://github.com/user-attachments/assets/acb70866-b29f-4304-88a6-d66a489d1a7c" />

Em suma, o GKE AI Inference Gateway simplifica a tarefa complexa de disponibilizar modelos de IA em escala. Ele atua como um controlador de tráfego inteligente, garantindo que seus modelos estejam sempre disponíveis, seguros e prontos para lidar com qualquer demanda que surja. Veja como o GKE Inference Gateway auxilia na inferência de IA:

Gerenciamento de tráfego

O gateway roteia automaticamente as solicitações recebidas para seus modelos de IA, garantindo que a solicitação correta seja direcionada ao modelo adequado. Isso é especialmente útil quando você utiliza vários modelos ou diferentes versões de um mesmo modelo.



Escalabilidade
Ele trabalha em conjunto com o Google Kubernetes Engine (GKE) para escalar automaticamente seus modelos (aumentando ou reduzindo a capacidade) com base na demanda. Se houver um aumento repentino no número de usuários solicitando previsões, o gateway ajuda a garantir que o GKE aloque mais recursos para lidar com a carga sem interrupções.



Segurança e controle
O gateway oferece um ponto de entrada seguro e controlado para seus modelos. Ele permite aplicar políticas de segurança e gerenciar o acesso, protegendo seus modelos contra uso não autorizado.

Como o GKE Inference Gateway funciona

O GKE Inference Gateway monitora a carga nos servidores de modelos usando métricas específicas de IA, como o tamanho da fila de solicitações pendentes e a utilização do KVCache. Em seguida, ele roteia de forma inteligente as solicitações recebidas para a GPU ou TPU com menor carga, garantindo uma distribuição equilibrada do trabalho em toda a sua infraestrutura.

<img width="1156" height="912" alt="Visuals for Course 4 (1)" src="https://github.com/user-attachments/assets/54a5a21b-599d-4aef-b771-19a20c9862b8" />

Passo 1
Solicitação do cliente: GET /completions

Passo 2
Selecionar o InferencePool como um serviço do K8s utilizando o nome do modelo (especificação da API da OpenAI).

Passo 3
Escolher a réplica do modelo com menor carga que possua o adaptador LoRA em memória.

Passo 4
Encaminhar para o InferencePool e para a réplica do modelo ideal, com base na prioridade.

O GKE Inference Gateway atua como um intermediário inteligente entre uma solicitação do cliente e uma instância de modelo. Quando um cliente envia uma solicitação (frequentemente formatada de acordo com a especificação da API da OpenAI), o GKE Inference Gateway a processa por meio de uma série de extensões especializadas:

Roteamento baseado no corpo da requisição
Esta extensão extrai o identificador do modelo diretamente do corpo da requisição. O GKE Inference Gateway utiliza essa informação para encaminhar a requisição ao modelo correto, com base em regras definidas por você. Isso é especialmente útil ao executar vários modelos em um único cluster.

Segurança
Esta extensão aplica políticas de segurança específicas para cada modelo, utilizando o Model Armor ou soluções de terceiros. Ela pode realizar filtragem de conteúdo, detecção de ameaças e higienização tanto nas requisições de entrada quanto nas respostas de saída.

Seletor de endpoint
Este é o núcleo da inteligência do GKE Inference Gateway. Ele monitora continuamente métricas importantes dos servidores de modelos, como requisições pendentes e a utilização do cache de chave-valor (KV-cache). Em seguida, encaminha a requisição para a réplica de modelo mais adequada e menos sobrecarregada, garantindo baixa latência e alta vazão.

Esse roteamento inteligente proporciona benefícios significativos de desempenho:

•
Maior vazão: Obtenha um aumento de até 40% na vazão ao utilizar de forma mais eficiente seus recursos de GPU e TPU.

•
Menor latência: Reduza os tempos de resposta em até 60%, resultando em uma experiência de usuário muito melhor.

Ao priorizar requisições sensíveis à latência e gerenciar com eficiência a capacidade dos aceleradores, o GKE Inference Gateway garante que você possa atender adequadamente a múltiplos casos de uso de IA a partir de um único cluster, sem comprometer a experiência do usuário.

Demonstração: Teste o GKE Inference Gateway

Principais pontos

O GKE AI Inference Gateway é um balanceador de carga especializado e ciente de modelos, projetado para otimizar a disponibilização de IA generativa e LLMs. Ao utilizar métricas específicas de IA (como fila de solicitações e utilização de KVCache) para rotear solicitações de forma inteligente, ele garante o uso eficiente de recursos, resultando em uma vazão significativamente maior e menor latência em comparação com balanceadores de carga tradicionais.

Agora você tem uma compreensão sólida das estratégias e ferramentas necessárias para criar uma infraestrutura de IA robusta, escalável e com bom custo-benefício no Google Cloud. Este curso concentrou-se na implantação e otimização de clusters de IA Hypercomputer usando o Google Compute Engine (GCE) e o Google Kubernetes Engine (GKE) para cargas de trabalho de treinamento e inferência distribuídos.

1
Você explorou todo o processo de criação de clusters, desde a escolha do tipo de máquina e da opção de consumo até a seleção do orquestrador adequado.

2
Você obteve insights sobre a arquitetura de ambientes de alto desempenho no GCE, incluindo o uso de redes multi-VPC especializadas para segregar o tráfego de alta largura de banda entre GPUs e a minimização da latência por meio de políticas de posicionamento compacto (compact placement policies).

3
Em seguida, você aprendeu como o GKE serve como uma base poderosa para IA/ML. Você examinou os modos do GKE (Standard versus Autopilot) e os principais recursos de otimização da plataforma, como o Kueue para o agendamento equitativo de cargas de trabalho e o *multislicing* para a criação de ambientes de computação grandes e contíguos.

4
Por fim, você focou na otimização da disponibilização de modelos (*model serving*), aprendendo que o GKE AI Inference Gateway é um balanceador de carga especializado e ciente dos modelos.

Parabéns! Você foi aprovado nesta avaliação.
check
1.

Uma pequena equipe de engenheiros de ML está criando protótipos de novos modelos em notebooks Jupyter em um cluster GKE. Suas cargas de trabalho geralmente são pequenas, e as GPUs ficam ociosas por longos períodos. Qual estratégia de compartilhamento de GPU eles devem usar para maximizar a utilização da GPU e reduzir custos?
NVIDIA MPS
Multi-Instance GPU (MIG)
Sem compartilhamento de GPU
check
GPU Time-Sharing
Correto. O GPU Time-Sharing (compartilhamento de tempo de GPU) é ideal para cargas de trabalho interativas e com picos de demanda, pois permite que vários contêineres compartilhem uma única GPU, evitando que hardware caro fique ocioso e melhorando a utilização de recursos.
check
2.

Sua equipe está executando um serviço de inferência de IA em tempo real, no qual baixa latência e desempenho consistente são fundamentais. Um pico nas solicitações de um usuário não deve afetar o serviço para outros usuários. Qual estratégia de compartilhamento de GPU é a mais adequada para esse cenário?
NVIDIA MPS
Implantação em massa de VMs
check
Multi-Instance GPU (MIG)
GPU Time-Sharing
Correto. O MIG oferece isolamento em nível de hardware, o que garante que um pico em uma carga de trabalho não afetará as outras, assegurando uma Qualidade de Serviço (QoS) consistente e previsível para todos os usuários.

