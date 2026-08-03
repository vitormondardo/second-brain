# Cloud Storage FUSE
O Cloud Storage FUSE é uma ferramenta poderosa que permite utilizar o Cloud Storage como se fosse um sistema de arquivos local, tornando o processo mais fácil e eficiente para cargas de trabalho de aprendizado de máquina (ML). Ele atua como uma ponte entre os recursos de armazenamento de objetos do Cloud Storage e as necessidades de sistemas de arquivos tradicionais das aplicações de ML.

Basicamente, o Cloud Storage FUSE permite montar um bucket do Cloud Storage como uma pasta local. Isso significa que, em vez de utilizar APIs específicas da nuvem, suas aplicações podem ler e gravar arquivos diretamente no Cloud Storage usando comandos padrão de manipulação de arquivos, como se estivessem em um disco local. Essa característica o torna uma excelente solução para garantir a portabilidade, pois não é necessário alterar o código existente para que ele funcione na nuvem. Embora possa ser instalado como um pacote Linux, em implantações gerenciadas e de grande escala, ele é frequentemente utilizado em conjunto com o driver CSI do Google Kubernetes Engine (GKE).

<img width="711" height="785" alt="fuseDiagram" src="https://github.com/user-attachments/assets/3b953a6d-4a28-48a3-bef7-70ed7e7fdd7e" />

Este diagrama mostra como isso funciona:

- Seus recursos de computação (TPUs/GPUs) em plataformas como Compute Engine, GKE ou Vertex AI utilizam um caminho de arquivo local (por exemplo, `/bucket1/`) para acessar dados.

- Esse caminho local é gerenciado pelo Cloud Storage FUSE, que traduz os comandos de arquivo em APIs de objetos do Cloud Storage para ler ou gravar nos buckets do GCS.

- O cache do GCSfuse — que pode residir em um SSD local, em um disco persistente ou na memória — atua como intermediário para proporcionar benefícios de desempenho.

--- 
## Otimizações para cargas de trabalho de treinamento e inferência

O Cloud Storage FUSE inclui as seguintes otimizações de desempenho para cargas de trabalho de treinamento e inferência de modelos de IA.
---
<img width="1154" height="445" alt="fuseWorkloads" src="https://github.com/user-attachments/assets/aa7b0ae0-27d3-4954-9c75-8284b81ffac3" />

1. Treinamento
O treinamento de aprendizado de máquina (ML) geralmente envolve a leitura dos mesmos dados várias vezes. O cache de arquivos do Cloud Storage FUSE armazena os dados acessados ​​com frequência em um disco local, como um SSD local (LSSD) incluído em determinados tipos de máquinas com GPU.

Esse cache local acelera significativamente as leituras repetidas, resultando em um treinamento até 2,3 vezes mais rápido, reduzindo a necessidade de buscar dados do Cloud Storage a cada vez.

2. Disponibilização
Ao disponibilizar um modelo para inferência, ele precisa ser carregado rapidamente. O Cloud Storage FUSE otimiza isso usando download paralelo. Ele cria automaticamente vários workers para buscar diferentes partes do modelo simultaneamente, o que pode tornar os tempos de carregamento do modelo até 9 vezes mais rápidos.

3. Checkpoint
Você obtém gravações de checkpoint 40% mais rápidas com gravações em streaming, que não exigem mais que o arquivo inteiro seja armazenado localmente primeiro. Você também obtém gravações de checkpoint 30 vezes mais rápidas com renomeações atômicas de diretórios com Namespace Hierárquico (HNS).

Essas são melhorias significativas, pois reduzem o tempo e os recursos necessários para salvar pontos de verificação, tornando seu processo de treinamento mais resiliente a interrupções.
---
## Teste seus conhecimentos

Leia o cenário e pense na melhor solução. Em seguida, selecione o flashcard para ver a resposta e a explicação corretas.

- Um cientista de dados está executando um trabalho de treinamento de IA que precisa ler um conjunto de dados de 100 GiB várias vezes. Ele está usando uma máquina com GPU e um SSD local. Qual recurso específico do Cloud Storage FUSE é mais benéfico para essa carga de trabalho e por quê?

Resposta:
O recurso mais benéfico é o cache de arquivos. Ele usará o SSD local para armazenar o conjunto de dados após a primeira leitura. Para todas as leituras subsequentes, os dados serão acessados ​​do cache local rápido em vez do Cloud Storage, o que reduz significativamente o tempo de carregamento de dados e acelera o processo de treinamento.

---
## Recurso do Cloud Storage Fuse:
Namespace hierárquico (HNS)

O recurso de namespace hierárquico (HNS) altera fundamentalmente a forma como o Cloud Storage organiza os dados.

• Estrutura semelhante a um sistema de arquivos: O HNS organiza os dados em um namespace hierárquico semelhante a um sistema de arquivos. Isso faz com que o Cloud Storage deixe de ser apenas um armazenamento de objetos (que utiliza caminhos planos) para passar a reconhecer e gerenciar uma hierarquia real.

• Habilita um novo recurso de "pasta" e APIs relacionadas: Permite criar, excluir, listar e renomear pastas diretamente, tratando-as como entidades de primeira classe em vez de apenas partes do nome de um objeto.

• Ativação: O HNS é um recurso de nível de bucket e só pode ser habilitado durante a criação do bucket.

O HNS organiza os dados do bucket em um namespace hierárquico real, semelhante a um sistema de arquivos. O diagrama ilustra visualmente esse conceito, mostrando um bucket que contém pastas explícitas (A, B, C, etc.) que estruturam os objetos subjacentes (objeto A, objeto B, etc.) em uma hierarquia clara.

Ao contrário dos buckets tradicionais do Cloud Storage, que simulam pastas usando prefixos nos nomes dos objetos, o HNS cria uma hierarquia formal e navegável.

<img width="611" height="753" alt="hns" src="https://github.com/user-attachments/assets/258cfce5-cfc3-47fc-be2b-d3e4be4fdd99" />

Habilitar o namespace hierárquico (HNS) é uma prática recomendada fundamental para o Cloud Storage em grande escala, proporcionando um aumento de 8 vezes nas QPS (consultas por segundo) e o recurso de renomeação atômica de pastas — pré-requisitos para implantações bem-sucedidas, confiáveis ​​e de alto throughput do GCSfuse em aceleradores potentes. Confira os principais benefícios:

### Experiência de sistema de arquivos aprimorada com o GCSfuse
O HNS fornece ao GCSfuse uma estrutura de pastas real com a qual interagir, fazendo com que a montagem do GCSfuse se comporte de forma mais semelhante a um sistema de arquivos POSIX padrão. O GCSfuse é o cliente que oferece ao sistema operacional acesso a um bucket do GCS como se fosse um sistema de arquivos local. No lado do servidor, o HNS otimiza o bucket para operações de arquivo (como renomeação e listagem). Juntos, eles proporcionam a experiência de armazenamento de alto desempenho e compatível com POSIX que os frameworks de IA/ML exigem.


### QPS inicial até 8 vezes maior
O HNS oferece QPS (consultas por segundo) iniciais até 8 vezes maiores para operações de leitura e gravação em comparação com buckets tradicionais de namespace plano. Esse é o ganho de desempenho mais crítico para que cargas de trabalho intensivas em dados possam escalar para centenas ou milhares de nós, conforme enfatizado nas práticas recomendadas do GCSfuse (onde o HNS foi recomendado para obter esse aumento de 8 vezes nas QPS).


### Checkpointing rápido com renomeação atômica de pastas
O HNS permite a renomeação atômica de pastas. Isso é essencial para um checkpointing rápido e confiável em tarefas de treinamento de aprendizado de máquina em larga escala (conforme indicado nas práticas recomendadas de ajuste do GCSfuse), pois garante que um diretório possa ser substituído instantaneamente e por completo, evitando perda ou corrupção de dados durante as operações de salvamento.


### Listagem rápida
O recurso permite a listagem rápida de todas as pastas no bucket ou dentro de uma pasta específica. Em implantações de grande escala, as consultas de metadados (listagem de conteúdo) podem se tornar um gargalo. Essa funcionalidade complementa a recomendação de usar pré-busca de metadados e um cache de metadados infinito: o HNS torna a estrutura subjacente mais fácil e rápida de listar, enquanto as camadas de cache mantêm essas informações locais e disponíveis instantaneamente.


### IAM de granularidade fina por meio de pastas gerenciadas
O HNS possibilita o uso de IAM (Gerenciamento de Identidade e Acesso) de granularidade fina por meio de pastas gerenciadas, permitindo que políticas de segurança sejam aplicadas diretamente a um recurso de pasta. Isso é vantajoso para ambientes complexos com múltiplos usuários ou equipes, comuns em organizações de grande porte.
