---
layout: post
title: Programming in Scala [2]
---

读书笔记(2)

# *Chapter 3* Next Steps in Scala

##带类型数组

Scala里的用参数创建实例(parameterize)的方法：

~~~scala
val greetStrings = new Array[String](3)
greetStrings(0) = "Hello"
greetStrings(1) = ","
greetStrings(2) = "world! \n"

for (i <- 0 to 2)
  print(greetStrings(i))
~~~
* 这里`greetStrings`的类型是`Array[String]`。（类似Java5引入的泛型。）
* 使用`val`后，不能再修改引用，但引用的对象可以修改。
* 跟Java不同，泛型使用方括号`[]`，下标引用使用圆括号`()`。
* `0 to 2`里的`to`是方法名，相当于`(0).to(2)`，它返回一个Scala的包含`0,1,2`的`iterator`。Scala里的`+,-,*,/`都是方法名。
* 在Scala里，Array也是类。当在实例后面使用`()`，Scala会调用一个叫`apply`的方法。`greetStrings(i)`相当于`greetStrings.apply(i)`。不止Array，任何实例后面接`()`都会调用对应的`apply`。
* 类似地，`greetStrings(0) = "Hello"`相当于`greetStrings.update(0, "Hello")`
* 不像其他语言，上述的这种变换在Scala里不会明显影响性能。Scala编译器会尽量把他们优化成Java的本地数组或者原始类型(primitive type，像int一类)。

##关于List和Tuple

* 如上例所示，Scala的Array是同一类型的值的可修改的序列。像上述的`Array[String]`，虽然长度不可修改，但内容可修改。所以Array是可修改的。（为避免混乱，本笔记系列会把所有mutable都译成可修改，反之immutable都译成不可修改）
* 比Array更适合函数语言的，是List，它不可修改。它也是同一类型的值的序列。跟Java的`java.util.List`不一样，Scala的List，也就是`scala.List`，它是总不可修改的。它是为函数风格编程设计的。

###List的示例

~~~scala
val oneTwo = List(1, 2)
val threeFour = List(3, 4)
val oneTwoThreeFour = oneTwo ::: threeFour

val twoThree = List(2, 3)
val oneTwoThree = 1 :: twoThree
~~~

* List的创建没用`new`，因为它调用的是List的伴生对象（companion object，后述）的`apply`方法。
* 两个List相接，用`:::`。它用于把一个List接到另一个List的*前面*，也就是`prepend`。
* 在Scala里一般的方法是从左到右的，比如`a * b`相当于`a.*(b)`。而`:`结尾的方法则反之，`a ::: b`相当于`b.:::(a)`，所以说是把a接于b的*前面*。
* `::` 念“cons”。cons用来把一个元素接到一个List*前面*，与`:::`同理。
* List是没有`append`的，因为它的时间复杂度是O(n)。

### 题外话：关于List的时间复杂度

自己总结了一下Scala的List的一些性能资料[^1][^2]：

* List是为后入先出（LIFO）优化的，类似Stack。如果要使用其他访问方式，别用List。
* List只有`prepend`，访问链首or链尾三个操作是O(1)的，其他如随机访问，`length`，`append`，`reverse`都是O(n)的。
* Java的List的话则有两个实现`ArrayList`和`LinkedList`，伪代码[^3]如下

~~~java
public ArrayList<T> {
    private Object[] array;
    private int size;
}
~~~

~~~java
public LinkedList<T> {
    class Node<T> {
        T data;
        Node next;
        Node prev;
    }
    private Node<T> first;
    private Node<T> last;
    private int size;
}
~~~

可以看出来，随机访问的话，ArrayList快，增删的话，LinkedList比较好[^4]。

### Scala的Tuple

* 跟List一样，是不可修改的。
* 跟List不同，Tuple的各个值类型是不一样的。
* Tuple非常适合同时返回多个值。（Python等语言也可以，非常实用。Java的话只能用类似JavaBean或直接修改输入，超级恶心）

~~~scala
val pair = (99, "Luftballons")
println(pair._1)
println(pair._2)
~~~

* 可以直接用`()`生成。Scala会自动判断类型，这里是生成了一个可容纳两个值的`Tuple2[Int, String]`。
* 不可以像List那样通过像`pair(1)`一样的下标访问的方式使用，因为Tuple各个值类型不一样。只能通过像`pair._1`一样访问
* 数据序号从1开始，Tuple最多只能到`Tuple22`,容纳22个值。这个数字其实是任意的。

### Set与Map

* Scala里的List总是不可修改的，Array总是可修改的。然而Set与Map则同时有可修改与不可修改两套实现。
* 无论Set或Map，它们的可修改不可修改版都是从同一个trait继承出来的两个subtrait，然后再继承成类。

![Class hierarchy for Scala Sets]({{ site.baseurl }}/images/scala_set_hierarchy.png "Class hierarchy for Scala Sets")

![Class hierarchy for Scala Haps]({{ site.baseurl }}/images/scala_map_hierarchy.png "Class hierarchy for Scala Maps")

#### 这是可修改Set的例子：

~~~scala
import scala.collection.mutable.HashSet

val jetSet = new HashSet[String]
jetSet += "Lear"
jetSet += ("Boeing", "Airbus")
println(jetSet.contains("Cessna"))
~~~

* HashSet要事先import。
* `("Boeing", "Airbus")`，这不是Tuple，而是相当于`jetSet.+=("Boeing", "Airbus")`。要传入Tuple的话，要使用`jetSet += (("Boeing", "Airbus"))`，双圆括号。


#### 这是不可修改Set的例子：

~~~scala
val movieSet = Set("Hitch", "Shrek")
println(movieSet)
~~~

* 这里用了`scala.collection.Set`的伴生对象的工厂方法，这个对象是自动import的。

#### 这是可修改的HashMap的例子：

~~~scala
import scala.collection.mutable.HashMap

treasureMap += (1 -> "Go to island.")treasureMap += (2 -> "Find big X on ground.")treasureMap += (3 -> "Dig.")println(treasureMap(2))
~~~

* `1 -> "Go to island."`相当于`(1).->("Go to island.")`，相当于生成一个两个元素的Tuple

~~~scala
val romanNumeral = Map(	1 -> "I",	2 -> "II",	3 -> "III",
	4 -> "IV",
	5 -> "V" )
println(romanNumeral(4))
~~~

* 跟不可修改的Set差不多，也是工厂方法。这个工厂方法就是`Map(...)`，也就是`Map.apple(...)`。

未完待续。



[^1]: <http://www.scala-lang.org/api/2.11.7/index.html#scala.collection.immutable.List>
[^2]: <http://docs.scala-lang.org/overviews/collections/performance-characteristics.html>
[^3]: <http://stackoverflow.com/questions/10656471/performance-differences-between-arraylist-and-linkedlist>
[^4]: <https://docs.oracle.com/javase/8/docs/api/java/util/List.html>