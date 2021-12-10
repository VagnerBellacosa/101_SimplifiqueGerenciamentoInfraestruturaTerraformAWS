- https://cloud.ibm.com/registration)



![img](https://1.cms.s81c.com/sites/default/files/2020-02-13/Learn%20Leadspace%204.jpg)

[IBM Cloud Learn Hub](https://www.ibm.com/br-pt/cloud/learn) [O que é o Terraform?](https://www.ibm.com/br-pt/cloud/learn/terraform)

# Terraform

Por **IBM Cloud Education**

13 de Fevereiro de 2020

- [Computação](https://www.ibm.com/br-pt/cloud/learn/compute) 
- [DevOps](https://www.ibm.com/br-pt/cloud/learn/devops)

- [O que é o Terraform?](https://www.ibm.com/br-pt/cloud/learn/terraform#toc-o-que--o-t-KdxgzVDp)
- [Por que usar a infraestrutura como código (IaC)?](https://www.ibm.com/br-pt/cloud/learn/terraform#toc-por-que-us-InjiOF78)
- [Por que usar o Terraform?](https://www.ibm.com/br-pt/cloud/learn/terraform#toc-por-que-us-aeC9auVU)
- [Módulos do Terraform](https://www.ibm.com/br-pt/cloud/learn/terraform#toc-mdulos-do--Tpjbu2S8)
- [Provedores do Terraform](https://www.ibm.com/br-pt/cloud/learn/terraform#toc-provedores-GnoPtwCH)
- [Terraform vs. Kubernetes](https://www.ibm.com/br-pt/cloud/learn/terraform#toc-terraform--Z5Eg4toU)
- [Terraform vs. Ansible](https://www.ibm.com/br-pt/cloud/learn/terraform#toc-terraform--40YxVHRe)
- [IBM e Terraform](https://www.ibm.com/br-pt/cloud/learn/terraform#toc-ibm-e-terr-p8wgiDv3)

# Terraform

Este guia destaca tudo o que você precisa saber sobre o Terraform, uma ferramenta que permite aos programadores desenvolver, mudar e gerar versões de infraestrutura de maneira segura e eficiente.

## O que é o Terraform?

Terraform é uma ferramenta de software livre de "infraestrutura como código" criada pela HashiCorp.

Uma ferramenta de programação *declarativa*, o Terraform permite aos desenvolvedores usar uma linguagem de configuração de alto nível chamada HCL (HashiCorp Configuration Language) para descrever a infraestrutura na cloud ou em implementação local de estado final desejada para executar um aplicativo. Em seguida, ele gera um plano para alcançar esse estado final e executa o plano para fornecer a infraestrutura.

Uma vez que o Terraform usa uma sintaxe simples, é possível fornecer a infraestrutura em [data centers](https://www.ibm.com/cloud/learn/data-centers) multicloud e locais, sendo possível fornecer novamente a infraestrutura de maneira segura e eficiente em resposta às mudanças de configuração, sendo atualmente uma das ferramentas de automação de infraestrutura mais populares disponíveis. Se sua organização tem a intenção de implementar um ambiente de [cloud híbrida](https://www.ibm.com/br-pt/cloud/learn/hybrid-cloud) ou [multicloud](https://www.ibm.com/cloud/learn/multicloud), você precisa conhecer o Terraform.

## Por que usar a infraestrutura como código (IaC)?

Para entender melhor as vantagens do Terraform, é interessante entender primeiro os benefícios da [infraestrutura como código (IaC)](https://www.ibm.com/cloud/learn/infrastructure-as-code). A IaC permite aos desenvolvedores codificar a infraestrutura de um modo que torna o provisionamento automatizado, mais rápido e reproduzível. Ela é um componente chave das práticas Agile e [DevOps](https://www.ibm.com/br-pt/cloud/learn/devops-a-complete-guide), como o controle de versão, [a integração contínua](https://www.ibm.com/br-pt/cloud/learn/continuous-integration) e [a implementação contínua](https://www.ibm.com/br-pt/cloud/learn/continuous-deployment).

A infraestrutura como código pode ajudar da seguinte forma:

- **Aumentar a velocidade:** a automação é mais rápida do que a navegação manual por uma interface quando é preciso implementar e/ou conectar recursos.
- **Melhorar a confiabilidade:** se a sua infraestrutura for grande, torna-se fácil configurar de maneira incorreta um recurso ou fornecer serviços na sequência errada. Com a IaC, os recursos são sempre provisionados e configurados exatamente conforme declarados.
- **Prevenir desvios de configuração:** o desvio de configuração ocorre quando a configuração que forneceu seu ambiente não mais corresponde ao ambiente real. (Veja 'Infraestrutura imutável' abaixo.)
- **Suporte à experimentação, aos testes e à otimização:** uma vez que infraestrutura como código torna o provisionamento de uma nova infraestrutura tão mais rápido e fácil, é possível realizar e testar mudanças experimentais sem investir muito tempo e recursos. Se você gostar dos resultados, é possível aumentar rapidamente a escala da nova infraestrutura para a produção.

Veja "O que é infraestrutura como código?" para saber mais detalhes:

<iframe width="854" height="480" frameborder="0" allowfullscreen="allowfullscreen" mozallowfullscreen="mozAllowFullscreen" webkitallowfullscreen="webkitallowfullscreen" allow="autoplay *; fullscreen *; encrypted-media *" src="https://cdnapisec.kaltura.com/html5/html5lib/v2.76/mwEmbedFrame.php/p/1773841/uiconf_id/39954662/entry_id/1_hjqpanjz?wid=_1773841&amp;iframeembed=true&amp;playerId=kplayer&amp;entry_id=1_hjqpanjz&amp;flashvars[streamerType]=auto" style="box-sizing: inherit; margin: 0px; padding: 0px; border: 0px; vertical-align: baseline; font-size: inherit; font-family: &quot;IBM Plex Sans&quot;, ibm-plex-sans, &quot;Helvetica Neue&quot;, Arial, sans-serif; position: absolute; top: 0px; left: 0px; width: 562.5px; height: 316.406px;"></iframe>



















## Por que usar o Terraform?



Há algumas razões importantes pelas quais os desenvolvedores optam por usar o Terraform em vez de outras ferramentas de infraestrutura como código:

- **Software livre:** o Terraform é usado por grandes comunidades de colaboradores que desenvolvem plug-ins para a plataforma. Independentemente de qual provedor de cloud você usa, é fácil encontrar plug-ins, extensões e suporte profissional. Isso também significa que o Terraform evolui rapidamente com novos benefícios e melhorias sendo incluídos constantemente.
- **Plataforma independente:** significa que é possível usá-la com *qualquer* provedor de serviços na cloud. A maioria das outras ferramentas de IaC são projetadas para funcionar com um único provedor de cloud.
- **Infraestrutura imutável:** a maioria das ferramentas de infraestrutura como código cria uma infraestrutura *mutável*, o que significa que a infraestrutura consegue mudar para se adaptar às mudanças, como uma atualização de middleware ou um novo servidor de armazenamento. O desafio da infraestrutura mutável é o *desvio de configuração*. À medida que mudanças se acumulam, o provisionamento real de diferentes servidores ou de outros elementos de infraestrutura se 'desvia' para mais longe da configuração original, tornando erros ou problemas de desempenho difíceis de diagnosticar e de corrigir. Terraform provisiona *infraestrutura imutável*, o que significa que a cada mudança no ambiente, a configuração atual é substituída por uma nova que considera a mudança e a infraestrutura é fornecida novamente. Melhor ainda, as configurações anteriores podem ser guardadas como versões para permitir retrocessos se necessário ou desejado.

## Módulos do Terraform

Os *módulos* do Terraform são configurações pequenas e reutilizáveis do Terraform para vários recursos de infraestrutura que são utilizados em conjunto. Os módulos do Terraform são úteis porque permitem que recursos complexos sejam automatizados com construções reutilizáveis e configuráveis. Até mesmo a composição de um arquivo muito simples do Terraform resulta em um módulo. Um módulo pode chamar outros módulos, chamados de *módulos filhos*, o que pode tornar a montagem da configuração mais rápida e mais concisa. Os módulos também podem ser usados várias vezes, tanto no âmbito da mesma configuração quanto em configurações separadas.

## Provedores do Terraform

Os *provedores* do Terraform são plug-ins que implementam tipos de recursos. Os provedores contêm todo o código necessário para autenticar e conectar a um serviço, geralmente de um provedor de cloud pública, em nome do usuário. É possível encontrar provedores para as plataformas e serviços na cloud que você usa, incluir à sua configuração e, em seguida, usar seus recursos para fornecer a infraestrutura. Os provedores estão disponíveis em praticamente todos os principais provedores de cloud, soluções de SaaS e muito mais, desenvolvidos e/ou apoiados pela comunidade Terraform ou por organizações individuais. Consulte a [documentação do Terraform](https://www.terraform.io/docs/providers/index.html) (link externo à IBM) para uma lista detalhada.

## Terraform vs. Kubernetes

Ainda há confusão entre o Terraform e o Kubernetes e o que eles realmente oferecem. A verdade é que eles não são alternativas e funcionam de forma eficaz juntos.

[Kubernetes](https://www.ibm.com/br-pt/cloud/learn/kubernetes) é um [sistema de orquestração de contêiner de software livre](https://www.ibm.com/cloud/blog/container-orchestration-explained) que permite aos desenvolvedores programar implementações em nós em um cluster de computação, gerenciando ativamente cargas de trabalho [conteinerizadas](https://www.ibm.com/cloud/learn/containerization) para garantir que seu estado corresponda às intenções dos usuários.

Terraform, por outro lado, é uma ferramenta de infraestrutura como código com um alcance muito mais amplo, permitindo aos desenvolvedores automatizar infraestruturas completas, o que abrange várias clouds públicas e privadas.

Terraform é capaz de automatizar e gerenciar capacidades em nível de [infraestrutura como um serviço (IaaS)](https://www.ibm.com/cloud/learn/iaas), [plataforma como um serviço (PaaS)](https://www.ibm.com/br-pt/cloud/learn/paas) ou até mesmo[software como um serviço (SaaS)](https://www.ibm.com/br-pt/cloud/learn/iaas-paas-saas)e desenvolver todos esses recursos em todos aqueles provedores de forma paralela. É possível usar o Terraform para automatizar o provisionamento do Kubernetes, especialmente [clusters de Kubernetes](https://www.ibm.com/cloud/blog/kubernetes-clusters-architecture-for-rapid-controlled-cloud-app-delivery) gerenciados em plataformas na cloud, e automatizar a implementação de aplicativos em um cluster.

## Terraform vs. Ansible

O Terraform e o Ansible são ambos ferramentas de infraestrutura como código, mas há algumas diferenças significativas entre os dois:

- Enquanto o Terraform é puramente uma ferramenta declarativa (veja acima), o Ansible combina tanto uma configuração declarativa quanto *procedural*. Na configuração procedural, você especifica as etapas ou a maneira precisa na qual deseja fornecer a infraestrutura até o estado desejado. A configuração procedural envolve mais trabalho, porém proporciona mais controle.
- O Terraform é software livre. O Ansible é desenvolvido e comercializado pela Red Hat.

## IBM e Terraform

[IBM Cloud Schematics](https://www.ibm.com/cloud/blog/announcements/ibm-cloud-schematics-enabling-infrastructure-as-code) é a ferramenta de automação gratuita da Cloud da IBM com base no Terraform. IBM Cloud Schematics permite gerenciar plenamente a automação de sua infraestrutura com base no Terraform para que você possa passar mais tempo construindo aplicativos e menos tempo criando ambientes.

Descubra mais sobre [como usar o IBM Cloud Schematics](https://www.ibm.com/cloud/blog/automate-kubernetes-infrastructure-with-ibm-cloud-schematics).

Para obter mais informações sobre o Terraform, cadastre-se para a IBMid e [crie sua conta IBM Cloud](https://cloud.ibm.com/registration).

