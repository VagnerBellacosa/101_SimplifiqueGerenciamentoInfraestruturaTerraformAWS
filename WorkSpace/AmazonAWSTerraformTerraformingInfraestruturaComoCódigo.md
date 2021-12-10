## Amazon AWS + Terraform + Terraforming = Infraestrutura como Código

  

<iframe id="google_ads_iframe_/10586137/PTI_Artigos_Inicio_300x250_0" title="3rd party ad content" name="google_ads_iframe_/10586137/PTI_Artigos_Inicio_300x250_0" width="300" height="250" scrolling="no" marginwidth="0" marginheight="0" frameborder="0" role="region" aria-label="Advertisement" tabindex="0" allow="attribution-reporting" srcdoc="" data-google-container-id="1" data-load-complete="true" style="max-width: 100%; border: 0px; background: none; box-shadow: none; vertical-align: bottom;"></iframe>

Neste artigo estaremos vendo como provisionar uma instância (máquina virtual) em poucos minutos no serviço IasS da Amazon AWS, o famoso EC2.

O provisionamento da instância, e outras configurações da infraestrutura sob a Amazon AWS, serão realizadas pela ferramenta Terraform da Hashicorp.

### Amazon AWS

O Amazon Web Services ou AWS, é uma plataforma de serviços de computação em nuvem que proporciona a plataforma de computação em nuvem da Amazon, plataforma essa líder de mercado, conforme mostra [Quadrante Mágico do Gartner de 2016](https://aws.amazon.com/pt/resources/gartner-2016-mq-learn-more/).

Seu portfólio de serviços vai muito além de Infraestrutura como Serviço e seu clássico serviço EC2, portfólio que pode ser acessado em [aws.amazon.com/pt/products/?nc1=f_cc](https://aws.amazon.com/pt/products/?nc1=f_cc).

### Mãos a obra: AWS

Caso não possua uma conta na AWS, você pode inicialmente criar uma conta para uso gratuito por 12 meses, o que demandará uma cartão de crédito internacional ou que você utilize um cartão do tipo pré pago.

A criação da conta e demais informações sob as condições da gratuidade estão em [aws.amazon.com/pt/free/](https://aws.amazon.com/pt/free/).

Com a conta já criada, basta acessar o console de gerenciamento onde iremos inicialmente criar um usuário.

Para o provisionamento na Amazon recomendo que crie no **IAM** (Identity and Access Management) um usuário acessando [console.aws.amazon.com/iam/home](https://console.aws.amazon.com/iam/home)



Como o Terraform irá autenticar-se com o serviço que proporciona a API da AWS, iremos necessitar de um ID e chave, respectivamente uma Access Key e Secret Key, portanto, por questões de segurança não iremos gerar a chave para conta root.

Em seguida acesse “**Create individual IAM users**“, e depois “**Manager Users**“.

Clique em “**Add User**“, preencha o campo “**User name**” e deixe marcado apenas o checkbox “**Programmatic access**“.

Clique em “**Next: Permissions**” e em seguida clique em “**Attach existing policies directly**” para podermos atribuir os privilégios a esta conta.

Marque o checkbox do privilégio “**AmazonEC2FullAccess**” e em seguida clique no botão “**Next: Review**“.

> Por questões de simplicidade para o presente artigo, selecionamos um privilégio elevado para o usuário, entretanto, para ambientes em produção, a recomendação é definir privilégios de maneira mais granular, atribuir os mesmos a grupos de usuários e associar os usuários criados a estes grupos.

Em seguida clique no botão “**Create user**” e clique no botão “**Download .csv**” para que a Access key e a Secret key sejam armazenados em sua estação em arquivo formato CSV.

Copie a Access key ID e a Secret access key, pois iremos utilizar estes valores para que o Terraform autentique na AWS e possa interagir com a API para assim provisionar a infraestrutura.



No provisionamento que iremos realizar estaremos especificando a subnet que a instância utilizará, portanto, clique no menu “**Services**” (que fica localizado no canto superior esquerdo do Console de Gerenciamento da AWS) e na caixa de texto informe o termo “**VPC**“.

No canto esquerdo clique em “Subnets”, copiaremos subnet-id da rede pública, no meu caso: **subnet-1aa9cf80**. Esta informação será utilizada no provisionamento da nossa instância no EC2.

Nos próximos artigos será demonstrado como criar a infraestrutura completa, envolvendo a criação de VPC (Virtual Private Cloud), ELB (Elastic Load Balance) e associar instâncias ao Load Balance, bem como Security Groups.

Veremos também em próximos artigos como segmentar a estrutura do código no Terraform para, por exemplo, definirmos variáveis e seus valores em arquivos separados (arquivos com extensão .tfvars).

### Terraform

O Terraform foi desenvolvido pela HashiCorp, empresa fundada em 2012 com um principal foco: desenvolver ferramentas que auxiliem profissionais Desenvolvedores e de Infraestrutura (Operations) a provisionar, proteger e executar infraestrutura de aplicações distribuídas.

> Assim como o Terraform, a HashiCorp desenvolveu outra excelente ferramenta para provisionamento chamada Vagrant e que será tema de outro artigo.



É uma ferramenta recente para implementações de infraestrutura como código (IaC), porém, com uma comunidade bastante ativa que ajuda no desenvolvimento de novas versões.

O uso de ferramentas como o Terraform não só proporciona grande agilidade para provisionar uma infraestrutura, como também o fato de todas as definições das estrutura estarem codificadas, isso possibilita a documentação de toda a infraestrutura.

O Terraform possui compatibilidade com diversos CSP (Cloud Service Providers) dentre eles Amazon AWS, Google Cloud Plataform, Microsoft Azure, Openstack, CloudStack, bem como provedores que oferecem SaaS (Software como Serviço) como, por exemplo, Cloudfare e DNSimple.

Outra excelente ferramenta para provisionamento, gerenciamento de configurações e entrega de aplicações é o Ansible, onde estaremos em um próximo artigo utilizando o Terraform em conjunto com o Ansible para instalar um Web Server com Apache.

Neste mesmo artigo utilizamos uma ferramenta chamada **Terraforming**, que complementará as capacidades do Terraform permitindo exportar todos os recursos alocados sob a AWS, de modo que esta exportação poderá, por exemplo, gerar os arquivos no formato que o Terraform utiliza.

Deste modo, podemos não só obter uma documentação de todos os recursos que compõem a infraestrutura sob a AWS, como podemos adaptar as informações exportadas da AWS para recriar a estrutura para outro projeto/finalidade.

Este artigo será baseado na distribuição CentOS 7, entretanto, o Terraform poderá ser utilizado em outras distribuições, inclusive sob Mac OS/X e Windows.

### Mãos a obra: Terraform e Terraforming

– Realize o download do Terraform:

```
wget https://releases.hashicorp.com/terraform/0.9.11/terraform_0.9.11_linux_amd64.zip
```

– Descompacte o arquivo do Terraform:

```
unzip terraform_0.9.11_linux_amd64.zip
```

– Mova o binário extraído e verifique a versão:

```
mv terraform /usr/local/bin/
terraform --version
```

– Instalando o Terraforming:

Para instalar o Terraforming e poder obter informações sobre os recursos em uso da sua infraestrutura na AWS, iremos necessitar instalar o pacote do Ruby Gems, pois o Terraforming foi desenvolvido em Ruby. O Ruby Gems é um gerenciador de pacotes que permite instalar pacotes e bibliotecas desenvolvidas utilizando a linguagem Ruby.

```
yum install gem -y
gem install terraforming
```

– Iremos  exportar três variáveis de ambiente do Shell para podermos realizar a autenticação na AWS.

As variáveis irão a Access Key, a Secret Access Key e a região que está utilizando, no meu caso, estou utilizando a região SA-EAST-1.

```
export AWS_ACCESS_KEY_ID=sua_access_key_id
export AWS_SECRET_ACCESS_KEY=sua_secret_key
export AWS_REGION=sa-east-1
```

– Para evitar problemas de autenticação relacionadas a divergência de horário em sua estação, recomendo que sincronize o horário, por exemplo:

```
yum install ntp -y
ntpdate br.pool.ntp.org
```

– Obtenha informações relacionadas a suas instâncias EC2 utilizando:

```
terraforming ec2
```

Caso você ainda não tenha criado qualquer instância no Console da AWS, você não receberá informações, entretanto, iremos a seguir provisionar uma instância Linux para poder testar o Terraforming novamente.

– Crie um diretório para armazenar os arquivos do provisionamento do Terraform e Ansible

```
mkdir ~/devops
cd ~/devops
```

– Crie um arquivo chamado “credentials.tf” para armazenarmos as credenciais de autenticação junto a AWS.

```
vi credentials.tf

provider "aws" {

    access_key = "sua_access_key"

    secret_key = "sua_secret_key"

    region = "sa-east-1"

}
```

– Crie um arquivo chamado “webserver-teste.tf” para armazenarmos a infraestrutura que será provisionada, neste caso, uma instância EC2:

```
resource "aws_instance" "web" {

    ami = "ami-d1a7ccbd"

    instance_type = "t2.micro"

    associate_public_ip_address = true

    tags {

        Name = "Web Server Teste"

    }

    subnet_id = "subnet-1aa9cf80"

}
```

O arquivo acima informa que será criada uma instância EC2 com nome de **web** e utilizará a Amazon Machine Image (AMI) ami-d1a7ccdb como base, que no caso é uma imagem do Ubuntu Linux 14.04.

> Para maiores detalhes, busque esta imagem no Ubuntu Cloud Image Finder em: [cloud-images.ubuntu.com/locator/](https://cloud-images.ubuntu.com/locator/)

Esta instância será do tipo **t2.micro**, tipo esse definido como uma instância de 1 vCPU e 1 GB de vRAM.

O parâmetro **associate_public_ip_address** fará com que a instância tenha disponibilizado um endereço IP público, onde qualquer requisição realizada nele será encaminhada para a instância.

O bloco **tags** no caso acima permitiu definir uma tag que facilitará a busca por essa instância, no caso, a tag **name**, pois é uma boa prática definir descrições breves a partir da tag name quando uma instância é criada.

Por fim, o parâmetro **subnet_id** permitiu especificar que a instância terá uma interface virtual conectada a subnet pública dentro da nossa VPC, e assim a instância poderá ser acessada via Internet através do endereço IP público associado a ela e, claro, de acordo com as regras definidas no Security Group associado a instância.

– Execute o provisionamento com o Terraform e visualize as informações sobre o provisionamento:

```
terraform apply
terraform show
```

– Verifique no Console da AWS se a instância foi criada e está em execução. O link abaixo leva diretamente a listagem de instâncias criadas:

https://sa-east-1.console.aws.amazon.com/ec2/v2/home?region=sa-east-1#Instances:sort=desc:tag:Name

*OBS: o link acima considera que a Região utilizada é a sa-east-1 (São Paulo).*

– Utilize o terraforming para obter as informações relacionadas a instância em execução:

```
terraforming ec2
```

Independente da opção show do terraform exibir muitos detalhes, a ferramenta terraforming apresenta a possibilidade de obter informações separadamente sobre cada recurso disponível sob o AWS, por exemplo:

– Se desejarmos obter dados sobre as VPCs existentes:

```
terraforming vpc
```

– Para obter dados sobre os Security Groups existentes:

```
terraforming sg
```

– Para obtermos dados sobre as tabelas de roteamos, dados como, bloco de endereçamento, default gateway e a qual VPC pertence estas tabelas de roteamos:

```
terraforming rt
```

– A informação que obtivemos via Console da AWS (subnet pública) também poderia ser obtida diretamente pelo Terraforming:

```
terraforming sn
```

Para verificar outras opções, informe o comando **terraforming** sem passar qualquer parâmetro. No caso deste artigo, diversas opções não resultaram na obtenção de dados, pois o usuário que foi criado não possui privilégios suficientes.

A grande vantagem do Terraform é que podemos obter informações já no formato que utilizamos para construir os arquivos (.tf) do Terraform. É possível unir o arquivo de provisionamento do Terraform (.tf) com os dados atualizados e obtidos pelo Terraforming (processo denominado merge no Terraforming).

Deste modo, caso alguma alteração tenha sido realizada depois do provisionamento, é possível unir os valores atualizados com os valores anteriores e existentes no arquivo de provisionamento criado.

No próximo artigo estaremos configurando o Terraform para utilizar o Ansible como provisionador, para que após a instância estar criada, o Ansible possa provisionar os demais recursos na instância (instalação de serviços como do Apache, por exemplo).

Veremos também como provisionar a instância pelo Terraform e definir no momento da criação um par de chaves pública-privada para podermos realizar a conexão via SSH na instância.