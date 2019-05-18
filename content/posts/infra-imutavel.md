---
title: "Infraestrutura Imutável: Devemos ainda nos apegar aos nossos servidores?"
date: 2019-05-18T12:33:08-02:00
draft: false
tags: ["devops", "infra", "sre", "pets vs cattle", "phoenix servers", "snowflake servers", "infra as a code"]
---

Olá mundo =)

Começa uma micro série de dois posts neste humilde blog sobre Infraestrutura Imutável. Este artigo será mais conceitual e o próximo artigo será algo mais prático aplicando os conceitos de Infra imutável com [Packer](https://www.packer.io/), [Ansible](https://www.ansible.com/) e [Terraform](https://www.terraform.io).

E antes de começar quero agradecer ao [Fernando Ike](https://www.twitter.com/fernandoike) por toda a ajuda e incentivo para mentoria neste processo.

## O que é Infraestrutura Imutável?

Infraestrutura Imutável é um paradigma, dentre vários, onde servidores não podem sob hipotése nenhuma modificados após o deploy, ou seja, se é necessário alguma mudança, correção e/ou atualização de alguma maneira novos servidores devem ser criados a partir de uma imagem comum com as suas respectivas alterações onde será realizado o provisionamento para substituir os antigos servidores. Depois de validados, os servidores novos são colocados em uso e os antigos são descartados.

Lembrando que a premissa principal para você aplicar infraestrutura imutável é você estar num ambiente de Cloud, infelizmente não irá rolar no seu ambiente On Premises. :(

Infraestrutura Imutável tem alguns sinônimos conhecidos, onde alguns iremos abordar com mais calma neste artigo:

- Pets vs Cattle
- Phoenix Servers vs Snowflake Servers;
- Golden Images
- Infrastructure as Code

Neste artigo vamos nos ater nos conceitos de "Pets vs Cattle" e "Phoenix Servers vs Snowflake Servers", os demais tópicos vamos abordar no próximo artigo.

## Pets vs Cattle

## Phoenix Servers vs Snowflake Servers;

## Conclusão

Deixo para vocês alguns links muito úteis:

- []()

Bom, é isso aí pessoal!

Até o próximo texto :)