---
title: "Ferramentas úteis para SysAdmins espertos: Nmap"
date: 2019-01-27T20:10:52-02:00
draft: false
tags: ["linux", "redes", "nmap", "sysadmin"]
---

Olá mundo =)

Este texto tem o intuito de mostrar que nem só de containers vive o SysAdmin moderno.

## O que é o nmap?

O `nmap` é um "mapeador de rede" (Networking Mapping) utilizado para escanear e identificar portas abertas, serviços rodando nestas portas e sua respectiva versão para que sejam identificadas vulnerabilidades nos mesmos.

É reconhecida como uma ferramenta muito rápida e já salvou a vida de muita gente, inclusive deste que vos escreve.

Para maiores detalhes, basta escrever no seu terminal `man nmap` ;)

## Instalando o nmap

Para instalar o nmap no Debian/Ubuntu basta digitar:

```
$ sudo apt-get install nmap
```

E para CentOS:

```
$ yum install nmap
```

Verificando se a instalação está correta:

```
$ nmap --version

Nmap version 7.60 ( https://nmap.org )
```

## Usando o nmap

Agora vamos ao que interessa, vamos usar o `nmap` para escanear os nossos servidores, vou pegar o exemplo que eu mais uso:

```
$ nmap -P0 -A 229.74.158.92
```

Esse comando lista apenas as portas importantes, ou seja, você pode verificar por exemplo se a porta 22 ou 80 está aberta em seu servidor, isso num início de troubleshooting pode lhe poupar um bom tempo, e me ajudou bastante em um caso onde as regras de firewall se perderam por algum motivo e consegui identificar o porquê da aplicação não estar rodando no momento.

Um outro exemplo que pode ser utilizado é usando o parâmetro `-p`, e passando a porta respectiva:

```
$ nmap -p 22 213.183.92.229
```

Usando desta maneira verificamos diretamente se a porta 22 está aberta no servidor informado.

E um último exemplo interessante que podemos usar neste post, é usando o parâmetro `-sS` que é a opção padrão usada pelo Nmap quando nenhuma opção for definida.

```
$ nmap -sS 118.32.111.217
```

## Conclusão

Esta ferramenta é extremamente útil para você fazer seu troubleshooting, e recomendo estudar a fundo, por isso colocarei abaixo uma série de links de textos e tutoriais que complementam este texto:

* [NMap Book](https://nmap.org/book/)
* [Scanning de portas com Nmap](https://infoslack.com/security/scanning-de-portas-com-nmap)
* [Install nmap command Software For Scanning Network](https://www.cyberciti.biz/faq/install-nmap-debian-ubuntu-server-desktop-system/)
* [Tutorial NMAP para iniciantes!](https://caveiratech.com/forum/scanning-e-scanners-de-vulnerabilidades/nmap-para-iniciantes!/)
* [Nmap Tutorial](https://hackertarget.com/nmap-tutorial/)
* [Top 32 Nmap Command Examples For Linux Sys/Network Admins](https://www.cyberciti.biz/security/nmap-command-examples-tutorials/)

Bom, é isso aí pessoal!

Até o próximo texto :)