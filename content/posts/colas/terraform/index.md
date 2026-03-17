---
title: Cola de Terraform
date: 2026-03-13
summary: ""

tags:
- terraform
- iac
- devops

categories:
- cola
---
<!--more-->

{{< toc >}}

## Conceitos

- [O que é o Terraform (en)](https://developer.hashicorp.com/terraform/intro#how-does-terraform-work)
- [Como o Terraform funciona (en)](https://developer.hashicorp.com/terraform/intro#how-does-terraform-work)
- [Casos de uso (en)](https://developer.hashicorp.com/terraform/intro/use-cases)
- [Instalação (en)](https://developer.hashicorp.com/terraform/install)
- [Fluxo de trabalho padrão (en)](https://developer.hashicorp.com/terraform/intro/core-workflow)
- [Tutoriais (en)](https://developer.hashicorp.com/terraform/tutorials)

## Linguagem

Resumo:

* Terraform utiliza HCL como linguagem
* HCL é uma linguagem de configuração construída por dois conceitos principais:

```hcl
# Atributos
attribute = "value"

# Bloco
type ["value" ...] {
    (outros atributos ou blocos)
}
```

* Valores de atributos também podem ser expressões

```hcl
# Operações entre valores
soma = 1 + valor

# Interpolação de strings
mensagem = "Olá, ${nome}"

# Utilização de funções
mensagem_gritando = upper(mensagem)
```

### Configuração do terraform

Referência: [terraform (en)](https://developer.hashicorp.com/terraform/language/block/terraform)

```hcl
terraform {
    # Declara a versão requerida do terraform
    required_version = "..."
    # Configuração do backend
    backend "<tipo>" {
        ...
    }
}
```

[Declarando uma versão](https://developer.hashicorp.com/terraform/language/expressions/version-constraints)

* `1.0.2`, ou `= 1.0.2`
* `!= 1.0.2`
* `> 2`
* `>= 2.0.5, < 3.0`
* `~> 1.0.2`
* `~> 2.1`

Referência sobre backend: [backend (en)](https://developer.hashicorp.com/terraform/language/backend)

### Provedor (provider)

Referência: [Provedores (en)](https://developer.hashicorp.com/terraform/language/providers)

```hcl
terraform {
    # Define a versão do provedor
    required_providers {
        <nome> = {
            source  = <endereço>
            version = <versão>
        }
    }
}

# Configurações do provedor
provider "<nome>" {
    ...
}
```

**Configuração múltipla de um provedor**

```hcl
provider "aws" {
    region = "us-east-1"
}

provider "aws" {
    # Define um alias para esta configuração
    alias  = "west"
    region = "us-east-2"
}

resource "aws_instance" "foo" {
    # Declara que este recurso faz uso da configuração
    # com alias "west" do provedor "aws"
    provider = aws.west
    ...
}
```

**Passando um provedor alternativo para um módulo**

Declarando o módulo:

```hcl
terraform {
    required_providers {
        aws = {
            source  = "hashicorp/aws"
            version = "~> 5.0"
            # Declara um provedor com alias para o módulo,
            # já que módulos não possuem blocos provider
            configuration_aliases = [aws.backup]
        }
    }
}

data "aws_ami" "amazon_linux" {
    provider = aws.backup
}
```

Ao utilizar o módulo:

```hcl
provider "aws" {
    alias  = "west"
    region = "us-west-2"
}

module "web-server" {
    source    = "./modules/web-server"
    # Passa o provedor com alias para o módulo
    providers = {
        aws.backup = aws.west
    }
}
```

### Recursos (resources)

Referência: [recursos (en)](https://developer.hashicorp.com/terraform/language/resources)

```hcl
# Deve ser referenciado como "tipo.nome"
resource "<tipo>" "<nome>" {
    ...

    # Permite definir timeouts para operações
    # específicas. Disponível apenas em alguns
    # recursos
    timeouts {
        create = "60m"
        delete = "2h
    }

}
```

### Fontes de dados (data source)

Referência: [fontes de dados (en)](https://developer.hashicorp.com/terraform/language/data-sources)

```hcl
# Deve ser referenciado como "data.tipo.nome"
data "<tipo>" "<nome>" {
    ...
}
```

### Variáveis (variables)

Referência: [variáveis (en)](https://developer.hashicorp.com/terraform/language/values/variables)

```hcl
# Deve ser referenciado como var.nome
variable "<nome>" {
    type        = string|number|bool|list(...)|tuple([...])|set(...)|map(...)|object({...})
    description = "..."

    # Opcionais

    # Declara um valor padrão, caso nenhum valor seja definido
    default   = ...
    # Marca o valor como sensível e previne que seja exibido
    # em saída da CLI
    sensitive = true
    # Declara que o valor pode ser nulo
    nullable  = true
    # Declara como o valor será validado
    validation {
        # Expressão que retorna verdadeiro ou falso
        condition     = ...
        # Mensagem que será exibida, caso o valor não seja válido
        error_message = "..." 
    }
}
```

Definindo valores para variáveis no módulo raiz:

1. Argumentos `-var` e `-var-file` da CLI
1. Arquivos
    1. `*.auto.tfvars` e `*.auto.tfvars.json` em ordem léxica
    1. `terraform.tfvars.json`
    1. `terraform.tfvars`
1. Variáveis de ambiente (`TF_VAR_*`)
1. O argumento `default` no bloco da variável

### Locais (locals)

Referência: [locais (en)](https://developer.hashicorp.com/terraform/language/values/locals)

```hcl
locals {
    # Deve ser referenciado como local.nome
    <nome> = <valor>
    ...
}
```

Podem existir múltiplos blocos `locals`

### Saídas (outputs)

Referência: [saídas (en)](https://developer.hashicorp.com/terraform/language/values/outputs)

```hcl
output "<nome>" {
    description = "..."
    value       = ...
    # Marca o valor como sensível e previne que seja exibido
    # em saída da CLI
    sensitive   = true
}
```

### Módulos (modules)

Referência: [módulos (en)](https://developer.hashicorp.com/terraform/language/modules)

Utilizando um módulo:

```hcl
# Deve ser referenciado como module.nome
module "<nome>" {
    source  = "..."
    version = "..."
    ...
}
```

### Meta-argumentos

Referência: [meta-argumentos (en)](https://developer.hashicorp.com/terraform/language/meta-arguments)

* [`depends_on = [...]`](https://developer.hashicorp.com/terraform/language/meta-arguments/depends_on)
* [`count = ...`](https://developer.hashicorp.com/terraform/language/meta-arguments/count) pode utilizar `count.index`
* [`for_each = ...`](https://developer.hashicorp.com/terraform/language/meta-arguments/for_each) pode utilizar `each.key` e `each.value`
* [`lifecycle {}`](https://developer.hashicorp.com/terraform/language/meta-arguments/lifecycle)
* [`provider = ...`](https://developer.hashicorp.com/terraform/language/meta-arguments/provider) utilizado em recursos e fontes de dados
* [`providers = {}`](https://developer.hashicorp.com/terraform/language/meta-arguments/providers) utilizado em módulos

### Valores efêmeros

Referência: [gerencie dados sensíveis (en)](https://developer.hashicorp.com/terraform/language/manage-sensitive-data)

Disponível a partir do terraform v1.10

* Argumento `ephemeral = true` em variáveis e saídas de módulos 
* Recursos efêmeros (ephemeral)

```hcl
# Deve ser referenciado como ephemeral.nome
ephemeral "<tipo>" "<nome>" {
    ...
}
```

Disponível a partir do terraform v1.11

* Argumento somente para escrita em recursos

### Importando recursos

Referência: [Importando (en)](https://developer.hashicorp.com/terraform/language/import)

Disponível a partir do terraform v1.12

```hcl
# Deve estar em um arquivo com extensão .tfquery.hcl
# Deve ser referenciado como list.tipo.nome
list "<tipo>" "<nome>" {
    provider         = ...
    config           = ...
    include_resource = ...
    limit            = ...
}
```

```hcl
import {
    id = "<id do recurso>"
    to = tipo.nome
}

resource "<tipo>" "<nome>" {
    ...
}
```

### Alterando o estado

Referência: [estado (en)](https://developer.hashicorp.com/terraform/language/state)

Disponível a partir do terraform v1.7

```hcl
# Declara um recurso como removido
removed {
    from = tipo.nome
    lifecycle {
        destroy = false
    }
}

# Declara um recurso como movido
moved {
    from = <endereço antigo>
    to   = <endereço novo>
}
```

### Ações (action)

Referência: [ações (en)](https://developer.hashicorp.com/terraform/language/invoke-actions)

Disponível a partir do terraform v1.14

```hcl
action "<tipo>" "<nome>" {
    config {
        ...
    }
}
```

### Validações

Referência: [valide sua configuração (en)](https://developer.hashicorp.com/terraform/language/validate)

Disponível a partir do terraform v1.5

```hcl
check "<nome>" {
    data "<tipo>" "<nome>" {
        ...
    }

    assert {
        condition = ...
        error_message = "..."
    }
}
```

### Teste da configuração

Referência: [Testes (en)](https://developer.hashicorp.com/terraform/language/tests)

Disponível a partir do terraform v1.6

```hcl
# Deve estar em um arquivo com extensão .tftest.hcl
mock_provider "<nome>" {}

run "<nome>" {
    command = ...

    assert {
        condition = ...
        error_message = "..."
    }
}
```

### Expressões

Referência: [expressões (en)](https://developer.hashicorp.com/terraform/language/expressions)

[Strings e templates](https://developer.hashicorp.com/terraform/language/expressions/strings)

```hcl
# String
"Olá"

# String heredoc
<<EOT
Essa string
pode abranger
várias linhas
EOT
<<-EOT
    Essa string
    pode ser identada
EOT

# Interpolação
"Olá, ${var.nome}"

# Diretivas
"Olá, %{ if var.name != "" }${ var.name }%{ else }unnamed%{ endif }"
<<EOT
%{ for ip in aws_instance.exemplo[*].private_ip }
server ${ip}
%{ endfor }
EOT
```

[Operadores](https://developer.hashicorp.com/terraform/language/expressions/operators)

* Aritméticos: `+`, `-`, `*`, `/` e `%`
* Igualdade: `==` e `!=`
* Comparação: `||`, `&&` e `!`

[Expressão condicional](https://developer.hashicorp.com/terraform/language/expressions/conditionals)

```hcl
<condição> ? <valor se verdadeiro> : <valor se falso>
```

[Expressão for](https://developer.hashicorp.com/terraform/language/expressions/for)

```hcl
# Criando uma tupla
[for <variável> in <coleção> : <resultado>]

# Criando um objeto
{for <variável> in {coleção} : <chave> => <valor>}

# Filtrando elementos
[for <variável> in <coleção> : <resultado> if <condição>]
{for <variável> in {coleção} : <chave> => <valor> if <condição>}

# Agrupando na mesma chave
{for <variável> in {coleção} : <chave> => <valor>...}
```

[Expressões splat](https://developer.hashicorp.com/terraform/language/expressions/splat)

```hcl
# Equivalente a [for o in <coleção> : o.<atributo>]
<coleção>[*].<atributo>
```

[Blocos dinâmicos](https://developer.hashicorp.com/terraform/language/expressions/dynamic-blocks)

```hcl
resource "<tipo>" "<valor>" {
    dynamic "<bloco>" {
        for_each = <coleção>
        content {
            ...
        }
    }
}
```

### Funções

Referência: [funções (en)](https://developer.hashicorp.com/terraform/language/functions)

```hcl
# Executando funções internas
<nome>(<argumentos>...)

# Funções de provedores
# Disponível a partir do terraform v1.8
provider::<nome provedor>::<nome função>(<argumentos>...)
```

#### Numéricas

| Função | Versão do Terraform | Exemplo |
|---|---|---|
| [`abs(n)`](https://developer.hashicorp.com/terraform/language/functions/abs) | v0.10.7 | `abs(-5) # 5` |
| [`ceil(n)`](https://developer.hashicorp.com/terraform/language/functions/ceil) | v0.10 | `ceil(4.2) # 5` |
| [`floor(n)`](https://developer.hashicorp.com/terraform/language/functions/floor) | v0.10 | `floor(4.8) # 4` |
| [`log(n, base)`](https://developer.hashicorp.com/terraform/language/functions/log) | v0.10 | `log(16, 2) # 4` |
| [`max(n...)`](https://developer.hashicorp.com/terraform/language/functions/max) | v0.10 | `max(5, 12, 9) # 12` |
| [`min(n...)`](https://developer.hashicorp.com/terraform/language/functions/min) | v0.10 | `min(5, 12, 9) # 5` |
| [`parseint(s, base)`](https://developer.hashicorp.com/terraform/language/functions/parseint) | v0.12.10 | `parseint("FF", 16) # 255` |
| [`pow(n, exp)`](https://developer.hashicorp.com/terraform/language/functions/pow) | v0.10 | `pow(2, 3) # 8` |
| [`signum(n)`](https://developer.hashicorp.com/terraform/language/functions/signum) | v0.6.12 | `signum(-42) # -1` |

#### Strings

| Função | Versão do Terraform | Exemplo |
|---|---|---|
| [`chomp(s)`](https://developer.hashicorp.com/terraform/language/functions/chomp) | v0.9.3 | `chomp("hello\n") # "hello"` |
| [`endswith(s, suffix)`](https://developer.hashicorp.com/terraform/language/functions/endswith) | v1.3.0 | `endswith("arquivo.txt", ".txt") # true` |
| [`format(s, values...)`](https://developer.hashicorp.com/terraform/language/functions/format) | v0.4 | `format("id-%03d", 7) # "id-007"` |
| [`formatlist(s, values...)`](https://developer.hashicorp.com/terraform/language/functions/formatlist) | v0.5.1 | `formatlist("srv-%02d", [1,2,3]) # ["srv-01","srv-02","srv-03"]` |
| [`indent(n, s)`](https://developer.hashicorp.com/terraform/language/functions/indent) | v0.10.5 | `indent(2, "a\nb") # "  a\n  b"` |
| [`join(sep, list)`](https://developer.hashicorp.com/terraform/language/functions/join) | v0.3 | `join(",", ["a","b","c"]) # "a,b,c"` |
| [`lower(s)`](https://developer.hashicorp.com/terraform/language/functions/lower) | v0.6.5 | `lower("HELLO") # "hello"` |
| [`regex(pattern, s)`](https://developer.hashicorp.com/terraform/language/functions/regex) | v0.12.7 | `regex("[0-9]+", "id=42") # "42"` |
| [`regexall(pattern, s)`](https://developer.hashicorp.com/terraform/language/functions/regexall) | v0.12.7 | `regexall("[0-9]+", "a1b22") # ["1","22"]` |
| [`replace(s, substr, repl)`](https://developer.hashicorp.com/terraform/language/functions/replace) | v0.4 | `replace("hello world","world","terraform") # "hello terraform"` |
| [`split(sep, s)`](https://developer.hashicorp.com/terraform/language/functions/split) | v0.4 | `split(",", "a,b,c") # ["a","b","c"]` |
| [`startswith(s, prefix)`](https://developer.hashicorp.com/terraform/language/functions/startswith) | v1.3.0 | `startswith("terraform", "terra") # true` |
| [`strcontains(s, substr)`](https://developer.hashicorp.com/terraform/language/functions/strcontains) | v1.5.0 | `strcontains("terraform", "form") # true` |
| [`strrev(s)`](https://developer.hashicorp.com/terraform/language/functions/strrev) | v0.12 | `strrev("abc") # "cba"` |
| [`substr(s, offset, length)`](https://developer.hashicorp.com/terraform/language/functions/substr) | v0.9.2 | `substr("terraform",0,4) # "terr"` |
| [`templatestring(template, vars)`](https://developer.hashicorp.com/terraform/language/functions/templatestring) | v1.9.0 | `templatestring("hello ${name}", {name="ana"}) # "hello ana"` |
| [`title(s)`](https://developer.hashicorp.com/terraform/language/functions/title) | v0.7.8 | `title("hello world") # "Hello World"` |
| [`trim(s, cutset)`](https://developer.hashicorp.com/terraform/language/functions/trim) | v0.12.17 | `trim("--hello--","-") # "hello"` |
| [`trimprefix(s, prefix)`](https://developer.hashicorp.com/terraform/language/functions/trimprefix) | v0.12.17 | `trimprefix("dev-app","dev-") # "app"` |
| [`trimsuffix(s, suffix)`](https://developer.hashicorp.com/terraform/language/functions/trimsuffix) | v0.12.17 | `trimsuffix("file.txt",".txt") # "file"` |
| [`trimspace(s)`](https://developer.hashicorp.com/terraform/language/functions/trimspace) | v0.6.11 | `trimspace("  hello  ") # "hello"` |
| [`upper(s)`](https://developer.hashicorp.com/terraform/language/functions/upper) | v0.6.5 | `upper("hello") # "HELLO"` |

#### Coleções

| Função | Versão do Terraform | Exemplo |
|---|---|---|
| [`alltrue(list)`](https://developer.hashicorp.com/terraform/language/functions/alltrue) | v0.14 | `alltrue([true, true, false]) # false` |
| [`anytrue(list)`](https://developer.hashicorp.com/terraform/language/functions/anytrue) | v0.15 | `anytrue([false, false, true]) # true` |
| [`chunklist(list, size)`](https://developer.hashicorp.com/terraform/language/functions/chunklist) | v0.10.8 | `chunklist(["a","b","c","d"], 2) # [["a","b"],["c","d"]]` |
| [`coalesce(a...)`](https://developer.hashicorp.com/terraform/language/functions/coalesce) | v0.6.7 | `coalesce("", "b") # "b"` |
| [`coalescelist(lists...)`](https://developer.hashicorp.com/terraform/language/functions/coalescelist) | v0.9.4 | `coalescelist([], ["a","b"]) # ["a","b"]` |
| [`compact(list)`](https://developer.hashicorp.com/terraform/language/functions/compact) | v0.6.4 | `compact(["a","", "b"]) # ["a","b"]` |
| [`concat(lists...)`](https://developer.hashicorp.com/terraform/language/functions/concat) | v0.12 | `concat(["a"], ["b","c"]) # ["a","b","c"]` |
| [`contains(collection, value)`](https://developer.hashicorp.com/terraform/language/functions/contains) | v0.12 | `contains(["a","b","c"], "b") # true` |
| [`distinct(list)`](https://developer.hashicorp.com/terraform/language/functions/distinct) | v0.7 | `distinct(["a","b","a"]) # ["a","b"]` |
| [`element(list, index)`](https://developer.hashicorp.com/terraform/language/functions/element) | v0.3.5 | `element(["a","b","c"], 1) # "b"` |
| [`flatten(list)`](https://developer.hashicorp.com/terraform/language/functions/flatten) | v0.10.3 | `flatten([["a","b"],["c"]]) # ["a","b","c"]` |
| [`index(list, value)`](https://developer.hashicorp.com/terraform/language/functions/index) | v0.6.4 | `index(["a","b","c"], "b") # 1` |
| [`keys(map)`](https://developer.hashicorp.com/terraform/language/functions/keys) | v0.12 | `keys({a=1,b=2}) # ["a","b"]` |
| [`length(collection)`](https://developer.hashicorp.com/terraform/language/functions/length) | v0.5 | `length(["a","b","c"]) # 3` |
| [`lookup(map, key, default)`](https://developer.hashicorp.com/terraform/language/functions/lookup) | v0.1 | `lookup({a=1}, "b", 0) # 0` |
| [`matchkeys(values, keys, searchset)`](https://developer.hashicorp.com/terraform/language/functions/matchkeys) | v0.9.4 | `matchkeys(["v1","v2"], ["k1","k2"], ["k2"]) # ["v2"]` |
| [`merge(maps...)`](https://developer.hashicorp.com/terraform/language/functions/merge) | v0.7 | `merge({a=1}, {b=2}) # {a=1,b=2}` |
| [`one(list)`](https://developer.hashicorp.com/terraform/language/functions/one) | v0.15 | `one(["only"]) # "only"` |
| [`range(max)`](https://developer.hashicorp.com/terraform/language/functions/range) | v0.12.2 | `range(3) # [0,1,2]` |
| [`reverse(list)`](https://developer.hashicorp.com/terraform/language/functions/reverse) | v0.12 | `reverse(["a","b","c"]) # ["c","b","a"]` |
| [`setintersection(sets...)`](https://developer.hashicorp.com/terraform/language/functions/setintersection) | v0.12 | `setintersection(["a","b"], ["b","c"]) # ["b"]` |
| [`setproduct(sets...)`](https://developer.hashicorp.com/terraform/language/functions/setproduct) | v0.12 | `setproduct(["a","b"], [1,2]) # [["a",1],["a",2],["b",1],["b",2]]` |
| [`setsubtract(set1, set2)`](https://developer.hashicorp.com/terraform/language/functions/setsubtract) | v0.13 | `setsubtract(["a","b","c"], ["b"]) # ["a","c"]` |
| [`setunion(sets...)`](https://developer.hashicorp.com/terraform/language/functions/setunion) | v0.12 | `setunion(["a","b"], ["b","c"]) # ["a","b","c"]` |
| [`slice(list, start, end)`](https://developer.hashicorp.com/terraform/language/functions/slice) | v0.9 | `slice(["a","b","c","d"], 1, 3) # ["b","c"]` |
| [`sort(list)`](https://developer.hashicorp.com/terraform/language/functions/sort) | v0.7 | `sort(["c","a","b"]) # ["a","b","c"]` |
| [`sum(list)`](https://developer.hashicorp.com/terraform/language/functions/sum) | v0.13 | `sum([1,2,3]) # 6` |
| [`transpose(map(list(string)))`](https://developer.hashicorp.com/terraform/language/functions/transpose) | v0.12 | `transpose({a=["x","y"], b=["x","z"]}) # {"x"=["a","b"],"y"=["a"],"z"=["b"]}` |
| [`values(map)`](https://developer.hashicorp.com/terraform/language/functions/values) | v0.12 | `values({a=1,b=2}) # [1,2]` |
| [`zipmap(keys, values)`](https://developer.hashicorp.com/terraform/language/functions/zipmap) | v0.7.8 | `zipmap(["a","b"], [1,2]) # {a=1,b=2}` |

#### Codificação

| Função | Versão do Terraform | Exemplo |
|---|---|---|
| [`base64decode(s)`](https://developer.hashicorp.com/terraform/language/functions/base64decode) | v0.6.4 | `base64decode("SGVsbG8=") # "Hello"` |
| [`base64encode(s)`](https://developer.hashicorp.com/terraform/language/functions/base64encode) | v0.6.4 | `base64encode("Hello") # "SGVsbG8="` |
| [`base64gzip(s)`](https://developer.hashicorp.com/terraform/language/functions/base64gzip) | v0.10.3 | `base64gzip("hello") # "H4sIAAAAA..."` |
| [`csvdecode(s)`](https://developer.hashicorp.com/terraform/language/functions/csvdecode) | v0.12 | `csvdecode("name,age\nana,30") # [{name="ana",age="30"}]` |
| [`jsondecode(s)`](https://developer.hashicorp.com/terraform/language/functions/jsondecode) | v0.12 | `jsondecode("{\"a\":1}") # {a=1}` |
| [`jsonencode(v)`](https://developer.hashicorp.com/terraform/language/functions/jsonencode) | v0.12 | `jsonencode({a=1}) # "{\"a\":1}"` |
| [`textdecodebase64(s, encoding)`](https://developer.hashicorp.com/terraform/language/functions/textdecodebase64) | v0.15 | `textdecodebase64("SGVsbG8=", "UTF-8") # "Hello"` |
| [`textencodebase64(s, encoding)`](https://developer.hashicorp.com/terraform/language/functions/textencodebase64) | v0.15 | `textencodebase64("Hello", "UTF-8") # "SGVsbG8="` |
| [`urlencode(s)`](https://developer.hashicorp.com/terraform/language/functions/urlencode) | v0.10.3 | `urlencode("a b") # "a%20b"` |
| [`yamldecode(s)`](https://developer.hashicorp.com/terraform/language/functions/yamldecode) | v0.12.2 | `yamldecode("a: 1") # {a=1}` |
| [`yamlencode(v)`](https://developer.hashicorp.com/terraform/language/functions/yamlencode) | v0.12.2 | `yamlencode({a=1}) # "a: 1\n"` |

#### Sistemas de Arquivo

| Função | Versão do Terraform | Exemplo |
|---|---|---|
| [`abspath(path)`](https://developer.hashicorp.com/terraform/language/functions/abspath) | v0.12.4 | `abspath("modules/vpc") # "/home/user/project/modules/vpc"` |
| [`dirname(path)`](https://developer.hashicorp.com/terraform/language/functions/dirname) | v0.9.3 | `dirname("/tmp/file.txt") # "/tmp"` |
| [`pathexpand(path)`](https://developer.hashicorp.com/terraform/language/functions/pathexpand) | v0.8.5 | `pathexpand("~/.config") # /home/user/.config` |
| [`basename(path)`](https://developer.hashicorp.com/terraform/language/functions/basename) | v0.9.3 | `basename("/tmp/file.txt") # "file.txt"` |
| [`file(path)`](https://developer.hashicorp.com/terraform/language/functions/file) | v0.1 | `file("${path.module}/config.txt") # conteúdo do arquivo` |
| [`fileexists(path)`](https://developer.hashicorp.com/terraform/language/functions/fileexists) | v0.12 | `fileexists("config.yaml") # true` |
| [`fileset(path, pattern)`](https://developer.hashicorp.com/terraform/language/functions/fileset) | v0.12.8 | `fileset("configs", "*.yaml") # ["a.yaml","b.yaml"]` |
| [`filebase64(path)`](https://developer.hashicorp.com/terraform/language/functions/filebase64) | v0.12 | `filebase64("script.sh") # "IyEvYmluL2Jhc2gK..."` |
| [`templatefile(path, vars)`](https://developer.hashicorp.com/terraform/language/functions/templatefile) | v0.12 | `templatefile("config.tftpl", {name="app"})` |

#### Data e Hora

| Função | Versão do Terraform | Exemplo |
|---|---|---|
| [`formatdate(layout, timestamp)`](https://developer.hashicorp.com/terraform/language/functions/formatdate) | v0.12 | `formatdate("YYYY-MM-DD", "2024-01-02T15:04:05Z") # "2024-01-02"` |
| [`plantimestamp()`](https://developer.hashicorp.com/terraform/language/functions/plantimestamp) | v1.5 | `plantimestamp() # "2026-03-16T12:00:00Z"` |
| [`timeadd(timestamp, duration)`](https://developer.hashicorp.com/terraform/language/functions/timeadd) | v0.11.2 | `timeadd("2024-01-01T00:00:00Z", "24h") # "2024-01-02T00:00:00Z"` |
| [`timecmp(timestamp_a, timestamp_b)`](https://developer.hashicorp.com/terraform/language/functions/timecmp) | v1.3 | `timecmp("2024-01-01T00:00:00Z","2024-01-02T00:00:00Z") # -1` |
| [`timestamp()`](https://developer.hashicorp.com/terraform/language/functions/timestamp) | v0.8 | `timestamp() # "2026-03-16T12:34:56Z"` |

#### Hash e Criptografia

| Função | Versão do Terraform | Exemplo |
|---|---|---|
| [`base64sha256(s)`](https://developer.hashicorp.com/terraform/language/functions/base64sha256) | v0.6.11 | `base64sha256("hello") # "LPJNul+wow4m..."` |
| [`base64sha512(s)`](https://developer.hashicorp.com/terraform/language/functions/base64sha512) | v0.9.5 | `base64sha512("hello") # "m3HSJL1i..."` |
| [`bcrypt(s, cost)`](https://developer.hashicorp.com/terraform/language/functions/bcrypt) | v0.9.7 | `bcrypt("secret", 10) # "$2a$10$..."` |
| [`filebase64sha256(path)`](https://developer.hashicorp.com/terraform/language/functions/filebase64sha256) | v0.12 | `filebase64sha256("script.sh") # "47DEQpj8HBSa..."` |
| [`filebase64sha512(path)`](https://developer.hashicorp.com/terraform/language/functions/filebase64sha512) | v0.12 | `filebase64sha512("script.sh") # "z4PhNX7vuL3x..."` |
| [`filesmd5(path)`](https://developer.hashicorp.com/terraform/language/functions/filesmd5) | v0.12 | `filesmd5("script.sh") # "2fd4e1c67a2d..."` |
| [`filesha1(path)`](https://developer.hashicorp.com/terraform/language/functions/filesha1) | v0.12 | `filesha1("script.sh") # "3aa8bfae18f2..."` |
| [`filesha256(path)`](https://developer.hashicorp.com/terraform/language/functions/filesha256) | v0.12 | `filesha256("script.sh") # "9f86d081884c..."` |
| [`filesha512(path)`](https://developer.hashicorp.com/terraform/language/functions/filesha512) | v0.12 | `filesha512("script.sh") # "cf83e1357eef..."` |
| [`md5(s)`](https://developer.hashicorp.com/terraform/language/functions/md5) | v0.6.12 | `md5("hello") # "5d41402abc4b2a76b9719d911017c592"` |
| [`rsadecrypt(cipher, privatekey)`](https://developer.hashicorp.com/terraform/language/functions/rsadecrypt) | v0.11.2 | `rsadecrypt(file("./ciphertext), file("privatekey.pem)) # "Olá, mundo!"` |
| [`sha1(s)`](https://developer.hashicorp.com/terraform/language/functions/sha1) | v0.6.9 | `sha1("hello") # "aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d"` |
| [`sha256(s)`](https://developer.hashicorp.com/terraform/language/functions/sha256) | v0.6.10 | `sha256("hello") # "2cf24dba5fb0a..."` |
| [`sha512(s)`](https://developer.hashicorp.com/terraform/language/functions/sha512) | v0.12 | `sha512("hello") # "9b71d224bd62..."` |
| [`uuid()`](https://developer.hashicorp.com/terraform/language/functions/uuid) | v0.6.13 | `uuid() # "3b12f1df-5232-4e0f-9b0e-3c3c9f0f0e91"` |
| [`uuidv5(namespace, name)`](https://developer.hashicorp.com/terraform/language/functions/uuidv5) | v0.12.2 | `uuidv5("dns","example.com") # "cfbff0d1-9375-5685-968c-48ce8b15ae17"` |

#### Redes IP

| Função | Versão do Terraform | Exemplo |
|---|---|---|
| [`cidrhost(prefix, hostnum)`](https://developer.hashicorp.com/terraform/language/functions/cidrhost) | v0.6.6 | `cidrhost("10.0.0.0/24", 5) # "10.0.0.5"` |
| [`cidrnetmask(prefix)`](https://developer.hashicorp.com/terraform/language/functions/cidrnetmask) | v0.6.6 | `cidrnetmask("10.0.0.0/24") # "255.255.255.0"` |
| [`cidrsubnet(prefix, newbits, netnum)`](https://developer.hashicorp.com/terraform/language/functions/cidrsubnet) | v0.6.6 | `cidrsubnet("10.0.0.0/16", 8, 2) # "10.0.2.0/24"` |
| [`cidrsubnets(prefix, newbits...)`](https://developer.hashicorp.com/terraform/language/functions/cidrsubnets) | v0.12.10 | `cidrsubnets("10.0.0.0/16", 8, 8) # ["10.0.0.0/24","10.0.1.0/24"]` |

#### Conversão de tipos

| Função | Versão do Terraform | Exemplo |
|---|---|---|
| [`tobool(v)`](https://developer.hashicorp.com/terraform/language/functions/tobool) | v0.12 | `tobool("true") # true` |
| [`tonumber(v)`](https://developer.hashicorp.com/terraform/language/functions/tonumber) | v0.12 | `tonumber("42") # 42` |
| [`tostring(v)`](https://developer.hashicorp.com/terraform/language/functions/tostring) | v0.12 | `tostring(42) # "42"` |
| [`tolist(v)`](https://developer.hashicorp.com/terraform/language/functions/tolist) | v0.12 | `tolist(["a","b"]) # ["a","b"]` |
| [`toset(v)`](https://developer.hashicorp.com/terraform/language/functions/toset) | v0.12 | `toset(["a","b","a"]) # ["a","b"]` |
| [`tomap(v)`](https://developer.hashicorp.com/terraform/language/functions/tomap) | v0.12 | `tomap({a=1, b=2}) # {a=1, b=2}` |

#### Utilidades

| Função | Versão do Terraform | Exemplo |
|---|---|---|
| [`can(expr)`](https://developer.hashicorp.com/terraform/language/functions/can) | v0.12.20 | `can(regex("[0-9]+", "abc")) # false` |
| [`try(exprs...)`](https://developer.hashicorp.com/terraform/language/functions/try) | v0.12.20 | `try(var.obj.name, "default") # "default"` |
| [`ephemeralasnull(v)`](https://developer.hashicorp.com/terraform/language/functions/ephemeralasnull) | v1.10 | `ephemeralasnull(var.tmp) # null (se valor efêmero)` |
| [`issensitive(v)`](https://developer.hashicorp.com/terraform/language/functions/issensitive) | v1.8 | `issensitive(var.secret) # true` |
| [`nonsensitive(v)`](https://developer.hashicorp.com/terraform/language/functions/nonsensitive) | v0.15 | `nonsensitive(var.secret)` |
| [`sensitive(v)`](https://developer.hashicorp.com/terraform/language/functions/sensitive) | v0.15 | `sensitive("secret")` |
| [`type(v)`](https://developer.hashicorp.com/terraform/language/functions/type) | v1.0 | `type({a=1}) # object({a=number})` |

### Links úteis

- [Documentação (en)](https://developer.hashicorp.com/terraform/language)
- [Repositório](https://github.com/hashicorp/hcl)
- [Aprenda HCL em y minutos (en)](https://learnxinyminutes.com/hcl/)

## CLI

### Subcomandos principais

```shell
# Prepara o diretório atual
{{< codelink text="terraform init" href="#init">}}      [opções]
# Checa se a configuração é válida
{{< codelink text="terraform validate" href="#validate">}}  [opções]
# Reformata a configuração no estilo padrão
{{< codelink text="terraform fmt" href="#fmt">}}       [opções] [alvo...]
# Mostra as mudanças requeridas pela configuração atual
{{< codelink text="terraform plan" href="#plan">}}      [opções]
# Cria ou atualiza a infraestrutura
{{< codelink text="terraform apply" href="#apply">}}     [opções] [arquivo]
# Destroi infraestrutura criada anteriormente
{{< codelink text="terraform destroy" href="#destroy">}}   [opções]
```

#### Init

Referência: [init (en)](https://developer.hashicorp.com/terraform/cli/commands/init)

Uso: `terraform init [opções]`

* `-upgrade` atualiza módulos e plugins
* `-backend-config=(<atributo>=<valor>|<arquivo>)` completa a configuração do backend
* `-migrate-state` tenta migrar o estado para o novo backend

#### Validate

Referência: [validate (en)](https://developer.hashicorp.com/terraform/cli/commands/validate)

Uso: `terraform validate [opções]`

#### Fmt

Referência: [fmt (en)](https://developer.hashicorp.com/terraform/cli/commands/fmt)

Uso: `terraform fmt [opções] [alvo...]`

* `-check` apenas checa se a configuração está formatada
* `-diff` mostra o diff das modificações
* `-list=false` não mostra a lista de arquivos inconsistentes

#### Plan

Referência: [plan (en)](https://developer.hashicorp.com/terraform/cli/commands/plan)

Uso: `terraform plan [opções]`

* `-var <nome>=<valor>` define o valor de uma variável
* `-var-file=<arquivo>` define o valor de variáveis através de um arquivo `.tfvars`
* `-out=<arquivo>` salva o plano gerado em um arquivo
* `-target=<endereço>` foca apenas no recurso em em recursos que ele depende
* `-destroy` cria um plano pra remover os recursos
* `-invoke=action.<tipo>.<nome>`cria um plano para uma ação

#### Apply

Referência: [apply (en)](https://developer.hashicorp.com/terraform/cli/commands/apply)

Uso: `terraform apply [opções] [arquivo]`

* `-auto-approve` pula a aprovação

#### Destroy

Referência: [destroy (en)](https://developer.hashicorp.com/terraform/cli/commands/destroy)

Uso: `terraform destroy [opções]`

### Outros subcomandos

```shell
# Permite testar expressões em um prompt interativo
{{< codelink text="terraform console" href="#console" >}}   [opções]
# Exibe o estado atual ou um plano
{{< codelink text="terraform show" href="#show" >}}      [opções] [arquivo]
# Mostra informações sobre os provedores requeridos
{{< codelink text="terraform providers" href="#providers" >}}
# Mostra informações sobre os módulos
# Disponível a partir do terraform v1.10
{{< codelink text="terraform modules" href="#modules" >}}
# Permite alterar o estado
{{< codelink text="terraform state" href="#state" >}}     <subcomando> [opções] [argumentos]
# Permite utilizar workspaces
{{< codelink text="terraform workspace" href="#workspace" >}} <subcomando> [opções] [argumentos]
```

#### Console

Referência: [console (en)](https://developer.hashicorp.com/terraform/cli/commands/console)

Uso: `terraform console [opções]`

* `-var <nome>=<valor>` define o valor de uma variável
* `-var-file=<arquivo>` define o valor de variáveis através de um arquivo `.tfvars`
* `-plan` cria um plano e permite avaliar expressões como se o plano já estivesse aplicado

#### Show

Referência: [show (en)](https://developer.hashicorp.com/terraform/cli/commands/show)

Uso: `terraform show [opções] [arquivo]`

#### Providers

Referência: [providers (en)](https://developer.hashicorp.com/terraform/cli/commands/providers)

Uso: `terraform providers`

#### Modules

Referência: [modules (en)](https://developer.hashicorp.com/terraform/cli/commands/modules)

Uso: `terraform modules`

#### State

Referência: [state (en)](https://developer.hashicorp.com/terraform/cli/commands/state)

Uso: `terraform state <subcomando> [opções] [argumentos]`

* [`list [opções] [endereços...]`](https://developer.hashicorp.com/terraform/cli/commands/state/list) lista os recursos
    * `-id=<id>` exibe o endereço do recurso com id `id`
* [`mv [opções] <origem> <destino>`](https://developer.hashicorp.com/terraform/cli/commands/state/mv) move o endereço de um recurso
    * `-dry_run` simula a operação
* [`pull`](https://developer.hashicorp.com/terraform/cli/commands/state/pull) baixa o e exibe o estado
* [`rm [opções] <endereços>...`](https://developer.hashicorp.com/terraform/cli/commands/state/rm) remove os recursos do estado
    * `-dry_run` simula a operação
* [`show [opções] <endereço>`](https://developer.hashicorp.com/terraform/cli/commands/state/show) exibe os atributos de um recurso

#### Workspace

Referência: [workspace (en)](https://developer.hashicorp.com/terraform/cli/commands/workspace)

Uso: `terraform workspace <subcomando> [opções] [argumentos]`

* [`list`](https://developer.hashicorp.com/terraform/cli/commands/workspace/list) lista todos os workspaces
* [`select [opções] <nome>`](https://developer.hashicorp.com/terraform/cli/commands/workspace/select) seleciona um workspace
    * `-or-create` cria o workspace, caso não exista
* [`new [opções] <nome>`](https://developer.hashicorp.com/terraform/cli/commands/workspace/new) cria um novo workspace
* [`delete [opções] <nome>`](https://developer.hashicorp.com/terraform/cli/commands/workspace/delete) apaga um workspace
    * `-force` apaga o workspace mesmo que o estado não esteja vazio
* [`show`](https://developer.hashicorp.com/terraform/cli/commands/workspace/show) mostra o workspace atual

## HCP Terraform

Referência: [O que é HCP Terraform? (en)](https://developer.hashicorp.com/terraform/cloud-docs)

```hcl
terraform {
    cloud {
        organization = "..."

        workspaces {
            project = "..."
            name    = "..."
        }
    }
}
```

### Linguagem

Referência: [stacks (en)](https://developer.hashicorp.com/terraform/language/stacks)

```hcl
# Deve estar em um arquivo com extensão .tfcomponent.hcl
required_providers {
    <nome_local> = {
        source  = "..."
        version = "..."
    }
}

provider "<nome_local>" "<nome>" {
    config {
        ...
    }
}

variable "<nome>" {
    ...
}

locals {
    ...
}

removed {
    source = "..."
    from   = ...

    providers = {
        ...
    }
}

component "<nome>" {
    source = "..."
    inputs = {
        ...
    }
    providers = {
        <nome> = provider.nome_local.nome
    }
}

output "<nome>" {
    ...
}
```

```hcl
# Deve estar em um arquivo com extensão .tfdeploy.hcl
# Deve ser referenciado como store.varset.nome
store "varset" "<nome>" {
    id       = "..."
    name     = "..."
    category = "..."
}

# Deve ser referenciado como identity_token.tipo.jwt
identity_token "<tipo>" {
    audience = [...]
}

# Deve ser referenciado como deployment_auto_approve.nome
deployment_auto_approve "<nome>" {
    check {
        condition = ...
        reason    = "..."
    }
}

# Deve ser referenciado como deployment_group.nome
deployment_group "<nome>" {
    auto_approve_checks = [
        deployment_auto_approve.nome
    ]
}

# Deve ser referenciado como upstream_input.nome
upstream_input "<nome>" {
    type   = "..."
    source = "..."
}

locals {
    ...
}

deployment "<nome>" {
    inputs = {
        ...
    }

    deployment_group = deployment_group.nome
}

publish_output "<nome>" {
    description = "..."
    value       = ...
}
```

### CLI

Referência: [terraform stacks (en)](https://developer.hashicorp.com/terraform/cli/commands/stacks)

```shell
# Inicializa a stack
{{< codelink text="terraform stacks init" href="#stack-init" >}}               [opções]
# Cria uma stack
{{< codelink text="terraform stacks create" href="#stack-create" >}}             -organization-name ... -project-name ... -stack-name ... [opções]
# Lista as stacks
{{< codelink text="terraform stacks list" href="#stack-list" >}}               -organization-name ... [opções]
# Valida a stack
{{< codelink text="terraform stacks validate" href="#stack-validate" >}}           [opções]
# Formata a stack
{{< codelink text="terraform stacks fmt" href="#stack-fmt" >}}                [opções] [arquivo...]
# Exibe as versões dos provedores da stack
{{< codelink text="terraform stacks provider-lock" href="#stack-provider-lock" >}}      [opções]
# Gerencia os deployments
{{< codelink text="terraform stacks deployment-run" href="#stack-deployment-run" >}}     <subcomando>
# Gerencia os grupos de deployment
{{< codelink text="terraform stacks deployment-group" href="#stack-deployment-group" >}}   <subcomando> [opções]
# Gerencia configurações
{{< codelink text="terraform stacks configuration" href="#stack-configuration" >}}      <subcomando> [opções]
```

#### Stack init

Referência: [stacks init (en)](https://developer.hashicorp.com/terraform/cli/commands/stacks/init)

Uso: `terraform stacks init [opções]`

* `-upgrade` Atualiza módulos e plugins

#### Stack create

Referência: [stacks create (en)](https://developer.hashicorp.com/terraform/cli/commands/stacks/create)

Uso: `terraform stacks create -organization-name ... -project-name ... -stack-name ... [opções]`

* `-with-template` Gera configuração inicial

#### Stack list

Referência: [stacks list (en)](https://developer.hashicorp.com/terraform/cli/commands/stacks/list)

Uso: `terraform stacks list -organization-name ... [opções]`

#### Stack validate

Referência: [stacks validate (en)](https://developer.hashicorp.com/terraform/cli/commands/stacks/validate)

Uso: `terraform stacks validate [opções]`

#### Stack fmt

Referência: [stacks fmt (en)](https://developer.hashicorp.com/terraform/cli/commands/stacks/fmt)

Uso: `terraform stacks fmt [opções] [arquivo...]`

#### Stack provider-lock

Referência: [stacks provider-lock (en)](https://developer.hashicorp.com/terraform/cli/commands/stacks/providers-lock)

Uso: `terraform stacks provider-lock [opções]`

#### Stack deployment-run

Referência: [stacks deployment-run (en)]()

Uso: `terraform stacks deployment-run <subcomando>`

* [`list [opções]`](https://developer.hashicorp.com/terraform/cli/commands/stacks/deployment-run/list) lista deployments em um grupo de deployment
* [`approve-all-plans -deployment-run-id ...`](https://developer.hashicorp.com/terraform/cli/commands/stacks/deployment-run/approve-all-plans) aprova todos os planos para um deployment
* [`cancel -deployment-run-id ...`](https://developer.hashicorp.com/terraform/cli/commands/stacks/deployment-run/cancel) cancela um deployment
* [`watch -deployment-run-id ...`](https://developer.hashicorp.com/terraform/cli/commands/stacks/deployment-run/watch) acompanha o progresso de um deployment

#### Stack deployment-group

Referência: [stacks deployment-group (en)](https://developer.hashicorp.com/terraform/cli/commands/stacks/deployment-group)

Uso: `terraform stacks deployment-group <subcomando>`

* [`list [opções]`](https://developer.hashicorp.com/terraform/cli/commands/stacks/deployment-group/list) lista grupos de deployment em uma configuração
* [`approve-all-plans <identificação>`](https://developer.hashicorp.com/terraform/cli/commands/stacks/deployment-group/approve-all-plans) aprova todos os planos para um grupo de deployment
    * `-deployment-group-id ...`
    * `-organization-name ... -project-name ... -stack-name ... -deployment-group-name ...`
* [`rerun -deployment-names ... <identificação>`](https://developer.hashicorp.com/terraform/cli/commands/stacks/deployment-group/rerun) reexecuta deployments dentro de um grupo de deployment
    * `-deployment-group-id ...`
    * `-organization-name ... -project-name ... -stack-name ... -deployment-group-name ...`
* [`watch <identificação>`](https://developer.hashicorp.com/terraform/cli/commands/stacks/deployment-group/watch) acompanha o progresso de um deployment
    * `-deployment-group-id ...`
    * `-organization-name ... -project-name ... -stack-name ... -deployment-group-name ...`

#### Stack configuration

Referência: [stacks configuration (en)](https://developer.hashicorp.com/terraform/cli/commands/stacks/configuration)

Uso: `terraform stacks configuration <subcomando>`

* [`list <identificação>`](https://developer.hashicorp.com/terraform/cli/commands/stacks/configuration/list) lista as versões de configurações de uma stack
    * `-stack-id ...`
    * `-organization-name ... -project-name ... -stack-name ...`
* [`upload <identificação>`](https://developer.hashicorp.com/terraform/cli/commands/stacks/configuration/upload) faz o upload das configurações de componente e deployment
    * `-stack-id ...`
    * `-organization-name ... -project-name ... -stack-name ...`
* [`fetch <identificação>`](https://developer.hashicorp.com/terraform/cli/commands/stacks/configuration/fetch) faz o download da última configuração da stack
    * `-stack-id ...`
    * `-organization-name ... -project-name ... -stack-name ...`
* [`watch <identificação>`](https://developer.hashicorp.com/terraform/cli/commands/stacks/configuration/watch) acompanha o progresso do deployment da stack
    * `-stack-id ...`
    * `-organization-name ... -project-name ... -stack-name ...`

## OpenTofu

Incompatibilidades:

* Meta-argumento `enabled`