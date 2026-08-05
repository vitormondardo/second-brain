#Ingestão de dados pela Cross-Cloud Network

Você precisa de um backbone de rede capaz de atender às demandas de ingestão de dados e processamento intenso. Seja treinando um modelo de linguagem de grande porte (LLM) ou executando inferência (geração de previsões), você precisa de uma rede que ofereça velocidade, confiabilidade e segurança para a movimentação massiva de dados e o processamento intenso.

## Cloud Interconnect

As cargas de trabalho de IA frequentemente exigem a movimentação de terabytes — ou até mesmo petabytes — de dados. Muitas vezes, esses dados essenciais para treinamento e ajuste fino (fine-tuning), ou para a Geração Aumentada por Recuperação (RAG), estão dispersos entre sistemas locais (*on-premises*) e diferentes ambientes de nuvem (uma estratégia *multi-cloud*). Para tornar esses dados acessíveis aos seus modelos de IA, é necessário acessá-los remotamente ou copiá-los rapidamente.

Para enfrentar esse desafio, novas soluções de rede concentram-se em conectividade segura e de alta largura de banda:

### Cloud Interconnect e Cross-Cloud Interconnect
Esses serviços foram projetados para acelerar a ingestão de seus conjuntos de dados de IA. Ofertas mais recentes, como o Cloud Interconnect de 400G e o Cross-Cloud Interconnect, fornecem a conectividade de alta velocidade necessária para conectar seus data centers locais ou outros ambientes de nuvem diretamente ao Google Cloud. Essa largura de banda massiva reduz significativamente o tempo necessário para transferir seus conjuntos de dados iniciais.


### Cross-Cloud Network
Essa estrutura oferece conectividade do tipo "qualquer um com qualquer um" (any-to-any) centrada em serviços, construída sobre a infraestrutura global do Google. Ela simplifica a criação e a montagem de aplicações distribuídas em múltiplas nuvens — um recurso fundamental, visto que um relatório da IDC indicou que 66% das empresas apontam a IA generativa (gen AI) e IA/ML como os principais casos de uso para redes multinuvem (multi-cloud networking).

## Reconhecimento de aplicações no Cloud Interconnect

Muitas soluções tradicionais de interconexão não conseguem priorizar o tráfego com base nas necessidades das aplicações, forçando você a superdimensionar a largura de banda ou a depender de dispositivos de rede autogerenciados. Essas soluções alternativas podem ser caras e complexas.

O tráfego entre nuvens (cross-cloud) provém de uma combinação de aplicações críticas e não críticas para o negócio, aplicações de usuário final, aplicações corporativas, processamento em lote (batch), backups e muito mais. Como a largura de banda tende a ser um recurso compartilhado, aumentos na demanda ou picos de tráfego podem ser imprevisíveis.

Quando ocorre congestionamento, se não houver priorização, todos os pacotes são tratados da mesma forma; isso significa que o tráfego de aplicações não críticas pode causar perda de pacotes do tráfego crítico, resultando em transações incompletas e usuários finais insatisfeitos.

É aí que entra o recurso de reconhecimento de aplicações (application awareness) do Google Cloud para o Cloud Interconnect.

## O que é o reconhecimento de aplicações
O reconhecimento de aplicações no Cloud Interconnect permite priorizar o tráfego dentro da conexão, garantindo que suas cargas de trabalho mais importantes recebam o tratamento diferenciado de que necessitam, mesmo durante horários de pico.

### Como funciona
O reconhecimento de aplicações no Cloud Interconnect permite classificar o tráfego com base nas prioridades do negócio. Você pode marcar seus fluxos de tráfego para associá-los a diferentes classes. Em seguida, esse tráfego é organizado em uma das seis classes predefinidas. O recurso oferece políticas flexíveis, seja para garantir prioridade estrita para que os dados mais importantes passem primeiro ou para compartilhar a largura de banda entre diferentes cargas de trabalho.


### Benefícios
Proteja o tráfego crítico para o negócio em conexões dedicadas e entre nuvens (cross-cloud) para garantir desempenho e confiabilidade consistentes.

Obtenha controle granular do desempenho do tráfego de aplicações, mesmo diante de congestionamentos na sua interconexão.

## Componentes-chave para a movimentação segura de dados

A Cross-Cloud Network foi projetada para oferecer uma estrutura de conectividade coesa, centrada em serviços e do tipo "qualquer um para qualquer um" (any-to-any), construída sobre uma rede global. Ela simplifica a criação e a conexão de aplicações distribuídas entre diferentes nuvens.

Ao observar o diagrama, é possível ver como os componentes trabalham em conjunto para levar dados até a VPC (Virtual Private Cloud) de *Service Landing* e conectá-los aos serviços de IA:

<img width="1367" height="663" alt="Networking (1)" src="https://github.com/user-attachments/assets/97ece2ef-f522-46ff-b137-edd5840c2a61" />


Otimize a largura de banda e os custos evitando o superdimensionamento e soluções alternativas complexas.

1 - Ingestão de alta velocidade
O Cross-Cloud Interconnect e o Cloud Interconnect são conexões de alta largura de banda (por exemplo, até 100 Gbps), frequentemente respaldadas por um SLA de 99,99% e criptografia de ponta a ponta, para garantir a transferência confiável e segura dos grandes volumes de dados necessários para o treinamento de modelos de IA.
2 - Cloud Interconnect
O Cloud Interconnect conecta dados locais diretamente à rede.

3 - Cross-Cloud Interconnect
O Cross-Cloud Interconnect conecta dados de ambientes multicloud diretamente à rede.

4 - Conectividade centralizada
O Network Connectivity Center atua como um hub central, simplificando o gerenciamento de suas conexões de rede provenientes de diversas fontes.

5 -Acesso seguro a modelos
O tráfego de dados e de aplicações flui pela VPC de Service Landing e utiliza o Private Service Connect para acessar com segurança:

Serviços do Google, como a Agent Platform (que hospeda mais de 130 modelos).

Modelos personalizados (DIY) executados na infraestrutura de computação especializada do Google (TPU e GPU).

## Suporte à inferência distribuída

A Cross-Cloud Network não serve apenas para a ingestão de dados de treinamento; ela também oferece suporte à inferência de modelos de IA em ambientes híbridos. Isso significa que um serviço de aplicação em execução em outro ambiente de nuvem pode acessar e consultar, de forma segura e confiável, um modelo em execução no Google Cloud, proporcionando uma experiência de usuário fluida, independentemente de onde a aplicação ou o modelo estejam hospedados.


## Principais conclusões

A otimização de fluxos de trabalho de IA e RAG exige uma infraestrutura de rede robusta, projetada para unificar grandes volumes de dados dispersos entre ambientes locais (*on-premises*) e multinuvem. Ao utilizar a Cross-Cloud Network e conexões Cloud Interconnect de alta largura de banda, as organizações podem ingerir terabytes de dados com segurança em uma VPC de destino de serviços (*Service Landing VPC*), viabilizando um acesso fluido e de baixa latência à plataforma de agentes e a recursos computacionais especializados, tanto para o treinamento de modelos quanto para a inferência distribuída.
