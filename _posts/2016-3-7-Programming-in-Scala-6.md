---
layout: post
title: Programming in Scala [5]
---

读书笔记(6)

# *Chapter 7* Built-in Control Structures

Scala的`if, for, try, match`都有返回值。

## Scala的`if`

```scala
val filename =
  if (!args.isEmpty)
    args(0)
  else
    "default.txt"
```

这种写法有两个优点： 

1. 更短
2. 使用`val`而不是`var`。

不带`else`的`if`也有返回值，会返回`Unit`类型的`()`。当一个值的类型转换成`Unit`，关于值的所有消息都会丢失。所以`()`不表示任何值。

## Scala的`while`
上
```scala
var line = ""
do {
  line = readline
  println("read: " + line)
} while (!line.isEmpty)
```

Scala的`while`和`do-while`跟Java里的差不多，然而`while`和`do`都返回不含任何值的`()`。这虽然有悖纯函数语言的哲学，但是方便了用户用命令式语言风格写出更具可读性的程序，比如一些算法。而有些算法虽然用`while`也能写，但是用递归等函数语言风格的话可能会更清晰，比如算最大公约数的算法：

```scala
def gcd(x: Long, y: Long): Long =
  if (b == 0) a else gcd(b, a % b)
```

总的来说，更推荐用函数风格来写程序，因为`while`不返回值，需要引入`var`。

## Scala的`for`

最简单的用法就是循环一整个collection

```scala
val fileHere = (new java.io.File(".")).listFiles
for (file <- filesHere)
  println(file)
```

这种写法能循环所有collection，不只数组（必须实现了`scala.Iterable`这个trait的`<-`方法）。比如说循环一个`Range`类型。

```scala
for (i <- 1 to 5)
  println("Iteration " + i)
```

**过滤**

```scalafor (file <- filesHere; if file.getName.endsWith(".scala"))  println(file)
```

或

```scalafor (file <- filesHere)  if (file.getName.endsWith(".scala"))    println(file)
```

因为`for`有返回值，所以`for`是一种expression。

可以加入更多的判断条件。

```scala
for (  file <- filesHere;  if file.isFile;  if file.getName.endsWith(".scala")) println(file)
```

为了更可读，可以把`()`换成`{}`。这样就可以省略掉分号`;`。

```scala
for {  file <- filesHere  if file.isFile  if file.getName.endsWith(".scala")} println(file)
```

**嵌套循环**

```scala
def fileLines(file: java.io.File) =  scala.io.Source.fromFile(file).getLinesdef grep(pattern: String) =  for {    file <- filesHere    if file.getName.endsWith(".scala")    line <- fileLines(file)    if line.trim.matches(pattern)  } println(file + ": " + line.trim)grep(".*gcd.*")
```

**中途赋值(Mid-stream assignment)**

```def grep(pattern: String) =  for {    file <- filesHere    if file.getName.endsWith(".scala")    line <- fileLines(file)    trimmed = line.trim    if trimmed.matches(pattern)  } println(file + ": " + trimmed)grep(".*gcd.*")
```

例子中的`trimmed = line.trim`的效果类似`val`，只是不用标明`val`。减少了一次算`trim`的重复开销。

**生成新collection**

```scala
def scalaFiles =
  for {    file <- filesHere    if file.getName.endsWith(".scala")  } yield file
```

这里在循环体之前用了`yield`，生成了新的collection。

当使用`yield`的时候，实际上是如下的结构：

`for` *循环判断语句* `yield` *循环体*

`yield`必须放在循环体之前。（跟ruby和C#等放置的位置不同）

## Scala的`try`

Scala的`try`跟其他语言的的异常处理差不多。

**抛出异常**

```scala
throw new NullPointerException
```

```scala
val half =  if (n % 2 == 0)
    n/2
  else    throw new Exception("n must be even")
```


* 用法跟Java差不多。
* 技术上来说，`throw`返回了类型`Nothing`。这使得上面例子的写法可以成立。

**捕获异常**

```
try {  doSomething()}catch {  case ex: IOException => println("Oops!")  case ex: NullPointerException => println("Oops!!")}
```

* 写法跟其他语言类似。
* 这里用了一种叫模式匹配（pattern matching）的方法，后面会提到。
* 上面的异常处理会按顺序从上往下检查。如果无法捕获，再往外层抛出。
* 不像Java，你无需处理已检查的异常。

**`finally`语句**

跟Java几乎一样。

**生成值**

`try-catch-finally`结构是有返回值的。比如：

```scala
val url = try {  new URL(path)}catch {  case e: MalformedURLException =>    new URL("http://www.scala-lang.org")}
```

这里有个要注意的地方，如果在Scala的`finally`的结构体内中途显式`return`返回值，这个值会覆盖之前的返回值，比如：

```scala
def f(): Int = try { return 1 } finally { return 2 }
```

```scala
def g(): Int = try { 1 } finally { 2 }
```

这里`f()`返回2，而`g()`返回1。

## Scala里的`match`

Scala里的`match`，相当于其他语言的`switch`。不过Scala的`match`能让你匹配任何模式（详见Chapter 12）。

```scala
val firstArg = if (args.length > 0) args(0) else ""firstArg match {  case "salt" => println("pepper")  case "chips" => println("salsa")  case "eggs" => println("bacon")  case _ => println("huh?")
```

* 这个例子依次把`firstArg`匹配`"salt","chips","eggs"`，而通配符`_`则匹配默认值。
* 不像Java里的`switch`，只能匹配整数类型和枚举常量，Scala的`match`能匹配所有常量。（虽然书里没提，实际上Java７的`switch`也能匹配字符串，是通过语法糖实现的，方法是先计算变量的`.hashCode()`，再比较常量的哈希值，最后用`equals`按值比较[^1]。枚举类型本来就是语法糖，实现方法应该也类似。）
* `break`不可用。（这东西本来就是语法盐。近代的语言基本都不保留了。）

## 没有`break`和`continue`也能活

有点意外的是，Scala保留了`while`，却非常前卫地完全去掉了`break`和`continue`。

书里提的例子就是用递归或者用其他函数式的方法去避免用`break`和`continue`，介绍得不怎么详细。有空做一下深入调查再写。

[^1]: <http://javarevisited.blogspot.jp/2014/05/how-string-in-switch-works-in-java-7.html>

