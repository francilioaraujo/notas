---
title: 'Cola de Linux'
date: '2026-03-20'
tags: []
categories: []
---
<!--more-->

{{< toc >}}

## Conceitos

- [Sobre o kernel linux (en)](https://www.kernel.org/linux.html)
- [Visão geral do sistema GNU](https://www.gnu.org/gnu/gnu-history.html)
- [Linux e o sistema GNU](https://www.gnu.org/gnu/linux-and-gnu.html)
- Distribuições GNU/Linux (mais podem ser encontradas em [DistroWatch](https://distrowatch.com/))
    - [Debian](https://www.debian.org/index.pt.html)
    - [Ubuntu](https://ubuntu.com/)
    - [Slackware](http://www.slackware.com/)
    - [Red Hat Enterprise Linux](https://www.redhat.com/pt-br/technologies/linux-platforms/enterprise-linux)
    - [Fedora](https://fedoraproject.org/)
    - [OpenSUSE](https://www.opensuse.org/)
    - [CentOS](https://www.centos.org/)
    - [Rocky Linux](https://rockylinux.org/)
    - [Arch Linux](https://archlinux.org/)
    - [Alpine Linux](https://www.alpinelinux.org/)
- [Base Padrão Linux (en)](https://refspecs.linuxfoundation.org/lsb.shtml)
- [Padrão de Hierarquia do Sistema de Arquivos (Filesystem Hierarchy Standard - FHS) (en)](https://refspecs.linuxfoundation.org/FHS_3.0/fhs/index.html)

## Bash

Referência: [bash](https://www.gnu.org/software/bash/manual/html_node/index.html)

```bash
# Pipeline de comandos
comando1 | comando2
comando1 |& comando2
# Lista de comandos
comando1 && comando2
comando1 || comando2
```

## Comandos Básicos

### Imprimir texto no terminal

[**echo**](https://www.gnu.org/software/bash/manual/html_node/Bash-Builtins.html#index-echo)

```shell
echo <texto>
```

[**printf**](https://www.gnu.org/software/bash/manual/html_node/Bash-Builtins.html#index-printf)

```shell
printf "Olá, %b\n" mundo
# Olá, mundo!
```

### Navegação entre diretórios

[**pwd**](https://www.gnu.org/software/bash/manual/html_node/Bourne-Shell-Builtins.html#index-pwd)

```shell
pwd
```

[**cd**](https://www.gnu.org/software/bash/manual/html_node/Bourne-Shell-Builtins.html#index-cd)

```shell
cd <diretório>
```

- `cd -`

### Gerenciamento de Arquivos e Diretórios

[**ls**](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/ls-invocation.html)

```shell
ls   [opções] [caminho]
dir  [opções] [caminho] # ls -C -b [opções] [caminho]
vdir [opções] [caminho] # ls -l -b [opções] [caminho]
```

- [`-A|--almost-all`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/Which-files-are-listed.html#index-_002d_002dalmost_002dall)
- [`-d|--directory`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/Which-files-are-listed.html#index-_002d_002ddirectory)
- [`-I <glob>|--ignore=<glob>`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/Which-files-are-listed.html#index-_002d_002dignore)
- [`-h|--human-readable`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/What-information-is-listed.html#index-_002d_002dhuman_002dreadable-1)
- [`-l|--format=long|--format=verbose`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/What-information-is-listed.html#index-long-ls-format)
- [`-Z|--context`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/What-information-is-listed.html#index-_002d_002dcontext)
- [`-r|--reverse`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/Sorting-the-output.html#index-_002d_002dreverse-1)
- [`-S|--sort=size`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/Sorting-the-output.html#index-_002d_002dsort-6)
- [`-t|--sort=time`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/Sorting-the-output.html#index-_002d_002dsort-7)
- [`-X|--sort=extension`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/Sorting-the-output.html#index-_002d_002dsort-12)
- [`-F|--classify[=none|auto|always]|--indicator-style=classify`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/General-output-formatting.html#index-_002d_002dindicator_002dstyle)

[**stat**](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/stat-invocation.html)

```shell
stat <arquivo>
```

[**mkdir**](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/mkdir-invocation.html)

```shell
mkdir [opções] <diretório>
```

- [`-m <modo>|--mode=<modo>`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/mkdir-invocation.html#index-_002d_002dmode-1)
- [`-p|--parents`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/mkdir-invocation.html#index-_002d_002dparents-1)
- [`-Z|--context[=<contexto>]`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/mkdir-invocation.html#index-_002d_002dcontext-4)

[**touch**](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/touch-invocation.html)

```shell
touch <arquivo>
```

[**rm**](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/rm-invocation.html)

```shell
rm [opções] <arquivo>...
```

- [`-f|--force`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/rm-invocation.html#index-_002d_002dforce-2)
- [`-i|-I|--interactive[=never|once|always]`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/rm-invocation.html#index-_002di-13)
- [`-r|-R|--recursive`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/rm-invocation.html#index-_002d_002drecursive-2)
- [`-v|--verbose`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/rm-invocation.html#index-_002d_002dverbose-6)

[**cp**](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/cp-invocation.html)

```shell
cp [opções] origem... destino
```

- [`-r|-R|--recursive`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/cp-invocation.html#index-_002d_002drecursive-1)
- [`-v|--verbose`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/cp-invocation.html#index-_002d_002dverbose-3)

[**mv**](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/mv-invocation.html)

```shell
mv [opções] origem... destino
```

- [`-i|--interactive`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/mv-invocation.html#index-_002d_002dinteractive-1)
- [`-v|--verbose`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/mv-invocation.html#index-_002d_002dverbose-5)

[**install**](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/install-invocation.html)

```shell
install [opções] origem... destino
```

- [`-g <grupo>|--group=<grupo>`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/install-invocation.html#index-_002d_002dgroup-1)
- [`-m <modo>|--mode=<modo>`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/install-invocation.html#index-_002d_002dmode)
- [`-o <dono>|--owner=<dono>`](https://www.gnu.org/savannah-checkouts/gnu/coreutils/manual/html_node/install-invocation.html#index-_002d_002downer)

### Permissões

chmod
chown
chgrp

### Busca de diretórios e arquivos

find

### Visualização de arquivo

cat
tac
nl
head
tail
less

### Busca de texto em arquivos

grep

## Links Úteis

- [Explain Shell](https://explainshell.com/)
- Páginas [TLDR](https://tldr.inbrowser.app/)
- Páginas Man online
    - [Man7 Linux Man Pages Online](https://man7.org/linux/man-pages/index.html)
    - [Die.net Linux Man Pages](https://linux.die.net/man/)
    - [Manned](https://manned.org/)
    - [Mankier](https://www.mankier.com/)
