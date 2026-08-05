Guia de Estudos: Redes em AI Hypercomputer e Máquinas A3
Questões para Prática
1. Você está configurando uma máquina A3 Ultra para uma carga de trabalho de treinamento distribuído. Como a arquitetura de rede "Dual Plane" (Plano Duplo) lida com o tráfego neste tipo de máquina?

[ ] Todo o tráfego, incluindo armazenamento e comunicação de GPU, é roteado pelas interfaces gVNIC.

[ ] As NICs NVIDIA CX-7 lidam com o tráfego geral da internet, enquanto as gVNICs lidam com o tráfego de GPU para GPU.

[ ] A CPU gerencia todo o tráfego de rede para garantir que as políticas de segurança sejam cumpridas.

[ ] As NICs NVIDIA CX-7 são dedicadas à comunicação de GPU para GPU, enquanto as gVNICs lidam com o tráfego de host e armazenamento.

2. No contexto de redes para o AI Hypercomputer, por que o "Goodput" (taxa de transferência útil) é considerado uma métrica mais crítica do que a taxa de transferência bruta (raw throughput) para o desempenho do treinamento de ML?

[ ] Ele mede a velocidade máxima teórica dos cabos de rede.

[ ] Ele rastreia especificamente o progresso efetivo de uma tarefa de treinamento, excluindo a sobrecarga (overhead).

[ ] Ele determina a rapidez com que os dados podem ser ingeridos da internet.

[ ] Ele mede a latência entre o usuário final e o modelo de inferência.

Gabarito e Explicações
Questão 1
Resposta correta: As NICs NVIDIA CX-7 são dedicadas à comunicação de GPU para GPU, enquanto as gVNICs lidam com o tráfego de host e armazenamento.

Por que essa é a resposta:
As instâncias A3 do Google Cloud utilizam uma arquitetura de rede "Dual Plane" para maximizar o desempenho. O tráfego de computação pesada (GPU para GPU) trafega por uma rede backend dedicada de altíssima velocidade utilizando as placas de rede (NICs) NVIDIA ConnectX-7 (CX-7) com suporte a RoCEv2. O restante do tráfego "front-end" — que inclui a comunicação padrão do host (CPU), acesso à internet, acesso a banco de dados e tráfego de armazenamento — é tratado de forma totalmente separada pelos adaptadores gVNIC (Google Virtual NIC) acoplados às DPUs de titânio do Google. Isso evita que gargalos de armazenamento interfiram no treinamento da rede neural.

Questão 2
Resposta correta: Ele rastreia especificamente o progresso efetivo de uma tarefa de treinamento, excluindo a sobrecarga (overhead).

Por que essa é a resposta:
Em redes de computadores, Throughput (taxa de transferência) mede o volume total de dados que passa pela rede, mas isso inclui pacotes perdidos, retransmissões e dados de controle de protocolo (overhead). O Goodput, por outro lado, mede apenas a quantidade de dados úteis entregues com sucesso à aplicação. Em treinamentos distribuídos de Machine Learning (ML), o que importa é a rapidez com que as GPUs conseguem trocar parâmetros e atualizar pesos efetivamente, sem contabilizar dados que precisaram ser reenviados devido a congestionamentos de rede.
