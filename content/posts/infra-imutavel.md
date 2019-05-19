---
title: "Infraestrutura Imutável: Devemos ainda nos apegar aos nossos servidores?"
date: 2019-05-18T12:33:08-02:00
draft: true
tags: ["devops", "infra", "sre", "pets vs cattle", "phoenix servers", "snowflake servers", "infra as a code"]
---

Olá mundo =)

Começa uma micro série de dois posts neste blog sobre Infraestrutura Imutável. Este artigo será mais conceitual e o próximo artigo será algo mais prático aplicando os conceitos de Infra imutável com [Packer](https://www.packer.io/), [Ansible](https://www.ansible.com/) e [Terraform](https://www.terraform.io).

E antes de começar quero agradecer a [Fernando Ike](https://www.twitter.com/fernandoike) e [Daniel Romero](https://twitter.com/infoslack) por toda mentoria neste assunto.

## O que é Infraestrutura Imutável?

Infraestrutura Imutável é um paradigma, dentre vários em Infraestrutura, onde servidores não podem sob hipotése nenhuma modificados após o deploy, ou seja, se é necessário alguma mudança, correção e/ou atualização de alguma maneira novos servidores devem ser criados a partir de uma imagem comum com as suas respectivas alterações onde será realizado o provisionamento para substituir os antigos servidores. Depois de validados, os servidores novos são colocados em uso e os antigos são descartados. Este é um conceito que surgiu por meados de 2012, porém existem documentações relacionadas a isso desde 2004.

Uma boa prática para você aplicar infraestrutura imutável é você estar num ambiente de Cloud, pode rolar num ambiente Bare Metal (On Premises), mas você deve tem que instalar um OpenStack, ou alguma ferramenta similar. Trocando em miúdos, é díficil, não compensa o esforço, por isso não recomendo.

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

Definição cruel, porém verdadeira, os servidores ganham um número ou código de identificação como os rebanhos de gado ganham nas fazendas por exemplo: `web001` até `web100`. Um exemplo prático é usando o *Instance Group Manager* no Google Cloud, geralmente quando uma máquina morre, seja lá por qual motivo, o *Instance Group Manager* vai recriar uma nova máquina para você com impacto zero para a sua aplicação rodando.

A mesma coisa vale para um cluster de Kubernetes, desde que usamos a boa prática de usar multi master como citei mais acima, e isso multi master impáres e com no mínimo 3 servidores. Porquê uma coisa é um worker cair, isso é tranquilo de recuperar, mas se tivermos um Master apenas, esse servidor se transforma num Pet.

E pessoas gostando ou não desta definição, Pets vs Cattle tem um contexto histórico relevante e é muito importante para entendermos o que é infraesturura imutável.

## PhoenixServers vs SnowflakeServers

Então [Martin Fowler](https://twitter.com/martinfowler) altera um pouco o termo, e os batiza em PhoenixServers (Cattle) e SnowflakeServers (Pets), os conceitos são parecidos, quase idênticos, por isso, para não deixar este artigo repetitivo vou resumir para vocês:

### PhoenixServers

> PhoenixServers são semelhantes a Cattle. São servidores que são sempre construídos do zero e são fáceis de recriar (ou "renascem") por meio de procedimentos automatizados.

### SnowflakeServers

> SnowflakeServers são semelhantes Pets. São servidores que são gerenciados manualmente, atualizados com freqüência e ajustados no local, levando a um ambiente exclusivo.

## Problemas comuns em Snowflake Servers

## Formas de Deploy em Infraestrutura Imutável

## Boas práticas em Infraestrutura Imutável

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

Bom, é isso aí pessoal!

Até o próximo texto :)