# Rede para instâncias do GCE
Tipos de máquina otimizados para aceleradores frequentemente exigem uma configuração de rede especializada com múltiplas VPCs para lidar com o tráfego de forma eficiente. Isso se aplica independentemente de você estar criando VMs isoladas, instâncias de VM em massa ou grupos de instâncias gerenciados (MIGs). Essa configuração é fundamental, pois permite segregar diferentes tipos de comunicação.

<img width="1038" height="869" alt="K9GXc4zMqRQZ2uh" src="https://github.com/user-attachments/assets/64c574b3-f8ba-4ffa-aaf3-3efc2ad4cdf9" />

## Tráfego geral entre hosts
Rede padrão para operações de sistema e transferência geral de dados.

## Comunicação de alta largura de banda entre GPUs:
Caminhos dedicados e de baixa latência, essenciais para tarefas de alto desempenho que envolvem comunicação direta entre aceleradores em VMs diferentes.

Essa separação garante que o tráfego de alta prioridade e alta largura de banda — crítico para o desempenho do acelerador — não sofra congestionamento causado pelo tráfego geral do sistema. Os requisitos de rede específicos e a configuração ideal variarão de acordo com o tipo de máquina e o acelerador escolhidos.

## Otimização do desempenho da rede

Vamos explorar como minimizar a latência de rede para suas máquinas virtuais (VMs).

Por padrão, ao criar VMs, você especifica apenas a zona delas. Mas e se você precisar que elas estejam muito próximas umas das outras para uma aplicação sensível à latência? É aí que entram essas políticas. Elas ajudam a posicionar suas VMs mais perto umas das outras para melhorar o desempenho da comunicação entre elas.

Imagine que você está tentando conversar com alguém. Se estiverem na mesma sala, a conversa é fácil e rápida (baixa latência). Se estiverem em lados opostos de um estádio lotado, é muito mais difícil e lento (alta latência). Essas políticas funcionam como uma disposição especial de assentos que coloca suas VMs na mesma sala.

### Políticas de posicionamento compacto

As políticas de posicionamento compacto destinam-se a VMs individuais ou a um conjunto de VMs criadas de uma só vez (em lote).

### Políticas de carga de trabalho

As políticas de carga de trabalho são específicas para grupos de instâncias gerenciadas (MIGs).

Políticas de posicionamento compacto para VMs

<img width="1921" height="559" alt="Applied Polices@2x" src="https://github.com/user-attachments/assets/05c4948e-14e8-4f37-9f58-5d8a04cf3d69" />


Considere uma política de posicionamento compacto como uma forma de dizer ao Compute Engine: "Por favor, coloque essas VMs o mais próximas possível umas das outras!" É uma tentativa de deixá-las bem próximas.

Mas e se você precisar que elas fiquem realmente próximas? Para aplicações em que cada milissegundo conta, você pode usar o campo `maxDistance`. Um valor menor para `maxDistance` significa que as VMs ficarão mais próximas, mas também aumenta a probabilidade de algumas VMs não serem criadas se não houver espaço disponível.

## Políticas de carga de trabalho para MIGs

Uma política de carga de trabalho é como um mapa de assentos especializado para seus MIGs. Ela permite definir o tipo de trabalho que suas VMs realizarão e como elas devem ser organizadas fisicamente.

<img width="784" height="785" alt="Workload policies for Managed Instance Groups (MIGs)" src="https://github.com/user-attachments/assets/b3dee1f1-5c53-46fe-a988-caa1f77f3a04" />

Tipo de carga de trabalho
Para necessidades de baixa latência, você especificaria um tipo de carga de trabalho de alto throughput. O Compute Engine tentará, então, posicionar as VMs no MIG o mais próximo possível umas das outras.

Requisitos adicionais
Aqui é onde você pode ser mais específico:

Colocação estrita: Usando o campo `maxTopologyDistance`, você pode definir uma distância física máxima entre as VMs. Se esse requisito estrito não puder ser atendido, o MIG não criará as VMs. É uma abordagem de "tudo ou nada" para obter o máximo desempenho.

Topologia de aceleradores: Destina-se a cargas de trabalho especializadas, como aquelas que utilizam GPUs com uma rede entre aceleradores (por exemplo, VMs A4X com NVLink). Isso garante que as VMs sejam posicionadas de forma a otimizar as conexões de alta velocidade entre os aceleradores.

## Demonstração: Explore políticas de posicionamento compacto para VMs do Compute Engine

Vamos colocar o conceito em prática assistindo a uma demonstração rápida.

Ponto-chave

As políticas de posicionamento ajudam a minimizar a latência de rede ao posicionar fisicamente as VMs mais próximas umas das outras. As políticas de posicionamento compacto aplicam-se a VMs individuais ou em lote e operam em regime de "melhor esforço" (best-effort), enquanto as políticas de carga de trabalho destinam-se a Grupos de Instâncias Gerenciadas (MIGs) e podem impor uma colocação rigorosa para garantir o desempenho máximo.

