# Introdução à sua jornada de aprendizado

Trilha de aprendizado Google Cloud Generative AI Leader

Esta trilha de aprendizado prepara você para a certificação Google Cloud Generative AI Leader. Ela foi desenvolvida para pessoas de todas as funções e áreas de atuação; não é necessária experiência técnica.

"Gen AI: Unlock Foundational Concepts" é o segundo curso da trilha de aprendizado. Recomendamos que você faça o primeiro curso antes de prosseguir com este.

Para aproveitar ao máximo este curso, recomendamos que você faça login na sua conta do Google; assim, poderá acessar facilmente as ferramentas necessárias para concluir as atividades. Se você não tiver uma conta do Google, visite a página de criação de conta do Google.

## Introdução
Vamos ouvir uma introdução aos conceitos fundamentais de IA generativa, apresentada pelos criadores do curso.

## O que você aprenderá neste curso
Neste curso, você obterá respostas para:

- Qual é a diferença entre IA, ML e IA generativa?
- Como diferentes tipos de dados, requisitos de dados e abordagens de aprendizado de máquina viabilizam a IA generativa?
- Quais estratégias do Google Cloud podem ser usadas para lidar com as limitações dos modelos de base (*foundation models*)?
- Quais são os principais desafios e fatores para o desenvolvimento e a implementação responsáveis ​​e seguros de IA em uma organização?

---
# Entendendo IA, ML e IA generativa
## Introdução
Vamos conferir uma breve visão geral sobre IA, ML e IA generativa apresentada pelos criadores do curso.

Diferenciando IA, ML e IA generativa

- Inteligência Artificial (IA): O campo abrangente de criação de máquinas capazes de realizar tarefas que exigem inteligência humana.
- Aprendizado de Máquina (ML): Um subcampo da IA ​​no qual as máquinas aprendem a partir de dados.
- IA generativa: Uma aplicação da IA ​​que cria novos conteúdos.
---
# O que são dados?
## Entendendo a importância dos dados na IA
Já falamos sobre o que são ML, IA e IA de geração. Mas a questão é: como os sistemas de IA, impulsionados por ML, realmente realizam essas tarefas? Tudo se resume aos dados que recebem. Essencialmente, os modelos de aprendizado de máquina preveem o futuro com base em dados existentes, assim como os humanos usam a experiência para fazer suposições fundamentadas. No entanto, enquanto os humanos podem confiar na intuição ou em palpites, esses modelos usam probabilidade.

Vamos ouvir uma breve introdução dos criadores do curso sobre por que escolher os dados certos é crucial.

# Modelos de aprendizado de máquina analisam os dados que lhes foram fornecidos, identificam padrões e, então, calculam a probabilidade de diferentes resultados ao serem apresentados a novas informações.

## Qualidade dos dados
É por isso que a qualidade e a quantidade dos dados com os quais os modelos de ML aprendem são absolutamente cruciais para o seu desempenho. Existem cinco fatores aos quais se deve prestar atenção ao considerar a qualidade dos dados.

- Precisão
Se os dados forem imprecisos, o modelo aprenderá padrões incorretos e fará previsões falhas. Imagine ensinar uma criança sobre animais usando um livro com imagens rotuladas erroneamente: ela aprenderia informações incorretas. O mesmo se aplica à IA.

- Integridade
A integridade refere-se tanto ao tamanho do conjunto de dados quanto à representatividade dentro dele. O tamanho do conjunto de dados é importante porque o modelo precisa de volume suficiente para fazer uma previsão precisa. Se um meteorologista tentar prever o tempo baseando-se apenas nos dados do dia anterior, a previsão será muito pior do que se utilizasse uma amostra muito maior.

- Representatividade
Os dados precisam ser representativos e inclusivos; caso contrário, podem levar a amostras distorcidas e resultados enviesados. Se um conjunto de dados sobre preferências de clientes não contiver informações sobre um determinado grupo demográfico, o modelo poderá fazer generalizações imprecisas ou enviesadas sobre esse grupo.

- Consistência
Formatos de dados ou rotulagens inconsistentes podem confundir o modelo e prejudicar sua capacidade de aprender de forma eficaz. Imagine tentar montar um quebra-cabeça em que algumas peças estão marcadas com números e outras com letras: seria uma confusão.

- Relevância
Os dados devem ser relevantes para a tarefa que a IA foi projetada para realizar. Por exemplo, dados sobre padrões de tráfego em Londres dificilmente serão úteis para prever a produtividade agrícola no Kansas.

## Acessibilidade de dados
A capacidade de sistemas de IA utilizarem esses dados de forma eficaz está diretamente ligada à acessibilidade dos dados. A acessibilidade de dados garante que as informações necessárias estejam prontamente disponíveis, utilizáveis ​​e com alta qualidade, permitindo um treinamento abrangente dos modelos e reduzindo possíveis vieses. Sem dados acessíveis, mesmo os algoritmos mais sofisticados têm sua capacidade de aprendizado e de geração de previsões precisas limitada.

- Disponibilidade
Se os dados necessários simplesmente não estiverem disponíveis, o modelo de IA não poderá ser treinado. Para alguns problemas, os dados podem até existir, mas estar bloqueados por barreiras de pagamento (*paywalls*) ou restritos devido a questões de privacidade.

- Custo
Coletar e limpar dados pode ser dispendioso. O custo de aquisição de dados de alta qualidade pode representar uma barreira significativa para o desenvolvimento de IA, especialmente para organizações menores.

- Formato
Os dados precisam estar em um formato que o modelo de IA consiga compreender e processar. Converter dados para o formato adequado pode ser um processo demorado e tecnicamente desafiador.

## Ponto-chave
Os dados constituem a base de qualquer sistema de IA, sendo a qualidade e a acessibilidade dos dados fundamentais para o desenvolvimento eficaz de IA. Compreender os tipos e a qualidade dos seus dados — incluindo precisão, integridade, consistência, relevância, disponibilidade, custo e formato — é crucial para o sucesso de iniciativas de IA.

---
# Data Types
## Compreendendo os tipos de dados
Os dados são fundamentais para muitos processos de negócios, e diversos tipos de dados podem ser utilizados em aprendizado de máquina; no entanto, dados não se resumem apenas a números e arquivos. Conjuntos de dados corporativos ajudam as organizações a compreender seus clientes, otimizar operações e embasar decisões estratégicas.

Conjuntos de dados maiores frequentemente melhoram o desempenho do modelo, especialmente no caso de modelos complexos de IA generativa que produzem conteúdos variados. Contudo, o tamanho do conjunto de dados não é o único fator relevante, pois o desempenho do modelo depende fortemente do tipo de dados utilizados para o treinamento — o que inclui o volume, a organização e a estrutura desses dados.

Compreender os dados da sua empresa — bem como sua qualidade, disponibilidade e formato — é essencial para entender as possibilidades de uso desses dados em aplicações de IA.

### Formatos de dados
Os dados apresentam-se em diversas formas, assim como a própria informação. Podemos classificar esses dados, de modo geral, em dois tipos principais: estruturados e não estruturados. Vamos explorar esse conceito por meio de uma empresa fictícia que vende produtos de limpeza ecológicos, chamada Cymbal Cleaning.

Os dados que a empresa armazena sobre os pedidos dos clientes podem incluir informações como:

- ID do cliente
- Nome do cliente
- Data da compra
- Valor do pedido
- Endereço de entrega
- Imagem do produto
- Avaliação (em uma escala de 1 a 5 estrelas)

### Dados estruturados
Imagine a lista de contatos do seu celular. Ela contém nomes, números de telefone e, talvez, endereços, tudo organizado em uma lista. Isso são dados estruturados! É fácil pesquisar e encontrar as informações de que você precisa.

Esse tipo de dado é frequentemente armazenado no que chamamos de banco de dados relacional, que funciona como um arquivo digital superorganizado, com informações dispostas de forma ordenada em tabelas. Outros exemplos de dados estruturados incluem itens como pedidos de compras online ou extratos bancários.

No caso do banco de dados da empresa de produtos de limpeza, isso incluiria:

- ID do cliente
- Nome do cliente
- Avaliação (em uma escala de 1 a 5 estrelas)
- Data da compra
- Valor do pedido

### Dados não estruturados
Dados não estruturados não possuem uma estrutura predefinida. Eles são, por natureza, desorganizados e complexos. Como não podem ser facilmente organizados em linhas e colunas, exigem técnicas de análise mais sofisticadas. Exemplos de dados não estruturados incluem documentos de texto (PDFs, e-mails, postagens em redes sociais), imagens (fotografias, artes digitais, exames médicos), áudio (gravações de voz, arquivos de música) e vídeo (filmes, vídeos do YouTube, vídeos de smartphones).

No caso do banco de dados da empresa de produtos de limpeza, isso incluiria:

- Feedback (texto livre)
- Imagem do produto
- Conteúdo de e-mails

## Ponto-chave
Compreender os dados da sua empresa é o primeiro passo essencial para qualquer implementação de IA. Os dados podem ser classificados, de modo geral, em duas categorias principais: dados estruturados — altamente organizados e encontrados em tabelas — e dados não estruturados — complexos e sem formato predefinido, como imagens ou textos de formato livre. O tipo, a qualidade e a estrutura dos seus dados são fatores cruciais para determinar o que é possível realizar com a IA.

---

