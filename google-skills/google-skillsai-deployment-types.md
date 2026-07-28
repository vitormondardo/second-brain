Visão geral do processo
Um fluxograma que mostra as seis etapas para criar um cluster no AI Hypercomputer. As etapas são: escolher o tipo de máquina, escolher a opção de consumo, escolher a opção de implantação, escolher o orquestrador, escolher a imagem e, por fim, criar e implantar.
O processo de criação de um cluster no AI Hypercomputer envolve seis etapas:

Escolher o tipo de máquina

Escolher uma opção de consumo e solicitar capacidade

Escolher uma opção de implantação

Escolher um orquestrador

Escolher a imagem do cluster

Criar e implantar

Suas escolhas dependerão de seus requisitos técnicos. O AI Hypercomputer foi projetado para oferecer flexibilidade e, ao mesmo tempo, garantir a otimização de suas cargas de trabalho específicas de IA.

Após fazer essas escolhas, você pode prosseguir para a etapa 6: criar e implantar seu cluster.

A próxima série de lições abordará cada uma dessas escolhas — e, especificamente, as opções de implantação — com mais detalhes.

<img width="782" height="897" alt="step-by-step" src="https://github.com/user-attachments/assets/f19abb7a-5e3d-431d-bd72-4062a5c83971" />

Escolha do tipo de máquina
A escolha do tipo de máquina é a primeira decisão do seu projeto de IA, e ela impacta diretamente tanto a velocidade quanto o custo. Antes de selecionarmos a máquina ideal, vamos entender os diferentes tipos de cargas de trabalho que determinarão a escolha do tipo de máquina.

<img width="854" height="233" alt="Workload types" src="https://github.com/user-attachments/assets/aa0e0f09-cb01-4fa1-8aaf-d41efb753365" />

Pré-treinamento de modelos de base
É como ensinar uma ampla variedade de disciplinas gerais a um novo aluno. Utiliza-se um conjunto de dados massivo para criar um novo modelo capaz de realizar tarefas genéricas, como um modelo de linguagem de grande porte (LLM). Esse processo consome muitos recursos e pode levar semanas ou meses.

Ajuste fino (*fine-tuning*) de modelos
Agora, imagine que aquele aluno de conhecimentos gerais está se preparando para uma prova específica. O ajuste fino consiste em pegar um modelo pré-treinado e adaptá-lo para uma tarefa específica e especializada, utilizando um conjunto de dados menor e mais focado. Esse processo é menos exigente do que o pré-treinamento, mas ainda requer recursos significativos.

Inferência ou disponibilização (*serving*)
É a etapa em que o modelo está pronto para ser utilizado pelo público. Trata-se do processo de pegar o modelo treinado e disponibilizá-lo para que usuários ou aplicações possam obter previsões ou gerar respostas. A inferência pode ser realizada em uma única máquina ou distribuída por várias máquinas, dependendo do tamanho do modelo.

Computação de alto desempenho
Envolve o uso de um cluster de computadores para resolver problemas complexos, grandes demais para uma única máquina. Pense nisso como uma equipe de especialistas colaborando em um projeto único e gigantesco, em áreas como pesquisa científica ou simulações de engenharia.

Pré-treinamento de modelos
Como se trata de um processo longo e intensivo, você precisará de uma infraestrutura potente e confiável.

Aceleradores: Para modelos de base (de centenas de bilhões a trilhões de parâmetros), o tipo de máquina recomendado é o A4X. Para modelos grandes (de dezenas a centenas de bilhões de parâmetros), os tipos recomendados são A4 e A3 Ultra. Eles são implantados usando um orquestrador como o Google Kubernetes Engine (GKE) ou o Slurm.

Ajuste fino (fine-tuning) de modelos

O ajuste fino é um processo mais direcionado que exige uma infraestrutura potente, porém ligeiramente menor do que a utilizada no pré-treinamento.

Aceleradores: As melhores máquinas para o ajuste fino de modelos grandes são a A3 Mega ou a A3 High, implantadas com um orquestrador como o GKE ou o Slurm.

Inferência
O objetivo da inferência é disponibilizar seu modelo aos usuários de forma rápida e confiável.

Aceleradores: A escolha aqui depende do tamanho do modelo.

Para inferência em múltiplos hosts (que abrange várias máquinas), utilize os mesmos aceleradores do pré-treinamento: A4, A4 Ultra, A3 Mega ou A3 High.

Para inferência em host único (restrita a uma única máquina), recomendam-se os mesmos tipos de máquina, mas implantados como VMs individuais em vez de um cluster.

Computação de alto desempenho (HPC)
As cargas de trabalho de HPC são altamente flexíveis e podem utilizar diversos tipos de hardware.

Aceleradores: Você pode utilizar qualquer série de máquinas otimizadas para aceleradores ou para computação. A escolha específica depende de quanto da carga de trabalho pode ser processado por uma GPU em comparação com a CPU.

Dentre as três placas abaixo, qual tipo de máquina é recomendado para o pré-treinamento de modelos?

A4X

Key takeaway

Here’s a quick reference table to help you remember the key recommendations for each workload.

Workload

Recommended machine family

Model pre-training

A4, A3 Ultra, A3 Mega, or A3 High

Model fine-tuning

A3 Mega or A3 High

Inference

A4, A3 Ultra, A3 Mega, or A3 High

HPC

Accelerator-optimized or compute-optimized machines


You now have key insights to choose the right machine type for your AI, ML, and HPC workloads.

