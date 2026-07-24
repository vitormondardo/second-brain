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

terraform.tfstate

Ele registra:

Terraform
   │
   ├── Código
   │
   └── terraform.tfstate
          │
          ▼
     Estado conhecido

Exemplo:

terraform.tfstate

VM:
  ID: ocid1.instance...
  Nome: app-server
  OCPU: 2
  RAM: 12 GB
  IP: 123.123.123.123

Imagine que você crie uma VM manualmente na Oracle Cloud.

Depois escreve:

resource "oci_core_instance" "app_server" {
  ...
}

O Terraform não sabe automaticamente que aquela VM já existe.

Ele pode tentar criar outra.

Por isso existe o conceito de import:

terraform import oci_core_instance.app_server <OCID_DA_VM>

O Terraform passa a conhecer aquele recurso.

Importante para você

Em um projeto pessoal:

terraform.tfstate

pode ficar localmente.

Em um projeto profissional:

Seu computador
       │
       ▼
Terraform State
       │
       ▼
Remote Backend
       │
       ▼
Compartilhado com equipe

Você não quer que cada desenvolvedor tenha uma versão diferente do estado da infraestrutura.

5. Você precisa entender Providers

O Terraform sozinho não sabe criar recursos na Oracle Cloud.

Você instala um Provider.

Terraform
    │
    ▼
Oracle Cloud Provider
    │
    ▼
Oracle Cloud API

Para Oracle Cloud, você usará o provider da OCI.

A estrutura básica será algo como:

terraform {
  required_providers {
    oci = {
      source  = "oracle/oci"
      version = "~> 7.0"
    }
  }
}

provider "oci" {
  tenancy_ocid     = var.tenancy_ocid
  user_ocid        = var.user_ocid
  fingerprint      = var.fingerprint
  private_key_path = var.private_key_path
  region           = var.region
}

Você precisa entender:

O que é um provider
Como autenticar o Terraform
Como proteger credenciais
Como usar variáveis
Como selecionar uma região

Nunca coloque chaves secretas diretamente no código versionado.

6. Variáveis, Outputs e Locals

Você não quer escrever:

region = "sa-saopaulo-1"

em vários arquivos.

Você cria:

variable "region" {
  type        = string
  description = "OCI region"
}

E:

region = var.region

Um arquivo:

terraform.tfvars

poderia conter:

region = "sa-saopaulo-1"
environment = "production"

Mas atenção: se tiver informações sensíveis, não commite esse arquivo.

Você pode ter:

terraform.tfvars

no .gitignore.

Outputs

Imagine que Terraform crie uma VM:

output "server_ip" {
  value = oci_core_instance.app_server.public_ip
}

Depois:

terraform output server_ip

Resultado:

123.123.123.123

Isso é muito útil para conectar recursos entre si.

7. Entender Resources

Um resource representa algo que será criado ou gerenciado.

Exemplos:

VM
Rede
Subnet
Security List
IP Público
Banco
DNS
Object Storage
Load Balancer

No Terraform:

resource "oci_core_instance" "app_server" {
  ...
}

A estrutura é:

resource "TIPO_DO_RECURSO" "NOME_LOCAL" {
  configuração
}

Por exemplo:

resource "oci_core_vcn" "main" {
  cidr_block = "10.0.0.0/16"
}

Você pode referenciar:

resource "oci_core_subnet" "app_subnet" {
  vcn_id = oci_core_vcn.main.id
}

O Terraform entende:

VCN
 │
 ▼
Subnet

E cria na ordem correta.

8. Dependências entre recursos

Esse código:

resource "oci_core_subnet" "app_subnet" {
  vcn_id = oci_core_vcn.main.id
}

cria uma dependência:

oci_core_vcn.main
          │
          ▼
oci_core_subnet.app_subnet

Você normalmente não precisa dizer manualmente a ordem.

Mas existe também:

depends_on = [
  oci_core_vcn.main
]

Use isso apenas quando o Terraform não conseguir descobrir a dependência automaticamente.

9. Entender módulos

Imagine que sua infraestrutura cresça:

terraform/
│
├── main.tf
├── variables.tf
├── outputs.tf
├── network.tf
├── compute.tf
├── database.tf
└── security.tf

Isso já é uma boa estrutura para começar.

Depois você pode ter:

modules/
│
├── network/
├── compute/
├── database/
└── security/

Por exemplo:

infra/
│
├── environments/
│   ├── development/
│   └── production/
│
└── modules/
    ├── network/
    ├── vm/
    └── database/

Para sua aplicação atual, eu não começaria criando dezenas de módulos.

Começaria com:

terraform/
├── provider.tf
├── variables.tf
├── network.tf
├── compute.tf
├── security.tf
├── outputs.tf
├── terraform.tfvars
└── .gitignore

Depois você refatora quando entender melhor o projeto.

10. Terraform não configura completamente o sistema operacional

Essa é uma distinção muito importante para o seu caso.

Terraform é excelente para:

Criar VM
Criar rede
Criar firewall
Criar banco
Criar IP
Criar DNS

Mas você ainda precisa configurar:

Dentro da VM:
  ├── Docker
  ├── Docker Compose
  ├── Nginx
  ├── Certbot
  ├── Firewall interno
  ├── Aplicação
  └── Variáveis de ambiente

Você pode fazer isso usando:

Terraform + cloud-init
Terraform
    │
    ▼
Cria VM
    │
    ▼
cloud-init
    │
    ├── Instala Docker
    ├── Instala ferramentas
    ├── Configura usuário
    └── Executa configurações iniciais

Ou:

Terraform
    │
    ▼
Cria infraestrutura
    │
    ▼
Ansible
    │
    ▼
Configura servidor

Ou:

Terraform
    │
    ▼
Cria infraestrutura
    │
    ▼
GitHub Actions
    │
    ▼
Faz deploy da aplicação

A separação profissional normalmente é:

Terraform
   │
   └── Infraestrutura

Ansible / cloud-init
   │
   └── Configuração do servidor

CI/CD
   │
   └── Deploy da aplicação

   
