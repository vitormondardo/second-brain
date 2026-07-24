Na aula anterior, você explorou a arquitetura interna de uma TPU, compreendendo componentes como TensorCores e MXUs, bem como a organização dos chips em *pods* e *slices*. Agora, o foco muda para a arquitetura em nuvem — especificamente, como essas poderosas TPUs são disponibilizadas e escaladas como recursos computacionais para suas cargas de trabalho de IA.

Compreender esse cenário é fundamental para integrar as TPUs de forma fluida aos seus fluxos de trabalho de aprendizado de máquina.

<img width="1601" height="901" alt="T-AIHYPE_Rise_m3_mw-17@2x" src="https://github.com/user-attachments/assets/47c72f3e-46ec-4497-bb12-e747ceecdc4b" />

## Como usar VMs de TPU

Você tem flexibilidade na forma como utiliza VMs de TPU para suas cargas de trabalho:

<img width="1088" height="383" alt="6dYghcQ2L5HBiTo" src="https://github.com/user-attachments/assets/4a2d5849-e0ef-4a6b-8185-ad8b96de0f1c" />

1. Direto: Você mesmo pode iniciar e gerenciar VMs de TPU, o que lhe confere controle granular sobre o seu ambiente.
2. Serviços gerenciados: Para fluxos de trabalho mais simplificados e integrados, você pode utilizar TPUs por meio de serviços do Google Cloud de nível superior, como GKE e Vertex AI.
3. Agent Platform: A Agent Platform é a plataforma de aprendizado de máquina abrangente e de ponta a ponta do Google Cloud, que simplifica tudo, desde a preparação de dados até a implantação de modelos.
4. GKE: O Google Kubernetes Engine (GKE) é ideal para orquestrar cargas de trabalho de aprendizado de máquina em contêineres em grande escala.

*Independentemente do caminho escolhido, em sua essência, você estará aproveitando a arquitetura subjacente da VM de TPU.

## Dimensionamento de cargas de trabalho: configurações de host único, múltiplos hosts e sub-host

A arquitetura de VM de TPU foi projetada para acomodar uma ampla variedade de tamanhos e complexidades de carga de trabalho, permitindo especificar quantas VMs e chips de TPU seu job utiliza. Um host de TPU é, essencialmente, uma VM executada em um computador físico com hardware de TPU conectado.

Suas cargas de trabalho de TPU podem aproveitar esses hosts em diferentes configurações:

1. Carga de trabalho em host único
Nesse cenário, toda a sua carga de trabalho é executada em uma única VM TPU.

Caso de uso: Ideal para modelos menores, experimentos iniciais ou quando as necessidades computacionais são compatíveis com a capacidade de um único dispositivo TPU.

Limitação: O total de recursos computacionais fica restrito a essa única VM TPU e aos chips conectados a ela.

2. Carga de trabalho em múltiplos hosts
Para tarefas maiores e mais exigentes, uma carga de trabalho de múltiplos hosts distribui o processamento entre várias VMs de TPU. Essas VMs comunicam-se entre si por meio das interconexões de alta velocidade do Google (que conectam pods e fatias de TPU, conforme abordado em nossa aula anterior).

Caso de uso: Essencial para o treinamento de modelos muito grandes e complexos que exigem imenso poder de computação paralela e paralelismo de dados em diversos chips de TPU.

Benefício: Permite tempos de treinamento significativamente mais rápidos para tarefas exigentes, ao distribuir a carga computacional.

3. Carga de trabalho em sub-host
Em alguns cenários, pode não ser necessário utilizar todos os chips disponíveis em uma determinada VM de TPU. Uma carga de trabalho de sub-host utiliza apenas uma parte dos chips presentes em uma única VM de TPU.

Caso de uso: Útil para experimentos menores, atividades de desenvolvimento ou quando se deseja executar várias tarefas pequenas e independentes em uma única VM de TPU para otimizar custos e a utilização de recursos.

Benefício: Oferece um controle mais granular sobre a alocação de recursos, o que pode resultar em um uso mais econômico para tarefas de menor escala.

Agora você tem uma compreensão clara da arquitetura do Cloud TPU, com foco na abordagem moderna de TPU VM e em suas diversas configurações de escalonamento. Esse conhecimento é fundamental para configurar e gerenciar com eficácia suas necessidades de aceleração de IA no Google Cloud.

