---
title: "Java Features através do tempo"
date: 2020-10-07
---
Em 2014 foi lançada a versão 8 da plataforma Java SE (sim, há 6 anos!) e trouxe consigo diversas funcionalidades muito esperadas como as expressões lambda. A partir da versao 9 cada nova versão é lançada a cada 6 meses (Atualmente estamos na versão 15). Este novo ritmo de atualizações faz com que programadores tenham mais dificuldades em descobrir novas possibilidades da linguagem. Neste texto compilarei algumas das adições, remoções e modificações que possam impactar diretamente na nossa rotina de desenvolvimento.

Estas informações podem ser consultadas em mais detalhes na página da [OpenJDK](https://openjdk.java.net/). Este texto deve servir apenas como referência rápida e encoraja a consulta à página da OpenJDK. Os exemplos de código podem ser das próprias JEPs ou JSRs por facilidade.

- [J2SE 5.0](#java5)
- [Java SE 6](#java6)
- [Java SE 7](#java7)
- [Java SE 8](#java8)
- [Java SE 9](#java9)
- [Java SE 10](#java10)
- [Java SE 11](#java11)
- [Java SE 12](#java12)
- [Java SE 13](#java13)
- [Java SE 14](#java14)
- [Java SE 15](#java15)

## [J2SE 5.0](https://docs.oracle.com/javase/1.5.0/docs/index.html) {#java5}

Nesta versão foram adicionados [Generics](https://jcp.org/en/jsr/detail?id=14), [Laço for melhorado, Autoboxing e Unboxing, Enums, Import estático, varargs](https://jcp.org/en/jsr/detail?id=201) e [Anotações](https://jcp.org/en/jsr/detail?id=175).

Generics permitem escrever classes que dependam de outra classe de forma parametrizada, por exemplo, uma `List` de `String` tem a mesma implementação de uma `List` de `Integer`, mas o compilador se certifica de que apenas objetos do tipo `String` serão adicionados e ao retornar um objeto desta `List` não é necessário realizar um cast, pois a `List` só contém objetos do tipo `String`.

O laço for melhorado, também chamado de for each, permite realizar iteração em um iterator sem usar explicitamente o iterator. Por exemplo, o código a seguir:

```java
Collection<String> c = ...;
for (Iterator<String> s = c.iterator(); s.hasNext(); )
  System.out.printlnt(s.next());
```

pode ser escrito:

```java
Collection<String> c = ...;
for (String s : c)
  System.out.printlnt(s);
```

O for each pode ser utilizado em qualquer classe que implemente `Iterable`.

Autoboxing/Unboxing permite que tipos primitivos e suas classes correspondentes, como `int` e `Integer`, possam ser utilizados de forma intercalada com algumas ressalvas. Por exemplo:

```java
Integer i = 1;
int j = i + 1;
```

Observe que o código a seguir provocará um erro:

```java
Integer i = null;
int j = i + 1; // Ocorrerá uma NullPointerException
```

Varargs permite escrever métodos com número variável de argumentos. Podemos escrever um método que calcula médias desta forma:

```java
public double media(Double... numeros) {
  double media = 0.0;
  for (Double n : numeros) {
    media += n;
  }
  return media/numeros.length; // numeros é do tipo Double[]
}
```

e o método pode ser executado de duas formas:

```java
media(1.0, 2.0, 3.0);
media(new Double[] {1.0, 2.0, 3.0});
```

na primeira forma os números são passados como argumento, na segunda é criado um array e passado como um argumento só.

Modificações à API incluem as adições das classes `ProcessBuilder`, para executar subprocessos, `Formatter`, um interpretador para `Strings` formatadas para printf, `Scanner`, que permite converter textos em formatos primitivos ou `String`, e várias classes para concorrência nos pacotes `java.util.concurrent`, `java.util.concurrent.atomic` e `java.util.concurrent.locks``Formatter`, um interpretador para `Strings` formatadas para printf, `Scanner`, que permite converter textos em formatos primitivos ou `String`, e várias classes para concorrência nos pacotes `java.util.concurrent`, `java.util.concurrent.atomic` e `java.util.concurrent.locks`.

## [Java SE 6](https://docs.oracle.com/javase/6/docs/index.html) {#java6}

A versão 6 não trouxe modificações à linguagem Java, mas trouxe várias modificações e adições às APIs. Foram adicionadas funcionalidades referentes ao uso de linguagens de script na JDK, adicionando APIs e permitindo o uso de objetos Java na linguagem de script.

## [Java SE 7](https://docs.oracle.com/javase/7/docs/index.html) {#java7}

Números inteiros podem ser escritos em forma binária usando o prefixo `0b` ou `0B`. Números, inteiros ou não, podem ter underscore entre quaisquer dois números, facilitando a legibilidade, por exemplo:

```java
int mil = 1_000;
double doisMilEMeio = 2_000.5;
double umDecimoDeMilesimo = 0.0_001;
int white = 0xFF_FF_FF;
long secret = 0b01101010_01100001_01110110_01100001;
```

Suporte a `Strings` em declarações `switch`. Adição do operador diamante, que permite omitir o parâmetro de tipo quando puder ser realizada inferência dele, por exemplo:

```java
Map<String, List<String>> mapa = new HashMap<String, List<String>>();
```

pode ser escrito com:

```java
Map<String, List<String>> mapa = new HashMap<>();
```

e os parâmetros de tipo podem ser inferidos corretamente como `String` e `List<String>`. Nesta versão também foi adicionada uma versão do `try` chamada de `try-with-resources` que permite melhor uso de recursos que precisam ser fechados, por meio de um método `close`. No código a seguir o método `close` é chamado no objeto `br` automaticamente, ocorrendo ou não exceção:

```java
static String readFirstLineFromFile(String path) throws IOException {
  try (BufferedReader br = new BufferedReader(new FileReader(path))) {
    return br.readline();
  }
}
```

Múltiplos recursos podem ser declarados, separados por ponto e vírgula (;). Um bloco `catch` pode tratar múltiplos tipos de excessão ao mesmo tempo, por exemplo:

```java
catch (IOException|SQLException ex) {
  logger.log(ex);
  throw ex;
}
```

Foi adicionado um framework fork/join para concorrência, barreiras de sincronização, geradores de números pseudo-aleatórios locais à thread e coleções para facilitar a comunicação entre threads.

Foram adicionadas APIs de IO com operações assíncronas. Estas APIs são apelidadas de projeto NIO.

## [Java SE 8](https://openjdk.java.net/projects/jdk8/) {#java8}

**Data de lançamento:** 18 de Março de 2014

### [Expressões lambda e métodos default](https://openjdk.java.net/jeps/126)

Composição de comportamentos em Java geralmente faz uso do seguinte padrão:

```java
button.addActionListener(new ActionListener() {
  public void actionPerformed(ActionEvent e) {
   ui.dazzle(e.getModifiers);
  }
})
```

onde uma classe anônima que implementa a interface `ActionListener` é passada como parâmetro para um método, permitindo que o objeto button execute código fornecido por você. Acontece que esta classe anônima possui apenas um método e tem funcionamento parecido com o de uma função (no sentido de programação, não no sentido matemático). Na versão 8 o mesmo código pode ser escrito como:

```java
button.addActionListener((ActionEvent e) -> {
  ui.dazzle(e.getModifiers);
})
```

O código compilado é essencialmente o mesmo, mas é bem mais fácil escrever a segunda versão.

Não só objetos que implementam `ActionListener` podem ser escritos desta forma. Qualquer interface que possua apenas um método abstrato (chamada interface funcional ou SAM)  pode ser escrito desta forma.

Se uma expressão lambda pode representar qualquer objeto que implemente uma interface funcional, então como o compilador sabe a interface correta? Nesta versão também foram incluídos mecanismos de inferência de tipos. A interface é inferida a partir do contexto. No exemplo anterior o compilador deve utilizar uma interface do tipo `ActionListener`, pois o método recebe um objeto do tipo `ActionListener`.

Isto apresenta uma limitação. Em contextos em que o tipo não possa ser inferido, não é possível utilizar expressões lambda.

Com o uso de expressões lambda seria comum a escrita de código como:

```java
(o) -> o.metodo()
```

por exemplo para ordenar objetos por alguma propriedade. Este código pode ser reescrito como `Clazz::metodo`, supondo que o objeto `o` é do tipo `Clazz`. Isto é chamado de referência a método.

Atualizar as APIs da plataforma para utilizar Expressões Lambda sem quebrar compatibilidade foi um desafio. Para resolver este problema foram introduzidos os métodos default e estáticos em interfaces, permitindo que métodos em interface possam ter implementação (estes métodos não contam para classificar uma interface como funcional).

Além destas modificações na linguagem a API de coleções foi modificada e foi adicionada uma API de Streams.

### [Engine de Javascript Nashorn](https://openjdk.java.net/jeps/174)

A Nashorn é uma engine de javascript leve e de alta performance integrada à JDK. A engine é baseada na especificação [ECMAScript-262 edição 5.1](https://www.ecma-international.org/ecma-262/5.1/) da linguagem. Por ser integrada à JDK, tem interoperabilidade com código Java com mapeamento direto para JavaBeans. O acesso à engine pode ser feito pela API `javax.script` ou pela ferramenta de linha de comando jjs.

### [Inferência de tipo generalizada](https://openjdk.java.net/jeps/101)

O mecanismo de inferência de tipos para generics tinha algumas limitações em versões anteriores. Considere a seguinte classe:

```java
class List<E> {
   static <Z> List<Z> nil() { ... };
   static <Z> List<Z> cons(Z head, List<Z> tail) { ... };
   E head() { ... }
}
```

O compilador consegue realizar inferência de tipos com este código:

```java
List<String> ls = List.nil();
```

mas não no seguinte código:

```java
List.cons(42, List.nil());
```

exigindo com que o desenvolvedor tenha de escrever o tipo explicitamente:

```java
List.cons(42, List.<Integer>nil());
```

Na versão 8 o compilador consegue realizar a inferência em argumentos e em chamadas aninhadas de métodos, como:

```java
String s = List.nil().head();
```

### [Anotações em tipos](https://openjdk.java.net/jeps/104)

Anotações só poderiam ser utilizadas em declarações, por exemplo em declarações de classes e em declarações de parâmetros. Esta modificação permite utilizar anotações em qualquer lugar em que você possa utilizar um tipo, por exemplo, o código a seguir é válido:

```java
@NonNull String str;
```

A anotação `@NonNull` é apenas um exemplo e não denota uma anotação existente.

### [Repetição de anotações](https://openjdk.java.net/jeps/120)

Permite que um elemento receba a mesma anotação múltiplas vezes. Em algumas APIs era comum ter uma anotação 'contêiner' que servia como uma lista de anotações.

### [Ordenação paralela de arrays](https://openjdk.java.net/jeps/103)

Adiciona métodos de ordenação para arrays baseado no framework de fork/join.

### [Codificação e Decodificação em Base64](https://openjdk.java.net/jeps/135)

Adiciona uma API na JDK para realizar operações de codificação em Base64.

### [API de data/hora](https://openjdk.java.net/jeps/150)

Adiciona uma API para trabalhar com data e hora visando a substituição das classes existentes de data e hora.

### [Atualizações para concorrência](https://openjdk.java.net/jeps/155)

Adiciona classes para variáveis atualizáveis que escalem. Modificações para `ForkJoinPools` e novas classes de Lock e Future.

## [Java SE 9](https://openjdk.java.net/projects/jdk9/) {#java9}

**Data de lançamento:** 21 de Setembro de 2017

### [Sistema de módulos](https://openjdk.java.net/jeps/261)

Adiciona módulos na linguagem. Módulos são conjuntos de pacotes com informações de encapsulamento. Um módulo pode expor pacotes publicamente e requerer a presença de outros módulos em tempo de execução.

Um módulo de nome `org.foo.bar` é declarado com:

```java
module org.foo.bar { }
```

em um arquivo `module-info.java`. Para expor um pacote é utilizado o `exports` e para requerer um módulo é utilizado o `require`, por exemplo:

```java
module org.foo.bar {
  requires org.faa.baz;
  exports com.foo.bar.beta;
}
```

### [Atualizações para a API de processos](https://openjdk.java.net/jeps/102)

Melhora a API de processos permitindo obter mais informações do processo controlado, como id do processo, comando, argumentos, tempo que foi iniciado, etc.

### [Aprimoramento do projeto Coin](https://openjdk.java.net/jeps/213)

Adiciona modificações relativas ao projeto Coin.

1. Permite que variáveis final ou efetivamente finais sejam utilizadas como recursos do `try-with-resources`;
2. Permite que o operador diamante possa ser utilizado em classes anônimas se o compilador seja capaz de inferir o tipo;
3. Remove o underscore do conjunto de identificadores permitidos;
4. Permite métodos privados em interfaces.

### [jshell](https://openjdk.java.net/jeps/222)

Adiciona à JDK uma ferramenta de linha de comando que funcione como um REPL para Java.

### [Resource Bundles de propriedades em UTF-8](https://openjdk.java.net/jeps/226)

Permite que a API `ResourceBundle` possa carregar arquivos de propriedades com codificação UTF-8. Esta codificação agora é padrão para estes arquivos (ISO-8859-1 anteriormente).

### [Métodos factory para coleções](https://openjdk.java.net/jeps/269)

 Adiciona métodos nas classes `List`, `Set` e `Map` para facilitar a criação de objetos com poucos elementos. Para criar um objeto do tipo `List` poderemos usar:

```java
List.of(a, b, c);
```

Similarmente para `Set` e `Map`. Para objetos do tipo `Map` também existe os métodos `entry` e `ofEntries`, que permitem construir `Map` com:

```java
Map.ofEntries(
  entry(k1, v1),
  entry(k2, v2),
  ...
  entry(kn, vn));
```

Os objetos retornados destes métodos são superficialmente imutáveis.

## [Java SE 10](https://openjdk.java.net/projects/jdk/10/) {#java10}

**Data de lançamento:** 20 de Março de 2018

### [Inferência de tipo para variável local](https://openjdk.java.net/jeps/286)

Permite que variáveis locais possam ser declaradas utilizando a palavra `var`. Declarações como:

```java
ArrayList<String> list = ArrayList<>();
```

possam ser escritas como:

```java
var list = ArrayList<String>();
```

e o compilador pode realizar inferência de tipo na mesma. Note que neste exemplo não é possível o uso do operador diamante `<>`, pois o compilador não poderia inferir o tipo. Pelo mesmo motivo não é possível atribuir `null`, uma inicialização de array ou uma expressão lambda.

### [Certificados root](https://openjdk.java.net/jeps/319)

Adiciona certificados root emitidos pelos CAs do programa Oracle's Java SE Root CA. Foram incluídos os certificados dos CAs que assinaram o acordo OCA ou equivalente.

## [Java SE 11](https://openjdk.java.net/projects/jdk/11/) {#java11}

### [Remoção dos módulos Java EE e CORBA](https://openjdk.java.net/jeps/320)

Vários módulos das tecnologias Java EE e CORBA foram marcados como depreciados no Java SE 9. Como consequência, estes módulos foram removidos da JDK nesta versão. No entanto, estes módulos ainda estão disponíveis externamente e ainda poderão ser adicionados em projetos. Eles passam a não ser adicionados na JDK.

Os módulos removidos são:

- `java.xml.ws`
- `java.xml.bind`
- `java.activation`
- `java.corba`
- `java.transaction`
- `java.se.ee`
- `jdk.xml.ws`
- `jdk.xml.bind`

Além dos módulos, ocorrerão as seguintes remoções:

- As ferramentas `wsgen` e `wsimport` (do módulo `jdk.xml.ws`);
- As ferramentas `schemagen` e `xjc` (do módulo `jdk.xml.bind`);
- As ferramentas `idlj`, `orbd`, `servertool` e `tnamesrv` (do módulo `java.corba`);
- O provider JNDI `CosNaming`.

### [cliente HTTP](https://openjdk.java.net/jeps/321)

Adiciona um cliente HTTP que implemente HTTP/2 e WebSockets e com suporte para HTTP/TLS. Este cliente foi adicionado ao pacote `java.net.http`. A implementação do cliente é assíncrona e integrada à API `java.util.concurrent.Flow`.

### [Sintaxe de variável local para parâmetros de lambda](https://openjdk.java.net/jeps/323)

Permite que `var` seja utilizado para declarar parâmetros de uma lambda. Quando utilizado, todos os parâmetros devem ser declarados com `var`.

## [Java SE 12](https://openjdk.java.net/projects/jdk/12/) {#java12}

### [Expressões com switch (Preview)](https://openjdk.java.net/jeps/325)

Esta funcionalidade é uma preparação para a funcionalidade [pattern matching](https://openjdk.java.net/jeps/305).

O comportamento padrão para a declaração de um switch é a de cascata, em que um `case` sem `break` executa o próximo, e segue o que acontece em C e C++. Esta alteração introduz a forma `case L ->` em que não é necessário o uso do break e permite o uso de um bloco `{}`. Esta nova forma também pode ser utilizada commo uma expressão, por exemplo:

```java
int numeroDeDias = switch (mes) {
  case JAN, MAR, MAI, JUL, AGO, OUT, DEZ -> 31;
  case ABR, JUN, SET, NOV -> 30;
  case FEV -> 28;
};
```

A forma anterior também permite o uso em expressões colocando o resultado após os break, como:

```java
int numeroDeDias = switch (mes) {
  case ABR:
  case JUN:
  case SET:
  case NOV:
    break 30;
  case FEV:
    break 28;
  default:
    break 31;
}
```

Como restrição todos os branches do switch devem terminar sem valor ou todos com valor e do mesmo tipo ou lançar uma exceção.

## [Java SE 13](https://openjdk.java.net/projects/jdk/13/) {#java13}


### [Expressões com switch (2º Preview)](https://openjdk.java.net/jeps/354)

No caso de retorno em uma expressão do tipo `case L ->` com um bloco `{}` foi adicionado o `yield`, por exemplo:

```java
int j = switch (day) {
  case MONDAY -> 0;
  case SUNDAY -> 1;
  default     -> {
    int k = day.toString().length();
    int result = f(k);
    yield result;
  }
};
```
Na versão 12 não era possível ter retorno de um bloco `{}`. Em expressões do tipo `case L:` o `yield value;` foi introduzido substituindo o `break value;`, por exemplo:

```java
int numeroDeDias = switch (mes) {
  case ABR:
  case JUN:
  case SET:
  case NOV:
    yield 30;
  case FEV:
    yield 28;
  default:
    yield 31;
}
```

### [Texto em bloco (Preview)](https://openjdk.java.net/jeps/355)

Texto em bloco é um texto em múltiplas linhas. Um texto em bloco é iniciado com três aspas duplas (""") e uma quebra de linha (obrigatória!) e terminado por três aspas duplas, por exemplo:
```java
"""
linha 1
linha 2
linha 3
"""
```
que é equivalente a:
```java
"linha 1\nlinha 2\nlinha 3\n"
```

Note que `""""""` não é válido. Caso hava identação no texto é feito uma normalização do número de espaços, retirando espaços sobressalentes do início de cada linha, por exemplo:

```java
String html = """
              <html>
                  <body>
                      <p>Olá, mundo!</p>
                  </body>
              </html>
              """
```
seria exibido como:

```html
<html>
    <body>
        <p>Olá, mundo!</p>
    </body>
</html>
```

Espaços em branco ao final de cada linha também são removidos. Caso alguma das linhas estivesse mais à esquerda, menos espaço seria removido para manter a estrutura do texto, por exemplo:

```java
String html = """
              <html>
                  <body>
                      <p>Olá, mundo!</p>
                  </body>
              </html>
"""
```

seria exibido como (note posição das três aspas finais):
```html
              <html>
                  <body>
                      <p>Olá, mundo!</p>
                  </body>
              </html>
```

É possível utilizar aspas duplas (") dentro de um bloco de texto sem a necessidade de escape, por exemplo:

```java
String cmd = """
             bash -c "echo a b c"
             """
```

é exibido como:

```bash
bash -c "echo a b c"
```

Blocos de texto são Strings. Todas as operações comuns com String podem ser realizadas com blocos de texto, inclusive bloco de texto com String e vice versa.

## [Java SE 14](https://openjdk.java.net/projects/jdk/14/) {#java14}

### [Pattern matching para instanceof (Preview)](https://openjdk.java.net/jeps/305)

Um idioma comum em java é testar o tipo de um objeto e realizar um cast, como:

```java
if (obj instanceof Integer) {
  int intValue = ((Integer) obj).intValue();
}
```

Esta funcionalidade permite que o mesmo código seja escrito como:

```java
if (obj instanceof Integer i) {
  int intValue = i.intValue();
}
```

A variável `i` só está definida onde `obj instanceof Integer` seja verdadeiro. Caso no código anterior houvesse um `else` a variável `i` não poderia ser utilizada. O seguinte código também é válido:

```java
if (obj instanceof String s && s.length() > 5) {
  ...
  s.contains(..)
  ...
}
```

pois para que `s.length() > 5` seja avaliado `obj instanceof String` tem de ser verdadeiro, permitindo o uso de `s`.

### [Melhoria em mensagens de NullPointerException](https://openjdk.java.net/jeps/358)

Mensagens de `NullPointerException` mostrarão que objeto é nulo. Em versões anteriores são mostradas o arquivo, o método e a linha em que a exceção ocorreu.

### [Registros (Preview)](https://openjdk.java.net/jeps/359)

Adiciona uma sintaxe leve para declarar classes de dados superficialmente imutáveis. Por exemplo, o registro seguinte:

```java
record Point(double x, double y) { }
```

é equivalente a:

```java
class Point {

  private final double x;
  private final double y;

  public Point(double x, double y) {
    this.x = x;
    this.y = y;
  }

  public double x() {
    return x;
  }

  public double y() {
    return y;
  }

  public boolean equals(Object o) { ... }

  public int hashCode() { ... }

  public String toString() {
    return "Point[x=" + x + ", y=" + y + "]";
  }
}
```

Registros não podem extender outras classes, não podem declarar outros atributos que não sejam os já declarados ou atributos estáticos. Registros são implicitamente final e não podem ser abstratos.

### [Expressões com switch (standard)](https://openjdk.java.net/jeps/361)

Adiciona as expressões switch como definidas nas versões 12 e 13 como standard.

### [Texto em bloco (2º Preview)](https://openjdk.java.net/jeps/368)

São adicionados duas novas sequências de escape. Uma linha com `\` no final serve como uma quebra visual, por exemplo:

```java
String text = """
                Lorem ipsum dolor sit amet, consectetur adipiscing \
                elit, sed do eiusmod tempor incididunt ut labore \
                et dolore magna aliqua.\
                """;
```

é equivalente a:

```java
String text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.";
```

A outra sequência de escape adicionada é `\s` que se transforma em um espaço. Mas pode ser utilizada para impedir que espaços ao final de linha de um bloco de texto sejam removidos.

## [Java SE 15](https://openjdk.java.net/projects/jdk/15/) {#java15}

### [Remove a engine de javascript Nashorn](https://openjdk.java.net/jeps/372)

Manter a Nashorn com o rápido desenvolvimento das especificações ECMAScript tornou-se desafiador demais. Ferramentas associadas também são removidas.

### [Classes seladas (Preview)](https://openjdk.java.net/jeps/360)

Adiciona classes e interfaces seladas na plataforma Java SE. Classes e interfaces seladas restringem quais classes e interfaces podem implementá-las.

### [Pattern matching para instanceof (2º Preview)](https://openjdk.java.net/jeps/375)

Não há modificações para a proposta da versão 15. Esta funcionalidade foi lançada como preview para recolher mais feedback.

### [Blocos de texto](https://openjdk.java.net/jeps/378)

Adiciona blocos de texto na plataforma Java SE assim como proposto na versão 13 com as modificações da versão 14.

### [Registros (2º Preview)](https://openjdk.java.net/jeps/384)

Registros podem ser declarados localmente (dentro do corpo de um método).
