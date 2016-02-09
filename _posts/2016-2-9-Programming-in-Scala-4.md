---
layout: post
title: Programming in Scala [4]
---

读书笔记(4)

# *Chapter 4* Classes and Objects

### 变量作用域

* 跟Java一样，Scala也是用花括号`{}`定义新的变量作用域（scope）。要在scope之外引用变量，只能通过继承/import/成员变量。
* 一旦定义了变量，不能在同一域里用同一名字定义变量

~~~scala
val a = 1
val a = 2  // 编译错误
println(a)
~~~

~~~scala
val a = 1;  // 这里";"是必须的
{
  val a = 2  // 编译通过
  a
}
println(a) // 输出1， 因为花括号内的a跟外面的a无关。
~~~

* 第二段代码在Scala能编译通过，然而在Java里是不允许括号内部的变量与外部的变量同名。在Scala里此括号外部的同名变量在括号内部是不可见的。
* Scala这么做是为了创造更方便的交互环境。
* 在interpreter里，Scala可以重复定义一个变量。

~~~scala> val a = 1a: Int = 1scala> val a = 2a: Int = 2
scala> println(a)
2
~~~

* interpreter可以这样做的原因是它会自动为每一段语句创建一个嵌套的花括号。上面的代码相当于

~~~scala
val a = 1;
{  val a = 2;
  {
    println(a)
  }}
~~~

### 分号推定

* Scala里句末的分号通常是可省略的。
* 只有在你要在一行里写多句语句时，分号才是必要的。
* 这个特性有时会产生有违你本意的结果，比如

~~~scala
x
+ y
~~~

Scala 会把这段分成两行：`x`和`+y`。要两行算`x+y`，可用

~~~scala
(x
+ y)
~~~

或

~~~scala
x +
y +
z
~~~

这样的写法。

如果下面三个条件满足一个或以上，该行就不会被判断为已完结

* 该行用`.`或`infix-operator`（后述）等不合法的语法结尾。
* 次行的开头的词不能用作开头。
* 该行的括号`()``[]`没完结。因为它们内部不允许多行。

### 单例对象

又是单例对象。

* 单例对象与其伴生类可以互相访问私有成员。
* 对Java程序员来说，理解单例对象最好的方法就是：它是Java里所有静态方法的集合体。用法跟Java里的也差不多。
* 跟Java最大的不同是，单例对象不能用`new`初始化，所以它们的构造函数是不能带参数的。
* 单例对象的实现方式是通过静态变量，实际上它们的初始化方式是跟Java的静态成员差不多的。单例对象只会在第一次被访问时初始化。
* 单例对象适合用来放置工具函数。（比如之前关于`List`的那章，就用`List.apply`这个工厂函数生成新的不可修改对象）
* 没有伴生类的`standalone object`还可以用来定义一个Scala程序的入口，书里的4.9小节有介绍，这里省略。

# *Chapter 5* 基本类型和操作符

* 虽然Scala里所有值皆是对象，但为了运行效率Scala编译器实际上会把一部分值用Java的原始类型表达。
* Scala的基本类型：`Byte`，`Short`，`Int`，`Long`，`Char`，`String`，`Float`，`Double`，`Boolean`。（本来想找官方文档的，没有。书里的Table 5.1）
* 除了`String`在`java.lang`包里，其他的基本类型都是`scala`包里。比如`Int`的全名是`scala.Int`。
* `scala`和`java.lang`会被自动引用到所有的Scala源文件里。你只用使用缩写，比如`Int`。
* 这些类型的取值范围跟Java的原始类型一致，这是为了能在转换成字节码时优化成Java的原始类型。
* 这些基本类型都有全小写的别名，跟Java的原始类型一样，但请跟随社区的选择，别使用。（我用2.11.7试了下，已经不能使用了）。

### Literals（符码）

* Literal指的是我们在代码里对常量的在文面上的表达方式。
* Scala的literals跟Java的几乎一样。
  - 整数（与Java一样）
  - 浮点小数（与Java一样）
  - 字符（与Java一样）
  - 字符串（与Java一样，唯一的不同，就是Scala支持多行字符串符码），比如：

~~~scala
println("""Welcome to Ultamix 3000.             Type "HELP" for help.""")  // 没有对齐
             
println("""|Welcome to Ultamix 3000.           |Type "HELP" for help.""".stripMargin) // 通过`stripMargin`方法对齐
~~~

用的是`"""`，跟python与ruby类似。

### 操作符是方法

* 反复说了。Java里的操作符是语法，Scala里的操作符是方法。既然是方法，自然也可重载。
* 当不使用`.()`来调用一个方法时，使用的是下面三个操作符记法的其中之一：
  - prefix（前缀）：操作符放于值之前，比如`-7`里的`-`。
  - postfix（后缀）：操作符放于值之后，比如`7 toLong`里的`to Long`。
  - infix（中缀）：操作符放于值之中，比如`7 + 2`里的`+`。
* 操作符与值之间的空格不是必须的，比如`1+2`与`1 + 2`与`(1).+(2)`与`1.+(2)`是完全一样的。（当然了，这只在不存在歧义时才成立）。
* 操作符记法可适用于任何方法，比如String的`indexOf`，如`str indexOf 'o'`。
* 当你使用中缀记法来调用一个多于一个参数的方法时，要用括号，如`str indexOf ('o'，5)`，相当于`str.indexOf('o', 5)`。
* 与中缀记法不一样，前缀与后缀记法是`unary`的，也就是一元操作符。
* 前缀记法是调用特定方法的简略写法，比如`-2.0`里的`-`，实际调用的是一个带`unary_`前缀的方法：`unary_-`。Scala会把`-2.0`自动转换成`(2.0).unary_-`。
* 只有`+`,`-`,`!`,`~`四个特殊字符能成为前缀方法。像`unary_*`一类的方法是不能用前缀记法调用的（但可正常调用）。
* 后缀就比较简单了，没有任何参数的方法都可以用后缀记法调用。

### Scala里的操作符

虽然本质不一样，用起来跟Java大同小异，只写比较关心的部分。

* Scala里的浮点小数求余用的不是IEEE 754标准，要用专用的`IEEEremainder`方法。（Java好像也是一样的。）
* Scala里的逻辑与和逻辑或跟Java一样，是可以短路的。由于Scala的逻辑与和逻辑或实际上是方法，所以其实Scala是可以做到选择性地不计算参数里的函数的。这是通过Scala的延迟计算特性来实现的，具体后述。

### 对象比较（Object equality）

* Scala里的对象比较可以比较所有对象，不仅是基本类型。如：

~~~scala
scala> 1 == 2res24: Boolean = falsescala> 1 != 2res25: Boolean = truescala> 2 == 2res26: Boolean = true

scala> List(1,2,3) == List(1,2,3)res27: Boolean = truescala> List(1,2,3) == List(4,5,6)
res28: Boolean = false

scala> 1 == 1.0res29: Boolean = truescala> List(1,2,3) == "hello"res30: Boolean = false

scala> List(1,2,3) == nullres31: Boolean = falsescala> null == List(1,2,3)res32: Boolean = false
~~~

* 在Java里，对于原始类型，`==`比较值；对于引用型变量，`==`比较的是两者是否引用JVM的heap里的相同对象。
* 在Scala里，`==`实际是调用了`equals`，只要实现了基于内容比较的`equals`，就能比较两者内容是否一致。
* Scala里有Java式的比较函数的实现`eq`和`ne`，后述。

### 操作符优先级(operator precedence)

* Scala里没有操作符优先级，但有方法优先级。
* 方法的优先级是通过方法名的首字符判断的。比如说，`*`开头的方法比`+`开头的方法优先，所以`2 + 2 * 7 = 2 + (2 * 7)`
* 具体优先级如下：

![Operator Precedence]({{ site.baseurl }}/images/operator_precedence.png "Operator Precedence")

* 当优先级一样时，Scala通过方法名的未字符判断其关联性（associativity，就是判断操作数与其左侧还是右侧的操作数组合）。一般的方法是从左到右结合，当方法名以`:`结尾时，从右到左结合（前面关于`String`的一章有提过）。所以，`a ::: b ::: c`相当于`a ::: (b ::: c)`，而`a * b * c`相当于`a * (b * c)`。
* 千万别为了炫耀自己对于操作符的理解而省略括号！！！你应该默认所有的程序员只知道`*,/,%`优先于`+,-`。除此之外的所有运算符都应该加括号！！！

关于这一点，我个人深有同感。我自己在给后辈做code review时，有违这个原则的，都打回去要求写括号。Scala作为一门为了简洁可以去掉句末`;`的语言，作者却要求明确写出括号注明优先级，可以看出这个括号是不可少的。究其原因，除了作者所述的原因外，我觉得还有一点：不同的语言的操作符优先级不一定一样。

比如说，PHP里，赋值运算符比比较运算符优先级高，而在JavaScript里，比较运算符比赋值运算符优先级高。在某些脚本语言里，逻辑与和逻辑或是同级的，如bash。更极端的甚至有语言是没有运算符优先级的，比如LISP那堆。要填上不同背景的工程师之间的鸿沟，括号是最优雅简单的选择。如果你觉得括号省去也没啥问题，很可能是你学的语言还不够多。

未完待续。



