---
title: "Infraestrutura Imutável: Devemos ainda nos apegar aos nossos servidores?"
date: 2019-09-09T00:00:08-02:00
draft: false
tags: ["devops", "infra", "sre", "pets vs cattle", "phoenix servers", "snowflake servers", "infra as a code"]
---

Olá mundo =)

Conto para vocês neste post minha saga de estudos sobre Infraestrutura Imutável. Este artigo irá abordar conceitos, mas também irá abordar a prática de infraestrutura imutável com [Packer](https://www.packer.io/), [Ansible](https://www.ansible.com/) e [Terraform](https://www.terraform.io).

E antes de começar quero agradecer a [Fernando Ike](https://www.twitter.com/fernandoike) e [Daniel Romero](https://twitter.com/infoslack) por toda mentoria neste assunto.

## O que é Infraestrutura Imutável?

Infraestrutura Imutável é um paradigma, dentre vários em Infraestrutura, onde servidores não podem sob hipotése nenhuma devem ser modificados após o deploy, ou seja, se é necessário alguma mudança, correção e/ou atualização de alguma maneira novos servidores devem ser criados a partir de uma imagem comum com as suas respectivas alterações onde será realizado o provisionamento para substituir os antigos servidores. Depois de validados, os servidores novos são colocados em uso e os antigos são descartados. Este é um conceito que surgiu por meados de 2012, porém existem documentações relacionadas a isso desde 2004.

Infraestrutura Imutável tem alguns sinônimos conhecidos, onde alguns iremos abordar com mais calma neste artigo:

- Pets vs Cattle;
- PhoenixServers vs SnowflakeServers;
- Golden Images;
- Infrastructure as Code.

Neste artigo vamos nos ater nos conceitos de "Pets vs Cattle" e "Phoenix Servers vs Snowflake Servers".

## Pets vs Cattle

O conceito de Pets vs Cattle não nasceram com este nome, mas sim com o conceito de "Scale-Up vs "Scale-Out" apresentado por Bill Baker na apresentação [Scaling SQL Server](https://www.pass.org/eventdownload.aspx?suid=1902) em 2012, sendo que ele nem abordava este assunto em conceito de Cloud Computing, o foco era em num contexto de arquitetura em geral.

Uma das pessoas a ver esta apresentação era [Randy Bias](https://twitter.com/randybias) que escreveu um artigo muito interessante sobre a História do termo [Pets vs Cattle](http://cloudscaling.com/blog/cloud-computing/the-history-of-pets-vs-cattle/) que inspirou este artigo. Bill na sua apresentação usou uma analogia muito interessante que chamou a atenção de Randy, onde ele falou sobre scale-up versus scale-out num contexto de comparar com animais de estimação (Pets) e um rebanho de gado (Cattle).

Então em um momento de iluminação, Randy trouxe para o contexto de nuvem onde ele enfatizou a descartabilidade dos servidores do tipo Cattle (Gado) e singularidade dos servidores do tipo Pets (Animais de estimação). Esse tipo de explicação foi muito bem aceita por membros da comunidade e por [Tim Bell](https://twitter.com/noggin143) do CERN que ajudaram a [popularizar](https://twitter.com/noggin143/status/354666097691205633) o termo e realizar a transição para a cloud.

Mas após toda essa introdução, vamos entrar mais a fundo sobre Pets e Cattle:

### Pets

> Servidores que são tratados como indispensáveis ou exclusivos que nunca podem ser desativados. Normalmente, eles são criados, gerenciados e ajustados manualmente. Exemplos: Mainframes, servidores solitários, loadbalancers/firewalls de HA, bancos de dados projetados como primário/secundário e assim por diante.

Quem tem ou já teve um animal de estimação sabe que tem alimentá-lo, se estiver doente tem que levar no veterinário, dar carinho e tudo que envolve ter um animal de estimação, e quando nosso animalzinho de estimação morre geralmente é muito triste para todos nós. 

Isso não fica muito atrás quando temos em nosso parque de máquinas servidores do tipo "Pets", temos que ter uma grande atenção com ele e quando ele morre geralmente tomamos um grande dano, pois quando um servidor de Banco de Dados ou um Load Balancer morre por qualquer motivo temos as nossas aplicações paradas e isso impacta muito na nossa vida, mais ainda se foi por descuido nosso.

### Cattle

> Conjuntos de dois ou mais servidores, construídos em cima de uma ferramenta de automatização, onde nenhum, dois ou até três servidores são insubstituíveis. Geralmente em caso de falhas, não é necessária ação humana, pois quando um servidor morre ele substituído por outro idêntico. Exemplos: Clusters diversos (Cassandra, Kafka, Kubernetes (Ressalva: Usando multi master.)), múltiplos servidores web, assim por diante.

Definição cruel, porém verdadeira, os servidores ganham um número ou código de identificação como os rebanhos de gado ganham nas fazendas por exemplo: `web001` até `web100`. Um exemplo prático é usando o [Instance Group Manager](https://cloud.google.com/compute/docs/instance-groups/) no Google Cloud, geralmente quando uma máquina morre, o *Instance Group Manager* vai recriar uma nova máquina para você com o mínimo de impacto para a sua aplicação rodando.

A mesma coisa vale para um cluster de Kubernetes, desde que usamos a boa prática de usar multi master como citei mais acima, e isso multi master impáres e com no mínimo 3 servidores. Porquê uma coisa é um worker cair, isso é tranquilo de recuperar, mas se tivermos um Master apenas, esse servidor se transforma num Pet.

E pessoas gostando ou não desta definição, Pets vs Cattle tem um contexto histórico relevante e é muito importante para entendermos o que é infraesturura imutável.

## PhoenixServers vs SnowflakeServers

Então [Martin Fowler](https://twitter.com/martinfowler) altera um pouco o termo, e os batiza em PhoenixServers (Cattle) e SnowflakeServers (Pets), os conceitos são parecidos, talvez idênticos, por isso, muito provavelmente este artigo ficará repetitivo mas é importante para fixar os conceitos:

### SnowflakeServers

> SnowflakeServers são semelhantes Pets. São servidores que são gerenciados manualmente, atualizados com freqüência e ajustados no local, levando a um ambiente exclusivo.

É o conceito mais tradicional, é muito comum usarmos estes tipos de servidores para hospedarmos nossas aplicações e onde o deploy é feito por uma ferramenta de pipeline como [Jenkins](https://jenkins.io/), [TravisCI](https://travis-ci.org/), etc. que é acessado pela ferramenta ou acessando diretamente o servidor e mudando os arquivos manualmente.

O cenário comum para SnowflakeServers são:
 
- Construção do artefato pela ferramenta de Continuous Delivery;
- Dependências são instalados e/ou reinstaladas a versão nova, e para esse se faz necessário uma ferramenta como Ansible, Chef, Puppet ou até mesmo um Shell Script para fazer este passo;
- Implementação do artefato em homologação;
- Implementação do artefato em produção.

Porém este processo corriqueiro pode ter efeitos colaterais como uma possível indisponibilidade do sistema de empacotamento (`pip`, `bundle`, `npm`, `apt`, `yum`, etc.) ocasionando a não instalação daquela biblioteca necessária ao seu projeto, e é um problema muito difícil de achar e se perde muito tempo fazendo troubleshooting. Podem haver casos de uma biblioteca necessária ao projeto não estar mais disponível no gerenciador de pacotes ou até mesmo uma atualização de biblioteca que podem fazer você perder um tempo precioso para resolver o problema.

E um ponto muito importante é a ordem dos comandos, um erro muito comum que muitos de nós já cometeram são comandos certos em ordem errada, por isso que a ordem dos comandos a serem dados é extrema importância nesse caso pois infelizmente temos dependência circular e pacotes que tem que estar numa ordem correta. E para identificar isso é bem díficil pois na maioria dos casos os logs são escassos e este problema vira uma grande bola de neve.

#### Problemas comuns em Snowflake Servers

- Aumento da complexidade operacional
- Maior exposição a falhas no pipeline e durante a operação (via serviços de terceiros)

E no cenário atual com oceano lotado de [ferramentas](https://xebialabs.com/periodic-table-of-devops-tools/) para implementação sujeitas a todo tipos de falhas, estamos expostos a erros, pois é algo que não podemos controlar.

### PhoenixServers

> PhoenixServers são semelhantes a Cattle. São servidores que são sempre construídos do zero e são fáceis de recriar (ou "renascem") por meio de procedimentos automatizados.

Em outras palavras, são os nossos servidores **imutáveis** geralmente provisionamentos por alguma ferramenta de gestão de configuração como Ansible, Chef, Puppet ou o bom e velho Shell Script. 

Um exemplo interessante para ilustar é o uso do [Packer](https://www.packer.io/) com alguma destas [ferramentas](https://www.packer.io/docs/provisioners), onde vai gerar a imagem para o seu servidor seja nos diversos [cloud providers](https://www.packer.io/docs/builders/) que o Packer suporta, garantindo assim a integridade do seu deploy e mitigando possíveis falhas no processo.

A criação desta imagem pode ser feita na ferramenta de pipeline, assim não se faz necessário rodar ferramentas de terceiros como `pip`, `bundle`, `composer` rodando no servidor de produção ou bibliotecas diversas no seu servidor tornando sua imagem de VM enxuta e com uma melhor performance, claro se seguir as boas práticas. Sem contar toda a parte de segurança como permissão de acesso e vulnerabilidades diversas.

E hoje com a disseminação da cultura DevOps todos tem que ter senso de *Ownership*, seja profissionais de Desenvolvimento e Operações e com o *Infrastructure as a Code* os códigos de infraestrutura fazem parte do Produto e é responsabilidade de todos, e não somente de uma figura específica, os possíveis problemas que podem ocorrer.

## Boas práticas em Infraestrutura Imutável

### Estar em um ambiente Cloud

Uma boa prática para você aplicar infraestrutura imutável é você estar num ambiente de Cloud, seja ele AWS, Google Cloud Platform, Azure, Digital Ocean, etc.

É possível fazer em um ambiente Bare Metal (On Premises), mas você deve tem que instalar um [OpenStack](https://www.openstack.org/),ou alguma ferramenta similar. Trocando em miúdos, é díficil, não compensa o esforço, por isso não recomendo.

Então é definitivo que para aplicarmos Infraestrutura Imutável deveremos estar em Cloud.

### Automação de todo o processo de Continuous Deployment

Passo importantissímo, como mencionado anteriormente a infra virou código, [Jez Humble](https://twitter.com/jezhumble) já havia referência de infraestrutura como código na sua mais famosa publicação [Continuous Delivery](https://www.amazon.com/Continuous-Delivery-Deployment-Automation-Addison-Wesley/dp/0321601912) de 2011 e se formos mais para trás [Mark Burgess](https://twitter.com/markburgess_osl), conhecido por ser o criador do [CFEngine](https://cfengine.com/) no seu paper [On the theory of system administration](http://markburgess.org/papers/sysadmtheory3.pdf) de 2003.

O ponto em comum disso tudo é a importância da automatização da infraestrutura, e hoje com a popularização de Cloud é crucial automatizar sua infraestrutura de rede, servidores e toda a parte de segurança tudo isso rodando num pipeline para rodar em qualquer serviço de Cloud que desejar.

Lembrando que não é infraestrutura imutável se não está em um pipeline e se o processo não é automatizado.

### Centralização de logs

Hoje há uma tendência muito forte em todo o mundo que é o *Observability* que visa condensar as informações sobre o comportamento de todo o ambiente para identifcação da causa raiz dos problemas e posterior resolução.

Então por isso que é uma boa prática a centralização de todos os logs seja da aplicação e da máquina para este fim, ou seja não pode haver escrita no seu servidor.

Você pode centralizar os logs em alguma das várias ferramentas pagas ou não que existem hoje disponíveis.

Aliás isso evita o acesso a máquina para verificar os logs do servidor, pois como os logs estão centralizados este trabalho fica mais simples.

### Armazenamento externo de dados

Como no tópico acima foi mencionado que não deve haver escrita nos servidores, o caso de persistência de dados *stateful* também deve ser realizado externamente.

Hoje com muitas aplicações rodando em containers existem a questão volumes sendo persitidos no próprio servidor, porém no contexto de infraestrutura imutável os dados devem ser persistidos em um Banco de Dados externo ou até mesmo em um bucket.

### Equipes engajadas

Principal boa prática. O engajamento não deve ser somente da equipe de Desenvolvimento e Operações, equipes de Segurança e QA (Quality Assurance) também devem se engajar neste processo para a resolução de incidentes e para que o deploy ocorra da melhor forma possível.

### Sem acesso ao servidor de Produção

Uma vez em Produção, o servidor se torna "intocável", ou seja alguma ações sob hipotése nenhuma devem ser feitas como atualizar pacotes, alterar configurações dos servidores ou modificações na aplicação, impedindo assim que o problema seja reproduzido em outros ambientes para que seja feito o troubleshooting.

Por isso, não é necessário o acesso ao servidor via SSH para as equipes seja Operações ou Desenvolvimento.

## Tornando seu servidor imutável

Existem alguns passos para transformar o seu servidor em imutável como:

- Provisionar um servidor novo;
- Testar o servidor novo, mas testar no viés de aplicação e não de operação;
- Alterar as referências para o servidor novo, geralmente isso é realizado pelo load balancer também por ser mais rápido do que por DNS;
- Manter a versão anterior (temporariamente) para fazer rollback, se necessário.

E além destes passos, a visibilidade da construção do servidor é de suma importância no porcesso de Infraestrutura Imutável, perguntas do tipo:

### Onde e quando foi construído o servidor? E por que?

Geralmente é importante termos esse lastro guardado em algum lugar. A melhor prática seria a utilização de Changelog explicando o porquê do motivo da construção do artefato com as suas melhorias, correções, etc.

### Qual a imagem anterior do servidor?

Em caso de falha, é importante sabermos qual a versão anterior da imagem seja da VM ou do Container utilizado, pois pode ser utilizado para rollback.

Claro que não precisa ficar guardando um histórico gigante de imagens, mas é interessante armazenar as mais recentes por precaução.

### Como posso iniciar, validar, monitorar e atualizar o servidor?

Esta é uma etapa importante do planejamento, pois é uma definição que deve ser tomada para que se adeque melhor ao projeto que está sendo desenvolvido.

E claro que um planejamento bem feito evita grandes dores de cabeça lá na frente.

### Qual repositório será utilizado e qual o hash do git foi utilizado para a construção da imagem?

Isto tem que ser claro para todos no projeto o reposótorio que está sendo utilizado, e geralmente útilizado o hash do git para a construção das imagens dos containers, assim é melhor identificado onde está a alteração que está em Produção e é mais transparente no processo.

Também pode ser utilizado git tag para este processo.

### Quais as tags específicas do container e/ou VM utilizada como registro do build?

Hoje a maioria dos orquestradores de containers (Docker Swarm, K8s, etc.) possuem tags ou labels que é possível descrever que o container pertence a qual projeto, squad, time, etc.

Pois, existem cenário onde dezenas, centenas e até milhares de containers podem estar rodando e quanto mais descritivos estiverem melhor para identificar na hora de um troobleshooting ou mesmo para a organizar os projetos dentro do cluster.

### Qual o nome do projeto do qual pertence o artefato?

A pergunta se auto responde não é mesmo? Nada melhor que identificar de qual projeto pertence o artefato. 

## Formas de Deploy em Infraestrutura Imutável

### BlueGreen Deployment

*BlueGreen Deployment* é uma forma automatizada de deploy que envolve em ter dois ambientes distintos, porém o mais idênticos possível. Onde você possui um novo ambiente com o software atualizado (Green) e após todos os testes e garantir que esteja 100% funcional, será efetuada troca do ambiente antigo (Blue) pelo novo (Green).

![](/img/blue_green_deployments.png)

E em caso de falha pode ser feito o *rollback* para o ambiente antigo automaticamente.

Para a parte de Banco de Dados que geralmente é um desafio nesta forma de deploy, então como [Martin Fowler](https://twitter.com/martinfowler) ensina, o pulo do gato nesse ponto é separar o deployment dos schemas para a atualização das aplicações. Então deve ser feita uma refatoração de banco de dados para alterar o schema para oferecer suporte à versão nova e antiga do aplicativo, faça deploy, verifique se tudo está funcionando bem para que você consiga fazer rollback em caso de problemas e faça deploy da nova versão do aplicativo.

E quando a versão atual ficar estável remova o suporte a versão antiga.

### Canary Release

*Canary Release* é uma outra forma de deploy similar ao *BlueGreen Deployment* onde também sobem dois ambientes com a versão antiga e nova da aplicação.

![](/img/canary-release-1.png)

Porém ao invés de realizar a troca de uma vez só, é feito o balanceamento dos usuários que podem acessar a versão atualizada da aplicação, pode ser de uma maneira mais simples liberando a versão atualizada usuários internos e funcionários como algumas empresas fazem ou utilizando uma abordagem sofisticada escolhendo os usuários com base no seu perfil e dados demográficos.

![](/img/canary-release-2.png)

E conforme a nova versão for sendo aceita, você vai fazendo o balanceamento até que 100% da base dos seus usuários utilizem a nova versão e a versão antiga seja descartada.

Para a parte de Banco de Dados tem os mesmos problemas para *BlueGreen Deployment*, mas usando a técnica de [*ParallelChange*](https://martinfowler.com/bliki/ParallelChange.html) pode atenuar este problema, pois ele mantém o banco de dados para as mesmas versões do aplicativo durante a implementação.

### Rolling Update

Em suma, é um *BlueGreen Deployment* automático, ao invés de fazer a troca do ambiente manualmente, o ambiente é trocado automaticamente sem a intervenção humana.

Na demo abaixo, usamos o tipo de deploy *Rolling Update*.

## Demo

Para essa demo usei as seguintes ferramentas:

- Ansible
- Packer
- Terraform
- API escrita em Go
- Docker
- Google Cloud Platform
- TravisCI

Essa demo foi gravada para o evento [Floripa Tech Day 2019](https://www.floripatechday.com/) no qual palestrei sobre Infraestrutura Imutável:

<iframe width="560" height="315" src="https://www.youtube.com/embed/Xl47-FmAt4g" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Conclusão

Nesta parte de conclusão, também fica como as lições aprendidas, eu custei muito a aprender que não poderia haver escrita em disco como a questão de logs centralizados, mas após estudos e mais estudos esta barreira foi superada.

Hoje em dia, cada vez mais as organizações estão aplicando os conceitos de Infraestrutra Imutável para realizar o seu deploy e tendo resultados cada vez mais expressivos, um conselho para que não aplica seria de pelo menos realizar um teste visto é possível aplicar independente da sua stack.

Deixo para vocês alguns links muito úteis de onde me baseei para fazer este artigo:

- [The History of Pets vs Cattle and How to Use the Analogy Properly](http://cloudscaling.com/blog/cloud-computing/the-history-of-pets-vs-cattle/)
- [DevOps Concepts: Pets vs Cattle](https://medium.com/@Joachim8675309/devops-concepts-pets-vs-cattle-2380b5aab313)
- [10Deploys - Episódio 17 - Infraestrutura imutável](https://www.10deploys.com/episodios/017-infraestrutura-imutavel)
- [A Saga do Continuous Delivery - Do Make ao Deploy Automatizado!](https://speakerdeck.com/perylemke/a-saga-do-continuous-delivery-do-make-ao-deploy-automatizado)
- [Infraestrutura Imutável - A base para aplicações nativas na nuvem](https://www.youtube.com/watch?v=JfhBkiSQynY)
- [PhoenixServer](https://martinfowler.com/bliki/PhoenixServer.html)
- [SnowflakeServer](https://martinfowler.com/bliki/SnowflakeServer.html)
- [Configuration Drift: Phoenix Server vs Snowflake Server Comic](https://www.digitalocean.com/community/tutorials/configuration-drift-phoenix-server-vs-snowflake-server-comic)
- [Infraestrutura Imutável - A base para aplicações nativas na nuvem](https://www.youtube.com/watch?v=JfhBkiSQynY)
- [BlueGreenDeployment](https://martinfowler.com/bliki/BlueGreenDeployment.html)
- [CanaryRelease](https://martinfowler.com/bliki/CanaryRelease.html)
- [immutable_infra](https://github.com/perylemke/immutable_infra)
- [Infraestrutura imutável e em nuvem no Nubank](https://www.infoq.com/br/presentations/infraestrutura-imutavel-e-em-nuvem-no-nubank/)

Bom, é isso aí pessoal!

Até o próximo texto :)