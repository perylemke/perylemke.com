---
title: "Ferramentas úteis para SysAdmins espertos: Nmap"
date: 2019-01-27T20:10:52-02:00
draft: false
tags: ["linux", "redes", "nmap", "sysadmin"]
---

Olá mundo =)

Este texto tem o intuito de mostrar que nem só de containers vive o SysAdmin moderno.

## O que é o nmap?

Para descrever o que é o `nmap` vamos utilizar a descrição do nosso querido comando `man`:


O Nmap (“Network Mapper”) é uma ferramenta em código aberto para exploração de rede e auditoria de segurança. Foi desenhada para rastrear(Scan) rapidamente redes amplas contudo funciona bem com um único anfitrião(host). 

Nmap usa pacotes IP em estado bruto(raw) sobre novas formas para determinar que anfitriões(hosts) estão disponíveis na rede, que serviços (nome e versão da aplicação) esses anfitriões(hosts) estão disponibilizando, que sistemas operativos (e versões de SO) estão em uso, que tipo de filtros de pacotes/firewalls estão em uso e dezenas de outras características. 

Enquanto o Nmap é frequentemente usado para auditorias de segurança, muitos sistemas e administradores de redes consideram-no útil para as tarefas de rotina como o inventário da rede, gestão de actualizações de serviços e monitorizar o uptime de anfitriões ou serviços.

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
$ nmap -P0 -A $IP_ADDRESS
```

Esse comando lista apenas as portas importantes, ou seja, você pode verificar por exemplo se a porta 22 ou 80 está aberta em seu servidor, e isso num início de troubleshooting pode lhe poupar um bom tempo.

Você pode usar usar o `nmap` de N formas, por isso colocarei abaixo uma série de links de textos e tutoriais que complementam este texto:

* [Install nmap command Software For Scanning Network](https://www.cyberciti.biz/faq/install-nmap-debian-ubuntu-server-desktop-system/)
* [Tutorial NMAP para iniciantes!](https://caveiratech.com/forum/scanning-e-scanners-de-vulnerabilidades/nmap-para-iniciantes!/)
* [Nmap Tutorial](https://hackertarget.com/nmap-tutorial/)
* [Top 32 Nmap Command Examples For Linux Sys/Network Admins](https://www.cyberciti.biz/security/nmap-command-examples-tutorials/)

Bom, é isso aí pessoal!

Até o próximo texto :)