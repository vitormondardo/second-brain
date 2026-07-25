# Arquitetura de referência
Quando se trata de construir infraestrutura de IA e ML de alto desempenho no Google Cloud, não se trata apenas de criar uma VM. Trata-se de projetar um ecossistema completo que suporte todo o ciclo de vida de suas cargas de trabalho de IA, desde a ingestão de dados até a disponibilização de modelos.

Nesta lição, vamos analisar a arquitetura de infraestrutura de IA no Compute Engine, conforme mostrado no diagrama. Nessa arquitetura, os nós de computação são segmentados em diferentes "espaços de trabalho" ou partições com base em sua finalidade e hardware.

<img width="1779" height="1080" alt="Visuals for Course 4 (3)" src="https://github.com/user-attachments/assets/ae0afbc3-f7ea-4d7b-8286-e507221ed4aa" />

## Conectividade
- Uma arquitetura de IA frequentemente envolve uma configuração de nuvem híbrida, na qual alguns componentes estão em instalações locais (*on-premises*) e outros na nuvem. O segredo para que isso funcione é uma conexão segura e de alta velocidade.

- Cloud Interconnect: É o seu serviço privado e dedicado de conexão por fibra óptica. Ele oferece uma conexão direta, de baixa latência e alta largura de banda entre sua rede local e a rede do Google.

- Cloud VPN: É o seu "carro-forte" seguro. Ele cria um túnel criptografado através da internet pública. Embora seja uma opção mais flexível e econômica, seu desempenho pode variar dependendo do tráfego da internet.

## Nós interativos
- Estas são suas estações de trabalho especializadas. Elas foram projetadas para tarefas administrativas e interação em tempo real.

- Para VMs Linux, você também pode usar um *bastion host*. Um *bastion host* é uma VM do GCE dedicada e protegida (*hardened*) que atua como o ponto de entrada externo único e controlado para a rede privada do cluster.

## Nós administrativos
- O diagrama mostra nós de login SSH e nós administrativos (como um gerenciador de cluster). É neles que seus administradores e cientistas de dados podem fazer login para gerenciar a infraestrutura e realizar tarefas rápidas e sob demanda (*ad hoc*).

## Nós de computação
- Este é o núcleo de processamento do seu pipeline de IA. Trata-se de nós de trabalho (*worker nodes*) de grande escala, otimizados para tarefas intensivas e paralelizáveis. O diagrama mostra partições para diferentes aceleradores.

## Nós de GPU, TPU e CPU
- Nós de GPU: Utilizados para treinamento e inferência, com GPUs especializadas como a NVIDIA H100 ou a partição de GPU H100 do A3.

- Nós de TPU: Utilizados para tarefas de treinamento massivas com paralelismo de dados, com partições TPU v4-16 projetadas para o hardware de ML personalizado do Google.

- Nós de CPU: Os componentes de uso geral que realizam o trabalho pesado, lidando com tarefas como pré-processamento de dados, pré e pós-processamento, e inferência em modelos não otimizados para aceleradores.

## Análise de dados
- Este é o seu hub de insights. O BigQuery é um data warehouse sem servidor (*serverless*) para analisar grandes conjuntos de dados, enquanto o Dataflow é um serviço gerenciado para processamento de dados, transformações e pipelines de ETL (extração, transformação e carregamento). Essas ferramentas são utilizadas tanto para o pré-processamento de dados para treinamento quanto para a análise dos resultados dos seus modelos.

## Suíte de operações
- Este é o seu centro de controle. Ela inclui ferramentas de registro (*logging*) e monitoramento para acompanhar a integridade de seus clusters e cargas de trabalho. Você monitorará métricas importantes, como utilização de GPU, erros de pods e logs do sistema, para identificar e corrigir problemas de forma proativa.

## Armazenamento
- O armazenamento e o acesso eficientes aos dados são fundamentais. Um sistema de armazenamento lento pode criar um gargalo até mesmo para as GPUs mais rápidas. A arquitetura separa o armazenamento de dados frios/de arquivamento do armazenamento de alta velocidade com acesso paralelo.

- Armazenamento em Nuvem (Cloud Storage): Este é o seu repositório principal de dados. Trata-se de um serviço de armazenamento de objetos altamente escalável e durável, adequado para armazenar dados brutos de treinamento, pontos de verificação (*checkpoints*) de modelos e resultados.

- GCS com FUSE: Este é um recurso muito útil que permite montar um *bucket* do GCS como um sistema de arquivos local. É uma maneira simples de tornar os dados do GCS acessíveis aos seus nós de computação, sem a sobrecarga de copiar os dados.

## Sistemas de arquivos paralelos (PFS)
- Esta é a sua área de trabalho temporária de alta velocidade para cargas de trabalho ativas. Trata-se de um sistema de arquivos especializado (como o DDN EXAScaler ou o IBM Spectrum Scale) que oferece vazão extremamente alta e acesso aos dados com baixa latência, distribuindo-os entre vários servidores de armazenamento.

## Aumentando a segurança do cluster com o IAP

Embora um nó de login SSH dedicado (*bastion host*) seja uma maneira válida e robusta de gerenciar o acesso, o Google Cloud oferece uma alternativa gerenciada baseada no modelo *zero-trust* que pode simplificar as operações: o Identity-Aware Proxy (IAP). Em vez de utilizar uma VM exposta publicamente, o IAP controla o acesso às suas VMs internas com base na identidade do usuário e nas políticas do IAM, e não na localização da rede.

O tunelamento SSH via IAP funciona permitindo que o tráfego destinado à porta SSH da sua VM passe pela borda da rede do Google diretamente para o endereço IP interno da VM. Isso significa que seus nós de computação de alto valor podem permanecer totalmente privados, permitindo, ao mesmo tempo, que seus cientistas de dados se conectem com segurança usando um simples comando `gcloud compute ssh`, desde que possuam as permissões IAM corretas.

Sua empresa precisa transferir regularmente conjuntos de dados de vários terabytes de seu data lake local para o Google Cloud Storage para treinamento de modelos em larga escala. Qual opção de conectividade é a mais adequada para essa tarefa?
- Cloud Interconnect

Uma equipe de pesquisa precisa de controle completo e granular sobre sua infraestrutura de IA, incluindo a capacidade de instalar e gerenciar agendadores e sistemas operacionais personalizados. Eles possuem vasta experiência em engenharia de infraestrutura. Qual opção de implantação é a mais adequada às suas necessidades?

Gerenciamento direto (Mecanismo de Computação)


Uma equipe precisa realizar modelagem científica em larga escala em um cluster de máquinas virtuais (VMs). O aplicativo é altamente sensível à latência de rede entre as VMs. Qual recurso do GCE eles devem usar para garantir que as VMs estejam fisicamente localizadas o mais próximas possível umas das outras?

Políticas de Colocação Compacta

