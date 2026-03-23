---
title: Cola de Git
date: 2026-03-18

tags:
- git
- desenvolvimento
- devops

categories:
- cola
---
<!--more-->

{{< toc >}}

## Conceitos

- Termos comuns
    - [Repositório](https://git-scm.com/docs/gitglossary/pt_BR#def_repository)
    - [Commit](https://git-scm.com/docs/gitglossary/pt_BR#def_commit)
    - [Índice (index)](https://git-scm.com/docs/gitglossary/pt_BR#def_index)
    - [Árvore de Trabalho (worktree)](https://git-scm.com/docs/gitglossary/pt_BR#def_working_tree)
    - [Ramo (branch)](https://git-scm.com/docs/gitglossary/pt_BR#def_branch)
    - [Tag](https://git-scm.com/docs/gitglossary/pt_BR#def_tag)
    - [Referência (ref) (en)](https://git-scm.com/docs/gitglossary/pt_BR#def_ref)
- Tutorial introdutório [parte 1 (en)](https://git-scm.com/docs/gittutorial) e [parte 2 (en)](https://git-scm.com/docs/gittutorial-2)
- [Tutorial para desenvolvedores (en)](https://git-scm.com/docs/gitcore-tutorial)
- [Manual do usuário (en)](https://git-scm.com/docs/user-manual)
- [Gitignore](https://git-scm.com/docs/gitignore/pt_BR)
- [Fluxos de trabalho (en)](https://git-scm.com/docs/gitworkflows)

## Comandos comuns

```shell
# Clona um repositório
{{< codelink text="git clone" href="#clone" >}} [opções] [--] <repositório> [diretório]
# Exibe informações de repositórios remotos
{{< codelink text="git remote" href="#remote" >}} ...
# Baixa objetos de repositórios remotos
{{< codelink text="git fetch" href="#fetch" >}} ...
# Exibe o log de modificações
{{< codelink text="git log" href="#log" >}} [opções] [intervalo] [[--] caminho...]
# Alterna entre ramos
{{< codelink text="git switch" href="#switch" >}} [opções] ...
# Exibe status da árvore de trabalho e índice
{{< codelink text="git status" href="#status" >}} [opções] [--] [caminho...]
# Exibe diferenças
{{< codelink text="git diff" href="#diff" >}} [opções] ...
# Adiciona ao índice
{{< codelink text="git add" href="#add" >}} [opções] [--] [arquivo...]
# Restaura o conteúdo do índice ou da árvore de trabalho
{{< codelink text="git restore" href="#restore" >}} [opções] [--] [arquivo...]
# Grava o índice no repositório
{{< codelink text="git commit" href="#commit" >}} [opções] [--] [arquivo...]
# Mescla commits
{{< codelink text="git merge" href="#merge" >}} ...
# Recria commits sobre um commit
{{< codelink text="git rebase" href="#rebase" >}} ...
# Gerencia ramos
{{< codelink text="git branch" href="#branch" >}} ...
# Gerencia tags
{{< codelink text="git tag" href="#tag" >}} ...
# Baixa objetos de repositórios remotos faz merge no ramo atual
{{< codelink text="git pull" href="#pull" >}} [opções] [<repositório> [referência...]]
# Sobe objetos para o repositório remoto
{{< codelink text="git push" href="#push" >}} [opções] [<repositório> [referência...]]
```

### Clone

Referência: [clone](https://git-scm.com/docs/git-clone/pt_BR)

Uso: `git clone [opções] [--] <repositório> [diretório]`

- [`-o|--origin <nome>`](https://git-scm.com/docs/git-clone/pt_BR#git-clone--oltnomegt)
- [`-b|--branch <nome>`](https://git-scm.com/docs/git-clone/pt_BR#git-clone--bltnomegt)
- [`--bare`](https://git-scm.com/docs/git-clone/pt_BR#git-clone---bare)
- [`--depth <n>`](https://git-scm.com/docs/git-clone/pt_BR#git-clone---depthltprofundidadegt)

### Remote

Referência: [remote](https://git-scm.com/docs/git-remote/pt_BR)

Uso:

```shell
git remote [-v|--verbose]
git remote [subcomando]
```

- [`add [opções] <nome> <url>`](https://git-scm.com/docs/git-remote/pt_BR#git-remote-emaddem)
    - `-t <ramo>` obtem apenas a referência `ramo`
- [`rename <antigo> <novo>`](https://git-scm.com/docs/git-remote/pt_BR#git-remote-emrenameem)
- [`get-url [opções] <nome>`](https://git-scm.com/docs/git-remote/pt_BR#git-remote-emget-urlem)
- [`set-url [--push] <nome> <url>`](https://git-scm.com/docs/git-remote/pt_BR#git-remote-emset-urlem)

### Busca (fetch)

Referência: [fetch](https://git-scm.com/docs/git-fetch/pt_BR)

Uso:

```shell
git fetch --all [opções]
git fetch [opções] [repositório [referência]]
```

- [`--depth=<n>`](https://git-scm.com/docs/git-fetch/pt_BR#git-fetch---depthltprofundidadegt)
- [`-t|--tags`](https://git-scm.com/docs/git-fetch/pt_BR#git-fetch--t)
- [`-4|--ipv4`](https://git-scm.com/docs/git-fetch/pt_BR#git-fetch--4)
- [`-6|--ipv6`](https://git-scm.com/docs/git-fetch/pt_BR#git-fetch--6)

### Log

Referência: [log](https://git-scm.com/docs/git-log/pt_BR)

Uso: `git log [opções] [intervalo] [[--] caminho...]`

- [`--decorate[=short|full|auto|no]`](https://git-scm.com/docs/git-log/pt_BR#git-log---no-decorate)
- [`--pretty[=<formato>]|--format=<format>`](https://git-scm.com/docs/git-rev-list/pt_BR#git-rev-list---prettyltformatogt)
- [`--oneline`](https://git-scm.com/docs/git-rev-list/pt_BR#git-rev-list---oneline)
- [`--stat`](https://git-scm.com/docs/git-diff/pt_BR#git-diff---statltlarguragtltlargura-do-nomegtltcountgt)
- [`--shortstat`](https://git-scm.com/docs/git-diff/pt_BR#git-diff---shortstat)
- [`--name-status`](https://git-scm.com/docs/git-diff/pt_BR#git-diff---name-status)
- [`--all`](https://git-scm.com/docs/git-rev-list/pt_BR#git-rev-list---all)
- [`--branches[=<padrão>]`](https://git-scm.com/docs/git-rev-list/pt_BR#git-rev-list---branchesltpadrogt)
- [`--tags[=<padrão>]`](https://git-scm.com/docs/git-rev-list/pt_BR#git-rev-list---tagsltpadrogt)
- [`--remotes[=<padrão>]`](https://git-scm.com/docs/git-rev-list/pt_BR#git-rev-list---remotesltpadrogt)
- [`--graph`](https://git-scm.com/docs/git-rev-list/pt_BR#git-rev-list---graph)
- [`--follow`](https://git-scm.com/docs/git-log/pt_BR#git-log---follow)
- [`-L<inicio>,<fim>:<arquivo`](https://git-scm.com/docs/git-log/pt_BR#git-log--Lltiniciogtltfimgtltarquivogt)
- [`-S=<texto>`](https://git-scm.com/docs/git-diff/pt_BR#git-diff--Slttextogt)
    - [`--pickaxe-regex`](https://git-scm.com/docs/git-diff/pt_BR#git-diff---pickaxe-regex)
    - [`--pickaxe-all`](https://git-scm.com/docs/git-diff/pt_BR#git-diff---pickaxe-all)
- [`--since=<data>|--after=<data>`](https://git-scm.com/docs/git-rev-list/pt_BR#git-rev-list---sinceltdatagt)
- [`--until=<data>|--before=<data>`](https://git-scm.com/docs/git-rev-list/pt_BR#git-rev-list---untilltdatagt)
- [`--author=<padrão>|--committer=<padrão>`](https://git-scm.com/docs/git-rev-list/pt_BR#git-rev-list---authorltpadrogt)
- [`--grep=<padrão>`](https://git-scm.com/docs/git-rev-list/pt_BR#git-rev-list---grepltpadrogt)
    - [`-E|--extended-grep`](https://git-scm.com/docs/git-rev-list/pt_BR#git-rev-list--E)
    - [`-P|--perl-regexp`](https://git-scm.com/docs/git-rev-list/pt_BR#git-rev-list--P)
    - [`-F|--fixed-strings`](https://git-scm.com/docs/git-rev-list/pt_BR#git-rev-list--F)
    - [`-i|--regexp-ignore-case`](https://git-scm.com/docs/git-rev-list/pt_BR#git-rev-list--i)
    - [`--invert-grep`](https://git-scm.com/docs/git-rev-list/pt_BR#git-rev-list---invert-grep)
    - [`--all-match`](https://git-scm.com/docs/git-rev-list/pt_BR#git-rev-list---all-match)
- [`--reverse`](https://git-scm.com/docs/git-rev-list/pt_BR#git-rev-list---reverse)
- [`-G=<padrão>`](https://git-scm.com/docs/git-diff/pt_BR#git-diff--Gltexpresso-regulargt)

### Switch

Referência: [switch](https://git-scm.com/docs/git-switch/pt_BR)

Uso:

```shell
git switch [opções] <ramo>
git switch [opções] -c|-C <ramo> [ponto de partida]
```

### Status

Referência: [status](https://git-scm.com/docs/git-status/pt_BR)

Uso: `git status [opções] [--] [caminho...]`

- [`-s|--short`](https://git-scm.com/docs/git-status/pt_BR#git-status--s)
- [`--branch`](https://git-scm.com/docs/git-status/pt_BR#git-status---branch)

### Diff

Referência: [diff](https://git-scm.com/docs/git-diff/pt_BR)

Uso:

```shell
git diff [opções] [commit] [--] [caminho...]
git diff [opções] --cached [commit] [--] [caminho...]
git diff [opções] --no-index 
git diff [opções] <commit>..<commit> [--] [caminho...]
```

### Add

Referência: [add](https://git-scm.com/docs/git-add/pt_BR)

Uso: `git add [opções] [--] [arquivo...]`

- [`-i|--interactive`](https://git-scm.com/docs/git-add/pt_BR#git-add--i)
- [`-p|--patch`](https://git-scm.com/docs/git-add/pt_BR#git-add--p)
- [`-u|--update`](https://git-scm.com/docs/git-add/pt_BR#git-add--u)
- [`-A|--all`](https://git-scm.com/docs/git-add/pt_BR#git-add--A)

### Restore

Referência: [restore](https://git-scm.com/docs/git-restore/pt_BR)

Uso: `git restore [opções] [--] [arquivo...]`

- [`-S|--staged`](https://git-scm.com/docs/git-restore/pt_BR#git-restore--S)
- [`-s=<referência>|--source=<referência>`](https://git-scm.com/docs/git-restore/pt_BR#git-restore--sltrvoregt)

### Commit

Referência: [commit](https://git-scm.com/docs/git-commit/pt_BR)

Uso: `git commit [opções] [--] [arquivo...]`

- [`-m=<msg>|--message=<msg>`](https://git-scm.com/docs/git-commit/pt_BR#git-commit--mltmsggt)
- [`--amend`](https://git-scm.com/docs/git-commit/pt_BR#git-commit---amend)

### Merge

Referência: [merge](https://git-scm.com/docs/git-merge/pt_BR)

Uso:

```shell
git merge [opções] [commit...]
```

- [`--no-commit`](https://git-scm.com/docs/git-merge/pt_BR#git-merge---no-commit)
- [`-e|--edit`](https://git-scm.com/docs/git-merge/pt_BR#git-merge--e)

```shell
git merge --continue
git merge --abort
git merge --quit
```

### Rebase

Referência: [rebase]()

Uso:

```shell
git rebase [opções] <ramo>
git rebase [opções] <ramo origem> <ramo destino>
```

- [`-i|--interactive`](https://git-scm.com/docs/git-rebase/pt_BR#git-rebase--i)
    - [`-x=<cmd>|--exec=<cmd>`](https://git-scm.com/docs/git-rebase/pt_BR#git-rebase--xltcmdgt)

```shell
git rebase --continue
git rebase --skip
git rebase --abort
git rebase --quit
git rebase --edit-todo
git rebase --show-current-patch
```

### Branch

Referência: [branch](https://git-scm.com/docs/git-branch/pt_BR)

Uso:

```shell
git branch
git branch [opções]
```

- [`--contains=<commit>`](https://git-scm.com/docs/git-branch/pt_BR#git-branch---containsltcommitgt)
- [`--no-contains=<commit>`](https://git-scm.com/docs/git-branch/pt_BR#git-branch---no-containsltcommitgt)
- [`--merged=<commit>`](https://git-scm.com/docs/git-branch/pt_BR#git-branch---mergedltcommitgt)
- [`--no-merged=<commit>`](https://git-scm.com/docs/git-branch/pt_BR#git-branch---no-mergedltcommitgt)

```shell
git branch [opções] [ramo]
```

- [`-d|--delete`](https://git-scm.com/docs/git-branch/pt_BR#git-branch--d)
- [`-m|--move`](https://git-scm.com/docs/git-branch/pt_BR#git-branch--m)
- [`-c|--copy`](https://git-scm.com/docs/git-branch/pt_BR#git-branch--c)
- [`-u=<upstream>|--set-upstream-to=<upstream>`](https://git-scm.com/docs/git-branch/pt_BR#git-branch--ultupstreamgt)

### Tag

Referência: [tag](https://git-scm.com/docs/git-tag/pt_BR)

Uso:

```shell
git tag
git tag -d <tag>
git tag [opções]
```

- [`--contains=<commit>`](https://git-scm.com/docs/git-tag/pt_BR#git-tag---containsltcommitgt)
- [`--no-contains=<commit>`](https://git-scm.com/docs/git-tag/pt_BR#git-tag---no-containsltcommitgt)
- [`--merged=<commit>`](https://git-scm.com/docs/git-tag/pt_BR#git-tag---mergedltcommitgt)
- [`--no-merged=<commit>`](https://git-scm.com/docs/git-tag/pt_BR#git-tag---no-mergedltcommitgt)

```shell
git tag [opções] <tag> [<commit>|<objeto>]
```

- [`-a|--anotate`](https://git-scm.com/docs/git-tag/pt_BR#git-tag--a)

### Pull

Referência: [pull](https://git-scm.com/docs/git-pull/pt_BR)

Uso: `git pull [opções] [<repositório> [referência...]]`

### Push

Referência: [push](https://git-scm.com/docs/git-push/pt_BR)

Uso: `git push [opções] [<repositório> [referência...]]`

- [`-d|--delete`](https://git-scm.com/docs/git-push/pt_BR#git-push--d)

## Outros comandos

```shell
# Exibe e define configurações
{{< codelink text="git config" href="#config">}}
# 
{{< codelink text="git show" href="#">}}
# 
{{< codelink text="git difftool" href="#">}}
# 
{{< codelink text="git mergetool" href="#">}}
# 
{{< codelink text="git stash" href="#">}}
# 
{{< codelink text="git worktree" href="#">}}
# 
{{< codelink text="git bisect" href="#">}}
```

### Config

Referência: [config](https://git-scm.com/docs/git-config/pt_BR)

Uso:

```shell
git config [opção-arquivo] [opções]
git config [opção-arquivo] <nome> [valor]
```

Opções de arquivo:

- [`--system`](https://git-scm.com/docs/git-config/pt_BR#git-config---system)
- [`--global`](https://git-scm.com/docs/git-config/pt_BR#git-config---global)
- [`--local`](https://git-scm.com/docs/git-config/pt_BR#git-config---local)
- [`--worktree`](https://git-scm.com/docs/git-config/pt_BR#git-config---worktree)
- [`-f=<arquivo>|--file=<arquivo>`](https://git-scm.com/docs/git-config/pt_BR#git-config--fltarquivo-de-configgt)

Outras opções:

- [`-l|--list`](https://git-scm.com/docs/git-config/pt_BR#git-config--l)
- [`-e|--edit`](https://git-scm.com/docs/git-config/pt_BR#git-config--e)

## Ferramentas auxiliares

- [Delta](https://dandavison.github.io/delta/)
- [Difftastic](https://difftastic.wilfred.me.uk/)
- [Meld](https://meldmerge.org/)
- [Mergiraf](https://mergiraf.org/)
- [Lazygit](https://github.com/jesseduffield/lazygit)
- [Tig](https://jonas.github.io/tig/)

## Forjas Git

- [Github](https://github.com/)
- [Gitlab](https://about.gitlab.com/)
- [Bitbucket](https://bitbucket.org/product/)
- [Codeberg](https://codeberg.org/)
- [Gitea](https://about.gitea.com/)
- [Forgejo](https://forgejo.org/)
- [Gogs](https://gogs.io/getting-started/introduction)
- [Sourcehut](https://sourcehut.org/)
- [Gerrit](https://www.gerritcodereview.com/)
- [Radicle](https://radicle.xyz/)