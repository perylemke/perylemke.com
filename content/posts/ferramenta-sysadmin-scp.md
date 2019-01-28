---
title: "Ferramentas úteis para SysAdmins espertos: scp"
date: 2019-01-28T12:33:08-02:00
draft: false
tags: ["linux", "redes", "scp", "sysadmin"]
---

Olá mundo =)

Aproveitando o hype de escrita para o blog, vou escrever sobre uma ferramenta que pode ajudar muito Devs, Devas e SysAdmins (creio que seja uma palavra unissex): o `scp`.

## O que é o scp?

O `scp` é uma ferramenta de envio e recebimento de arquivos através do protocolo [ssh](https://www.ssh.com/). Onde você pode enviar arquivos para o seu servidor remoto e também fazer o caminho reverso.

## Usando o scp

Enviando um arquivo específico para o servidor remoto:

```
$ scp /var/lib/files/teste.py gimli@227.51.135.220:/home/gimli
```

Este comando está enviando o arquivo `teste.py` para o usuário `gimli` do servidor remoto `227.51.135.220` no diretório `/home/gimli`.

Mas se ao invés de enviar somente um arquivo, quero enviar todos os arquivos do diretório `/var/lib/files/`, é muito simples somente usar asterisco ao invés do nome do arquivo, conforme o comando abaixo:

```
$ scp /var/lib/files/* gimli@227.51.135.220:/home/gimli
```

Como o `scp` usa o ssh, ele tem como padrão a porta 22, mas você pode passar uma porta específica usando o parâmetro `-P`, conforme o exemplo abaixo:

```
$ scp -P 991 /var/lib/files/* gimli@227.51.135.220:/home/gimli
```

E também podem ser enviados arquivos do servidor remoto para a sua máquina local, é bem simples, o comando abaixo ilustra bem isso:

```
scp gimli@227.51.135.220:/home/gimli/* /var/lib/files/
```

Somente invertemos a ordem do comando para mandarmos os arquivos para a nossa máquina local.

## Conclusão

Como podemos ver, o `scp` é uma mão na roda para enviarmos e recebermos arquivos. Abaixo, deixo mais alguns links que complementam este texto:

* [12 scp command examples to transfer files on Linux](https://www.binarytides.com/linux-scp-command/)
* [Usando o comando SCP!](https://www.vivaolinux.com.br/dica/Usando-o-comando-SCP)

Mas não deixem de conferir o `man scp` e claro que não esqueci do `rsync`, mas essa ferramenta é assunto para outro post. ;)

Bom, é isso aí pessoal!

Até o próximo texto :)