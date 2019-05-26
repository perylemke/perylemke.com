---
title: "Infraestrutura Imutável: Devemos ainda nos apegar aos nossos servidores?"
date: 2019-05-18T12:33:08-02:00
draft: true
tags: ["devops", "infra", "sre", "pets vs cattle", "phoenix servers", "snowflake servers", "infra as a code"]
---

Olá mundo =)

Conto para vocês neste post minha saga de estudos sobre Infraestrutura Imutável. Este artigo irá abordar conceitos, mas também irá abordar a prática de infraestrutura imutável com [Packer](https://www.packer.io/), [Ansible](https://www.ansible.com/) e [Terraform](https://www.terraform.io).

E antes de começar quero agradecer a [Fernando Ike](https://www.twitter.com/fernandoike) e [Daniel Romero](https://twitter.com/infoslack) por toda mentoria neste assunto.

## O que é Infraestrutura Imutável?

Infraestrutura Imutável é um paradigma, dentre vários em Infraestrutura, onde servidores não podem sob hipotése nenhuma modificados após o deploy, ou seja, se é necessário alguma mudança, correção e/ou atualização de alguma maneira novos servidores devem ser criados a partir de uma imagem comum com as suas respectivas alterações onde será realizado o provisionamento para substituir os antigos servidores. Depois de validados, os servidores novos são colocados em uso e os antigos são descartados. Este é um conceito que surgiu por meados de 2012, porém existem documentações relacionadas a isso desde 2004.

Infraestrutura Imutável tem alguns sinônimos conhecidos, onde alguns iremos abordar com mais calma neste artigo:

- Pets vs Cattle
- PhoenixServers vs SnowflakeServers;
- Golden Images
- Infrastructure as Code

Neste artigo vamos nos ater nos conceitos de "Pets vs Cattle" e "Phoenix Servers vs Snowflake Servers", os demais tópicos vamos abordar no próximo artigo.

## Pets vs Cattle

O conceito de Pets vs Cattle não naseceram com este nome, mas sim com o conceito de "Scale-Up vs "Scale-Out" apresentado por Bill Baker na apresentação [Scaling SQL Server](https://www.pass.org/eventdownload.aspx?suid=1902) em 2012, sendo que ele nem abordava este assunto em conceito de Cloud Computing, o foco era em num contexto de arquitetura em geral.

Uma das pessoas a ver esta apresentação era [Randy Bias](https://twitter.com/randybias) que escreveu um artigo muito interessante sobre a História do termo [Pets vs Cattle](http://cloudscaling.com/blog/cloud-computing/the-history-of-pets-vs-cattle/) que inspirou este artigo. Bill na sua apresentação usou uma analogia muito interessante que chamou a atenção de Randy, onde ele falou sobre scale-up versus scale-out num contexto de comparar com animais de estimação (Pets) e um rebanho de gado (Cattle).

Então em um momento de iluminação, Randy trouxe para o contexto de nuvem onde ele enfatizou a descartabilidade dos servidores do tipo Cattle (Gado) e singularidade dos servidores do tipo Pets (Animais de estimação). Esse tipo de explicação foi muito bem aceita por membros da comunidade e por [Tim Bell](https://twitter.com/noggin143) do CERN que ajudaram a [popularizar](https://twitter.com/noggin143/status/354666097691205633) o termo e realizar a transição para a cloud.

Mas após toda essa introdução, vamos entrar mais a fundo sobre Pets e Cattle:

### Pets

> Servidores que são tratados como indispensáveis ou exclusivos que nunca podem ser desativados. Normalmente, eles são criados, gerenciados e ajustados manualmente. Exemplos: Mainframes, servidores solitários, loadbalancers/firewalls de HA, bancos de dados projetados como master/slave e assim por diante.

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
 
- Construção do artefato pela ferramenta de Continuos Delivery;
- Dependências são instalados e/ou reinstaladas a versão nova, e para esse se faz necessário uma ferramenta como Ansible, Chef, Puppet ou até mesmo um Shell Script para fazer este passo;
- Implementação do artefato em homologação;
- Implementação do artefato em produção.

Porém este processo corriqueiro pode ter efeitos colaterais como uma possível indisponibilidade do sistema de empacotamento (`pip`, `bundle`, `npm`, `apt`, `yum`, etc.) ocasionando a não instalação daquela biblioteca necessária ao seu projeto, e é um problema muito difícil de achar e se perde muito tempo fazendo troubleshooting. Podem haver casos de uma biblioteca necessária ao projeto não estar mais disponível no gerenciador de pacotes ou até mesmo uma atualização de biblioteca que podem fazer você perder um tempo precioso para resolver o problema.

E um ponto muito importante é a ordem dos comandos, um erro muito comum que muitos de nós já cometeram são comandos certos em ordem errada, por isso que a ordem dos comandos a serem dados é extrema importância nesse caso pois infelizmente temos dependência circular e pacotes que tem que estar numa ordem correta. E para identificar isso é bem díficil pois na maioria dos casos é difícil identificar o problema e vira uma grande bola de neve.

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

### Logs Centralizados

Hoje há uma tendência muito forte em todo o mundo que é o *Observability* que visa condensar as informações sobre o comportamento de todo o ambiente para identifcação da causa raiz dos problemas para resolução dos mesmos.

Então por isso que é uma boa prática a centralização de todos os logs seja da aplicação e da máquina para este fim, ou seja não pode haver escrita no seu servidor.

Você pode centralizar os logs em alguma das várias ferramentas pagas ou não que existem hoje disponíveis.

### Armazenamento de dados externo

### Equipes engajadas

## Formas de Deploy em Infraestrutura Imutável

### Blue/Green Deployment

### Canary Release

### Rolling Update

## Demo

## Erros que cometi durante minha jornada de aprendizado

## Conclusão

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

Bom, é isso aí pessoal!

Até o próximo texto :)