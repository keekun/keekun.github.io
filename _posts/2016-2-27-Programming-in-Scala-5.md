---
layout: post
title: Programming in Scala [5]
---

读书笔记(5)

# *Chapter 6* Functional Objects

这章用了一个叫`ChecksumCalculator`的实例介绍Scala里的OOP。

* Scala的理念里`val`与`val`只是实现目的的工具，没有分轻重。
* 使用不可改变的对象，不用关心值的改变，更容易在不同的计算间分享数据，无论是串行还是并行。
* Scala跟Java一样，也有`private`与`protected`。
* 跟Java固定，Scala的对象里也可用`this`引用自己。

* Scala的identifiers（标识符）有如下两种：
 - **alphanumeric identifer**: 就是以非数字起始的，以大小字英数字与下划线组成的标识符。虽然符号`$`也算字符，但只供Scala编译器内部使用。虽然`$`也能编译，但用户的程序里不应含`$`，否则会跟Scala编译器生成的标识符冲突。（Java的标识符的字符集 = Scala的alphanumeric identifer的字符集 + `$`）。
 - **operator identifier**： 以一个或多个操作符字符组成。这里的操作符字符指的是7位ASCII里字母，数字，和`_ ( ) [ ] { } . ; , " ' ‘`中的任一个字符以外的字符。例如`:->`。Scala编译器会自动把`:->`转成合法的Java标识符：`$colon$minus$greater`。
 - **mixed identifer**： 以alphanumeric identifier起始，后面加一个下划线和一个operator identifier。比如：`vector_+`。
 - **literal identifier**: 被`‘`包住的任意字符串，可用于在使用跟Scala保留字相同名字的时候，比如`yield`是Scala的保留字，在调用Java的`Thread.yield()`时，可以写成`Thread.‘yield‘()`。
* Scala支持任意长度的标识符，这会引起小问题。比如`x<-y`在Java里会被识别为`x < - y`四个符号，而在Scala则会被识别是为`x <- y`三个符号。要分开它们，只要在`<`和`-`间插一个空格。

* `implicit`关键字告诉编译器如何进行自动类型转换。
* 要小心设计自动类型转换，因为它会降低程序的可读性。（这种坑太多了，可看《Java Puzzlers》等，一抓一大堆。）