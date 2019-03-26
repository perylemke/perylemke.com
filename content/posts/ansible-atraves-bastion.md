---
title: "Rodando playbooks do Ansible através de um host Bastion"
date: 2019-03-25T21:11:52-02:00
draft: false
tags: ["linux", "ansible", "automação", "infra"]
---

Olá mundo =)

Antes de começarmos, peço desculpas pela falta de postagens do blog, é o que o mal do mundo moderno acometeu-se em mim, a maldita "correria", mas tentarei ser mais presente aqui :)

Aliás, isso é só um aquecimento, quero explicar em próximos posts o processo de implementação de Continuous Delivery que passei e utilizei [Packer](https://www.packer.io/), [Ansible](https://www.ansible.com/) e [Terraform](https://www.terraform.io).

Porém neste post, vamos a um texto rápido e rasteiro sobre como rodar playbooks do Ansible através de um host Bastion.

## O que é um Bastion?

Um host Bastion é um servidor cujo objetivo é fornecer acesso a uma rede privada através de uma rede pública, como ilustra muito bem o desenho abaixo, que foi retirado do site da AWS:

![](/img/bastion.png)

## Em versões anteriores a 2 do Ansible

É importante explicar que antes da versão 2 do Ansible, não era nativa essa possibilidade sendo que tinha ser feita algumas configurações no `.ssh/config` e no arquivo `ansible.cfg`, como setar o ProxyCommand.

Para deixar este texto mais curto, deixarei um link do artigo do [Scott Lowe](https://twitter.com/scott_lowe) que explica muito como fazer esse processo em versões antigas do Ansible: [Running Ansible Through an SSH Bastion Host](https://blog.scottlowe.org/2015/12/24/running-ansible-through-ssh-bastion-host/).

Então com o lançamento da versão 2 do Ansible a Red Hat facilitou um pouco a nossa vida, e isto será explicado no próximo tópico.

## Em versões recentes do Ansible

É possível agora setar o ProxyCommand na variável `ansible_ssh_common_args` no seu diretório do Ansible, como por exemplo abaixo tenho os seguintes servers configurados no meu arquivo `hosts`:

```
[dwarfcities]
nogrod ansible_host=10.120.0.4
belegost ansible_host=10.120.0.5
```

Então com isso, você irá criar o arquivo `group_vars/dwarfcities.yml` com o seguinte conteúdo:

```
ansible_ssh_common_args: '-o ProxyCommand="ssh -W %h:%p -q user@ip-bastion"'
```

O Ansible vai usar esses argumentos para realizar a tentativa de conexão nos hosts do groupo `dwarfcities`, e assim você poderá rodar seus playbooks tranquilamente realizando automações nos seus servers que estão na rede privada.

## Conclusão

Esta dica é bem importante para quem precisa gerenciar muitos servidores, e usar Bastion, além de uma boa prática, é algo imprescindível hoje em dia em Cloud Computing.

Depois que descobri o Ansible minha vida como SysAdmin melhorou bastante, automatizei boa parte do meu trabalho e apesar de ter ficado confuso no início, hoje consigo me virar bem com a ferramenta. Apesar de utilizar mais o Ansible com o Packer, mas isso é assunto para os próximos posts! :)

Deixo vocês com alguns links:

- [How do I configure a jump host to access servers that I have no direct access to?](https://docs.ansible.com/ansible/latest/reference_appendices/faq.html#how-do-i-configure-a-jump-host-to-access-servers-that-i-have-no-direct-access-to)
- [Using Ansible with a bastion SSH host](https://alexbilbie.com/2014/07/using-ansible-with-a-bastion-host/)
- [Ansible SSH: Using Bastion](https://medium.com/@yagonobre/ansible-ssh-using-bastion-95ec5b28b87e)

Bom, é isso aí pessoal!

Até o próximo texto :)