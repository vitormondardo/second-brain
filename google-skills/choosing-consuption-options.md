# Choosing consumption options

Você já conheceu a arquitetura poderosa das Cloud TPUs e como elas são escalonadas no Google Cloud. Agora, vamos explorar os aspectos financeiros e logísticos fundamentais para acessar e utilizar esses recursos computacionais — especificamente, as diversas opções de consumo disponíveis para você.

Considere as opções de consumo como diferentes "contratos de locação" para a sua capacidade de computação em TPUs. Cada opção possui termos específicos que determinam a rapidez com que você obtém a capacidade, por quanto tempo pode mantê-la e qual é o custo.

Ao selecionar uma opção, considere estes fatores-chave:

- Velocidade: Com que rapidez você precisa que a capacidade de TPU esteja disponível?
- Duração: Por quanto tempo você precisará de capacidade para a sua carga de trabalho?
- Flexibilidade: Você precisa de recursos para um período preciso e definido, ou pode ser mais flexível?
- Tolerância à preempção: Sua carga de trabalho consegue lidar com uma interrupção inesperada (preempção) pelo Google Cloud?
- Preço: Qual é o seu orçamento?

## Entendendo as cotas: sua franquia de computação

Antes de explorarmos as opções, vamos esclarecer o conceito de cota.

### Uma cota é como uma franquia pré-aprovada para o uso de núcleos de Cloud TPU.

O Google Cloud utiliza cotas para garantir uma distribuição justa de recursos e evitar picos de utilização, ajudando os clientes a evitar custos excessivos. Uma cota limita a quantidade de um recurso específico do Google Cloud que seu projeto pode consumir, aplicando-se a componentes de hardware, software e rede.

Por exemplo, as cotas podem restringir o volume de chamadas de API, o número de balanceadores de carga simultâneos ou o total de projetos que você pode criar. Esse sistema protege todos os usuários do Google Cloud ao evitar a sobrecarga do serviço e ajuda você a gerenciar de forma eficaz o seu próprio consumo de recursos.

Ao trabalhar com Cloud TPUs, os requisitos de cota variarão dependendo de você estar usando as APIs do Cloud TPU diretamente ou integrando-as ao Google Kubernetes Engine (GKE).


