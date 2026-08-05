# Rede para treinamento distribuído
O treinamento distribuído envolve o uso de múltiplas unidades computacionais (nós) para treinar um único modelo mais rapidamente e com conjuntos de dados maiores. A arquitetura típica é composta pelo seguinte:

<img width="1503" height="1081" alt="compute storage network" src="https://github.com/user-attachments/assets/c46e9db9-7139-4870-b32f-70f1fe9f054d" />



1 - Nós de computação
Cada nó contém uma CPU e múltiplas GPUs/TPUs. Eles são os principais motores para o treinamento de modelos.

2 - Armazenamento
Um componente central para acessar dados de treinamento e armazenar pontos de verificação (*checkpoints*) do modelo (instantâneos periódicos).

3 - Rede de computação e armazenamento
Uma rede de uso geral que conecta as CPUs e os nós ao sistema de armazenamento.

4 - Rede de GPU/TPU
Uma interconexão de alta velocidade e baixa latência, específica para a comunicação entre os aceleradores (GPUs/TPUs) em diferentes nós de computação. Isso é vital para operações como a sincronização de gradientes.

## Principais padrões de tráfego e considerações de rede

Uma rede eficiente deve atender a dois tipos principais de tráfego de dados, cada um com requisitos distintos: comunicações entre GPUs/TPUs e acesso a dados.

### Comunicações entre GPUs/TPUs (nó a nó)

Refere-se à comunicação de alta frequência entre os aceleradores em diferentes nós de computação, realizada principalmente pela rede dedicada de GPU/TPU. Esse tráfego é essencial para operações de sincronização, como o compartilhamento de gradientes ou pesos do modelo, necessários para garantir que todas as partes do modelo distribuído sejam atualizadas corretamente.

Baixa latência: O atraso entre o envio e o recebimento de dados deve ser mínimo. Uma latência alta retarda diretamente a etapa de sincronização, forçando os aceleradores a aguardar e reduzindo a vazão (*throughput*) geral do treinamento.

Alta vazão: A rede deve ser capaz de transferir rapidamente um grande volume de dados (gradientes) entre os nós para suportar modelos e conjuntos de dados massivos.

Escala: O projeto da rede deve lidar de forma eficiente com a complexidade da comunicação à medida que o número de nós (escala) aumenta, frequentemente utilizando topologias especializadas como *torus* ou *fat-tree* para manter a baixa latência em todo o cluster.

### Acesso a dados (armazenamento para nó e nó para armazenamento)

Envolve o acesso aos dados de treinamento e a gravação de pontos de verificação do modelo, ocorrendo principalmente pela rede de computação e armazenamento. Esse tráfego flui entre o sistema de armazenamento central e os nós de computação.

• Instantâneos periódicos (*snapshots*): Gravação do estado completo do modelo (*checkpoint* ou *snapshot*) no armazenamento, geralmente em intervalos definidos (por exemplo, a cada hora ou época).

• Modelos/conjuntos de dados: Leitura dos pesos iniciais do modelo e, crucialmente, o fluxo (*streaming*) dos dados brutos de treinamento para os nós de computação. Abaixo estão alguns requisitos fundamentais:

Volume/Velocidade: Clique para expandir
O treinamento com petabytes de dados exige que a rede e o sistema de armazenamento forneçam dados com volume e velocidade (largura de banda) consistentemente elevados, para evitar que as GPUs/TPUs fiquem ociosas — uma condição conhecida como "escassez de dados" (*data starvation*).

Consistência: Os *snapshots* devem ser gravados de forma confiável e rápida para garantir uma recuperação ágil em caso de falhas e preservar o progresso realizado.

| Consideration | TPU Version | Data access |
| :--- | :--- | :--- |
| **Primary network** | GPU/TPU network (Dedicated Interconnect) | Compute and storage networks |
| **Key requirements** | Latency, throughput, scale | Volume/speed (bandwidth), reliability |
| **Impact of poor performance** | Slow synchronization, reduced training throughput. | Data-served accelerators, wasted compute time. |
