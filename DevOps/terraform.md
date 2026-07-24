# Terraform
Antes de começar a escrever a infraestrutura da sua aplicação em Terraform, você precisa entender principalmente o que está tentando provisionar, como os recursos dependem uns dos outros e como o Terraform controla o estado da infraestrutura.

Como você está trabalhando com uma aplicação web em uma VM da Oracle Cloud, eu estudaria nesta ordem:

1. Entender a arquitetura que você quer provisionar

Antes de escrever qualquer .tf, desenhe mentalmente a infraestrutura.

Por exemplo:

Internet
   │
   ▼
Cloudflare
   │
   ▼
IP Público
   │
   ▼
Oracle Cloud VM
   │
   ├── Docker
   │    ├── Frontend
   │    ├── Backend
   │    └── PostgreSQL
   │
   └── Firewall / Security List

Você precisa responder:

Onde a aplicação ficará hospedada?
Terá uma VM ou várias?
O banco ficará na mesma VM?
Terá um banco gerenciado?
Qual será o domínio?
Como o tráfego chegará na aplicação?
Quais portas serão abertas?
Como será o acesso SSH?
Como os dados serão persistidos?
Como será o backup?
Como será o deploy da aplicação?

O Terraform não decide a arquitetura por você. Ele automatiza a criação da arquitetura que você definiu.

2. O conceito mais importante: Infrastructure as Code

Normalmente você faria:

1. Entrar na Oracle Cloud
2. Criar uma VM
3. Criar uma rede
4. Configurar firewall
5. Criar IP
6. Criar banco
7. Configurar regras

Com Terraform:

Código Terraform
       │
       ▼
Terraform
       │
       ▼
Oracle Cloud API
       │
       ▼
Infraestrutura criada

Por exemplo:

resource "oci_core_instance" "app_server" {
  display_name = "app-server"

  shape = "VM.Standard.E4.Flex"

  # outras configurações
}

Você descreve:

"Eu quero uma VM com essas características."

E o Terraform conversa com a API da Oracle para criar essa VM.

3. Terraform é declarativo

Esse é um dos conceitos mais importantes.

Você não escreve:

Crie uma VM
Depois crie uma rede
Depois configure a porta 80
Depois configure a porta 443

Você declara:

resource "oci_core_instance" "app_server" {
  # configuração desejada
}

O Terraform entende:

Estado atual:
Não existe VM

Estado desejado:
Existe uma VM

Ação:
Criar VM

Depois:

Estado atual:
Existe VM com 2 OCPUs

Código:
2 OCPUs

Ação:
Nenhuma

Se você mudar:

shape = "VM.Standard.E4.Flex"
ocpus = 4

O Terraform compara:

Estado atual: 2 OCPUs
Estado desejado: 4 OCPUs

Ação:
Alterar infraestrutura

O fluxo principal é:

terraform init
        │
        ▼
terraform plan
        │
        ▼
terraform apply
4. Você precisa entender o Terraform State

Esse é talvez o conceito mais importante antes de usar Terraform profissionalmente.

O Terraform mantém um arquivo chamado:
