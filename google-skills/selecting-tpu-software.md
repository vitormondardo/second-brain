# Seleção de software para TPU
Você já aprendeu a provisionar TPUs e conheceu os diferentes componentes de arquitetura. Agora, vamos abordar uma etapa crucial na criação de suas Cloud TPUs: a seleção do ambiente de software adequado para seus modelos de aprendizado de máquina. Escolher a versão de software correta garante compatibilidade, desempenho ideal e acesso aos recursos de que você precisa.

O que é uma versão de software de TPU?
Ao criar recursos de TPU, você especifica uma versão de software de TPU (também conhecida como versão de *runtime*). Esse não é apenas um número arbitrário; ele se refere ao ambiente de software completo que já vem pré-instalado na sua VM de TPU.


Esse ambiente inclui:

O sistema operacional Ubuntu subjacente.

Docker (para conteinerização).

Todos os outros softwares essenciais necessários para executar seu código de aprendizado de máquina diretamente nas TPUs.

Google Cloud CLI

Você usará o parâmetro --version ou --runtime-version.

gcloud compute tpus tpu-vm create lab-tpu --zone ${ZONE} \

--accelerator-type=v6e-4 \

--version v2-alpha-tpuv6e

Console do Google Cloud
Basta selecionar uma opção em uma lista de versões de software de TPU.

Se você estiver trabalhando com PyTorch ou JAX, selecionar a versão do software da TPU é bastante simples. Você escolhe uma versão base compatível com o seu hardware de TPU e, em seguida, instala o framework de sua preferência sobre ela. A seguir, apresentamos as versões comuns de software de TPU para PyTorch e JAX, categorizadas pela versão do hardware da TPU:

TPU Version

Common TPU software version

Trillium (v6e)

v2-alpha-tpuv6e

v5p

v2-alpha-tpuv5

v5e

v2-alpha-tpuv5-lite

v4 and older

tpu-ubuntu2204-base

Demonstração: Executando PyTorch em TPU

Aqui está uma demonstração simples de como executar PyTorch em TPU.

Experimente!
Você acabou de ver como executar o PyTorch em uma TPU. Agora, é a sua vez de praticar. Esta simulação opcional é a sua oportunidade de realizar exatamente as mesmas etapas e aplicar os mesmos conceitos da demonstração.
