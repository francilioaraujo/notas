---
title: "YAML como língua estrangeira"
date: "2021-03-23"

tags:
- "yaml"
- "json"
- "ansible"
- "kubernetes"
- "cloud formation"

categories:
- guias
---

## Introdução

YAML é um formato de serialização de dados que objetiva ser fácil para humanos lerem e fácil de ser tratada por computadores. Por esta característica, várias ferramentas utilizam arquivos neste formato quando espera-se bastante interação humana.

Mesmo tendo como objetivo ser um formato fácil de ser lido, não é difícil ocorrerem erros quando escrevemos neste formato. Quem nunca ouviu um "basta colocar/retirar dois espacinhos" ao escrever um YAML não escreveu YAML o suficiente.

Em caso de dúvidas, o bom senso diz para ler a documentação. No caso do YAML, a documentação é uma [especificação](https://yaml.org/) bem detalhista nada convidativa a uma rápida referência.

Neste artigo busco prover uma referência rápida e agradável ao YAML.

## JSON como língua materna

O formato JSON (JavaScript Object Notation) é um formato já bastante utilizado na internet. Ao falar sobre API (REST!), serviço ou backend é comum supor que haverão dados em formato JSON envolvidos. JSON é baseado em um subconjunto do javascript, mas isso não impediu que fosse utilizado com outras linguagens.

"Por que falar de JSON em um texto sobre YAML?", você deve estar pensando. Acontece que JSON é um subconjunto de YAML. Na verdade só a partir da [versão 1.2](https://yaml.org/spec/1.2/spec.html) da especificação é que há compatibilidade real entre YAML e JSON (e por consequência, JSON vira um subconjunto de YAML), mas boa parte do que existe em JSON também existe em YAML. Se o parser implementar a versão 1.2 da especificação, você pode utilizar arquivos JSON como YAML! (resguardadas as funcionalidades requeridas).

## Estrutura

Um arquivo YAML é composto por um ou mais documentos YAML independentes. O início de um documento YAML é marcado por três traços `---` em uma única linha. Esta marcação é chamada de *indicador de início de documento* e será referida apenas como *início de documento* neste texto. O primeiro início de documento pode ser omitido, mas os seguintes devem ser utilizados, se existir mais de um documento. Também não podem haver espaços em branco no início da linha do início de documento.

A relação entre elementos dentro de um documento YAML é determinada pelo nível de indentação. Então é de suma importância estar atento à indentação ao escrever em YAML. Apenas espaços são permitidos para realizar indentação.

Comentários são permitidos por meio do caractere `#`, independente da indentação. Comentários também podem ser colocados ao final de uma linha com outro elemento, mas neste caso deve haver um espaço antes do `#`. Comentários não podem ser colocados na mesma linha do início de documento.

Um documento, além de comentários, pode conter nós. Um nó representa conteúdo e pode ser uma coleção, um escalar ou um alias. Neste texto, utilizarei nó sempre que estiver fazendo referência aos três elementos ao mesmo tempo.

Escalares em YAML são valores primitivos, como inteiros, decimais, strings, booleanos, etc. Na [versão 1.0](https://yaml.org/spec/1.0/#tag-repository) da especificação existiam apenas strings como escalares e qualquer interpretação do valor era totalmente dependente da implementação. Na [versão 1.1](https://yaml.org/spec/1.1/) é feita referência a uma [especificação independente](https://yaml.org/type/index.html) para os tipos de escalares, com binários, booleanos, inteiros, decimais, valor nulo, etc.

Há dois tipos de coleção em YAML, sequências e mapas, e eles podem ser representados em dois estilos, em bloco e em fluxo.

### Sequências

<!-- markdownlint-disable MD038 -->
Uma sequência é uma lista ordenada de nós. A sequência é similar ao array em JSON. Sua representação em fluxo é similar à versão em JSON, iniciando com `[`, terminando com `]` e com os itens separados por `,`. A representação em bloco é feita colocando cada item em uma linha após os caracteres traço e espaço `- `. Por exemplo, uma sequência formada pelas três primeiras letras em YAML seria:
<!-- markdownlint-enable MD038 -->

```yaml
# Representação em fluxo
["a", "b", "c"]

# Representação em bloco
- "a"
- "b"
- "c"
```

Como o conteúdo de um item é um nó, também é possível ter listas aninhadas, até misturando estilos:

```yaml
# fluxo-fluxo
[ [1, 2, 3] ]

# bloco-fluxo
- [1, 2, 3]

# bloco-bloco
-
  - 1
  - 2
  - 3

# bloco-bloco sem nova linha
- - 1
  - 2
  - 3
```

Note a importância da identação pra definir a relação entre os nós. No exemplo *bloco-bloco* caso a linha com o item *2* tivesse uma indentação igual à indentação da primeira linha faria este item pertencer à sequência pai ao invés da sequência filha.

### Mapa

<!-- markdownlint-disable MD038 -->
Um mapa é uma lista não ordenada de chave-valor. O mapa é similar ao objeto em JSON. Assim como acontece com as sequências a representação em fluxo é similar à versão em JSON, iniciando com `{`, terminando com `}`, separando a chave do valor por `:` e com cada par chave-valor separado por `,`. A representação em bloco é feita colocando a chave, os caracteres dois pontos e espaço `: ` e o valor, uma linha por cada par chave-valor. Por exemplo, um mapa formado pelas três primeiras letras do alfabeto e suas respectivas posições em YAML seria:
<!-- markdownlint-enable MD038 -->

```yaml
# Representação em fluxo
{ "a": 1, "b": 2, "c": 3}

# Representação em bloco
"a": 1
"b": 2
"c": 3
```

Também é possível aninhar mapas, ter sequências dentro de mapas e mapas dentro de sequências. A seguir mostro exemplos em YAML e sua contraparte em JSON:

```yaml
# [{"a": 1, "b"; 2}]
-
  "a": 1
  "b": 2

# Outra forma de fazer
- "a": 1
  "b": 2

# Mais uma forma
- { "a": 1, "b": 2 }

# { "ab": [1, 2] }
"ab":
  - 1
  - 2
```

### Escalares

Assim como em json, podemos escrever strings e números como escalares.

Números inteiros podem ser escritos nas bases 2, 8, 10, 16 e 60. Exemplos:

```yaml
canônico: 685230
base:
  2:  0b1010_0111_0100_1010_1110
  8:  02472256
  10: +685_230
  16: 0x_0A_7A_AE
  60: 190:20:30
```

Números de ponto flutuante podem ser escritos em notação científica, em base decimal ou em base 60. Também há os valores especiais `.NaN`, `-.inf` e `.inf` que podem ser utilizados. Exemplos:

```yaml
canônico:          6.8523015e+5
exponencial:       685.230_15e+03
fixo:              685_230.15
base 60:           190:20:30.15
infinito negativo: -.inf
não é número:      .NaN
```

Booleanos podem ser escritos como `true` e `false`, mas YAML também suporta booleanos como `yes`/`no`, `y`/`n` e `on`/`off`. Exemplos:

```yaml
canônico: y
resposta: NO
lógica:   True
opção:    on
```

Valores nulos podem ser escritos como `null`, `~` ou simplesmente um valor vazio. Exemplos:

```yaml
canônico: ~
nulo:     Null
vazio:    # Note que ainda há um espaço após o :
```

Além destes tipos, o YAML também dá suporte a timestamps. Exemplos:

```yaml
canônico:             2001-12-15T02:59:43.1Z
iso8601 válido:       2001-12-14t21:59:43.10-05:00
separado por espaços: 2001-12-14 21:59:43.10 -5
Sem timezone:         2001-12-15 2:59:43.10
data (00:00:00Z):     2002-12-14
```

E suporte a valores binários codificados em base64. Exemplos:

```yaml
# O binário abaixo é uma imagem GIF com
# uma seta pequena

canônico: !!binary "\
 R0lGODlhDAAMAIQAAP//9/X17unp5WZmZgAAAOfn515eXvPz7Y6OjuDg4J+fn5\
 OTk6enp56enmlpaWNjY6Ojo4SEhP/++f/++f/++f/++f/++f/++f/++f/++f/+\
 +f/++f/++f/++f/++f/++SH+Dk1hZGUgd2l0aCBHSU1QACwAAAAADAAMAAAFLC\
 AgjoEwnuNAFOhpEMTRiggcz4BNJHrv/zCFcLiwMWYNG84BwwEeECcgggoBADs="
genérico: !binary |
 R0lGODlhDAAMAIQAAP//9/X17unp5WZmZgAAAOfn515eXvPz7Y6OjuDg4J+fn5
 OTk6enp56enmlpaWNjY6Ojo4SEhP/++f/++f/++f/++f/++f/++f/++f/++f/+
 +f/++f/++f/++f/++f/++SH+Dk1hZGUgd2l0aCBHSU1QACwAAAAADAAMAAAFLC
 AgjoEwnuNAFOhpEMTRiggcz4BNJHrv/zCFcLiwMWYNG84BwwEeECcgggoBADs=
```

### Strings

Strings, no entanto, são mais versáteis em YAML do que em JSON. Assim como as coleções, strings também podem ser representadas em fluxo ou em bloco. A representação em fluxo permite que strings sejam escritas delimitadas por aspas simples `'` ou aspas duplas `"`. Ao delimitar a string por aspas simples o único escape permitido é `\'` e nenhuma outra sequência de escape será interpretada. Ao delimitar por aspas duplas, as sequências serão interpretadas, mas todo caractere `\\` e `"` deverá ser escrito `\\\\` e `\\"`, respectivamente.

<!-- markdownlint-disable MD038 -->
Strings também podem ser escritas sem delimitadores, este sendo chamado de estilo plano. O estilo plano é interpretado como uma string delimitada por aspas simples, mas há restrições adicionais. Strings planas não podem começar com os caracteres `[`, `]`, `{`, `}`, `!`, `*`, `&`, `|`, `>`, `'`, `"`, `#`, `%`, `@` ou `` ` ``. Os caracteres `-`, `?`, `:` e `,` são permitidos desde que não sejam sucedidos por um espaço. Strings planas também não podem conter as sequências ` #` e `: `. Note que estas restrições são para que não haja ambiguidade entre strings planas e outros tipos de nó. Por exemplo:
<!-- markdownlint-enable MD038 -->

```yaml
"Esta é uma string delimitada por \" (aspas duplas)"

'Esta é uma string delimitada por \' (aspas simples)'

Esta é uma string plana
```

É possível escrever uma string em fluxo em múltiplas linhas, seja ela delimitada por aspas simples, duplas ou uma string plana, mas as quebras de linhas não são mantidas. Todo o espaço em branco ao redor da linha é removido, ou seja, linhas sequenciais são mescladas em uma linha só. Caso haja uma linha em branco, esta será transformada em uma quebra de linha. Por exemplo:

```yaml
# String original
"Esta é uma string longa
dividida em várias linhas.

  Esta é uma outra linha"

# Resultado
Esta é uma string longa dividida em várias linhas.
Esta é uma outra linha
```

No caso de strings planas, linhas com indentação maior são mantidas. Por exemplo:

```yaml
# String original
Esta é uma string longa
dividida em várias linhas.

  Esta é uma outra linha

# Resultado
Esta é uma string longa dividida em várias linhas.
  Esta é uma outra linha
```

Strings podem ser escritas de forma literal, sem processamento exceto normalização de fim de linha e retirada da indentação. Strings literais são indicadas pelo caractere `|`. Por exemplo:

```yaml
python: |
  def function():
    pass
bash: |
  function () 
```

Strings folded não sofrem processamento, como strings literais, mas as quebras de linhas são substituídas por espaços e linhas em branco por quebras de linha. Strings folded são indicadas pelo caractere `>`. Por exemplo:

```yaml
python: >
  def
  function():

    pass
```

### Âncoras, alias e mescla de mapas

Em YAML é possível fazer reuso de valores já declarados. Você pode realizar o reuso de valores inteiros, ou de pares chave-valor de um mapa. O primeiro é feito por âncoras e o segundo por mescla de mapas.

Uma âncora é como uma identificação de um valor e um alias é uma referência a este valor. Por exemplo:

```yaml
# Aqui criamos uma âncora 'nome' para o valor da chave nome_chave
nome_chave: &nome Esta chave tem uma string como valor

# Aqui criamos um alias para a âncora 'nome', reutilizando
# o valor declarado na âncora
outra_chave: *nome

# Aqui a âncora faz referência ao mapa interno
mapa_1: &valores
  nome: nome_da_app
  porta: 1111
  usuário: admin
  senha: admin

# Aqui o alias utilizará o mapa como valor para
# a chave dados_acesso
dados_acesso: *valores
```

Utilizando o exemplo anterior, se você quisesse adicionar um par chave-valor em *dados_acesso* ou sobrescrever valores em chaves já presentes na âncora teria de utilizar a mescla de mapas. A mescla de mapas é indicada por uma chave `<<` no mapa com o valor sendo um alias ou uma sequência de alias. Exemplo:

```yaml
# Aqui definimos os dados da conexão
conexão: &conexao
  host: host_da_app
  porta: 1111

# Aqui definimos as credenciais
credenciais: &credenciais
  usuário: admin
  senha: admin

# Aqui fazemos uso dos mapas anteriores e
# utilizamos mais um par chave-valor
dados_acesso:
  <<: [ *conexao, *credenciais ]
  nome: nome_da_app

# O mapa anterior será idêntico a
dados_acesso:
  host: host_da_app
  porta: 1111
  usuário: admin
  senha: admin
  nome: 

# Fazendo uso apenas da âncora de
# credenciais
dados_acesso:
  host: host_da_app
  porta: 1111
  <<: *credenciais
  nome: nome_da_app
```

## Conclusão

YAML é um formato muito versátil. Este é um dos motivos para ele ser escolhido como formato de diversas ferramentas muito utilizadas, como kubernetes, docker compose, ansible e AWS CloudFormation, para citar algumas. O conhecimento da estrutura do YAML pode ajudar muito em debugar o porquê uma destas ferramentas está dando erro ao aplicar seu manifesto YAML. Este conhecimento também possibilita um melhor uso do formato e abre a possibilidade do uso do YAML por outras ferramentas.