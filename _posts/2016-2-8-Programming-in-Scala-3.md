---
layout: post
title: Programming in Scala [3]
---

读书笔记(3)

# *Chapter 3* Next Steps in Scala

## 类与单例对象

### 构造类

* Scala里的构造函数比简洁。

~~~scala
class FancyGreeter(greeting: String) {
	def greet() = println(greeting)
}val g = new FancyGreeter("Salutations, world")
~~~

这里的`greeting`是私有的不可修改量。

* 所有的包含在花括号内的非函数或变量定义的部分，都是构造函数。称之为`primary constructor`。

~~~scala
class RepeatGreeter(greeting: String, count: Int) {
	def this(greeting: String) = this(greeting, 1)
	def greet() = {
		for (i <- 1 to count)
			println(greeting)
	}
}
val g1 = new RepeatGreeter("Hello, world", 3)g1.greet()val g2 = new RepeatGreeter("Hi there!")g2.greet()
~~~

* Scala也支持多个构造函数。但是必须选定一个`primary constructor`，其他的是`auxiliary constructor`。
* `auxiliary constructor`的名字必须是`this`，必须无返回值，第一个参数必须是同一类内的其他构造函数。

### 单例对象

* Scala不允许类内有静态变量或静态方法。
* 取而代之，Scala引入了`singleton object`。
* `singleton object`不能被，也不应该用`new`创建实例。它会在第一次被引用时被创建，且只有一个实例。
* `singleton object`的名字可以与类相同，这个`singleton object`被称为这个类的`companion object`（伴生对象）。
* 只有`object`没有`class`的话，叫`stand-alone object`。

实例： `WorldlyGreeter.scala`（非脚本，故用驼峰式命名。实际上不像Java，Scala的文件名跟内容没关系，只是推荐像Java一像用跟内容类一致的文件名。）

~~~scala
// The WorldlyGreeter classclass WorldlyGreeter(greeting: String) {
	def greet() {
		val worldlyGreeting = WorldlyGreeter.worldify(greeting)
		println(worldlyGreeting)
	}
}// The WorldlyGreeter companion objectobject WorldlyGreeter {
	def worldify(s: String) = s + ", world!"
}
~~~

### `trait`与`mixin`

* Scala的`trait`可以包含非抽象方法，而Java的`interface`就只能包含抽象方法。所以Java里是*实现*`interface`，而Scala是*扩展*`trait`。
* Java跟Scala的类都只能继承自一个父类。
* Java的类可以实现0~无限个`interface`，Scala的类可以扩展0~无限个`trait`。
* `implements`*并非*Scala的关键字。
* 不同于Java，Scala里重载一个函数要有`override`关键字。
* 不同于Java，Scala可以在初始化时混合`trait`。

混合例子

~~~scala
trait ExclamatoryGreeter extends Friendly {
	override def greet() = super.greet() + "!"}

val pup: Friendly = new Dog with ExclamatoryGreeterprintln(pup.greet())
~~~

* 这里创建的类叫`synthetic class`，意为“编译器创建的类”。（应该是编译时创建而非运行时）

# *Chapter 4* Classes and Objects

* A class should be responsible for an *understaandable* amount of functionality. （不知道怎么译好）
* Scala里定义一个值的时候必须初始化。
* Java里的类变量是可以不初始化的，它们会被根据类型被自动初始化对应的值（数值就是0或0.0，布尔值是false，引用则是null，等等）。Scala里要获得同样效果可以用`_`，比如`var x: Int = _`。

（文章里还介绍了一个关于`unreachable`的例子，不难，割爱掉。）

### 4.2 Mapping to Java

感觉这一小节挺重要的。

* 如前所述，Scala的类与对象等概念跟Java并非完全一一对应的。
* Java的所有对象的内存分配都在JVM的heap里。比如说Java里的`String`，不管是否本地引用，都会放在heap里。如果是在本地创建的`String`，这个`String`的引用是放在stack里，而`String`的本体则放在heap里。
* 相反地，由于Java里的`int`不是对象，而是原始类型，所以不分配在heap。不过，Java里提供了一个不可修改的int的wrapper，也就是`java.lang.Integer`，跟`String`一样，它也是分配在heap里。Java里区分原始类型跟wrapper的原因是为了优化性能（当真？？？）。
* 为了让这两个概念没这么难用，Java 5里引入了`autoboxing`，也就是自动装箱，类似的还有自动拆箱。（Java基础，不说了）

* 再看回Scala。Scala里所有值都是对象，包括数字。
* Scala里的`String`跟Java一样，也是放于heap里，实际上Scala的编译器会创建一个`java.lang.String`。
* 然而原始类型就有些复杂了。编译器会自己优化，自动选择`int`或`java.lang.Integer`。实际上Scala会频繁的自动装箱和拆箱。
* 为免歧义，此书把所有`reference`（引用），定义为“只有确定会在Java运行层面里生成引用才叫引用”。`String`在Java里必然是引用，而`Int`则有可能是引用（`java.lang.Integer`的时候），有可能不是引用（`int`的时候）。
* 同样的，本书的`unreferenced`，只用于“确定Java层面上没有引用”的情况（被垃圾回收）。这里会使用`unreachable`来表述Scala的同样情况，Scala抽象层的所有对象都可以是`unreachable`。（这书真严谨，好喜欢）
* 举个例子，如果一个Scala的`Int`定义在本地，且用原始类型`int`实现。当方法返回时栈帧(stack frame，JVM基础)弹出，这个变量会变成`unreachable`。而实际上因为不存在Java对象，它并非`unreferenced`。

### Fields and methods

~~~scala
class ChecksumCalculator {
	private var sum = 0
	def add(b: Byte) { sum += b }
	def checksum: Int = ∼(sum & 0xFF) + 1
}
~~~

* 如果方法前*没有*`=`，会返回`Unit`类型，因为Scala会把任何类型转换为`Unit`

~~~scala
scala> def g { "this String gets lost too" }g: Unit

scala> def h = { "this String gets returned!" }h: java.lang.String
~~~

未完待续。