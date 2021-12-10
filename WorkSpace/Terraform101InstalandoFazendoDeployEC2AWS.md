# Terraform 101 - Instalando e fazendo deploy de EC2 na AWS

- [![Caio Delgado](https://www.gravatar.com/avatar/53fda418662ad245cab1d60fc20e49ff?s=250&d=mm&r=x)](https://caiodelgado.dev/author/caiodelgado/)

- [Caio Delgado](https://caiodelgado.dev/author/caiodelgado/) - 12 de Abr de 2020 • 8 min read

![Terraform 101 -  Instalando e fazendo deploy de EC2 na AWS](https://caiodelgado.dev/content/images/size/w2000/2020/04/terraform_d56939b1fa30e9c48acec1ccd8d4e507.png)

**[Terraform](https://caiodelgado.dev/p/13fdebd0-f105-4ada-93f0-1354e78f5fe8/www.terraform.io)** é uma ferramenta da [**Hashicorp**](https://caiodelgado.dev/p/13fdebd0-f105-4ada-93f0-1354e78f5fe8/www.hashicorp.com) para construir, modificar e versionar a infraestrutura de maneira segura e eficiente.

Através do terraform adicionamos camadas de abstração a serviços de cloud como Google Cloud Platform, Amazon Web Services, Microsoft Azure, Digital Ocean, dentre outros serviços não somente de cloud.

Primeiramente precisamos entender dois recursos chaves do Terraform para gerenciar nossa infraestrutura: **Providers** e **Provisioners.**

**[Providers](https://www.terraform.io/docs/providers/index.html)** são responsáveis por interagir com a API e expor os recursos. Geralmente quando falamos de providers estamos falando de IaaS (Infrastructure as a Service), PaaS (Platform as a Service) ou Saas (Softwares as a Service)

Alguns exemplos de providers:

- **IaaS:** GCP, AWS, Azure, Digital Ocean, Alibaba Cloud, Openstack.
- **PaaS:** Heroku.
- **SaaS:** Cloudflare, DNSSimple, DNSMadeEasy.

**[Provisioners](https://www.terraform.io/docs/provisioners/index.html)** são responsáveis por provisionar o ambiente, podemos dizer que provisioners são o ultimo recurso e devem ser utilizados apenas se a API do provider não fornecer todos os recursos que precisamos.

Alguns exemplos de provisioners:

- file
- local-exec
- remote-exec
- chef
- puppet
- salt

O Terraform utiliza a linguagem **[HCL](https://github.com/hashicorp/hcl/blob/hcl2/hclsyntax/spec.md)** (Hashicorp Configuration Language) que é uma linguagem declarativa de fácil entendimento e compatível com **[JSON](https://www.json.org/).**

### Vamos a prática

> Em todos os laboratórios deste blog utilizamos distribuições de linux, também utilizamos os comandos como super user, em ambientes de produção lembre-se de tomar as precauções de segurança.

> **ATENÇÃO:** Como estamos trabalhando em um ambiente de Cloud, lembre-se de destruir o laboratório quando terminar o mesmo para evitar dores de cabeça com cobrança, uma vez que os provedores de Cloud irão cobrar pelos recursos que estiverem na infraestrutura.

Para rodar o terraform não precisamos mais do que uma máquina linux e terminal. Caso esteja utilizando outro sistema operacional, verifique no [site do terraform na seção de downloads ](https://www.terraform.io/downloads.html)o binário correto para seu sistema.

### Amazon Web Services

Primeiramente precisamos da nossa conta na AWS, pode ser inclusive a conta **FreeTier** pra quem ainda não tem uma conta na mesma.

Não irei cobrir o passo a passo da criação da conta uma vez que já possúo minha conta da AWS e existem diversos outros blogs explicando o passo a passo de como criar uma e inclusive o próprio site da **AWS** [](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)

Acesse o [console da AWS ](https://console.aws.amazon.com/)e faça o login com sua conta e pesquise pelo produto **IAM** (Identity and Access Management)

![img](https://caiodelgado.dev/content/images/2020/04/image-2.png)AWS Services Console 

Iremos criar um usuário para interagir com a AWS, clique em **USERS** e em seguida em **ADD USER**

![img](https://caiodelgado.dev/content/images/2020/04/image-3.png)Add User

Digite um nome para o usuário, selecione **programatic access** para que seja criado um ID e Senha para a chave de acesso e clique em **Next.**

![img](https://caiodelgado.dev/content/images/2020/04/image-18.png)Programatic Access

Adicione a politica **AmazonEC2FullAccess** ao usuário, o que dará permissão total ao usuário apenas a recursos da EC2, e clique em **Next.**

![img](https://caiodelgado.dev/content/images/2020/04/image-12.png)Attaching Policies

**Tags** são utilizadas para adicionar informações relevantes ao usuario, clique em **Next.**

![img](https://caiodelgado.dev/content/images/2020/04/image-13.png)

Verifique os dados e clique em **Create user**

![img](https://caiodelgado.dev/content/images/2020/04/image-14.png)Create User

Clique em **show** e copie o **Access key ID** e **Secret access key**

![img](https://caiodelgado.dev/content/images/2020/04/image-15.png)Access Key and Secret Access Key 

> O usuário criado e chave de acesso não devem ser compartilhados, uma vez que quem tiver acesso a estes dados terá controle sobre os recursos adicionados como política, por questões de segurança este usuário não existe mais em minha conta.

### Instalando o AWS CLI

Iremos [instalar o AWS CLI ](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)(Command Line Interface) para autenticarmos via terminal e possibilitar nosso acesso via terraform.

```bash
$ cd /tmp
$ curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
$ unzip awscliv2.zip
$ sudo ./aws/install
```

Verifique a instalação através do comando `aws --version` que deve retornar a versão atual do `aws cli` instalado, agora iremos configurar nosso cli com o ID e chave de acesso criadas anteriormente, execute o comando `aws configure` e em seguida digite o ID e Chave (para região e output format apenas tecle **<ENTER>**).

![img](https://caiodelgado.dev/content/images/2020/04/image-21.png)$ aws configure

Isso vai configurar o arquivo `~/.aws/credentials` com suas credenciais

![img](https://caiodelgado.dev/content/images/2020/04/image-20.png)file: ~/.aws/credentials

### Instalando o Terraform

Iremos fazer o download da versão do terraform **0.12.24** no momento da escrita deste post.

```bash
$ cd /tmp 
$ curl https://releases.hashicorp.com/terraform/0.12.24/terraform_0.12.24_linux_amd64.zip -o terraform.zip
$ unzip terraform.zip
$ sudo mv terraform /usr/local/bin
$ terraform --version
```

Caso o comando `terraform --version` retorne a versão correta, no nosso caso `Terraform v0.12.24` , significa que a instalação ocorreu com sucesso.

## Criando o código HCL

Os arquivos utilizados pelo terraform tem a terminação `.tf` , eu particularmente gosto de separar em diversos arquivos para que fique mais fácil a manutenção do mesmo, então vamos criar nosso primeiro código para criar uma instância na AWS.

Criaremos o diretório para armazenar nosso código bem como um arquivo para configurar nosso backend.

```bash
$ mkdir ~/terraform-101
$ cd ~/terraform-101
$ vim backend.tf
```

No arquivo `backend.tf` iremos adicionar o conteúdo para informar qual será o provider utilizado, no nosso caso **aws**, bem como a região que será aplicada.

file: `~/terraform-101/backend.tf`

```terraform
provider "aws" {
   region = var.region
}
```

Declaramos um conteudo proveniente de uma variavel chamada region através do parâmetro `var.<nome_variavel>` , iremos definir o conteúdo desta variável posteriormente.

> O uso de variáveis não é necessário, podemos simplesmente declarar os valores diretamente no arquivo, porém ao utilizar as variáveis a manutenção e o reaproveitamento do código é feito de maneira mais simples

Vamos criar um arquivo `ec2.tf` que será responsável pela definição da nossa instância ec2.

```bash
$ vim ec2.tf
resource "aws_instance" "server" {
  ami           = var.ami
  instance_type = var.instance_type

  tags = {
    Name        = var.name
    Environment = var.env
    Provisioner = "Terraform"
  }
}
```

Novamente estamos declarando diversas variáveis e precisamos definilas em um arquivo para que seja utilizada. Criaremos então o arquivo `variables.tf` onde definiremos estas variáveis.

```bash
$ vim variables.tf
variable "region" {
  description = "Define what region the instance will be deployed"
  default = "us-east-1"
}

variable "name" {
  description = "Name of the Application"
  default = "server01"
}

variable "env" {
  description = "Environment of the Application"
  default = "prod"
}

variable "ami" {
  description = "AWS AMI to be used "
  default = "ami-07ebfd5b3428b6f4d"
}

variable "instance_type" {
  description = "AWS Instance type defines the hardware configuration of the machine"
  default = "t2.micro"
}
```

> O campo `description` informa a descrição de cada variável, é uma boa prática dizer o que cada variável significa e/ou para que é utilizada.

> O campo `default` informa o valor padrão da variável.

Agora que ja criamos nosso arquivo precisamos executar o comando `terraform init` para que seja feito download dos providers do terraform em nosso diretório.

![img](https://caiodelgado.dev/content/images/2020/04/image-22.png)$ terraform init

Agora que iniciamos o provider teremos uma pasta oculta `.terraform` em nosso diretório com os plugins necessários.

![img](https://caiodelgado.dev/content/images/2020/04/image-24.png)Terraform 101 Directory Structure

Podemos agora efetuar o planejamento da nossa infraestrutura através do comando `terraform plan` , que será responsável por nos mostrar qual seria o resultado após a aplicação do nosso código.

![img](https://caiodelgado.dev/content/images/2020/04/image-25.png)$ terraform plan

No final da saída do comando é exibido quantos planos serão aplicados, modificados ou destruídos.

![img](https://caiodelgado.dev/content/images/2020/04/image-26.png)$ terraform plan

Para aplicar o plano utilizaremos o comando `terraform apply` que nos mostrará o resultado do plan e perguntando se queremos aplicar o recurso, digite **yes** para iniciar a aplicação.

![img](https://caiodelgado.dev/content/images/2020/04/image-28.png)$ terraform apply

Após isto é exibida a informação dos recursos que estão sendo criados bem como o estado final após execução

![img](https://caiodelgado.dev/content/images/2020/04/image-31.png)$ terraform apply

No painel da **AWS** podemos ir em **EC2** e verificar a instância que foi criada conforme as configurações listadas.

![img](https://caiodelgado.dev/content/images/2020/04/image-32.png)AWS EC2

Ao executar um terraform, é criado um arquivo `terraform.tfstate` no diretório com o conteúdo do que foi criado/modificado para que o terraform possa efetuar ações de modificação na infraestrutura sabendo seu estado anterior.

Vamos alterar o nome da variável `name` para `server02` e executar novamente o terraform apply.

```bash
vim variables.tf
variable "region" {
  description = "Define what region the instance will be deployed"
  default = "us-east-1"
}

variable "name" {
  description = "Name of the Application"
  default = "server02"
}

variable "env" {
  description = "Environment of the Application"
  default = "prod"
}

variable "ami" {
  description = "AWS AMI to be used "
  default = "ami-07ebfd5b3428b6f4d"
}

variable "instance_type" {
  description = "AWS Instance type defines the hardware configuration of the machine"
  default = "t2.micro"
}
```

Execute o `terraform plan` e verifique que o nome da instância será modificado, porém ela não será destruida e recriada.

![img](https://caiodelgado.dev/content/images/2020/04/image-34.png)$ terraform plan

Podemos executar o `terraform apply` para aplicar a configuração**.**

![img](https://caiodelgado.dev/content/images/2020/04/image-35.png)$ terraform apply

Note que agora é exibido que um componente foi atualizado.

![img](https://caiodelgado.dev/content/images/2020/04/image-36.png)$ terraform apply

Podemos também verificar na **EC2** da **AWS** que a modificação foi efetuada com sucesso.

![img](https://caiodelgado.dev/content/images/2020/04/image-37.png)AWS EC2

Para destruir a instância podemos executar o comando `terraform destroy`

![img](https://caiodelgado.dev/content/images/2020/04/image-33.png)$ terraform destroy 

Digite **yes** para destruir a instância, note que o processo todo levará um tempo para ser executado, aguarde o retorno pelo terminal.

![img](https://caiodelgado.dev/content/images/2020/04/image-38.png)$ terraform destroy

Verifique também no **EC2** da **AWS**  que a instância foi terminada.

![img](https://caiodelgado.dev/content/images/2020/04/image-40.png)AWS EC2

Irei continuar uma série de posts sobre Terraform e gradativamente aumentando a complexidade com outros recursos e inclusive integrando com **ansible** e outras ferramentas. Não deixe de acompanhar o blog!

O código deste post encontra-se no repositório: https://github.com/caiodelgadonew/blog-terraform-101 

Ficamos por aqui com esse post e nos vemos em uma próxima!