---
layout: post
title: Programming in Scala [1]
---

读书笔记(1)

# *Chapter 1* A Scalable Language

## 进化的语言

* Scala能够方便的把自定义的类型封装得像本地类型一样。如`BigInt`。
* Scala能够把方法封装成本地语法一样。
  - Scala里函数都是对象，可以被继承。
* Scala面向对象，Scala比很多语言在面向对象上走得更远。
  - Scala里有`traits`，类似Java的`interface`。Scala的方法对象可以通过`mixin composition`构建，类似于在对象上面打补丁。(其实Ruby的对象也常用类似方法构建，Javascript里也常用mixin技巧)。
* Scala是函数语言。
  
函数语言有两大中心思想。[^1]

* 函数是`first-class`变量，地位类似整数与字符串。
* 函数语言的操作，输入与输出要能构成映射关系，不可直接修改输入的数据。
    - Java里的字符串就是不可修改(immutable)的。只考虑字符串类型的话。Java是函数语言，而Ruby不是。不可修改的数据结构是函数类型的基石。
    - 也就是说，函数语言的方法不会造成`side effects（函数副作用）`，它们只能通过输入与输出跟外部打交道。
    - 如果程序中任意两处具有相同输入值的函数调用能够互相置换，而不影响程序的动作，那么该程序是`referentially transparent（引用透明的)`。
    - 函数语言鼓励使用不可修改的数据和引用透明的方法，有些函数语言甚至要求必须遵守，而这在Scala里是可选的。

## 为何选择Scala

* Scala是兼容的。Scala运行在JVM（其实.net也可），大量使用了Java库。
* Scala语法间洁。
* Scala支持类型推断。
* Scala的语言比较高级（high-level，相对于Java）。（文中举了用一个`predicates`代替传统枚举的例子，`predicate`指的是返回`Boolean`型的函数）
* Scala是强类型的。
  - 很多人觉得这是缺点，作者认为是优点。（同感）
  - 强类型可以在编译期间确认某些运行时错误不会出现。
  - 安全的引用。（Java里经常遇到的，可以方便地改掉同一属性or函数or类的名字/引用）
  - 可以方便地生成文档。
  - 虽然Scala是强类型，但没有Java那么烦。比如声明一个带泛型的`HashMap`，在Java的话两侧都要写上泛型的类型，Scala只要写单侧。

## Scala的起源
* 直接看维基，不总结。

# *Chapter 2* First Steps in Scala

关于语法，不总结，只整理有意义的内容。Step 1~5跳过。

* Java，C++和C的那种用可变的参数实现的循环风格，叫`imperative style`。而Scala的循环风格更为`functional style`。
* Scala里也可以写`imperative style`的`for`，其实实现方法跟Java的不一样，Scala的`for`为循环体的每个循环都创建了一个新的不可变的`arg`并初始化。

~~~scala
// Scala
for (arg <- args)
	println(arg)
~~~

~~~java
// java
for (String arg : args) {
	System.out.println(args);
}
~~~

# *Chapter 3* Next Steps in Scala

* 提到上面的循环的例子。Scala用的是`val`，所以是`functional style`。Java用的是可变的状态（相当于`var`），所以是`imperative style`。
* Scala不是纯函数语言它是`imperative/functional language`，就是混合的语言。


未完待续。

    	
    	
    	
[^1]: 关于函数语言还有很多可参考的资料：<http://www.jianshu.com/p/390147c78967>




