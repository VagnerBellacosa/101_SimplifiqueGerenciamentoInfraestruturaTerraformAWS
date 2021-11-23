# Terraform: Automatize a infraestrutura na nuvem

## Provisionando a primeira infraestrutura com o Terraform - Introdução

<video class="elasticMedia" frameborder="0" controls="" controlslist="nodownload" style="box-sizing: inherit; margin: 0px; padding: 0px; position: absolute; top: 0px; left: 0px; width: 820px; height: 461.25px; border: 0px;"></video>

Está procurando uma forma mais simples de administrar a infraestrutura? Script para lá, script para cá, esse negócio acaba saindo do controle. E está na hora, você, DevOps, migrar para uma ferramenta que te auxilie nesse processo.

E essa é a ideia desse curso. Nós vamos estudar o Terraform, uma ferramenta bem legal, bem popular no mundo do DevOps, e ela vai nos auxiliar a criar, administrar, tomar conta da nossa infraestrutura de um jeito simples.

Essa ferramenta é bem legal. Ela é open source e ela tem uma característica importante: ela não está presa a nenhum provedor. Com ela, você consegue administrar tanto teu ambiente na WS, no Google Cloud, não importa. Ela é uma ferramenta multiplataforma. Quero te dar boas-vindas ao curso. Eu sou o Ricardo Mercês e vou te acompanhar durante as aulas.

## Provisionando a primeira infraestrutura com o Terraform - Preparando o ambiente na AWS

<video class="elasticMedia" frameborder="0" controls="" controlslist="nodownload" style="box-sizing: inherit; margin: 0px; padding: 0px; position: absolute; top: 0px; left: 0px; width: 820px; height: 461.25px; border: 0px;"></video>

Então, vamos começar a trabalhar. Ricardo, qual é o pré-requisito para esse curso? Preciso de alguma coisa, além da ferramenta que nós vamos instalar? A ideia do Terraform, como eu disse na apresentação, vai nos ajudar a provisionar a infraestrutura. Para provisionar essa infraestrutura, é importante que você esteja ambientado com os recursos do teu provedor.

Por exemplo, você usa WS, é importante você saber: como é que funciona as instâncias, bucket, banco de dado. Você vai usar o Google Cloud, a mesma coisa. Cada provedor tem a sua particularidade. O que o Terraform vai nos permitir é programar o recurso que queremos de uma maneira mais simples. Mas é importante que você conheça bem a estrutura do provedor.

Por exemplo, nós vamos colocar uma Instância ec2. Para eu acessar o ec2, eu tenho que ter um security group. Não é o Terraform que te diz isso. Quem diz isso é a AWS. Se você tem alguma dúvida ainda sobre esse ambiente em nuvem, tem muitos serviços, muitos cursos, bem legais dentro da plataforma para você acompanhar, nivelar o conhecimento e tirar as tuas dúvidas.

No curso, eu optei por utilizar infra da AWS. Então nós vamos preparar o nosso Terraform para provisionar uma infra lá na AWS. E nessa hora você pergunta assim: Ricardo, se eu vou usar o Terraform para provisionar a infra da AWS, porque não utilizar o Cloud Formation, que é um recurso nativo da AWS? Excelente pergunta que você está me fazendo.

A ideia é nós aprendemos a usar uma ferramenta e abstrair os provedores. Ou seja, você aprendendo a utilizar o Terraform, você vai utilizar não só para AWS, mas para o Google Cloud, para a Digital Ocean, não importa a plataforma. Por isso que nós não estamos usando uma ferramenta nativa e sim partindo para uma ideia onde consigamos abstrair e gerenciar qualquer tipo de ambiente.

Vamos começar a trabalhar. Primeiro passo é fazer o download da ferramenta. Então aqui no site do Terraform io, você vai em download e escolhe aqui o teu ambiente. Você vai fazer download seja para o Linux, para Mac, não importa.

Botão direito, copiar o link, vamos abrir aqui o nosso shell para trabalharmos. Independente da distribuição, estou aqui no Mac, Linux, entre aspas é tudo a mesma coisa.

Então eu estou aqui no meu shell. Em geral, as distribuições novas tem vindo com esse diretório aqui: local./bin. Esse é um diretório aqui de binários é legal para nós organizarmos isso. Tem a liberdade de colocar aonde você quiser. Eu estou seguindo só um padrão, eu vou colocar o meu arquivo aqui dentro.

Que arquivo? O executável do Terraform. Então eu estou dentro desse diretório, lembra que ele é um diretório aqui oculto, escondido, .localbin. Eu digito: w get e jogo o link que eu copiei.

Fez o download, ls, só para limpar aqui. Está lá o Terraform. Você vai dar um unzip nele. E o executável já está instalado. Só isso, Ricardo? Sim, só isso. A Terraform é um executável, acabou. Não tem bibliotecas, dependências, é simples assim. Eu acho muito legal. Está trabalhando já há algum tempo com essa ferramenta, eu a acho bem interessante.

E “./terraform”. Pronto, já está executando. O que é que nós precisamos fazer? Eu estou executando-a dentro do ponto local/bin. Se você instalar e está rodando uma distribuição nova, esse path já está configurado no teu shell.

No caso aqui no meu, eu tirei e vou colocar de novo para te mostrar como é que faz. Então no Ubuntu, em geral, você vai editar “o .profile”, que é o arquivo onde nós configuramos isso. No meu caso aqui, no teu home, voltei para o home, “vi.bash_profile” ou então “vi.profile” ou o editor que você quiser.

E eu tenho aqui os paths definidos. Toda vez que o meu shell é carregado, ele lê esse arquivo aqui. O que tiver nesse diretório, ele já coloca no meu path. Então, possivelmente, você já tem uma linha aqui embaixo dizendo o seguinte: “.local/bin” já está definido no teu path. Rodei aqui, não tem. Como é que eu faço isso? Como é que eu coloco?

Bom, você pode pegar uma linha, incluir aqui no meio ou, simplesmente, no final você pode dizer que a variável path pega o teu home, importante, em letra maiúscula “PATH=“HOME/.local/bin”. Ela vai pegar esse diretório e vai incluir no path. Mas e o resto do path? Você coloca dois pontos (:), aqui o dólar “${PATH}”. E finalizando, “export PATH”. Só isso.

Vamos gravar. E o que é que nós fazemos? Vamos testar. Como é que eu carrego o shell sem ter que abrir e fechar isso? Você fecha e abre o teu terminal, então tem esse recurso, bem legal “soucer .bosh_profile”.

E “echo $PATH”, para saber se carregou, e está lá a nossa variável path. E tem, lógico, um milhão de coisas, olha quem aparece aqui: .”local/bin” dizendo que está no path. Então se nós digitarmos “terraform”, dei um “TAB”, ele mostra que está no path, tudo carregado, tudo funcionando direito.

Faz essa config, porque agora nós vamos partir para AWS. Como assim? O Terraform está resolvido, mas vamos trabalhar com AWS, então é interessante que nós criemos uma chave e façamos também a configuração da AWS.

Então vamos voltar aqui para o browser. Abre AWS, loga na tua console, e vamos criar uma chave. Eu venho no IAM, no serviço IAM e vou criar aqui meu usuário. Usuário, adicionar usuário, vou chamar ele de terraform-aws, vai ser o meu usuário para fazer a gestão do ambiente aqui dentro.

Que tipo de usuário é? Esse primeiro tipo aqui, que ele usa access key e a secret key. Marquei essa opção, avançar. O que é importante aqui? É importante que você dê permissão para essa chave de usar todos os recursos da AWS.

Então se você não tem, vamos criar um grupo. Criar o grupo. Eu vou chamar o grupo de admin. E qual apólice que eu vou vincular a esse grupo? O acesso administrativo. Então ele diz aqui: ele vai dar acesso full a todos os recursos da AWS. Nós precisamos, lógico, de um acesso full.

Admin, criei o grupo. Tá aqui. Avançar. Aqui não tem mistério. Enfim, criar o usuário. Para você que já está acostumado com AWS, sabe que esse passo é importantíssimo: download do .csv. O que que é isso? O usuário e a chave de acesso. Se passar dessa tela e não fizer um download, esquece, apaga o usuário e cria de novo, porque ele não vai deixar você voltar aqui.

Fechei. Usuário criado, dentro do grupo, com as permissões. O que nós fazemos? É só configurar AWS. E aqui eu vou dar uma pausa para nós continuarmos no próximo vídeo.

Enquanto isso, o que você precisa fazer? Não tem a AWS instalada? Dá uma olhada nos outros cursos na plataforma, porque tem essa instalação. Se ainda assim tiver alguma dúvida, AWS CLI install. É simples, vai baixar. Já deixei o link aqui para te ajudar, tem aqui para várias plataformas. Você faz a instalação da CLI que nós voltamos no próximo vídeo fazendo essa configuração. Então voltamos já.

## Provisionando a primeira infraestrutura com o Terraform - Configurando a primeira infraestrutura

<video class="elasticMedia" frameborder="0" controls="" controlslist="nodownload" style="box-sizing: inherit; margin: 0px; padding: 0px; position: absolute; top: 0px; left: 0px; width: 820px; height: 461.25px; border: 0px;"></video>

De posse da chave que criamos no dashboard AWS, vamos continuar aqui com a nossa configuração que já vai terminar. Então, baixamos o CSV, ele está aqui, ele fica no download, que nada mais é do que o usuário, está vendo, a chave do usuário e a respectiva password.

Então, você vai copiar essas informações. Já está com a tua AWS instalada e você digita o seguinte “aws configure”. Se você nunca utilizou, isso não vai estar aparecendo assim, é claro. É que eu já tinha uma chave, não tem problema nenhum. Então aquilo que eu falei: copiou e colou o usuário e aqui a secret key. Cuidado com as vírgulas, eu sempre copio esse negócio errado e não funciona.

Qual é a região default? No meu caso, eu vou usar como default essa região aqui. Qual o output do arquivo? Se teu também estiver em branco, botar o “json”. Pode ser json, pode ser txt, eu gosto mais assim.

CLI está configurada. Com a CLI configurada, temos que agora organizar o diretório, colocar os arquivos, para nós trabalharmos no nosso projeto. Então eu vou fazer o seguinte, eu tenho o meu diretório aqui, você já está até acostumado dos cursos anteriores. Dentro do LABS, eu vou criar o “mkdir terraform”.

Vai ser aqui que eu vou criar todo o meu projeto. E para nós trabalharmos com a edição do arquivo, eu vou usar o meu vs code, você pode usar o editor da tua preferência. O legal do vs code é que vamos instalar uma extension para facilitar na hora de checar a sintaxe. Isso é bem útil.

Estou com o vs code aqui, vou apontar para o meu projeto, file, labs, terraform, vai ser o diretório aqui do projeto. Então já estou com o editor apontando para lá. O que é que nós instalamos, que é bem legal? Você vai na extensão e você simplesmente “Terraform”. Olha o que aparece? Um milhão de downloads, ou seja, a galera toda está usando. Install. Terminou a instalação, bem rápido. Olha o que ele criou para nós, já criou aqui o tal do Terraform Explorer, que é a extensão.

Vamos aqui nos nossos arquivos, Terraform, e vamos criar o nosso primeiro arquivo, “main.tf” de Terraform. Já associou o ícone, para saber que está linkado aqui. “E Ricardo, por que você está usando esse nome? Posso usar qualquer nome?” Pode usar qualquer nome. Eu estou seguindo uma boa prática que está na documentação. O arquivo principal, vou chamar de main, só para seguir a organização. Pode colocar o que você quiser.

Nós começamos assim: “provider”. Ele já preencheu para nós o nosso provedor vai ser aws. E a partir de agora, o curso vai ser tudo nós configurando aqui e entendendo as funções, entendendo os componentes.

Provider AWS. Quais são os recursos, quais são as opções que eu tenho? Eu vou voltar no Browser e te mostrar que esse link é importante até para você continuar com os teus estudos: Terraform, Docs, Providers. Olha aqui a quantidade de provedores que nós podemos trabalhar, linkando com aquilo que eu falei no início do curso: você não está preso a AWS nem a Asure, nem nenhum deles. Você tem a opção de trabalhar com qualquer um.

Tem essa lista aqui. Ele já divide em Major Cloud, os maiores estão listados aqui: AWS, tudo o mais, e nessa opção Cloud, aquela lista enorme. Então não falta opção. Deixa-me vir aqui para AWS, só para você entender da onde nós vamos partir.

Está aqui o exemplo de configuração, já vou copiar isso daqui e agora nós vamos seguir todo no nosso vscode. Versão, atualmente, na data de gravação do curso, está na versão 2 alguma coisa, ele usa esse símbolo aqui. Particularmente, eu não gosto disso, mas ele coloca assim “~>2.0”.

Região. Qual é a região que estamos trabalhando? us-east-1. Ricardo, eu posso ter multi região na minha configuração? Pode, e vamos ter durante o curso. Então está aqui o nosso setup, e para essa primeira infra, imagine o cenário que você tem que prover um ambiente para cinco desenvolvedores, para três desenvolvedores, como é que você faz isso?

Eu vou criar o que ele chama de resource. O resource tem um tipo e um nome. Qual é o tipo? “aws_instance”. E aqui, o nome dele. Eu vou colocar “dev”. Então vou criar um recurso chamado instance.

O que interessante aqui é que agora, por isso que eu falei que você tem que conhecer a estrutura, você vai colocar os parâmetros que a aws te exige. Quais seriam esses parâmetros? Se é uma Instância, eu tenho que ter um tipo. Eu tenho que ter também qual é a imagem que eu vou utilizar. Então nós usamos aqui: “ami =” da onde a gente pega isso? Não tem mágica. Você pode usar cil ou já criar uma tabela com as duas imagens.

Nós vamos fazer já. Mas eu vou usar o browser aqui. Fica atento, porque cada imagem da AWS, independente da região que ela está e do nome, tem um número diferente. A mesma imagem, EC2. A mesma imagem, Ubuntu, que tem na us-east-1, tem um ID. Esse mesmo nome, em Ohio, aparece com outro ID. Então toma cuidado com isso.

Se eu vier aqui em launch instances, eu vou usar as padrões. É isso que eu estou me referindo aqui. Nós vamos provisionar, o meu time de desenvolvimento pediu o recado: precisa de três máquinas para trabalhar. Fala: espera um minuto que eu mando isso para você.

Aqui. Essa é a imagem relacionada aqui meu Ubuntu Server. Essa imagem na us-east-1. Não pode perder essa referência. Colei aqui. Quê mais? Qual é o tipo da instância? “instance type = “vamos utilizar aquela máquina, “t2.micro”, que é aquela máquina que está dentro do free trial, está lembrando? O uso gratuito da AWS. Então está aqui, “t2.micro”. Agora nós precisamos, “key_name =” e aqui, nós vamos dar uma pausa para nós fecharmos essa configuração da nossa infraestrutura aqui.

Você sabe que a máquina está na nuvem. Como é que eu gerencio ela? Com SSH. Para fazer SSH, eu preciso de um par de chaves para isso funcionar. Eu posso vir aqui na AWS, vou lá no EC2, se você já tem uma chave, você pode fazer referência à ela, ou então, eu estou zerado aqui.

Eu poderia criar uma chave aqui, mas aí a dica de quem já passou, já caminhou bastante aqui por esse ambiente, é o seguinte: cada região da AWS, você precisa criar uma chave diferente. Você não consegue criar essa chave aqui e depois levar a chave para Ohio, para São Paulo, seja qual for a região.

O que eu vou fazer? Como a minha gestão de ambiente é via Terraform, eu vou gerar uma chave local de SSH e essa chave eu vou distribuindo pela AWS à medida que eu preciso fazer uso das regiões. O legal é que, com essa chave local, você não precisa estar preso à AWS. Essa chave local eu posso jogar no Google, posso jogar na Asure, e trabalhar dentro das infraestruturas em cada um dos provedores.

Como é que fazemos isso? Volta aqui para o terminal. Estou dentro do diretório do projeto, e eu vou usar o seguinte: ssh-keygen. Esse comando já é nativo do SSH, já está no Linux, já vem quando você tem lá o clive SSH. “– f”, o nome do arquivo que eu quero gerar. “terraform-aws”, que é a mesma chave que nós geramos, só para manter a organização.

O que mais?” –t”, o tipo de algoritmo que vamos utilizar o “rsa”. Então vou gerar a chave. Ele pergunta se eu quero colocar uma senha para essa chave, eu vou dizer que não, vou dar enter aqui, de novo, e ele gerou a chave.

Eu tenho um par de chaves, uma chave pública e a minha chave aqui privada. Para manter a organização, eu aconselho muito que você faça isso, é o seguinte: eu não vou deixar essa chave privada aqui. Eu vou mover essa chave para o diretório aonde ela tem que permanecer. Que diretório é esse? É esse daqui. “Ricardo, se eu não fizer isso, não vai funcionar?” Vai funcionar, é só para a questão de organizar.

Eu vou mover essa chave para onde? Esse é o diretório default do SSH. Então eu vou jogar a minha chave privada para lá e também, por questão só minha, de organização, eu vou copiar também a chave pública para lá.

Então no nosso projeto, vamos voltar lá para vs code, já apareceu a chave pública aqui, certo? Que que precisamos fazer? Pegar ela e mandar para AWS. É fácil: browser, criar chave não, importar uma chave. O que é que você faz? Labs, terraform, olha ela aqui. Selecionar. Aqui está a chave. Importar. Pronto, já temos a nossa chave na AWS.

E essa chave, como eu disse, vai distribuindo na medida em que vamos avançando no curso. Qual é o nome da chave? Você já sabe: “terraform-aws”. Não precisa aqui colocar nenhuma outra extensão, é só o nome, mesmo, da chave.

Então está quase prontinho, falta dois, três parâmetros aqui. Próximo vídeo, nós continuamos escrevendo esse código com o objetivo de provisionar três máquinas para o nosso time de desenvolvimento. Chega até aqui, vê se está tudo certinho, próximo vídeo nós continuamos.

## Sobre o curso Terraform: Automatize a infraestrutura na nuvem

O [curso Terraform: Automatize a infraestrutura na nuvem](https://www.alura.com.br/curso-online-terraform) possui **124 minutos de vídeos**, em um total de **59 atividades**. Gostou? Conheça nossos outros **[cursos de Infraestrutura como Código](https://www.alura.com.br/cursos-online-devops/infraestrutura-como-codigo)** em **[DevOps](https://www.alura.com.br/cursos-online-devops)**, ou leia nossos [artigos de DevOps](https://www.alura.com.br/artigos/devops).

Matricule-se e comece a estudar com a gente hoje! Conheça outros tópicos abordados durante o curso:

- Provisionando a primeira infraestrutura com o Terraform
- Modificando a infraestrutura e inserindo novos recursos
- Utilizando referências e dependências entre os recursos
- Organizando a configuração com a separação dos arquivos e aliases
- Trabalhando com variáveis
- Gerenciando recursos e outputs
- Trabalhando em equipe - Terraform Cloud