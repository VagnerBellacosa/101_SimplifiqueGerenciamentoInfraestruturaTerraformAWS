# Conhecendo o Terraform

Publicado em [20 de julho de 2018](http://blog.aeciopires.com/conhecendo-o-terraform/)

![img](https://www.datocms-assets.com/2885/1506457071-blog-terraform-list.svg)

O [Terraform](https://www.terraform.io/) é uma ferramenta para construir, alterar e configurar uma infraestrutura de rede de forma segura e eficiente. Com ele é possível gerenciar serviços de nuvem bem conhecidos, bem como soluções internas personalizadas. Veja a lista completa de serviços de infraestrutura de nuvem suportados em: https://www.terraform.io/docs/providers/index.html.

Os arquivos de configuração do Terraform descrevem os componentes necessários para executar um único aplicativo ou todo o seu datacenter. Ele pode gerar um plano de execução descrevendo o que ele fará para atingir o estado desejado e, em seguida, ele pode executar as instruções para construir a infraestrutura descrita. Conforme a configuração muda, o Terraform é capaz de determinar o que mudou e criar planos de execução incrementais que podem ser aplicados.

O Terraform trata a infraestrutura como código e dessa forma você pode versioná-lo usando o Git, por exemplo, e ter um backup, fazer rollback em caso de problemas e fazer auditoria à medida que o tempo passa e as alterações vão sendo realizadas no seu ambiente.

O Terraform é desenvolvido e mantido pela empresa [Hashicorp](https://www.hashicorp.com/). Ele é gratuito com código fonte aberto e assim pode receber contribuições da comunidade no GitHub (https://github.com/hashicorp/terraform). Ele está disponível para download na página: https://www.terraform.io/downloads.html

Neste tutorial, será mostrado como instalar e usar o Terraform para criar um instância na AWS.

# Instalando o Terraform

\0) Instalando a versão 0.11.13 no Ubuntu 18.04 64 bits.

```
sudo su
apt-get update
apt-get install -y unzip
wget https://releases.hashicorp.com/terraform/0.11.13/terraform_0.11.13_linux_amd64.zip
unzip terraform_0.11.13_linux_amd64.zip -d /usr/bin/

terraform -v
terraform --help
```

# Usando o Terraform

\1) Crie uma conta **Free Tier** na Amazon https://aws.amazon.com/ siga as instruções das páginas: https://docs.aws.amazon.com/chime/latest/ag/aws-account.html e https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/free-tier-limits.html. Na criação da conta será necessário cadastrar um cartão de crédito, mas como você criará instâncias usando os recursos oferecidos pelo plano **Free Tier**, nada será cobrado se você não ultrapassar o limite para o uso dos recursos e tempo oferecidos e descritos no link anterior.

\2) Após a criação da conta na AWS, acesse a interface CLI da Amazon na página: https://aws.amazon.com/cli/

\3) Clique no nome do usuário (canto superior direito) e escolha a opção “**Security Credentials**“. Em seguida clique na opção “**Access Keys (Access Key ID and Secret Access Key)**” e clique no botão “**New Access Key**” para criar e visualizar o ID e o Secret da chave, conforme exemplo abaixo (https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html). A **Access Key** e **Secret Key** mostradas a seguir são apenas para exemplo. Elas são inválidas e você precisa trocar pelos dados reais gerados para sua conta.

```
Access Key ID: AKIAI3IZDFASDFASDFAS2OSCH7MDV3SQ
Secret Access Key: y+7sVdfsdfsZMfOSsdfasdfasfdfasdfasSHT
```

\4) Crie o diretório abaixo.

```
mkdir -p /home/NOME_USUARIO/.aws/
```

\5) Acesse o diretório criado anteriormente e crie o arquivo **credentials** com o seguinte conteúdo. A **Access Key** e **Secret Key** mostradas a seguir são apenas para exemplo. Elas são inválidas e você precisa trocar pelos dados reais gerados para sua conta.

```
[default]
aws_access_key_id = AKIAI3IZDFASDFASDFAS2OSCH7MDV3SQ
aws_secret_access_key = y+7sVdfsdfsZMfOSsdfasdfasfdfasdfasSHT
```

\6) Crie o diretório **packt-terraform**.

```
mkdir -p /home/NOME_USUARIO/packt-terraform/
```

\7) Acesse o diretório criado anteriormente e crie o arquivo **template.tf** com o seguinte conteúdo.

```
#AWS emUS-East-Ohio
provider "aws" {
region = "us-east-2"
}
```

\8) Dentro desse mesmo diretório em que está o template do terraform, execute os comandos abaixo para testar a comunicação e visualizar as mudanças a serem realizadas antes de aplicá-las no ambiente.

```
terraform providers
terraform init
terraform plan
terraform apply
```

\9) A lista de recursos e providers suportados pelo Terraform está disponível em:
https://www.terraform.io/docs/providers

\10) Agora atualize o conteúdo do arquivo **template.tf** para conter o seguinte conteúdo. Com estas configurações, será criada uma instância no serviço EC2 (Elastic Compute Cloud) da AWS do tipo **t2.micro** (Variable ECUs, 1 vCPUs, 2.5 GHz, Intel Xeon Family, 1 GiB memory, EBS only) usando o Ubuntu 16.04 64 bits.

```
# AWS in US-East-Ohio
provider "aws" {
  region = "us-east-2"
}
# Resource configuration
# This AMI is equivalent to Ubuntu 16.04 64 bits
resource "aws_instance" "hello-instance" {
ami = "ami-5e8bb23b"
  instance_type = "t2.micro"
  tags {
    Name = "hello-instance"
  }
}
```

\11) Salve as alterações e execute os comandos abaixo para visualizar as mudanças a serem realizadas e aplicá-las no ambiente.

```
terraform plan
terraform apply
```

\12) Acesse https://console.aws.amazon.com/ec2 para visualizar se a instância realmente foi criada e está sendo executada.

\13) Voltando ao diretório **/home/NOME_USUARIO/packt-terraform/,** você verá o arquivo **terraform.tfstate,** contendo o estado atual do seu ambiente. Esse arquivo será atualizado a cada mudança no ambiente usando o Terraform. As informações são mostradas no padrão JSON, mas você pode obter uma visualização no padrão “chave = valor” com o comando abaixo.

```
terraform state show aws_instance.hello-instance
```

Onde: **aws_instance.hello-instance** é o nome do recurso criado no arquivo **template.tf**.

\14) Se quiser destruir o ambiente, execute o comando abaixo. **É importante realizar este passo para evitar que a instância AWS fique ligada por tempo indefinido e você acabe ultrapassando os limites oferecidos pelo plano Free Tier e seja cobrado por isso posteriormente.** Se quiser apenas remover um recurso específico, sem destruir todo o ambiente, basta editar o aquivo **template.tf**, apagar o recurso e executar o comando **terraform apply**.

```
terraform destroy
```

\15) Organize e separe as configurações do ambiente gerenciado pelo Terraform em diretórios (um para cada tipo de ambiente) para evitar conflitos. Você também pode salvar e versionar o código no Git, dando commit, push e criando tags a cada mudança de configuração. Fazendo isso você estará praticando IAC (Infrastructure as code) https://en.wikipedia.org/wiki/Infrastructure_as_Code

\16) No Github eu publiquei alguns arquivos de exemplo de uso do Terraform: https://github.com/aeciopires/terraform

\17) Mais informações sobre o Terraform por ser obtidas em:

- [https://www.terraform.io/docs](https://www.terraform.io/docs/)
- https://www.slideshare.net/DevOpsWebinars/best-practices-of-infrastructure-as-code-with-terraform
- https://www.slideshare.net/leetrout/terraform-an-overview-introduction
- https://www.packtpub.com/networking-and-servers/getting-started-terraform-second-edition
- [https://www.oreilly.com/library/view/terraform-up-and/9781491977071](https://www.oreilly.com/library/view/terraform-up-and/9781491977071/)
- https://developers.cloudflare.com/terraform/tutorial/hello-world/
- https://hackernoon.com/introduction-to-aws-with-terraform-7a8daf261dc0
- [https://www.greenreedtech.com/vsphere-immutable-infrastructure-with-terraform](https://www.greenreedtech.com/vsphere-immutable-infrastructure-with-terraform/)
- https://www.youtube.com/watch?v=lrAycU7_XnQ
- https://www.youtube.com/watch?v=8mRZJcCgoS0
- https://github.com/hashicorp/terraform-guides
- https://github.com/GoogleCloudPlatform/terraformer

Esta entrada foi publicada em [Tutoriais](http://blog.aeciopires.com/category/tutoriais/) com as palavras-chave [aws](http://blog.aeciopires.com/tag/aws/), [docker](http://blog.aeciopires.com/tag/docker/), [google cloud](http://blog.aeciopires.com/tag/google-cloud/), [terraform](http://blog.aeciopires.com/tag/terraform/). Adicione o [link permanente](http://blog.aeciopires.com/conhecendo-o-terraform/) aos seus favoritos.