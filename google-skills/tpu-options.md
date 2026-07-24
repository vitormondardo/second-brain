#TPU Options

Você já conheceu os conceitos fundamentais da arquitetura de TPUs e como o Google Cloud as disponibiliza. Agora, vamos explorar as especificidades das diferentes versões de hardware de TPU do Google. Abordaremos desde as inovações mais recentes até os modelos pioneiros, ajudando você a escolher o acelerador ideal para suas cargas de trabalho de IA mais exigentes.

## Família Cloud TPU: uma visão geral rápida

Antes de mergulharmos nos detalhes técnicos, vamos ter uma noção geral do que cada versão do Cloud TPU oferece:

<img width="1906" height="1022" alt="Rise - AI Hypercomputer Architecture_Google TPU evolution" src="https://github.com/user-attachments/assets/ae869621-1872-4ab0-9d55-a187240c5538" />

### TPU v2
A Cloud TPU pública pioneira, que introduziu o conceito de fatias de TPU escaláveis ​​com uma interconexão em toro 2D.

### TPU v3
Um avanço significativo em relação à v2, oferecendo maior capacidade de processamento por TensorCore e maior capacidade de memória, sendo ideal para modelos maiores que não cabiam na v2.

### TPU v4
Uma opção flexível e eficiente que apresenta uma topologia de malha 3D e um sistema de memória aprimorado, oferecendo opções de configuração avançadas, como toros entrelaçados (*twisted tori*).

### TPU v5p
Um acelerador poderoso e escalável, conhecido por sua topologia de interconexão 3D em larga escala e alta capacidade de HBM, projetado para modelos de grande porte.

### TPU v6e (Trillium):
A geração mais recente, focada em oferecer o máximo valor e desempenho para modelos modernos de Transformer, conversão de texto em imagem e CNNs, contando com SparseCore para embeddings.

### TPU v7e (Ironwood)
Esta versão, com base nas capacidades existentes da Trillium, aprimora significativamente os aceleradores de IA personalizados, principalmente ao otimizá-los para cargas de trabalho de inferência.

Agora, vamos examinar mais de perto cada membro da família Cloud TPU, começando pela geração mais recente.

---

## Ironwood: Aceleração de inferência

O Ironwood foi projetado para oferecer alto desempenho, escalabilidade e eficiência energética. Seu desenvolvimento reflete uma mudança estratégica voltada para a aceleração da inferência em larga escala.

Esse foco na inferência apoia a transição de sistemas de IA reativos (que fornecem dados para interpretação humana) para agentes de IA mais proativos. Esses agentes são capazes de recuperar, gerar e interpretar dados de forma autônoma para fornecer insights e respostas, em vez de apenas informações brutas. O Ironwood foi desenvolvido especificamente para atender aos exigentes requisitos computacionais e de comunicação desta emergente "era da inferência".

<img width="801" height="601" alt="T-AIHYPE_Rise_m3_mw-20@2x" src="https://github.com/user-attachments/assets/3d3bda82-ecf0-4e74-bbb3-9cf4f0f23348" />

O Ironwood foi projetado para lidar com as complexas demandas computacionais e de comunicação de modelos avançados de IA. Isso inclui grandes modelos de linguagem (LLMs), modelos de "Mistura de Especialistas" (MoEs) e diversas tarefas de raciocínio. Seu projeto prioriza a minimização da movimentação de dados e da latência no chip durante a execução de extensas manipulações de tensores.

Especificações principais e desempenho

## Principais avaliações de desempenho
- Capacidade de computação de pico por chip: Cada chip Ironwood oferece uma capacidade de computação de pico de 4.614 TFLOPs (TeraFLOPs).

- Capacidade de HBM por chip: Oferece 192 GB de memória de alta largura de banda (HBM).

- Largura de banda de HBM por chip: Fornece 7,37 TB/s de largura de banda de HBM.

- Largura de banda de interconexão entre chips (ICI): Apresenta largura de banda ICI bidirecional de 1,2 TB/s, suportando comunicação coordenada e sincronizada em todo o pod de TPU.

## Escalabilidade e eficiência energética
- Escala do pod: Os pods Ironwood podem escalar até 9.216 chips com refrigeração líquida.

- Capacidade computacional agregada: Um pod de 9.216 chips oferece 42,5 exaflops de potência computacional.

- Eficiência energética: O Ironwood foi projetado para proporcionar melhorias significativas na eficiência energética. Ele oferece o dobro do desempenho por watt em comparação ao Trillium e é aproximadamente 30 vezes mais eficiente energeticamente do que a primeira Cloud TPU (v2), de 2018. Essa eficiência é viabilizada por soluções avançadas de refrigeração líquida.

<img width="2517" height="696" alt="ironwood6" src="https://github.com/user-attachments/assets/459e5b13-07d7-4056-b2aa-b7e5eeaaa4cf" />

