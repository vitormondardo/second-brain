# Soluções de armazenamento e pipeline de dados para IA

O desempenho do seu pipeline de dados de IA depende diretamente do armazenamento utilizado. A solução de armazenamento que você implementar poderá acelerar ou criar gargalos em todas as etapas do projeto de IA, desde a preparação dos dados até a inferência.

Vamos explorar como as demandas de cada etapa do seu pipeline de dados de IA podem orientar suas decisões sobre armazenamento.

<img width="1388" height="665" alt="dataPipeline_NOPROCESS" src="https://github.com/user-attachments/assets/2b437dac-10c6-4582-bda4-9a7a6fd2de0d" />

1. Preparação de dados
Esta etapa inicial envolve a coleta, a validação e o pré-processamento de dados brutos. Essa fase exige capacidade de armazenamento capaz de lidar com grandes volumes de dados não estruturados com alta vazão, o que é essencial para limpar e preparar os dados de forma eficiente para as etapas subsequentes.

2. Treinamento
Durante esta fase intensiva, dados preparados são utilizados para treinar o modelo. Uma solução de armazenamento de altíssimo desempenho é fundamental para alimentar rapidamente grandes volumes de dados às unidades de processamento gráfico (GPUs), permitindo um treinamento eficiente do modelo.

3. Inferência
Após o treinamento, o modelo é utilizado para realizar previsões ou tomar decisões com base em novos dados. A solução de armazenamento nesta etapa deve apresentar alta capacidade de resposta, fornecendo rapidamente novos dados ao modelo para a inferência. Os resultados desse processo podem, então, ser reaproveitados para treinamentos futuros, estabelecendo um ciclo de feedback valioso.

4. Entrega
Esta etapa final envolve a entrega dos resultados do modelo ao usuário final. Por exemplo, imagens geradas por um modelo como o Midjourney exigem armazenamento confiável e escalável para lidar com inúmeras solicitações de entrega e o posterior arquivamento.

5. Proteção
Após a entrega, tanto os dados quanto os modelos requerem proteção. Isso inclui a criação de arquivos e backups para garantir a integridade e a segurança de todo o sistema de IA.
---
## Soluções de armazenamento

O Google Cloud oferece uma gama abrangente de soluções de armazenamento para dar suporte a todas as fases do seu pipeline de dados de IA: desde a escalabilidade massiva do Cloud Storage para data lakes até o desempenho extremo do Managed Lustre para treinamento de modelos e os recursos de alta velocidade e baixa latência do Hyperdisk ML para inferência e entrega de modelos.

<img width="1501" height="679" alt="fourStorageTypes" src="https://github.com/user-attachments/assets/e9fc2c9c-4768-4281-be11-f02a2155b66b" />

### Cloud Storage
O Google Cloud Storage é um serviço de armazenamento de objetos projetado para o armazenamento massivo e plano de dados não estruturados. É um repositório vasto e flexível para qualquer tipo de conteúdo, desde documentos e imagens até conjuntos de dados completos, sendo ideal para informações que não se adequam a formatos rígidos de banco de dados.

Desempenho: Oferece capacidade substancial de leitura/escrita, lidando com terabytes por segundo (TB/s), embora apresente latência mais alta em comparação com outros tipos de armazenamento.

Caso de uso: Perfeito para *data lakes*, backups e fornecimento de conteúdo web, priorizando o volume massivo de dados em detrimento do acesso ultrarrápido a arquivos individuais.

Custo: Geralmente é a solução com melhor custo-benefício para a retenção de dados em larga escala.

### Rapid Storage
O Rapid Storage é o sistema de arquivos interno do Google, operando em nível de cluster zonal, que coloca o armazenamento e os aceleradores de IA no mesmo *bucket* zonal. Ao operar sobre o Colossus, o Rapid Storage evita a latência típica do armazenamento regional, que ocorre quando aceleradores e dados residem em zonas diferentes.

Desempenho: O Rapid Storage oferece uma latência cinco vezes menor para leituras e escritas aleatórias em comparação com outros grandes provedores de nuvem (*hyperscalers*). Combinado com uma vazão de até 6 TB/s por *bucket* e até 20 milhões de consultas por segundo (QPS), ele permite treinar modelos de IA com novos níveis de desempenho.

Caso de uso: Com o protocolo *stateful* do Rapid Storage, é possível estabelecer um fluxo de dados no início do treinamento antes de executar leituras de intervalos (*ranged-reads*) massivamente paralelas em velocidades de sub-milissegundo. Isso ajuda a garantir que os aceleradores não fiquem ociosos devido à latência de armazenamento.

Custo: O Rapid Storage é fundamental para fornecer serviços confiáveis ​​a bilhões de usuários; seus recursos sofisticados de alocação em SSD ajudam a manter os custos baixos e o desempenho elevado, adaptando-se automaticamente às variações na carga de trabalho.

### Managed Lustre
O Managed Lustre é um sistema de arquivos persistente (PFS) de alto desempenho, otimizado para baixa latência e alta vazão. Ele foi projetado para cargas de trabalho de computação de alto desempenho (HPC) e IA/ML, distribuindo dados entre vários servidores para permitir acesso simultâneo e um desempenho drasticamente superior.

Desempenho: Oferece armazenamento persistente com latência muito baixa, atingindo velocidades agregadas de até 1 terabyte por segundo (TB/s).

Caso de uso: É adequado para cargas de trabalho de alto desempenho que exigem armazenamento de dados confiável e de longo prazo. 

### Hyperdisk ML
O Hyperdisk ML é um serviço de armazenamento em blocos especializado e otimizado para IA/ML. O armazenamento em blocos trata os dados como blocos de tamanho fixo, sem metadados adicionais, sendo comumente utilizado como discos rígidos virtuais.

Desempenho: Otimizado para cargas de trabalho de ML, oferecendo alto desempenho mesmo com capacidades menores. É um serviço de "leitura persistente" (somente leitura), ideal para a fase de inferência de modelos de IA, atingindo velocidades agregadas de até 1,2 TB/s.

Caso de uso: Ideal para as etapas de inferência e disponibilização (serving) do pipeline de IA, nas quais previsões em tempo real exigem acesso a dados extremamente rápido e de baixa latência.

Compreender essas opções de armazenamento é fundamental para otimizar o desempenho, gerenciar custos e agilizar as operações em todo o seu pipeline de dados de IA. Seja priorizando o gerenciamento unificado para obter simplicidade ou uma abordagem híbrida para demandas específicas, o Google Cloud oferece a flexibilidade necessária para adaptar suas escolhas de armazenamento a cada etapa da sua jornada de IA/ML.
---
## Escolhendo a solução de armazenamento ideal

A tabela de decisão abaixo ajuda você a escolher o serviço de armazenamento do Google Cloud adequado para o seu projeto de IA, simplificando decisões complexas e concentrando-se em três fatores principais: capacidade, vazão e latência. Ao responder "Sim" ou "Não" às perguntas, você pode identificar a solução de armazenamento ideal para as suas necessidades.

<img width="1335" height="817" alt="decisionTable" src="https://github.com/user-attachments/assets/9e96f67c-db2c-4b94-99af-96d3bf0b2f1b" />

### Requisitos de capacidade de armazenamento para treinamento
- Menos de 100 TiB: Para conjuntos de dados com menos de 100 terabytes, a maioria das opções de armazenamento do Google Cloud é adequada.

- Mais de 100 TiB: Para conjuntos de dados que excedem 100 terabytes, suas opções se limitam a Cloud Storage, Rapid Storage e Managed Lustre, pois esses serviços são projetados para volumes massivos de dados na escala de petabytes.


### Requisitos de vazão (throughput)
- Menos de 125 GB/s: Todos os serviços são adequados se suas necessidades de velocidade de transferência de dados forem inferiores a 125 gigabytes por segundo.

- Menos de 1 TB/s: Para velocidades inferiores a 1 terabyte por segundo, todas as opções permanecem disponíveis.

- Mais de 1 TB/s: Se você precisar de uma vazão muito alta, superior a 1 terabyte por segundo, suas opções se restringem a Cloud Storage e Cloud Storage Perf*.

- Mais de 2,4 TB/s: Para a vazão mais alta possível, apenas o Cloud Storage Perf* pode atender às suas demandas.


### Requisitos de latência (para arquivos pequenos e leituras aleatórias)
- A latência refere-se à rapidez com que um único dado pode ser acessado, o que é fundamental para tarefas como o treinamento de modelos com muitos arquivos pequenos.

- Menos de 30 ms: Todos os serviços oferecem esse nível de baixa latência.

- Menos de 10 ms: Para acessos mais rápidos, inferiores a 10 milissegundos, considere o Rapid Storage, o Managed Lustre e o Hyperdisk ML. O Cloud Storage padrão não é recomendado neste caso.

- Menos de 1 ms: Para latência ultrabaixa (inferior a um milissegundo), suas únicas opções são os serviços especializados de armazenamento de arquivos e blocos: Rapid Storage, Managed Lustre e Hyperdisk ML.

Teste seus conhecimentos

Leia cada pergunta e pense na sua resposta. Em seguida, selecione o cartão correspondente para ver a resposta correta e a explicação.

Selecione os cartões correspondentes para verificar sua resposta.

Sua equipe está executando uma tarefa de análise de dados em larga escala com um petabyte de dados brutos e não estruturados. Qual serviço de armazenamento você deve considerar?

Resposta: Armazenamento em Nuvem

Ele foi projetado para grandes quantidades de dados não estruturados, é altamente escalável e é a opção mais econômica para ingestão e armazenamento iniciais de dados.

Uma equipe de pesquisa está usando um cluster HPC para executar simulações que exigem armazenamento persistente extremamente rápido. Qual serviço de armazenamento eles devem considerar?

Resposta: Lustre Gerenciado

Ele fornece armazenamento persistente com latência muito baixa, atingindo velocidades agregadas de até 1 TB/s, o que é perfeito para tarefas de computação de alta velocidade.

Você precisa implantar um modelo de IA para inferência em tempo real. O modelo é grande e você precisa fornecer previsões o mais rápido possível. Qual serviço de armazenamento você deve considerar?

Resposta: Hyperdisk ML

Esta solução de armazenamento em blocos, com alto desempenho e recursos persistentes de leitura somente, é ideal para servir grandes modelos de IA para inferência.

Você tem um projeto de IA com um conjunto de dados de 120 TiB. Seu principal requisito é lidar com essa grande capacidade. Quais serviços de armazenamento você deve considerar?

Resposta: Você pode considerar o Cloud Storage, o Rapid Storage ou o Managed Lustre. Todas essas opções são adequadas para conjuntos de dados maiores que 100 TiB.

Seu projeto tem um conjunto de dados pequeno (50 TiB) e precisa atingir uma taxa de transferência superior a 1,5 TB/s. Com base na árvore de decisão, quais opções de armazenamento são adequadas para esse requisito?

Resposta: Cloud Storage e Rapid Storage são as únicas duas opções que atendem ao requisito de conjunto de dados pequeno e taxa de transferência superior a 1 TB/s.

Uma carga de trabalho de HPC requer latência muito baixa (inferior a 1 ms) e taxa de transferência superior a 1 TB/s. Qual é a melhor opção de armazenamento?
Resposta: Não existe uma única opção que satisfaça ambos os critérios. Você pode reavaliar seus requisitos ou considerar a combinação de soluções.

Resposta: Não existe uma única opção que satisfaça ambos os critérios. Você pode reavaliar seus requisitos ou considerar a combinação de soluções.
