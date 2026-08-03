# Carregamento de dados

Durante o carregamento de dados, GPUs ou TPUs importam repetidamente lotes de dados para treinar o modelo. Seu objetivo principal é treinar o modelo com a máxima eficiência, porém ao menor custo. Nesta fase, você pode utilizar um cache para otimizar as tarefas de carregamento de dados, dependendo do tamanho dos lotes e da ordem em que eles são solicitados.

Para otimizar o desempenho e o custo do carregamento de dados, considere os seguintes fatores ou estratégias:

<img width="1365" height="408" alt="8EUtaAhYpCzaw7c" src="https://github.com/user-attachments/assets/c372e7d0-a608-472a-805f-e55d4a309a47" />

1. Tamanho dos dados
O tamanho do seu corpus geral de dados de treinamento e o tamanho de cada conjunto de dados de treinamento individual.

2. Tamanho do arquivo
Os tamanhos típicos das solicitações de leitura (ou seja, a quantidade de dados lida em uma única operação).

3. Padrões de acesso
Como a sua carga de trabalho de treinamento acessa os dados (padrão de acesso de E/S). A escolha da solução de armazenamento adequada depende fortemente de como o seu modelo lerá os dados.

Categorias de padrões de acesso de E/S para cargas de trabalho de treinamento:

- Acesso paralelo e sequencial: Um arquivo é atribuído a um único nó (por exemplo, uma GPU) e lido sequencialmente, do início ao fim.

- Acesso paralelo e aleatório: Um arquivo é atribuído a um único nó e lido de forma aleatória (não sequencial) para criar um lote de amostras.

- Acesso totalmente aleatório: Um nó pode ler qualquer intervalo de qualquer arquivo para criar um lote. Esse é o padrão mais complexo para sistemas de armazenamento suportarem de forma eficiente.
---
# A ferramenta Dataflux Dataset

Modelos de aprendizado de máquina (machine learning) demandam grandes volumes de dados. Para aprender de forma eficaz, eles precisam processar conjuntos de dados massivos, frequentemente compostos por milhões de arquivos pequenos, como imagens ou clipes de áudio. Um grande desafio é a velocidade com que esses dados podem ser carregados do armazenamento para o modelo de ML durante o treinamento. Se a sua GPU ficar ociosa aguardando a chegada dos dados, ela não estará sendo utilizada com eficiência. Esse tempo de inatividade resulta em tempos de treinamento mais longos e custos operacionais mais elevados. Como podemos resolver esse gargalo no carregamento de dados e alimentar nossos modelos mais rapidamente?

Para solucionar isso, você pode utilizar o Dataflux Dataset, uma ferramenta especializada para conjuntos de dados PyTorch, projetada para acelerar o carregamento de dados diretamente do Google Cloud Storage. Trata-se de uma biblioteca de código aberto que acelera significativamente o treinamento, especialmente ao lidar com grandes quantidades de arquivos pequenos. Testes de desempenho (benchmarks) indicam que ela pode acelerar o treinamento em até 3,5 vezes em comparação com outras bibliotecas comuns.

A seguir, apresentamos os principais recursos e benefícios da ferramenta Dataflux Dataset:

## Integração direta com o Cloud Storage
Treine seus modelos usando dados diretamente do Cloud Storage, sem a necessidade de baixá-los primeiro para um disco local.


## Desempenho otimizado
Reduza drasticamente o tempo de ociosidade da GPU e diminua a duração do treinamento, resultando em economia de custos.


## Experiência nativa com PyTorch
Integra-se perfeitamente aos seus fluxos de trabalho existentes no PyTorch como uma primitiva de Dataset. Não é necessário aprender um novo framework.


## Sistema de checkpoint integrado
Salve e carregue checkpoints de modelos diretamente no Cloud Storage com facilidade, simplificando seu fluxo de trabalho.

## Como funciona?

Então, como o Dataflux consegue um ganho de velocidade tão expressivo? Ele utiliza duas otimizações inteligentes.

- Composição dinâmica de objetos. Em vez de buscar milhares de arquivos pequenos, um por um (o que é lento), o Dataflux utiliza um recurso do Cloud Storage para compor dinamicamente muitos arquivos pequenos em alguns objetos temporários maiores, em tempo real. Em seguida, ele baixa esses objetos grandes em alta velocidade e os descompacta na memória. Essa mudança simples reduz drasticamente a latência associada à abertura de muitos arquivos individuais.

- Listagem paralela de alto throughput. Antes de o treinamento começar, o conjunto de dados precisa de uma lista de todos os arquivos que serão utilizados. Para conjuntos de dados com dezenas de milhões de arquivos, essa listagem inicial pode ser muito lenta. O Dataflux emprega um algoritmo sofisticado de *work-stealing* (roubo de trabalho) para realizar essa listagem em paralelo, tornando o processo significativamente mais rápido. Isso significa que até mesmo a primeira execução de treinamento (ou "época") começa muito mais rapidamente.

Combinadas, essas duas técnicas garantem que sua GPU seja alimentada de forma consistente com dados, maximizando a utilização e minimizando o tempo e o custo do treinamento.

## Demonstração: Carregamento de dados em alta velocidade com Cloud Storage e Dataflux

Vamos assistir a uma breve demonstração de como utilizar um conjunto de dados Dataflux no PyTorch para carregar dados de treinamento ou realizar *checkpointing* de forma eficiente.

## Principais pontos

- O carregamento lento de dados é um grande gargalo no treinamento de IA, resultando em desperdício de tempo e dinheiro.

- A ferramenta Dataflux Dataset acelera o treinamento em até 3,5 vezes ao otimizar o carregamento de dados do Google Cloud Storage. Ela alcança essa velocidade por meio da composição dinâmica de objetos e da listagem em paralelo.
--- 
# Criação de pontos de verificação

<img width="1096" height="620" alt="cpt" src="https://github.com/user-attachments/assets/a84b7eb9-7d33-408d-af86-56614c25ee3a" />

Para garantir tolerância a falhas e recuperação rápida em caso de falhas de instâncias, os trabalhos de treinamento devem salvar periodicamente seu estado, um processo chamado checkpoint.

O checkpoint consiste em salvar o estado atual de um modelo de treinamento, incluindo seus pesos e estados do otimizador, para que o treinamento possa ser retomado a partir desse ponto de verificação após uma interrupção. Por exemplo, quando ocorre uma falha de hardware ou preempção, o trabalho é reiniciado, carrega o último ponto de verificação e o processo de restauração, e continua o treinamento.

Um objetivo fundamental é equilibrar a perda de treinamento devido a falhas com o impacto do checkpoint, visto que o checkpoint é uma operação de E/S intensiva. Os checkpoints podem variar de gigabytes (GiB) a terabytes (TiB), afetando diretamente o tempo necessário para salvá-los ou carregá-los. Quanto maior o modelo e o cluster de treinamento, maior o tempo médio entre falhas (MTBF), o que exige uma frequência maior de checkpoint para evitar perdas significativas.

Para otimizar a velocidade e a eficiência de salvar e restaurar pontos de verificação, considere estes três fatores cruciais:

1. Tamanho do modelo
O número de parâmetros no seu modelo de IA determina o tamanho dos arquivos de checkpoint.

2. Frequência de checkpoint
A frequência com que o modelo salva um checkpoint. Uma frequência mais alta oferece melhor tolerância a falhas (você perde menos progresso), mas aumenta os custos de armazenamento e pode reduzir a velocidade geral do treinamento. Normalmente, você só precisa manter alguns checkpoints de cada vez.

3. Tempo de recuperação de checkpoint
Seu objetivo é minimizar o tempo de recuperação de checkpoint — o tempo necessário para carregar um checkpoint e retomar o treinamento após uma falha. Esse tempo é afetado pelo tamanho do checkpoint, pelo desempenho do sistema de armazenamento e pela largura de banda de rede disponível.

## Técnicas de checkpoint
A técnica ideal de checkpoint depende de como você pondera as vantagens e desvantagens entre sincronização, centralização e seu meio de armazenamento.

Selecione as setas para saber mais sobre as três vantagens e desvantagens.

### Síncrono versus assíncrono

- Síncrono: Todo o treinamento é pausado até que o *checkpoint* seja totalmente gravado no armazenamento. Isso garante a integridade dos dados, mas aumenta o tempo de pausa do treinamento.

- Assíncrono: Reduz as pausas no treinamento. Por exemplo, após os dados da memória da GPU serem transferidos para o *host*, o treinamento pode continuar enquanto o *host* grava os dados no armazenamento final de forma assíncrona. Essa abordagem é preferível para a criação frequente de *checkpoints*.

### Centralizado versus distribuído

- Centralizado: Um *worker* (por exemplo, o *rank* 0) coleta todos os pesos e grava o *checkpoint* completo no armazenamento.

- Distribuído: Cada nó grava sua parte do arquivo de *checkpoint* diretamente no armazenamento, melhorando o paralelismo e a velocidade de gravação.

### Armazenamento em rede versus disco em RAM (RAM disk)

- Armazenamento em rede (por exemplo, *Persistent Disk*): Persistente, porém mais lento e com maior capacidade. A frequência de criação de *checkpoints* é tipicamente da ordem de dezenas de minutos.

- Disco em RAM: Não persistente, mais rápido e com menor capacidade. Os *checkpoints* são replicados dentro do *cluster* para fins de recuperação. A frequência de criação de *checkpoints* pode ser da ordem de segundos ou minutos.

O gerenciamento de checkpoints envolve gravações frequentes (salvamento de novos checkpoints), exclusões ocasionais (remoção dos antigos) e leituras pouco frequentes (carregamento de um checkpoint em caso de falha). A maneira específica como os checkpoints são criados e carregados depende do framework:

• TensorFlow Core: Consulte a documentação sobre checkpoints de treinamento (abre em uma nova guia).

• PyTorch: Consulte a documentação sobre salvamento e carregamento de modelos (abre em uma nova guia).

Managed Lustre

Escolha o Managed Lustre se a sua principal preocupação for velocidade e *checkpointing* de alto desempenho. Confira os principais recursos:

- Gravações rápidas: Mais de 12 GiB/s por VM A3.

- Desempenho consistente: Gravações em menos de 40 segundos e restaurações em menos de 12 segundos para um modelo de 60 bilhões de parâmetros em até 256 nós.

- Escalabilidade de armazenamento: Gravações de *checkpoint* muito rápidas, superiores a 20 GiB/s por VM A3.

Por que escolhê-lo?

- Você precisa de *checkpointing* frequente e de alto desempenho: O Managed Lustre foi projetado para alto rendimento (*throughput*), permitindo gravações de *checkpoint* muito rápidas e maximizando a vazão por VM.

- Seus dados de treinamento já são carregados a partir do Managed Lustre: Usar o mesmo armazenamento tanto para o carregamento de dados quanto para o *checkpointing* simplifica seu fluxo de trabalho e maximiza a utilização de recursos, minimizando o tempo ocioso de aceleradores, que é custoso.

Dicas para otimização de armazenamento e custos:

- Você pode manter seus *checkpoints* diretamente na instância persistente do Managed Lustre.

- Para economizar custos, você pode exportar periodicamente os *checkpoints* para o Cloud Storage.

- Utilize um cluster de armazenamento tanto para leituras de treinamento quanto para gravações de *checkpoint*, visando maximizar a utilização da GPU.

- Selecione quais *checkpoints* deseja copiar para o Cloud Storage para armazenamento de longo prazo.

## Cloud Storage

Escolha o Cloud Storage se a sua prioridade for a durabilidade e a disponibilidade dos dados em vez da velocidade bruta de armazenamento.

Por que escolhê-lo?

- Você prioriza a durabilidade dos dados e a alta disponibilidade: o Cloud Storage oferece excelente resiliência e acessibilidade para o estado salvo.

- Seus dados de treinamento já são acessados ​​via Cloud Storage FUSE: alinhar o armazenamento de checkpoints ao acesso aos dados simplifica sua configuração.

Dicas para otimizar o desempenho:

- Use o Cloud Storage FUSE com namespaces hierárquicos (HNS): ative o HNS para aproveitar a operação rápida de renomeação atômica — frequentemente usada ao salvar checkpoints — e para salvá-los de forma assíncrona. Você pode obter uma gravação de checkpoint até 30 vezes mais rápida com o HNS.

- Use um bucket separado: sempre armazene seus checkpoints em um bucket do Cloud Storage separado do seu conjunto de dados de treinamento para evitar a exposição acidental de informações confidenciais durante a disponibilização do modelo. Além disso, considere usar salvamentos assíncronos para evitar o bloqueio do job.

- Gerenciamento de latências de gravação: se um upload travar, o Cloud Storage FUSE tentará novamente de forma automática após 10 segundos, ajudando a reduzir latências de gravação de cauda (tail-end latencies).

- Cloud Storage Connector para PyTorch: você pode obter uma gravação de checkpoint até 5 vezes mais rápida com o Cloud Storage Connector para PyTorch em comparação com uma configuração padrão.

- Cloud Storage FUSE: oferece gravação por streaming com buffer em memória, sem necessidade de staging completo do arquivo.

## Teste seus conhecimentos

Você está projetando uma estratégia de *checkpointing* (pontos de verificação) para uma tarefa de treinamento de IA em larga escala. Seu objetivo é realizar *checkpoints* com frequência para melhorar a tolerância a falhas, mas você deseja minimizar o "tempo ocioso" em que o treinamento é interrompido para salvar dados. Qual abordagem minimiza melhor essas pausas no treinamento?

- *Checkpointing* assíncrono
Selecionado corretamente


Correto
O *checkpointing* assíncrono reduz as pausas no treinamento porque permite que o treinamento continue imediatamente após a transferência dos dados da memória da GPU para o host, enquanto o sistema grava os dados no armazenamento em segundo plano.

## Principais pontos

O *checkpointing* garante a tolerância a falhas no treinamento de IA ao salvar periodicamente o estado do modelo, permitindo que as tarefas se recuperem rapidamente de interrupções, como falhas de hardware. Para otimizar o desempenho, é fundamental escolher a solução de armazenamento adequada — como o Managed Lustre, para velocidade, ou o Cloud Storage, para durabilidade — e equilibrar a frequência dos *checkpoints* com a sobrecarga gerada pelas pausas no treinamento.
