



![img](https://miro.medium.com/max/1216/1*1ildCwyXO8um1sxsBuNLfQ.png)

# (IaC) Parte 1: Criando uma instância EC2 na AWS usando Terraform

[![Amaury Borges Souza](https://miro.medium.com/fit/c/56/56/1*eKEH_4YYruI87-NjUlGBsw.png)](https://amauryborgesouza.medium.com/?source=post_page-----e364f5d31570-----------------------------------) - [Amaury Borges Souza](https://amauryborgesouza.medium.com/?source=post_page-----e364f5d31570-----------------------------------) - [Nov 27, 2019·8 min read](https://amauryborgesouza.medium.com/terraform-e364f5d31570?source=post_page-----e364f5d31570-----------------------------------)



Opa, maravilha pessoal. Hoje começo apresentando o primeiro de 5 artigos sobre infra-as-code, onde vamos aprender sobre Terraform, Ansible e AWS. Existem outras ferramentas que gerenciam a infra como código mas irei mostrar apenas as listadas acima. Pegue seu café, se acomode na cadeira que vem DevOps. 🚀 💻

Só vendo um pouco o que é o Terraform, é uma das ferramentas de infraestrutura muito populares, é um dos produtos da HashiCorp, uma empresa de software de código aberto com sede em San Francisco, Califórnia. Basicamente, o Terraform permite definir a infraestrutura para uma variedade de fornecedores. Por exemplo: AWS, Azure, GCP, Digital Ocean e OpenStack, etc.

Sugiro a leitura do artigo do pessoal da *concrete.com* que é topzera da balada. Seria bom você ler antes de prosseguir no artigo, isso vai ajudar você a entender melhor o processo de criação da instância. [Link do artigo](https://www.concrete.com.br/2018/12/28/automotize-a-sua-infraestrutura-com-terraform/).

- **Principais comandos do Terraform**

`terraform init` baixa os plugins necessários para executar o código.**
**`terraform plan` mostra o que o Terraform irá realizar na infra.
`terrraform apply` executa tudo que foi definido no arquivo de configuração.
`terraform destroy` irá destruir tudo que foi aplicado no provider.

Antes de iniciar, vale destacar que estou usando o sistema Ubuntu, então mostrarei a instalação da ferramenta nesse sistema, caso você queira instalar em outra distro, acesse a documentação [aqui](https://www.terraform.io/), é sensacional.

- **Instalação do Terraform no Linux Ubuntu**

Para fazer a devida instalação basta navegar até a página abaixo e selecionar a sua distro, neste caso vou usar o Linux:

https://www.terraform.io/downloads.html

Agora vamos aos comandos:

- Verifique se você possui o pacote unzip instalado, se não possuir faça a instalação conforme abaixo:

```
# sudo apt-get install unzip
```

- Nesse passo você precisa extrair o arquivo baixado do Terraform, lembre-se que você fez o download do pacote do Terraform:

```
# unzip terraform_0.12.16_linux_amd64.zip
```

- Em seguida mova o binário para o diretório /usr/local/bin

```
# sudo mv terraform /usr/local/bin/
```

- Feito isso, para testar vamos executar o comando abaixo:

```
# terraform --version
Terraform v0.12.16
```

- Veja as opções de uso do comando Terraform, basta executar `# terraform`

```
# terraform 
Usage: terraform [-version] [-help] <command> [args]The available commands for execution are listed below.
The most common, useful commands are shown first, followed by
less common or more advanced commands. If you're just getting
started with Terraform, stick with the common commands. For the
other commands, please read the help and docs before usage.Common commands:
    apply              Builds or changes infrastructure
    console            Interactive console for Terraform interpolations
    destroy            Destroy Terraform-managed infrastructure
    env                Workspace management
    fmt                Rewrites config files to canonical format
    get                Download and install modules for the configuration
    graph              Create a visual graph of Terraform resources
    import             Import existing infrastructure into Terraform
    init               Initialize a Terraform working directory
    output             Read an output from a state file
    plan               Generate and show an execution plan
    providers          Prints a tree of the providers used in the configuration
    refresh            Update local state file against real resources
    show               Inspect Terraform state or plan
    taint              Manually mark a resource for recreation
    untaint            Manually unmark a resource as tainted
    validate           Validates the Terraform files
    version            Prints the Terraform version
    workspace          Workspace managementAll other commands:
    0.12upgrade        Rewrites pre-0.12 module source code for v0.12
    debug              Debug output management (experimental)
    force-unlock       Manually unlock the terraform state
    push               Obsolete command for Terraform Enterprise legacy (v1)
    state              Advanced state management
```

- **Criação de usuário na AWS**

É necessário criar um usuário para gestão da instância EC2, vou deixar aqui a documentação da AWS que mostra de forma bem resumida e direta esse procedimento:

[Criação de um usuário do IAM na sua conta da AWS](https://docs.aws.amazon.com/pt_br/IAM/latest/UserGuide/id_users_create.html).

Vale ressaltar que esse case pode ser feito usando o nível gratuito da AWS:

[Conheça o nível gratuito e todos os recursos disponibilizados](https://aws.amazon.com/pt/free/?all-free-tier.sort-by=item.additionalFields.SortRank&all-free-tier.sort-order=asc).

O que é importante nesse processo?
Depois que você terminar de criar seu usuário e adicionar ele em um grupo, será necessário salvar o **ID da chave de acesso** e a **chave de acesso secreta**. Isso será usado em um arquivo para que o terraform entenda onde estão as credenciais. Você verá uma tela similar a essa abaixo:

![img](https://miro.medium.com/max/60/1*MUTz1Gi_WRTgsejDuostCg.png?q=20)

![img]()

Criação de usuário na AWS com IAM

- **Criação do arquivo de credentials**

Vamos agora criar um arquivo chamado credentials no seu /home e adicionar as chaves que foram criadas. Para isso execute:

```
# mkdir .aws
# vim .aws/credentials
[awsterraform]
aws_access_key_id = "KDKDKSDKSDKSDSKD8D88"
aws_secret_access_key = "jdjdjdadSSDssjskdASlsdlsSGhUSsiiSLOiUs"
```

Feito isso, vamos criar outro arquivo onde serão feitas as configurações da nossa infra na AWS, o terraform vai ler esse arquivo para criar nossa infra.

Vale mencionar que a HashiCorp usa o HCL (HashiCorp Configuration Language) é uma linguagem de configuração. O objetivo do HCL é criar uma linguagem de configuração estruturada que seja humana e amigável à máquina para uso com ferramentas de linha de comando, mas voltada especificamente para ferramentas, servidores, DevOps, etc.

Para mais informações sobre essa linguagem, [clique aqui](https://www.terraform.io/docs/configuration/syntax.html).

Vamos então à criação do arquivo de nome *instance.tf* conforme abaixo:

```
  1 # Configure the AWS Provider
  2 provider "aws" {
  3   region  = "us-east-1"
  4   shared_credentials_file = "/home/userteste/.aws/credentials"
  5   profile = "awsterraform"
  6 }
  7 
  8 
  9 resource "aws_instance" "modeloteste" {
 10   ami = "ami-00a208c7cdba991ea"
 11   instance_type = "t2.micro"
 12 }
```

Aqui nesse arquivo especificamos que estamos usando o provider AWS, estamos usando a região “us-east-1”, compartilhamos nossas chaves de acesso, que foram obtidas lá na criação do usuário na AWS.

Passamos o AMI (seria um identificador) para dizer que estamos criando uma distro Ubuntu, e usando o tipo de instância “t2.micro”, esse tipo de instância faz parte do free tier da Amazon. Sempre que for consultar os providers use a documentação da ferramenta, link [aqui](https://www.terraform.io/docs/providers/index.html).

- **Iniciando o case com o Terraform**

Feito isso, vamos testar usando o comando `terraform init` esse comando faz o download dos plugins da AWS.

```
# terraform initInitializing the backend...Initializing provider plugins...
- Checking for available provider plugins...
- Downloading plugin for provider "aws" (hashicorp/aws) 2.40.0...The following providers do not have any version constraints in configuration,
so the latest version was installed.To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.* provider.aws: version = "~> 2.40"Terraform has been successfully initialized!You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

Veja que foi criado um arquivo oculto de nome `.terraform`

```
# ls -la
total 16
drwxr-xr-x  3 root    root         4096 nov 27 14:51 .
drwxr-xr-x 46 user1   root         4096 nov 27 08:56 ..
-rw-r--r--  1 root    root          266 nov 27 14:47 instance.tf
drwxr-xr-x  3 root    root         4096 nov 27 14:48 .terraform
```

Agora vamos executar o comando `terraform plan` . Isso nos permitirá ver o que o Terraform fará antes de decidirmos definir a infra, pode ser que demore um pouco a saída desse comando. Mostra tudo sobre a instância na AWS:

```
# terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.------------------------------------------------------------------------An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + createTerraform will perform the following actions:# aws_instance.modeloteste will be created
  + resource "aws_instance" "modeloteste" {
      + ami                          = "ami-00a208c7cdba991ea"
      + arn                          = (known after apply)
      + associate_public_ip_address  = (known after apply)
      + availability_zone            = (known after apply)
      + cpu_core_count               = (known after apply)
      + cpu_threads_per_core         = (known after apply)
      + get_password_data            = false
      + host_id                      = (known after apply)
      + id                           = (known after apply)
      + instance_state               = (known after apply)
      + instance_type                = "t2.micro"
      + ipv6_address_count           = (known after apply)
      + ipv6_addresses               = (known after apply)
      + key_name                     = (known after apply)
      + network_interface_id         = (known after apply)
      + password_data                = (known after apply)
      + placement_group              = (known after apply)
      + primary_network_interface_id = (known after apply)
      + private_dns                  = (known after apply)
      + private_ip                   = (known after apply)
      + public_dns                   = (known after apply)
      + public_ip                    = (known after apply)
      + security_groups              = (known after apply)
      + source_dest_check            = true
      + subnet_id                    = (known after apply)
      + tenancy                      = (known after apply)
      + volume_tags                  = (known after apply)
      + vpc_security_group_ids       = (known after apply)+ ebs_block_device {
          + delete_on_termination = (known after apply)
          + device_name           = (known after apply)
          + encrypted             = (known after apply)
          + iops                  = (known after apply)
          + kms_key_id            = (known after apply)
          + snapshot_id           = (known after apply)
          + volume_id             = (known after apply)
          + volume_size           = (known after apply)
          + volume_type           = (known after apply)
        }+ ephemeral_block_device {
          + device_name  = (known after apply)
          + no_device    = (known after apply)
          + virtual_name = (known after apply)
        }+ network_interface {
          + delete_on_termination = (known after apply)
          + device_index          = (known after apply)
          + network_interface_id  = (known after apply)
        }+ root_block_device {
          + delete_on_termination = (known after apply)
          + encrypted             = (known after apply)
          + iops                  = (known after apply)
          + kms_key_id            = (known after apply)
          + volume_id             = (known after apply)
          + volume_size           = (known after apply)
          + volume_type           = (known after apply)
        }
    }Plan: 1 to add, 0 to change, 0 to destroy.------------------------------------------------------------------------Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

Nesse momento sabemos tudo que o Terraform vai fazer, sendo assim, para criar a instância, executamos o comando `terraform apply`

```
# terraform applyAn execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + createPlan: 1 to add, 0 to change, 0 to destroy.Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.Enter a value: yesaws_instance.modeloteste: Creating...
aws_instance.modeloteste: Still creating... [10s elapsed]
aws_instance.modeloteste: Still creating... [20s elapsed]
aws_instance.modeloteste: Still creating... [30s elapsed]
aws_instance.modeloteste: Still creating... [40s elapsed]
aws_instance.modeloteste: Creation complete after 40s [id=i-0f7e4c2a21bf6949c]Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

Se formos para o console da AWS, podemos ver que uma instância t2.micro foi criada usando o Terraform:

![img](https://miro.medium.com/max/60/1*PSfU40bsWejieKcUq5Q-cQ.png?q=20)

![img](https://miro.medium.com/max/700/1*PSfU40bsWejieKcUq5Q-cQ.png)

AWS EC2 Instance

Caso você queira destruir a infraestrutura do Terraform que foi criada, podemos executar o comando `terraform destroy` .Isso destruirá todos os recursos criados por nós nesse artigo.

Esse artigo não cobre a parte de acesso à instância criada via SSH, foi apenas um exemplo básico de como você pode começar a usar o Terraform. Nos próximos artigos irei detalhar outros exemplos com Ansible e Terraform.

Gostaria de citar a semana DevOps da LinuxTips que ocorreu esse ano, eu aprendi muito com o pessoal 

[Jeferson Fernando](https://medium.com/u/a958bab29066?source=post_page-----e364f5d31570-----------------------------------)

, 

[Mateus Prado](https://medium.com/u/cadbfb2a9902?source=post_page-----e364f5d31570-----------------------------------)

, 

[Rafael Gomes](https://medium.com/u/74d7a70cb8c2?source=post_page-----e364f5d31570-----------------------------------)

 entre outros. Aproveito também para divulgar a semana de IAAS que vai ocorrer agora em Dezembro, não deixem de participar. Link [aqui](https://www.iaasweek.com/). #VAIIII



[Amaury Borges Souza](https://amauryborgesouza.medium.com/?source=post_sidebar--------------------------post_sidebar--------------)

Passion for DevOps

Passion for DevOps

[Nov 20, 2019](https://amauryborgesouza.medium.com/capacityplanning-8991ddb3bef3?source=follow_footer-----e364f5d31570----0-------------------------------)[![img](https://miro.medium.com/max/700/1*vmTO2iRrSDUyGCVPbdumJg.jpeg)](https://amauryborgesouza.medium.com/capacityplanning-8991ddb3bef3?source=follow_footer-----e364f5d31570----0-------------------------------)[Planejamento de capacidade: Conheça os comandos iostat, vmstat e mpstat](https://amauryborgesouza.medium.com/capacityplanning-8991ddb3bef3?source=follow_footer-----e364f5d31570----0-------------------------------)Fala pessoal, a partir de hoje irei mostrar uma série de artigos aqui sobre Linux, desde a parte de performance, comandos essenciais de rede, manutenção do sistema até serviços web. Fique ligado nos próximos posts.Hoje o artigo será sobre planejamento de capacidade, e vamos aprender alguns comandos que são…[Read more · 5 min read](https://amauryborgesouza.medium.com/capacityplanning-8991ddb3bef3?readmore=1&source=follow_footer-----e364f5d31570----0-------------------------------)4[1](https://amauryborgesouza.medium.com/capacityplanning-8991ddb3bef3?responsesOpen=true&source=follow_footer-----e364f5d31570----0-------------------------------)[Nov 15, 2019](https://amauryborgesouza.medium.com/jenkins-4c799968a0a0?source=follow_footer-----e364f5d31570----1-------------------------------)[![img](https://miro.medium.com/max/700/1*jPLKp7XMzFDGNuE8vShYDQ.png)](https://amauryborgesouza.medium.com/jenkins-4c799968a0a0?source=follow_footer-----e364f5d31570----1-------------------------------)[Jenkins: Veja a instalação com Docker](https://amauryborgesouza.medium.com/jenkins-4c799968a0a0?source=follow_footer-----e364f5d31570----1-------------------------------)Opa pessoal! Já fazia tempo que eu estava querendo escrever sobre essa ferramenta de CI/CD mas por muitos contratempos tive que ficar adiando… Enfim por aqui e agora mãos à obra.O objetivo aqui e mostrar de forma simples como efetuar a instalação do Jenkins usando o Docker. …[Read more · 3 min read](https://amauryborgesouza.medium.com/jenkins-4c799968a0a0?readmore=1&source=follow_footer-----e364f5d31570----1-------------------------------)[Nov 1, 2019](https://amauryborgesouza.medium.com/docker-owncloud-7e3b425f3fba?source=follow_footer-----e364f5d31570----2-------------------------------)[![img](https://miro.medium.com/max/646/1*WR5A69_MmOwQsfTRjjbbxA.png)](https://amauryborgesouza.medium.com/docker-owncloud-7e3b425f3fba?source=follow_footer-----e364f5d31570----2-------------------------------)ownCloud[Read more · 6 min read](https://amauryborgesouza.medium.com/docker-owncloud-7e3b425f3fba?readmore=1&source=follow_footer-----e364f5d31570----2-------------------------------)4[Oct 30, 2019](https://amauryborgesouza.medium.com/devops-days-e30aefbf445?source=follow_footer-----e364f5d31570----3-------------------------------)[![img](https://miro.medium.com/max/339/1*PuU39zs9gkieTaCOKoQOgA.png)](https://amauryborgesouza.medium.com/devops-days-e30aefbf445?source=follow_footer-----e364f5d31570----3-------------------------------)DevOps Days Campinas[DevOps Days Campinas: O primeiro de MUITOS 🚀](https://amauryborgesouza.medium.com/devops-days-e30aefbf445?source=follow_footer-----e364f5d31570----3-------------------------------)Falaaaaaaaaaaaaaaa pessoal, nós aqui de novo, dessa vez o artigo será sobre o DevOps Days, deixarei as ferramentas técnicas um pouco de lado para mostrar um pouco da minha experiência nesse evento TOP que ocorreu na sexta-feira. 🔝[Read more · 3 min read](https://amauryborgesouza.medium.com/devops-days-e30aefbf445?readmore=1&source=follow_footer-----e364f5d31570----3-------------------------------)2[Oct 16, 2019](https://amauryborgesouza.medium.com/comandos-docker-af525b957004?source=follow_footer-----e364f5d31570----4-------------------------------)[![img](https://miro.medium.com/max/700/1*KjeQrI4HL76XnpLIb_Y2oQ.png)](https://amauryborgesouza.medium.com/comandos-docker-af525b957004?source=follow_footer-----e364f5d31570----4-------------------------------)Docker[Docker: COMANDOS básicos com EXEMPLOS que vão te AJUDAR no dia a dia](https://amauryborgesouza.medium.com/comandos-docker-af525b957004?source=follow_footer-----e364f5d31570----4-------------------------------)Opa pessoal, iniciando mais um artigo hoje com foco em Docker. O objetivo é mostrar de forma básica e rápida o uso de alguns comandos do Docker que são essenciais na administração de containers e por cima, exemplificar a utilização. Sem mais delongas… 😃[Read more · 6 min read](https://amauryborgesouza.medium.com/comandos-docker-af525b957004?readmore=1&source=follow_footer-----e364f5d31570----4-------------------------------)14

