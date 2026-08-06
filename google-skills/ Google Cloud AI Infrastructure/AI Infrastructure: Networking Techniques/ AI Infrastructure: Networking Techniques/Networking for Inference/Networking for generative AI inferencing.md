# Redes para inferência de Inteligência Artificial Generativa

Quando as empresas implantam LLMs e outras aplicações de IA generativa, elas encontram desafios de rede muito diferentes dos das aplicações web tradicionais. Por quê? Porque o comportamento da inferência de IA (fornecimento de previsões aos usuários) muda fundamentalmente a forma como a rede é utilizada.

## Tráfego de IA Generativa vs. Tráfego web tradicional
As aplicações web tradicionais normalmente possuem padrões de tráfego previsíveis. As solicitações e respostas são pequenas, e os tempos de processamento são rápidos (milissegundos). No entanto, as aplicações de IA generativa são únicas e apresentam diversas dificuldades de rede:

## O principal desafio de rede
Como uma única consulta de LLM pode ocupar 100% de uma GPU ou TPU custosa por um tempo significativo, as antigas formas de roteamento de tráfego — como o simples *round-robin* ou técnicas básicas baseadas em utilização — geralmente são ineficientes e podem levar a uma experiência ruim para o usuário.

| Característica | Tráfego web tradicional | Tráfego de inferência de IA Generativa |
| :--- | :--- | :--- |
| **Tamanho da requisição/resposta** | Pequeno e simples (ex.: texto e imagem pequena) | Respostas dinâmicas e com picos de tráfego (bursty), por vezes imprevisíveis, dependendo de quão ocupados estão os pontos de extremidade. A rajada de tráfego é explicada por comprimentos de resposta variáveis (imagens grandes, geração de vídeo e modelos que fornecem respostas com capacidades de raciocínio). |
| **Paralelismo de computação** | Muitas consultas podem ser processadas em paralelo em um único servidor. | Uma única consulta de LLM pode consumir 100% do tempo de computação de uma GPU/TPU. |
| **Disponibilidade de recursos** | As requisições são frequentemente processadas assim que chegam. | As requisições frequentemente aguardam até que os recursos computacionais (GPU/TPU), que são custosos e limitados, fiquem disponíveis. |
| **Latência/tempo de processamento** | Medido em milissegundos (ms). | Altamente variável, variando de segundos a minutos devido ao custo computacional. |
| **Cache** | Requisições similares frequentemente podem ser atendidas a partir de um cache rápido. | As requisições frequentemente geram conteúdo novo e exclusivo, tornando o uso de cache menos eficaz. |
| **Gerenciamento de custos** | O custo do tráfego é gerenciado dentro de um backend padronizado. | O tráfego pode ser inteligentemente direcionado para um modelo mais barato ou mais caro, dependendo da requisição específica do usuário. |
