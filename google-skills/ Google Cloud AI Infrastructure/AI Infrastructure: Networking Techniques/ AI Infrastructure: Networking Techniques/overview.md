# Overview

<img width="1285" height="291" alt="networking" src="https://github.com/user-attachments/assets/5a194521-53e9-4625-b729-1bc68d1b2da1" />

A primeira fase do pipeline de IA/ML — ingestão e preparação de dados — é, fundamentalmente, um problema de movimentação de dados. Lidar com esse desafio depende de quatro pilares fundamentais que influenciam diretamente o sucesso da fase de ingestão.

Capacidade de Movimentação de Dados
O principal desafio de rede aqui é a escala. Você precisa mover quantidades massivas de dados — frequentemente chamadas de "fluxos de elefante" (elephant flows) — rapidamente.

Planejamento e entrega de capacidade do Google Cloud Interconnect/Cross-Cloud Interconnect: Refere-se ao planejamento da capacidade para o Google Cloud Interconnect e Cross-Cloud Interconnect. Estes são seus tubos dedicados para a movimentação de dados.

Alta largura de banda – Interconnects de 400G: Links de alta largura de banda (como 400 Gbps) são necessários para lidar com os volumes de transferência de dados em massa.

Transferências em massa – MTU/Jumbo: Configurar a rede para lidar com tamanhos de pacotes grandes (unidade máxima de transmissão [MTU]), às vezes chamados de Jumbo Frames, permite transferências em massa mais eficientes, reduzindo a sobrecarga de processamento de muitos pacotes pequenos.

A interface de rede de uma única máquina virtual (VM) possui um limite rígido de taxa de transferência de ingestão (throughput), independentemente do tamanho da VM ou do desempenho do GCS. Para atingir altas taxas de transferência de dados, você deve escalar horizontalmente para aumentar a taxa de transferência de rede de forma linear.

Segurança
Como você está movendo dados proprietários e frequentemente sensíveis, a segurança é inegociável.

Media Access Control Security (MACsec), Sistemas de Prevenção de Intrusão (IPS), criptografia/EIT: Implemente segurança em várias camadas de rede. O MACsec protege a conexão física. O IPS monitora ameaças. A criptografia (tanto em trânsito quanto em repouso) é crítica para a integridade e a privacidade dos dados.

Proteção contra malware (armazenamento de arquivos): Uma vez que os dados são ingeridos e armazenados (por exemplo, em um bucket de armazenamento em nuvem), eles devem ser escaneados em busca de malware ou vírus antes de serem usados no restante do pipeline.

Qualidade de Serviço (QoS) e Confiabilidade
A conexão de rede em si deve ser consistente e confiável.

Modelagem/proteção para o Interconnect: Use mecanismos de QoS para reservar largura de banda ou priorizar o tráfego de dados relacionado ao processo de ingestão, evitando que tráfego menos crítico sobrecarregue o link dedicado.

Consciência de aplicação para o Interconnect: Garante que as configurações de rede sejam otimizadas para o protocolo de aplicação específico que está sendo usado para a transferência de dados.

Acordo de Nível de Serviço (SLA) do Interconnect: Um SLA garante um determinado nível de tempo de atividade (uptime) e desempenho para a conexão dedicada, o que é crucial para a execução previsível do pipeline.

Observabilidade
Você não pode gerenciar o que não pode medir. Monitorar a rede é fundamental para a solução de problemas e otimização.

Métricas de perda e latência: As métricas fundamentais a serem rastreadas são a perda de pacotes e a latência. Alta perda ou alta latência podem desacelerar significativamente o processo de ingestão de dados e desperdiçar tempo de computação.

