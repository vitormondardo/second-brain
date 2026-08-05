# Considerações sobre rede para treinamento
Ao treinar grandes modelos de ML — especialmente LLMs e outras cargas de trabalho distribuídas —, a velocidade e a confiabilidade da conexão de rede impactam diretamente o tempo de treinamento. Uma conexão lenta ou instável entre as GPUs pode anular, na prática, a velocidade do próprio hardware.

Esta lição aborda as principais considerações de rede para a execução de treinamento distribuído de modelos de alto desempenho no Google Cloud.

<img width="2401" height="721" alt="4nZ6P695BDH9EZ8" src="https://github.com/user-attachments/assets/e5ccf20c-74d0-48ec-a428-8c683f995fa8" />

- Confiabilidade: A base do treinamento
A confiabilidade garante que os dados sejam transferidos de forma consistente e ininterrupta, o que é essencial para tarefas de treinamento distribuído de longa duração.

Caminho de dados direto entre GPUs/TPUs: O requisito mais crítico é uma conexão direta e de alta velocidade entre dispositivos aceleradores (GPUs ou TPUs). Isso evita passar pela CPU do host para a comunicação entre dispositivos, reduzindo a latência e maximizando a taxa de transferência (throughput) para operações como a troca de gradientes.

Janelas de manutenção/atualizações de software: Esteja atento a como as atualizações da plataforma são gerenciadas. Para treinamentos de missão crítica, busque plataformas que ofereçam recursos para minimizar interrupções durante a manutenção.

- Alto desempenho e *goodput*
O *goodput* refere-se à taxa de dados úteis efetivamente entregues, excluindo o *overhead* (sobrecarga). Alcançar um *goodput* elevado é o objetivo para um treinamento rápido.

Benefício para o treinamento

Alta largura de banda: Permite a transferência massiva de dados entre GPUs para modelos complexos e distribuídos.

Clusters de GPU/TPU de baixa latência: Garante que pacotes de dados não sejam descartados, evitando retransmissões custosas que desaceleram o treinamento. Baixa latência significa uma comunicação geral mais rápida.

Conectividade em malha completa (*full mesh*): Cada GPU/TPU pode se comunicar diretamente com todas as outras, o que é crucial para operações eficientes de comunicação coletiva (por exemplo, *all-reduce*) em treinamentos distribuídos.

Topologias ajustáveis: A capacidade de configurar o layout da rede para tráfego específico de treinamento e *checkpointing* ajuda a otimizar o fluxo de dados e acelerar a conclusão da tarefa.

O RDMA permite que um acelerador acesse diretamente a memória de outro acelerador, sem envolver o sistema operacional ou a CPU. Isso resulta em uma comunicação de latência extremamente baixa e alta taxa de transferência. Isso é frequentemente disponibilizado por meio de um perfil de rede VPC com RDMA.

- Escalabilidade: De clusters a *exascale*
A escalabilidade envolve a capacidade de expandir eficientemente o ambiente de treinamento para lidar com modelos e conjuntos de dados maiores.

Clusters escaláveis ​​para LLMs: LLMs exigem clusters com centenas ou até milhares de aceleradores. A arquitetura de rede deve ser projetada para suportar essa escala sem degradação de desempenho.

Gerenciamento de pools de GPU com mega-clusters do GKE: O uso do GKE para gerenciar grandes pools de GPUs simplifica a implantação e garante a utilização eficiente de recursos para tarefas de treinamento massivas e complexas.

40 mil *endpoints* de GPU por VPC para treinamentos de grande porte: A infraestrutura subjacente deve suportar um número excepcionalmente alto de *endpoints* (aceleradores individuais) em uma única rede, permitindo tarefas de treinamento que abrangem vastos recursos de hardware. - Observabilidade
A observabilidade proporciona visibilidade do tráfego de rede para a solução de problemas de desempenho.

Logs de fluxo com anotações RDMA: O acesso a logs detalhados de fluxo de rede — especificamente aqueles anotados com informações de RDMA — é fundamental. Isso permite identificar gargalos na rede e verificar se o canal RDMA de alta velocidade está sendo utilizado corretamente.
---
Abordamos as considerações e tecnologias essenciais que dão suporte à fase de treinamento. Este curso focará especificamente em uma análise aprofundada do treinamento distribuído e das placas de rede (NICs) Titanium.
