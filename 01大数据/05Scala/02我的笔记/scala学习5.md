# 1 函数式编程高级部分

## 1、偏函数

### 1、需求

给你一个集合val list = List(1, 2, 3, 4, "abc") ，完成如下要求:

+ 将集合list中的所有数字+1，并返回一个新的集合。
+ 要求忽略掉 非数字 的元素，即返回的 新的集合 形式为 (2, 3, 4, 5)。

（1）方法1-map+fliter

```scala
object PartialFunDemo01 {
  def main(args: Array[String]): Unit = {
    val list = List(1, 2, 3, 4, "abc")

    //思路1：使用map+fliter的思路
    def f1(n: Any): Boolean = {
      n.isInstanceOf[Int]
    }

    def f2(n: Int): Int = {
      n + 1
    }

    def f3(n: Any): Int = {
      n.asInstanceOf[Int]
    }

    /**
     * 1、f1：将list中的Int类型的数据给过滤出来，
     * 但此时过滤出来的集合对应类型为Any
     * 2、f2：对数据进行 + 1操作
     * 3、f3：将Any类型的集合编程Int类型
     */
    val list2 = list.filter(f1).map(f3).map(f2)
    //List(2, 3, 4, 5)
    println("list2=" + list2)
  }
}
```

（2）方法2-模式匹配

```scala
object PartialFunDemo02 {
  def main(args: Array[String]): Unit = {
    val list = List(1, 2, 3, 4, "abc")
    val list2 = list.map(addOne2)
    //List(2, 3, 4, 5, ()),有个()，不太好
    println(list2)
  }

  //思路2:模式匹配
  def addOne2(i: Any): Any = {
    i match {
      case x: Int => x + 1
        //代表()
      case _ =>
    }
  }
}
```

（3）基本介绍

（a）在对符合某个条件，而不是所有情况进行逻辑操作时，使用偏函数是一个不错的选择。

（b）将包在大括号内的一组case语句封装为函数，我们称之为**偏函数**，它只对会作用于指定类型的参数或指定范围值的参数实施计算，超出范围的值会忽略（未必会忽略，这取决于你打算怎样处理）。

（c）偏函数在Scala中是一个特质`PartialFunction`。

（4）方法3-偏函数

```scala
object PartialFunDemo03 {
  def main(args: Array[String]): Unit = {
    //使用偏函数解决
    val list = List(1, 2, 3, 4, "abc")
    /**
     * 1、定义一个偏函数
     * 2、PartialFunction[Any, Int]表示接收类型是Any,
     * 返回类型是Int
     * 3、def isDefinedAt(any: Any) 如果返回true，就去调用
     * apply构建一个对象实例，反之，apply不执行，x会被过滤掉（对象也不构建）
     * 4、 def apply(any: Any) 其中的any是接收来自isDefinedAt中的x的值，
     * 并执行相应逻辑后（+1返回）并返回到一个新的集合addOne3。
     */
    val addOne3 = new PartialFunction[Any, Int] {
      def isDefinedAt(x: Any) = if (x.isInstanceOf[Int]) true else false

      def apply(any: Any) = any.asInstanceOf[Int] + 1
    }
    /**
     * 1、如果使用偏函数，不能使用map
     * 应该使用collect
     */
    val list3 = list.collect(addOne3)
      //list3=List(2, 3, 4, 5)
    println("list3=" + list3)
  }
}
```

小结：

+ 使用构建特质的实现类(使用的方式是PartialFunction的匿名子类)。
+  `PartialFunction` 是个特质。
+  构建偏函数时，参数形式  `[Any, Int]`是泛型，第一个表示参数类型，第二个表示返回参数。
+ 当使用偏函数时，会遍历集合的所有元素，编译器执行流程时先执行`isDefinedAt()`如果为`true` ,就会执行 `apply`, 构建一个新的Int 对象返回。
+ 执行`isDefinedAt()` 为`false` 就过滤掉这个元素，即不构建新的Int对象。
+ `map`函数不支持偏函数，因为map底层的机制就是所有循环遍历，无法过滤处理原来集合的元素。
+ `collect`函数支持偏函数。

### 2、偏函数简化形式

​		声明偏函数，需要重写特质中的方法，有的时候会略显麻烦，而Scala其实提供了简单的方法。

1、简化形式1

```scala
def f2: PartialFunction[Any, Int] = {
    //不写case _ （默认操作）也不会报错。
  case i: Int => i + 1 // case语句可以自动转换为偏函数
    //执行其他逻辑
  case i: Double => (i*2).toInt
}
val list2 = List(1, 2, 3, 4,1.2,"ABC").collect(f2)
```

2、简化形式2

```scala
val list3 = List(1, 2, 3, 4,"ABC").collect{ case i: Int => i + 1 }
println(list3)
```

## 2、作为参数的函数

​		函数作为一个变量传入到了另一个函数中，那么该**作为参数的函数的类型**是：function1，即：(参数类型) => 返回类型。

```scala
object FunParameter01 {
  def main(args: Array[String]): Unit = {
    /**
     * 1、在scala中，函数也是有类型的，下面的类型为<function1>
     */
    def plus(x: Int) = 3 + x

    //说明
    val result1 = Array(1, 2, 3, 4).map(plus(_))
    //4,5,6,7
    println(result1.mkString(","))
  }
}
```

小结：

（1）map(plus(_)) 中的plus(_) 就是将plus这个函数当做一个参数传给了map，`_`这里代表从集合中遍历出来的一个元素。

（2）plus(_) 这里也可以写成 plus 表示对 Array(1,2,3,4) 遍历，将每次遍历的元素传给plus的 x。

（3）进行 3 + x 运算后，返回新的Int ，并加入到新的集合 result1中。

（4）def map`[B, That ](f: A => B)` 的声明中的 f: A => B 一个函数。

## 3、匿名函数

没有名字的函数就是匿名函数，可以通过`函数表达式`来设置匿名函数。

1、简单案例

```scala
/**
* 1、不需要写def
* 2、不需要写返回类型，使用类型推导
* 3、= 变成 =>
*/
val triple = (x: Double) => 3 * x

//执行函数
println(triple(3))

//正常函数定义
def getsum2(n1: Int,n2: Int):Unit = {
	return n1 + n2
}
```

（1）`(x: Double) => 3 * x` 就是匿名函数 。

（2）`(x: Double)` 是形参列表， `=>` 是规定语法表示后面是函数体， `3 * x` 就是函数体，如果有多行，可以 `{}` 换行写。匿名函数的返回是通过类型推导来实现的。

（3）`triple` 是指向匿名函数的变量，可以将其看成一个函数。

## 4、高阶函数

​		能够接受函数作为参数的函数，叫做高阶函数 (higher-order function)。可使应用程序更加健壮。

1、入门案例

```scala
//test 就是一个高阶函数，它可以接收f: Double => Double 
def test(f: Double => Double, n1: Double) = {
f(n1)
}

//sum 是接收一个Double,返回一个Double
def sum(d: Double): Double = {
d + d
}

val res = test(sum, 6.0)
println("res=" + res)
```

2、高阶函数可以返回函数类型

```scala
object HighOrderDemo01 {
  def main(args: Array[String]): Unit = {
    /**
     * 1、minusxy是一个高阶函数，
     * 因为它返回一个匿名函数(y: Int) => x - y
     * 2、返回的匿名函数，可以使用变量接收
     */
    def minusxy(x: Int) = {
      (y: Int) => x - y //匿名函数
    }

    /**
     * 执行方法1：分步执行
     * 1、f1代表 (y: Int) => 3 - y，在f1的生命周期以内
     * "3"是固定不变的。
     * 2、f1(5) 等价于 3- 5
     */
    val f1 = minusxy(3)
    println(f1(5))
    
      //执行方法2：一起执行
    println(minusxy(3)(5))
      //执行方法3：一起执行2
    //    val result3 = minusxy(3)(5)
    //    println(result3)    //-2
  }
}
```

## 5、参数（类型）推断

​		参数推断省去类型信息（在某些情况下，参数类型是可以推断出来的，如list=(1,2,3) list.map()  map中函数参数类型是可以推断的)，同时也可以进行相应的简写。

1、参数类型推断写法说明

（1）**参数类型**（类似Int）是可以推断时，可以省略参数类型。

（2）当传入的函数，只有**单个参数**时，可以省去括号。

（3）如果变量只在=>右边只出现一次，可以用_来代替。

```scala
object ParameterInfer {
  def main(args: Array[String]): Unit = {

    //1、案例1
    val list = List(1, 2, 3, 4)
    //传入匿名函数；输出(2,3,4,5)
    println(list.map((x: Int) => x + 1))
    
    //简写
    //当遍历list时，参数类型是可以推断出来的，可以省略数据类型Int
    println(list.map((x) => x + 1))
    //当传入的函数，只有单个参数时，可以省去括号  
    println(list.map(x => x + 1))
    //如果变量只在=>右边只出现一次，可以用_来代替
    println(list.map(_ + 1))

    //案例2
    println(list.reduce(f1))
    println(list.reduce((n1: Int, n2: Int) => n1 + n2))
    println(list.reduce((n1, n2) => n1 + n2))
    val res = list.reduce(_ + _) //此时f1函数可以去掉了
    println(res) //10
  }

  def f1(n1: Int, n2: Int): Int = {
    n1 + n2
  }
}
```

## 6、闭包（closure）

基本介绍：闭包就是一个函数和与其相关的引用环境组合的一个整体(实体)。

1、案例

```scala
def minusxy(x: Int) = (y: Int) => x - y

//f函数就是闭包.
val f = minusxy(20) 
println("f(1)=" + f(1)) // 19
println("f(2)=" + f(2)) // 18
```

2、小结

（1）下面返回的是一个匿名函数 ，因为该函数引用到到函数外的 x,那么 该函数和x整体形成一个闭包

```scala
(y: Int) => x – y
```

如：这里 **val** f = minusxy(20) 的f函数就是闭包。

（2）你可以这样理解，返回函数是一个对象，而x就是该对象的一个字段，他们共同形成一个闭包。

（3）当多次调用f时（可以理解多次调用闭包），发现使用的是同一个x, 所以x不变。

（4）在使用闭包时，主要搞清楚返回函数引用了函数外的哪些变量，因为他们会组合成一个整体(实体),形成一个闭包。

## 7、函数柯里化（curry）

1、介绍

（1）函数编程中，接受**多个参数的函数**都可以转化为接受**单个参数的函数**，这个转化过程就叫柯里化。

（2）柯里化就是证明了函数只需要一个参数而已。

（3）不用设立柯里化**存在的意义**这样的命题。柯里化就是以**函数为主体这种思想**发展的必然产生的结果。(即：柯里化是面向函数思想的必然产生结果)

2、入门案例

编写一个函数，接收两个整数，可以返回两个数的乘积。

```scala
object CurryDemo01 {
  def main(args: Array[String]): Unit = {
    //使用常规的方式完成
    def mul(x: Int, y: Int) = x * y

    println(mul(10, 10))

    //使用闭包的方式完成
    def mulCurry(x: Int) = (y: Int) => x * y

    println(mulCurry(10)(9))

    //使用函数柯里化完成
    def mulCurry2(x: Int)(y: Int) = x * y

    println(mulCurry2(10)(8))
  }
}
```

3、案例2

比较两个字符串在忽略大小写的情况下是否相等，注意，这里是两个任务：

1)全部转大写（或小写）

2)比较是否相等

针对这两个操作，我们用一个函数去处理的思想，其实也变成了两个函数处理的思想（柯里化）。

```scala
object CurryDemo02 {
  def main(args: Array[String]): Unit = {
    //方式2：使用稍微高级的用法(隐式类)：形式为 str.方法()
    //这是一个函数，可以接收两个字符串，比较是否相等
    def eq(s1: String, s2: String): Boolean = {
      s1.equals(s2)
    }

    /**
     * 1、体现了将比较字符串的事情，分解成两个任务完成
     * 2、checkEq 完成转换大小写
     * 3、f 完成比较任务
     * 4、运行str1.checkEq("HeLLO")(eq)时，
     * 参数 s 是 hello,ss是 HEllo。
     */
    implicit class TestEq(s: String) {
      def checkEq(ss: String)(f: (String, String) => Boolean): Boolean = {
        f(s.toLowerCase, ss.toLowerCase)
      }
    }
    val str1 = "hello"
    println(str1.checkEq("HeLLO")(eq))

    //方式3：直接传入匿名函数方式
//    str1.checkEq("HELLO")((s1: String, s2: String) =>  s1.equals(s2))
//    str1.checkEq("HELLO")((s1, s2) =>  s1.equals(s2))
    str1.checkEq("HELLO")(_.equals(_)) //true
  }

  //方式1: 简单的方式,使用一个函数完成
  def eq2(s1: String)(s2: String): Boolean = {
    s1.toLowerCase == s2.toLowerCase
  }
}
```

## 8、控制抽象

1、介绍

控制抽象是这样的函数，满足如下条件

（1）参数是函数。

（2）函数参数没有输入值也没有返回值。

2、基本使用

```scala
object AbstractControl {
  def main(args: Array[String]): Unit = {
    //    myRunInThread {
    //      () =>
    //        //下面三个行为等价于放到f1()函数中去执行
    //        println("干活咯！5秒完成...")
    //        Thread.sleep(5000)
    //        println("干完咯！")
    //    }
    /**
     * 对于没有输入也没有返回值的函数，可以简写成这样的形式
     */
    myRunInThread2 {
      // 去掉() =>，当成一个代码块去执行
      println("干活咯！5秒完成...")
      Thread.sleep(5000)
      println("干完咯！")
    }
  }

  /**
   * 1、myRunInThread是一个抽象控制
   * 2、f1: () => Unit 没有输入，也灭有输出
   */
  //  def myRunInThread(f1: () => Unit) = {
  //    new Thread {
  //      override def run(): Unit = {
  //        f1()
  //      }
  //    }.start()
  //  }
  
  def myRunInThread2(f1: => Unit) = {
    new Thread {
      override def run(): Unit = {
        f1 //简化处理，省略()以便执行代码块
      }
    }.start()
  }
}
```

3、进阶使用

实现类似while的until函数。

```scala
object AbstractControl02 {
  def main(args: Array[String]): Unit = {
    var x = 10

    //循环操作
    while(x > 0){
      x -= 1
      println("x="+x)
    }

    println("---------------------------------")
    //使用控制抽象写出until函数，实现类似while的循环效果
    x = 10
    until(x > 0) {
      x -= 1
      println("x=" + x)
    }
  }

  /**
   * 1、接收两个抽象控制，一个没有传入值，但返回boolean
   * 另一个是无传入和返回的值
   */
  def until(condition: => Boolean)(block: => Unit): Unit = {
    //类似while循环，递归
    if (condition) {
      block
      //递归调用until
      until(condition)(block)
    }
  }
}
```

# 2 递归

​		递归时告诉计算机做什么，而不是告诉计算机怎么做（迭代），因为每一步做什么不是很清晰。使代码看起来比较优雅。

1、案例

(1) 使用递归求list中的最大值

```scala
object RecursiveDemo01 {
  def main(args: Array[String]): Unit = {
      println(max1(List(1,9,-1,10)))
  }

  //使用递归求list中的最大值
  def max1(xs: List[Int]): Int = {
    if (xs.isEmpty)
      throw new java.util.NoSuchElementException
    if (xs.size == 1) //如果有一个元素，就是器本身
      xs.head
      //将头部一步步进行拆解
    else if (xs.head > max1(xs.tail)) xs.head
    else max1(xs.tail)
  }
}
```

（2）翻转字符串

```scala
object RecursiveDemo02 {
  def main(args: Array[String]): Unit = {
    var str:String = "abcd"
    println(reverse(str))
  }

  //使用递归方式来翻转字符串
  def reverse(xs: String): String =
    if (xs.length == 1) xs
      //将尾巴和头互换
    else reverse(xs.tail) + xs.head
}
```

（3）求阶乘

```scala
object RecursiveDemo03 {
  def main(args: Array[String]): Unit = {
    println(factorial(3))
  }

  def factorial(n: Int): Int =
    if (n == 0) 1
    else n * factorial(n - 1)
}
```

2、注意事项

```scala
object RecursiveDemo04 {
  def main(args: Array[String]): Unit = {
    var count = BigInt(0)
    println(fbn(21))
    //fbn(20) -> 13529;fbn(21) -> 21891
    println("使用fbn次数为：" + count)

    def fbn(n: BigInt): BigInt = {
      count += 1
      if (n == 1 || n == 2) 1
      //使用递归，重复计算时，需要考虑优化-》迭代
      else fbn(n - 1) + fbn(n - 2)
    }
  }
}
```

