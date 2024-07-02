---
title: Escrevendo aplicações para o kubernetes com o carvel
date: 2024-06-19

tags:
- kubernetes
- cncf

categories:
- guias
---

[Carvel](https://carvel.dev/) é um projeto que fornece um conjunto de ferramentas para construir, configurar e implantar aplicações em kubernetes. Essas ferramentas são de propósito específico e podem ser compostas entre si (filosofia posix).

O projeto carvel foi aceito na [cncf](https://www.cncf.io/projects/carvel/) no dia 14 de Setembro de 2022 no nível sandbox.

No período da escrita deste texto, o projeto carvel é composto por sete ferramentas e por mais duas em fase experimental. São elas:

* ytt: Faz templating e overlay em estruturas YAML
* kbld: Constroi imagens a partir de referências nos manifestos kubernetes e os atualiza com referências imutáveis
* kapp: Gerencia a implantação de recursos kubernetes como uma só "aplicação"
* imgpkg: Armazena e realiza o upload de configuração de aplicações e referências a imagens como uma imagen docker
* kapp-controller: Um gerenciador de pacotes para kubernetes orientado a gitops
* secretgen-controller: Gera secrets e replica secrets através de namespaces
* vendir: verifica arquivos que devem estar em um diretório de forma declarativa

E as ferramentas experimentais:

* kwt: Expõe a rede kubernetes como uma rede overlay na máquina
* terraform-provider-carvel: Permite utilizar ytt, kbld e kapp com terraform

A seguir, um detalhamento sobre algumas dessas ferramentas.

## Ytt

Ytt (pronunciado uaititi) é uma ferramenta para template e overlay em yaml. Por este motivo, podemos traçar um paralelo do ytt com [helm charts](https://helm.sh/) e o [kustomize](https://kustomize.io/).

Os templates utilizados nos helm charts são processados pela [biblioteca de templates do go](https://pkg.go.dev/text/template). Os templates são processados como texto sem estrutura e, por este motivo, é necessário ter cuidado com as identações produzidas para que o resultado final seja um yaml válido. Os templates utilizados pelo ytt levam em conta que estão em um documento YAML, logo, é muito mais fácil produzir um resultado válido e os erros são mais informativos.

O kustomize utiliza um arquivo `kustomize.yaml` para definir de forma declarativa como os manifestos kubernetes serão modificados. Neste arquivo são declarados os [transformers](https://kubectl.docs.kubernetes.io/references/kustomize/glossary/#transformer) que farão as alterações nos yamls processados. Cada transformer tem uma configuração específica. Por exemplo, utilizar jason patch para remover algum conteúdo. O ytt utiliza anotações em yaml para declarar as operações.

Comparações do ytt com outras ferramentas podem ser encontradas [aqui](https://carvel.dev/ytt/docs/v0.49.x/ytt-vs-x/).

O ytt utiliza um dialeto do python chamado [starlark](https://github.com/bazelbuild/starlark) para os templates. Ao final da linha é adicionado um comentário `#@` e tudo que está após e na mesma linha é processado como código starlark. Por ser baseado em starlark, é possível adicionar estruturas if..else, for e definir funções. Também é possível componentizar em vários arquivos e utilizar como biblioteca. Também é possível externalizar parâmetros em arquivos de valores e em arquivos de dados. Além da já citada possibilidade de aplicar overlays. Ao final, o ytt produz um yaml após realizadas todas as operações de template e de overlay.

Como as instruções do ytt são feitas dentro de comentários YAML, é possível processar os arquivos com ferramentas para YAML, como validadores e linters. E o ytt pode ser utilizado para qualquer YAML, não apenas manifestos kubernetes.

{{< columns >}}
Fonte ytt
```yaml
#! Este é um comentário yaml. Comentários processados pelo ytt são iniciados por #@
#! 1. Variável
#@ um_mapa = {
#@   "chave1": "valor1",
#@   "chave2': "valor2"
#@ }
chave: #@ um_mapa

#! 2. If
#@ if True:
chave_true: valor true
#@ else
chave_false: valor false
#@ end

#! 3. For
#@ for v in [7, "teste", { "chave": "valor" }]
- item: #@ v
#@ end


#! 4. Função
#@ def funcao1():
#@   return [1, 2, {"chave: "valor"}]
#@ end

#@ def funcao2():
chave1: valor1
chave2:
 - valor3
 - valor4
#@ end

exemplo1: #@ funcao1()
exemplo2: #@ funcao2()
```
{{< column >}}
Resultado
```yaml

#! 1. Variável
chave:
  chave1: valor1
  chave2: valor2



#! 2. If
chave_true: valor true





#! 3. For
- item: 7
- item: teste
- item:
    chave: valor

#! 4. Função
exemplo1:
  - 1
  - 2
  - chave: valor
exemplo2:
  chave1: valor1
  chave2:
  - valor3
  - valor4
```
{{< endcolumns >}}

## Kbld

O kbld (pronunciado kei-bild) encontra quais imagens devem ser construídas a partir de arquivos YAML, as constrói, envia pro repositório e modifica os arquivos para referências imutáveis das mesmas. A construção é delegada para ferramentas como o docker e pack do buildpack. Todo o processo de encontrar as imagens e o fonte, construção e envio pro repositório pode ser customizado. O kbld também pode produzir um arquivo de lock para garantir que as imagens corretas estão sendo utilizadas.

Por exemplo, considere o seguinte manifesto yaml:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app1-deployment
  labels:
    app: app1
spec:
  replicas: 3
  selector:
    matchLabels:
      app: app1
  template:
    metadata:
      labels:
        app: app1
    spec:
      containers:
      - name: app1
        image: app1 # <-- Imagem a ser construída
        ports:
        - containerPort: 80
---
apiVersion: kbld.k14s.io/v1alpha1
kind: Config
sources:
- image: app1
  path: . # <-- Onde está o fonte da imagem
destinations:
- image: app1
  newImage: docker.io/hk/simple-app # <-- Para onde realizar o push
```

Ao ser processado pelo kbld, produzirá o seguinte yaml:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app1-deployment
  labels:
    app: app1
  annotations:
    # Metadata informacional sobre como a imagem foi construída
    kbld.k14s.io/images: |
      - origins:
        - path: /users/pivotal/workspace/simple-app
          type: local
        - dirty: false
          remoteURL: git@github.com:k14s/super-secret-simple-app
          sha: e877718521f7ccea0ab0844db0f86fe123a8d8ef
          type: git
        url: index.docker.io/hk/simple-app@sha256:e932e46fd...      
spec:
  replicas: 3
  selector:
    matchLabels:
      app: app1
  template:
    metadata:
      labels:
        app: app1
    spec:
      containers:
      - name: app1
        image: index.docker.io/hk/simple-app@sha256:e932e46fd... # <-- Referência absoluta da imagem construída e enviada pro registro
        ports:
        - containerPort: 80
```

## Kapp

O kapp (pronunciado kép) gerencia múltiplos recursos do kubernetes como uma unidade. O kapp calcula as ações que serão realizadas (adições, modificações e remoções de recursos) e só as executa mediante aprovação. Também ordena as operações de acordo com dependências conhecidas e, caso necessário, podem ser configuradas novas dependências. Ele pode trabalhar com yaml produzido por qualquer ferramenta, como ytt, helm template, kustomize, etc.

A implantação de uma aplicação através do kapp acontece em dois estágios, o estágio de diff e o estágio de apply. No estágio de diff é calculada a diferença dos manifestos e a aplicação no cluster e a diferença é exibida, perguntando se o usuário deseja prosseguir para o estágio de apply. No estágio de apply, as modificações são ordenadas para respeitar as dependências, por exemplo, Namespaces são criados antes dos recursos que os referenciam. Caso haja uma dependência não prevista pelo kapp, é possível adicionar novas por meio de `changeGroupBindings` e `changeRuleBindings`. Ao realizar as operações no estágio de apply, o kapp espera até que algumas condições sejam satisfeitas para finalizar a implantação. Também é possível adicionar esperas customizadas.

Considere o [repositório do kapp](https://github.com/carvel-dev/kapp) para os exemplos seguintes. Ao executar o comando `kapp deploy -a app1 -f examples/simple-app-example/config-1.yml`, teremos o seguinte retorno:

```
Target cluster 'https://0.0.0.0:45793' (nodes: k3d-k3s-default-server-0)

Changes

Namespace  Name        Kind        Age  Op      Op st.  Wait to    Rs  Ri
default    simple-app  Deployment  -    create  -       reconcile  -   -
^          simple-app  Service     -    create  -       reconcile  -   -

Op:      2 create, 0 delete, 0 update, 0 noop, 0 exists
Wait to: 2 reconcile, 0 delete, 0 noop

Continue? [yN]:
```

Ao escolher continuar (y), teremos o seguinte:

```
10:02:55PM: ---- applying 2 changes [0/2 done] ----
10:02:55PM: create deployment/simple-app (apps/v1) namespace: default
10:02:55PM: create service/simple-app (v1) namespace: default
10:02:55PM: ---- waiting on 2 changes [0/2 done] ----
10:02:55PM: ok: reconcile service/simple-app (v1) namespace: default
10:02:55PM: ongoing: reconcile deployment/simple-app (apps/v1) namespace: default
10:02:55PM:  ^ Waiting for generation 2 to be observed
10:02:55PM:  L ok: waiting on replicaset/simple-app-7474f47c7c (apps/v1) namespace: default
10:02:55PM:  L ongoing: waiting on pod/simple-app-7474f47c7c-6ccz8 (v1) namespace: default
10:02:55PM:     ^ Pending
10:02:55PM: ---- waiting on 1 changes [1/2 done] ----
10:02:55PM: ongoing: reconcile deployment/simple-app (apps/v1) namespace: default
10:02:55PM:  ^ Waiting for 1 unavailable replicas
10:02:55PM:  L ok: waiting on replicaset/simple-app-7474f47c7c (apps/v1) namespace: default
10:02:55PM:  L ongoing: waiting on pod/simple-app-7474f47c7c-6ccz8 (v1) namespace: default
10:02:55PM:     ^ Pending: ContainerCreating
10:03:04PM: ok: reconcile deployment/simple-app (apps/v1) namespace: default
10:03:04PM: ---- applying complete [2/2 done] ----
10:03:04PM: ---- waiting complete [2/2 done] ----
```

Ao executar o comando `kapp ls` teremos o seguinte resultado:

```
Target cluster 'https://0.0.0.0:45793' (nodes: k3d-k3s-default-server-0)

Apps in namespace 'default'

Name  Namespaces  Lcs   Lca
app1  default     true  3m

Lcs: Last Change Successful
Lca: Last Change Age

1 apps

Succeeded
```

O comando `kapp inspect -a app1` mostra alguns detalhes da aplicação, como mostra a seguir:

```
Target cluster 'https://0.0.0.0:45793' (nodes: k3d-k3s-default-server-0)

Resources in app 'app1'

Namespace  Name                         Kind           Owner    Rs  Ri  Age
default    simple-app                   Deployment     kapp     ok  -   8m
^          simple-app                   Endpoints      cluster  ok  -   8m
^          simple-app                   Service        kapp     ok  -   8m
^          simple-app-7474f47c7c        ReplicaSet     cluster  ok  -   8m
^          simple-app-7474f47c7c-6ccz8  Pod            cluster  ok  -   8m
^          simple-app-7474f47c7c-6ccz8  PodMetrics     cluster  ok  -   0s
^          simple-app-mt5ck             EndpointSlice  cluster  ok  -   8m

Rs: Reconcile state
Ri: Reconcile information

7 resources

Succeeded
```

O comando `kapp logs -a app1` pode exibir os logs de todos os pods da aplicação.

## Imgpkg

O imgpkg (pronunciado imágepécage) serve para armazenar arquivos arbitrários (manifestos kubernetes, por exemplo) e referências a imagens docker em uma imagem OCI. Deste modo, é possível criar artefatos imutáveis e com referências absolutas que podem ser enviados para e recuperados de registros docker.

Para utilizar o imgpkg é necessário ter, no mínimo, um arquivo `.imgpkg/images.yml` no formato `ImagesLock`. Este arquivo pode ser produzido por meio do `kbld` com a flag `--imgpkg-lock-output`.

Considere o [repositório do imgpkg](https://github.com/carvel-dev/imgpkg) para o exemplo seguinte:

```shell
$ cd examples/basic-step-1
$ mkdir .imgpkg
$ kbld -f config.yml --imgpkg-lock-output .imgpkg/images.yml
resolve | final: index.docker.io/dkalinin/k8s-simple-app -> index.docker.io/dkalinin/k8s-simple-app@sha256:4c8b96d4fffdfae29258d94a22ae4ad1fe36139d47288b8960d9958d1e63a9d0
---
apiVersion: v1
kind: Service
(conteúdo removido por brevidade)
        image: index.docker.io/dkalinin/k8s-simple-app@sha256:4c8b96d4fffdfae29258d94a22ae4ad1fe36139d47288b8960d9958d1e63a9d0
        name: simple-app
$ cat .imgpkg/images.yml
---
apiVersion: imgpkg.carvel.dev/v1alpha1
images:
- annotations:
    kbld.carvel.dev/id: index.docker.io/dkalinin/k8s-simple-app
    kbld.carvel.dev/origins: |
      - resolved:
          tag: latest
          url: index.docker.io/dkalinin/k8s-simple-app
  image: index.docker.io/dkalinin/k8s-simple-app@sha256:4c8b96d4fffdfae29258d94a22ae4ad1fe36139d47288b8960d9958d1e63a9d0
kind: ImagesLock
```

## kapp-controller

O kapp-controller (pronunciado kép controler) permite instalar, atualizar e modificar aplicações dentro de um cluster kubernetes como um pacote de forma declarativa. A arquitetura em camadas do kapp-controller permite com que aplicações possam ser escritas como manifestos yaml, charts helm, templates ytt, templates jsonnet, etc e obtidas a partir de endpoints http, repositórios git, repositórios helm, etc. O kapp-controller realiza três passos para instalar pacotes no cluster: obter a configuração e imagens OCI, montar os yaml a partir das configurações obtidas e deploy dos recursos para o cluster.

Um exemplo de aplicação utilizando o kapp-controller:

```yaml
apiVersion: kappctrl.k14s.io/v1alpha1
kind: App
metadata:
  name: simple-app
  namespace: default
spec:
  serviceAccountName: default-ns-sa
  fetch: # <-- Onde obter os arquivos
  - git:
      url: https://github.com/carvel-dev/simple-app-on-kubernetes
      ref: origin/develop
      subPath: config-step-2-template
  template: # <-- Como gerar os manifestos
  - ytt: {}
  deploy: # <-- Como realizar o deploy para o cluster
  - kapp: {}
```

## Secretgen-controller

O secretgen-controller provê CRDs para especificar quais Secrets devem residir no cluster. Estes secrets podem ser gerados pelo próprio secretgen-controller, obtidos a partir de outros recursos kubernetes, ou replicados de outros namespaces. Senhas, certificados (CA e folhas), chaves RSA e chaves SSH são suportadas.

Um exemplo de como gerar uma senha com o secretgen-controller:

```yaml
apiVersion: secretgen.k14s.io/v1alpha1
kind: Password
metadata:
  name: complex-password
spec:
  length: 27
  digits: 2
  uppercaseLetters: 4
  lowercaseLetters: 10
  symbols: 3
```

O `Password` anterior cria um recurso `Secret` com o nome `complex-password`.

## Conclusão

Escrever e gerenciar aplicações para o kubernetes pode ser uma tarefa complexa e com vários desafios. O projeto carvel busca lidar com esses desafios a partir de um conjunto de ferramentas que podem ser utilizadas juntas ou isoladas. Apesar de ser um projeto sandbox na CNCF, é bastante promissor e pode vir a ser um projeto bem difundido na comunidade kubernetes.