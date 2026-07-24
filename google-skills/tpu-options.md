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

## Cloud Trillium

O Trillium (designado como v6e) é o acelerador de IA de última geração do Cloud TPU, projetado para oferecer desempenho e eficiência de ponta.

### Arquitetura e especificações do sistema

Cada chip Trillium contém um único TensorCore, que abriga duas unidades de multiplicação de matrizes (MXU), uma unidade vetorial e uma unidade escalar. Cada VM TPU Trillium pode conter 1, 4 ou 8 chips. Fatias (slices) com 4 chips ou menos compartilham o mesmo nó de acesso não uniforme à memória (NUMA).

O Trillium compartilha muitas semelhanças arquitetônicas com o v5e, mas traz avanços significativos.

<img width="1601" height="901" alt="T-AIHYPE_Rise_m3_mw-17 copy@2x (1)" src="https://github.com/user-attachments/assets/34d5845b-e118-436a-8ce1-418f84336826" />

Especificações
Como especificar o Trillium:

- API do TPU: Use o parâmetro `AcceleratorType`, como `v6e-8` (para uma fatia de 8 Tensor Cores).

- API do GKE: Use a flag `--machine-type`, como `t6e-standard-8t`.

Principais benefícios
Aumento massivo de desempenho: Espere picos de capacidade computacional e largura de banda significativamente maiores por chip.

- Memória ampliada: O dobro da capacidade e da largura de banda de memória HBM por chip em comparação com a versão v5e.

- Inclusão do SparseCore: O Trillium v6e incorpora SparseCores para acelerar modelos que dependem de *embeddings*, comuns em sistemas de recomendação.

- Otimizado para: Treinamento, ajuste fino (*fine-tuning*) e inferência (*serving*) de modelos Transformer, modelos de conversão de texto em imagem e redes neurais convolucionais (CNNs).


## Cloud TPU v5p: Desempenho em escala com topologia 3D

A ​​TPU v5p é mais um acelerador de alto desempenho, que se destaca por sua grande escala e topologia de interconexão 3D.

### Arquitetura e especificações do sistema

Cada chip v5p contém um TensorCore, que inclui quatro Unidades de Multiplicação de Matrizes (MXU), uma unidade vetorial e uma unidade escalar.

- Todas as fatias v5p de configuração 4x4x4 (um cubo) ou maior possuem conectividade de toro 3D completa.

- Fatias menores que um cubo completo possuem conectividade 3D, mas não contam com as conexões de retorno (*wrap-around links*) que caracterizam um verdadeiro toro 3D.

### Configurações compatíveis com a v5p

Um pod de TPU v5p é composto por 8.960 chips com rede flexível de alta velocidade. É possível configurar esses chips em diversos formatos de *slice*. O treinamento em *slice* único é compatível com até 6.144 chips, e você pode escalar ainda mais — chegando a 18.432 chips — utilizando o Multislice (conforme abordado em uma aula anterior).

Veja como especificar a v5p e quais são os principais benefícios:

#### Especificações
- API de TPU: Use AcceleratorType com o formato v$VERSION_NUMBERp-$CORES_COUNT. Por exemplo, v5p-256 especifica uma fatia v5p com 256 TensorCores (128 chips).

- API do GKE: Todas as VMs de TPU v5p utilizam o tipo de máquina ct5p-hightpu-4t.


#### Principais benefícios
- Resiliência de ICI para v5p: Assim como na TPU v4, a resiliência de ICI é ativada por padrão para fatias v5p de tamanho igual ou superior a um cubo (por exemplo, v5p-128 ou configuração de acelerador 4x4x4).

- Isso ajuda a melhorar a tolerância a falhas e a disponibilidade de agendamento.

<img width="2600" height="1377" alt="TPU_v5L_Pod_-_Front_View_-_Web max-2600x2600" src="https://github.com/user-attachments/assets/9354a4aa-bdb0-4d6f-b48c-87c07aa59a6b" />

## TPU v4

A TPU v4 oferece uma plataforma poderosa e flexível, destacando-se especialmente por sua topologia de interconexão em malha 3D.

### Arquitetura e especificações do sistema

Cada chip TPU v4 contém dois TensorCores. Cada TensorCore possui quatro MXUs, uma unidade vetorial e uma unidade escalar.

<img width="1601" height="839" alt="T-AIHYPE_Rise_m3_mw-17 copy 2@2x" src="https://github.com/user-attachments/assets/c95c4301-c33d-414d-8ef8-1361ebb17e7d" />

#### Especificações
Conexões de malha 3D e toro 3D:

- Malha 3D: as TPUs v4 conectam-se diretamente aos seus vizinhos mais próximos em três dimensões, formando uma malha 3D.

- Toro 3D: para determinadas topologias (onde as dimensões são 2A=B=C ou 2A=2B=C), as conexões podem ser configuradas como um toro 3D. Isso geralmente resulta em melhor desempenho devido a caminhos de comunicação aprimorados.

- A ilustração mostra algumas topologias comuns de TPU v4.

<img width="1921" height="1081" alt="T-AIHYPE_Rise_M3_74_1" src="https://github.com/user-attachments/assets/62493ca7-1fc7-40d8-a050-77ff7908bd14" />

#### Principais benefícios
Melhorias de desempenho e no sistema de memória na v4 (em relação à v3):

- Localidade NUMA: A v4 oferece suporte a acesso não uniforme à memória (NUMA). É possível vincular o script de treinamento ao nó NUMA 0 para aumentar a taxa de transferência de memória, especialmente em cargas de trabalho com uso intensivo da CPU.

- HBM unificada: Os chips v4 possuem um espaço de memória HBM unificado de 32 GiB compartilhado entre os TensorCores, o que melhora a coordenação.

- HBM e DMA aprimorados: Desempenho de HBM aprimorado com os padrões mais recentes e melhor acesso direto à memória (DMA) para operações de *striding* de alto desempenho.

- Melhorias nos TensorCores: Dobro de MXUs, frequências de clock mais altas, maior largura de banda para transposição e permutação, e carregamento de pesos das MXUs mais rápido.

Teste seus conhecimentos

Vamos revisar o que você aprendeu sobre as opções de TPU. Identifique a versão do Cloud TPU associada a cada marco.

Selecione os flashcards para verificar sua resposta.

Clique para virar
A versão do Cloud TPU que implementou pela primeira vez a tecnologia de switches de circuito óptico (OCSs) para reconfigurações dinâmicas de interconexão. -> TPU v4

Clique para virar
O Cloud TPU público fundamental que introduziu o conceito de fatias de TPU escaláveis ​​com uma interconexão de toro 2D. -> TPU v2

Clique para virar
A versão poderosa e escalável do TPU conhecida por sua topologia de interconexão 3D em larga escala e alta capacidade de HBM. -> TPU v5


