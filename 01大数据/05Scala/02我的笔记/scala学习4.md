# 1 嵌套类

​		在Scala中，你几乎可以在任何语法结构中内嵌任何语法结构。如在类中可以再定义一个类，这样的类是嵌套类，其他语法结构也是一样。

嵌套类类似于Java中的内部类**。**

Java中，类共有五大成员，属性、方法、内部类、构造器、代码块。

## 1、Java内部类的简单回顾

​		在Java中，一个类的内部又完整的嵌套了另一个完整的类结构。被嵌套的类称为内部类(inner class)，嵌套其他类的类称为外部类。

​		内部类最大的特点就是可以直接访问私有属性，并且可以体现类与类之间的包含关系。

```java
class Outer { //外部类

  class Inner {} //内部类
}

class Other {}  //外部其他类
```

（1）Java内部类的分类

从定义在外部类的**成员位置**上来看，

（a）成员内部类（没用static修饰）

（b） 静态内部类（使用static修饰）

定义在外部类**局部位置上（**比如方法内）来看：

（a）分为局部内部类（有类名）

（b）匿名内部类（没有类名）

```java
public class TestJavaClass {
    public static void main(String[] args) {
        //创建一个外部类对象
        OuterClass outer1 = new OuterClass();
        //创建一个外部类对象
        OuterClass outer2 = new OuterClass();

        // 创建Java成员内部类
        // 说明在Java中，将成员内部类当做一个属性，因此使用下面的方式来创建 outer1.new InnerClass().
        OuterClass.InnerClass inner1 = outer1.new InnerClass();
        OuterClass.InnerClass inner2 = outer2.new InnerClass();

        //下面的方法调用说明在java中，内部类只和类型相关，也就是说,只要是
        //OuterClass.InnerClass 类型的对象就可以传给 形参 InnerClass ic
        inner1.test(inner2);
        inner2.test(inner1);

        // 创建Java静态内部类
        // 因为在java中静态内部类是和类相关的，使用 new OuterClass.StaticInnerClass()
        OuterClass.StaticInnerClass staticInner = new OuterClass.StaticInnerClass();
    }
}

class OuterClass { //外部类
    class InnerClass { //成员内部类
        public void test(InnerClass ic) {
            System.out.println(ic);
        }
    }

    static class StaticInnerClass { //静态内部类
    }
}
```

## 2、Scala嵌套类的使用

定义Scala的成员内部类和静态内部类，并创建相应的对象实例，并在内部类中访问外部内的属性。

```scala
object ScalaInnerClassDemo01 {
  def main(args: Array[String]): Unit = {
    val outer1: ScalaOuterClass = new ScalaOuterClass();
    val outer2: ScalaOuterClass = new ScalaOuterClass();

    /**
     * 1、Scala创建内部类的方式和Java不一样，将new关键字放置在前
     * 使用  对象.内部类  的方式创建
     * 2、默认情况下，内部类实例和外部对象关联
     */
    val inner1 = new outer1.ScalaInnerClass()
    val inner2 = new outer2.ScalaInnerClass()

    /**
     * 1、测试使用inner1，调用info()
     * 在内部类中访问外部类的属性。
     */
    inner1.info()

    //创建静态内部类对象，和java一样
    val staticInner = new ScalaOuterClass.ScalaStaticInnerClass()
    println(staticInner)
  }
}

class ScalaOuterClass {
  
  //  var name: String = "scott"
  //  private var sal: Double = 1.2
  //
  //  class ScalaInnerClass { //成员内部类
  /**
   * 方式1:在内部类中访问外部内的属性
   * 内部类如果想要访问外部类的属性，可以通过外部类对象访问。
   * 即：访问方式：外部类名.this.属性名
   */
  //    def info() = {
  //      // 访问方式：外部类名.this.属性名
  //      // 怎么理解 ScalaOuterClass.this 就相当于是 ScalaOuterClass 这个外部类的一个实例,
  //      // 然后通过 ScalaOuterClass.this 实例对象去访问 name 属性
  //      // 只是这种写法比较特别，学习java的同学可能更容易理解 ScalaOuterClass.class 的写法.
  //      println("name = " + ScalaOuterClass.this.name
  //        + " sal =" + ScalaOuterClass.this.sal)
  //    }
  //  }

  /**
   * 方式2：
   * 内部类如果想要访问外部类的属性，也可以通过外部类别名访问(推荐)。
   * 即：访问方式：外部类名别名.属性名   （外部类名.this  等价 外部类名别名）
   * myOuter => 这样写，你可以理解成这样写，myOuter就是代表外部类的一个对象.
   */
  myOuter => //这样写，你可以理解成这样写，myOuter就是代表外部类的一个对象.
  class ScalaInnerClass { //成员内部类
    def info() = {
      println("name = " + ScalaOuterClass.this.name
        + " age =" + ScalaOuterClass.this.sal)
      println("-----------------------------------")
      println("name ~= " + myOuter.name
        + " sal~ =" + myOuter.sal)
    }
  }

  // 当给外部指定别名时，需要将外部类的属性放到别名后.
  var name: String = "scott"
  private var sal: Double = 1.2
}

object ScalaOuterClass { //伴生对象
  class ScalaStaticInnerClass { //静态内部类
  }

}
```

## 3、类型投影

​		类型投影是指：在方法声明上，如果使用 **外部类#内部类** 的方式，表示忽略内部类的对象关系，等同于Java中内部类的语法操作，我们将这种方式称之为 类型投影（即：忽略对象的创建方式，只考虑类型）。

```scala
myOuter => //这样写，你可以理解成这样写，myOuter就是代表外部类的一个对象.
class ScalaInnerClass { //成员内部类

  /**
   * 1、投影（外部类加进来：ScalaOuterClass#）：编译器会知道接收内部类
   * 不要考虑外部类的对象，只需要接收内部类就可以了。
   * 作用：屏蔽外部对象对于内部实例的影响
   * 2、inner1.test(inner2) 不会报错
   */
  def test(ic: ScalaOuterClass#ScalaInnerClass): Unit = {
    System.out.println(ic)
  }

}

/**
* 1、在默认情况下，scala的内部类的实例和创建该内部实例
* 的外部对象相关联
* 2、inner1在Scala嵌套类的使用中创建。
 */
inner1.test(inner1)
inner1.test(inner2) //类型投影解决报错
```

# 2 隐式转换和隐式参数

## 1、介绍

隐式函数基本介绍：隐式转换函数是以implicit关键字声明的带有单个参数的函数。这种函数将会自动应用，将值从一种类型转换为另一种类型。目的：指定某些数据类型的相互转化。

入门案例：

```scala
object ImplitcitDemo01 {
  def main(args: Array[String]): Unit = {


    //编写一个隐式函数转换成Double -> Int转换
    //隐式函数应该在作用域中才能生效
    implicit def f1(d: Double): Int = { //底层生成 f1$1
      d.toInt
    }

    //引用了隐式转换
    var num: Int = 3.5 //底层编译 f1$1(3.5)
    println("num=" + num)
  }
}
```

## 2、隐式转换的注意事项和细节

（1）隐式转换函数的函数名可以是任意的，隐式转换与函数名称无关，只与函数**签名**（函数参数类型和返回值类型）有关。

（2）隐式函数可以有多个(即：隐式函数列表)，但是需要保证在当前环境下，只有一个隐式函数能被识别。

```scala
//在当前环境中，不能存在满足条件的多个隐式函数
implicit def a(d: Double) = d.toInt
implicit def b(d: Double) = d.toInt 

val i1: Int = 3.5 //（X）在转换时，识别出有两个方法可以被使用，就不确定调用哪一个，所以出错
println(i1)
```

## 3、用隐式转换丰富类库功能

​		如果需要为一个类增加一个方法，可以通过隐式转换来实现。（动态增加功能）比如想为MySQL类增加一个delete方法。

分析：

​		当前程序中，如果想要给MySQL类增加功能是非常简单的，但是在实际项目中，如果想要增加新的功能就会需要改变源代码，这是很难接受的。而且违背了软件开发的OCP开发原则 (闭合原则 open close priceple) 。在这种情况下，可以通过隐式转换函数给类动态添加功能。

```scala
object ImplicitDemo02 {
  def main(args: Array[String]): Unit = {
    //编写一个隐式函数，丰富mysql功能
    implicit def addDelete(mysql: MySQL): DB = {
      new DB
    }

    //创建对象
    val mysql = new MySQL
    mysql.insert() //insert
    mysql.delete() //delete
  }
}

class MySQL {
  def insert(): Unit = {
    println("insert")
  }
}

class DB {
  def delete(): Unit = {
    println("delete")
  }
}
```

## 4、隐式值

​		隐式值也叫**隐式变量**，将某个形参变量标记为`implicit`，所以编译器会在方法省略隐式参数的情况下去搜索作用域内的隐式值作为缺省参数。

```scala
object ImplicitDemo03 {
  def main(args: Array[String]): Unit = {
    //隐式值
    implicit val str1: String = "jack"

    //implicit name: String：name就是隐式参数
    def hello(implicit name: String): Unit = {
      println(name + " hello")
    }
    //底层 hello("jack")
    hello //调用时不用带()
  }
}
```

## 5、隐式类

​		在scala2.10后提供了隐式类，可以使用`implicit`声明类，隐式类的非常强大，同样可以扩展类的功能，比前面使用隐式转换丰富类库功能更加的方便，在集合中隐式类会发挥重要的作用。

1、隐式类使用有如下几个特点：

（1）其所带的构造参数有且只能有一个。

（2）隐式类必须被定义在“类”或“伴生对象”或“包对象”里，即隐式类不能是顶级的(top-level objects)。

（3）隐式类不能是case class。

（4）作用域内不能有与之相同名称的标识符。

```scala
object ImplicitDemo04 {
  def main(args: Array[String]): Unit = {
    /**
     * 1、DB1会对应生成隐式类
     * 2、DB1是一个隐式类，当我们在该隐式类的作用域范围内，
     * 创建 MySQL1 的实例时，该隐式类就会生效。
     * 这个工作仍然时编译器实现的。
     */
    implicit class DB1(val m: MySQL1) {
      def addSuffix(): String = {
        m + " scala"
      }
    }

    val mysql1 = new MySQL1
    mysql1.sayOk()
    //mysql1.addSuffix()  ==> 使用这个进行关联：DB1$1(mysql1).addSuffix()
    //DB1$1(mysql1)返回的类型是 ImplicitClass$DB1$2
    println(mysql1.addSuffix())
  }
}

class MySQL1 {
  def sayOk(): Unit = {
    println("sayOk")
  }
}
```

控制台输出结果：

```ini
sayOk
com.xiaolun.chapter09.MySQL1@35bbe5e8 scala
```

隐式的转换时机：

（1）当方法中的参数的类型与目标类型不一致时。

（2）当对象调用所在类中不存在的方法或成员时，编译器会自动将对象进行隐式转换（根据类型）。

2、隐式解析机制

（1）首先会在当前代码作用域下查找隐式实体（隐式方法、隐式类、隐式对象）。(**一般是这种情况**)

（2）如果第一条规则查找隐式实体失败，会继续在隐式参数的类型的作用域里查找。类型的作用域是指与该类型相关联的全部伴生模块，一个隐式实体的类型T它的查找范围如下(**第二种情况范围广且复杂在使用时，应当尽量避免出现**)：

（a）如果T被定义为T with A with B with C,那么A,B,C都是T的部分，在T的隐式解析过程中，它们的伴生对象都会被搜索。

（b）如果T是参数化类型，那么类型参数和与类型参数相关联的部分都算作T的部分，比如List[String]的隐式搜索会搜索List的伴生对象和String的伴生对象。

（c）如果T是一个单例类型p.T，即T是属于某个p对象内，那么这个p对象也会被搜索。

（d）如果T是个类型注入S#T，那么S和T都会被搜索。

3、隐式转换的前提

在进行隐式转换时，需要遵守两个基本的前提：

（1）不能存在二义性。

（2）隐式操作不能嵌套使用。

<img src="D:\04桌面\Spring Boot学习\03学习文件\scala学习4.assets\image-20200801212648497.png" alt="image-20200801212648497" style="zoom: 80%;" />

# 3 模式匹配

​		Scala中的模式匹配类似于Java中的switch语法，但是更加强大。

​		模式匹配语法中，采用`match`关键字声明，每个分支采用`case`关键字进行声明，当需要匹配时，会从第一个`case`分支开始，如果匹配成功（代码块），那么执行对应的逻辑代码，如果匹配不成功，继续执行下一个分支进行判断。如果所有`case`都不匹配，那么会执行`case _` 分支，类似于Java中`default`语句。

## 1、入门案例

```scala
object MatchDemo01 {
  def main(args: Array[String]): Unit = {
    val oper = '#'
    val n1 = 20
    val n2 = 10
    var res = 0

    /**
     * 1、match(类似于java中的switch)和case是关键字
     * 2、每个代码块不需要写break
     */
    oper match {
      case '+' => res = n1 + n2
      case '-' => res = n1 - n2
      case '*' => res = n1 * n2
      case '/' => res = n1 / n2
      case _ => println("oper error")
    }
    println("res=" + res)
  }
}
```

注意事项：

+ 如果所有case都不匹配，那么会执行**case _** 分支，类似于Java中default语句。

+ 如果所有case都不匹配，又没有写**case** **_** 分支，那么会抛出MatchError。

+ 每个case中，不用break语句，自动中断case。

+ 可以在match中使用其它类型，而不仅仅是字符。

+ => 等价于 java swtich 的 :

+ => 后面的代码块到下一个 case， 是作为一个整体执行，可以使用{} 扩起来，也可以不扩。 

## 2、Match中的守卫

如果想要表达**匹配某个范围的数据**，就需要在模式匹配中增加条件守卫。

```scala
object MatchDemo02 {
  def main(args: Array[String]): Unit = {
    for (ch <- "+-3!") {
      var sign = 0
      var digit = 0
      ch match {
        case '+' => sign = 1
        case '-' => sign = -1

        /**
         * 1、如果case后面又条件守卫即if,那么这时的 _ 不是表示默认匹配，而是隐藏变量名，即表示传入的 ch 不接收。
         */
        case _ if ch.toString.equals("3") => digit = 3
        case _ => sign = 2
      }
      println(ch + " " + sign + " " + digit)
    }
  }
}
```

输出结果：

```ini
+ 1 0
- -1 0
3 0 3
! 2 0
```

## 3、模式中的变量

如果在case关键字后跟变量名，那么match前表达式的值会赋给那个变量。

```scala
val ch = 'V'
ch match {
  case '+' => println("ok~")
  case mychar => println("ok~" + mychar)
  case _ => println("ok~~")
}

//match是一个表达式，因此可以有返回值,返回值就是匹配到的代码块的最后一句话的值。
var res = ch match{}
```

控制台输出：

```ini
ok~V
```

## 4、类型匹配

​		可以匹配对象的任意类型，这样做避免了使用`isInstanceOf`和`asInstanceOf`方法。

```scala
object MatchTypeDemo01 {
  def main(args: Array[String]): Unit = {
    val a = 7

    //根据a的值来返回obj实例的类型
    val obj = if (a == 1) 1
    else if (a == 2) "2"
    else if (a == 3) BigInt(3)
    else if (a == 4) Map("aa" -> 1)
    else if (a == 5) Map(1 -> "aa")
    else if (a == 6) Array(1, 2, 3)
    else if (a == 7) Array("aa", 1)
    else if (a == 8) Array("aa")

    //根据obj的类型来匹配
    val result = obj match {
      case a: Int => a
      case b: Map[String, Int] => "对象是一个字符串-数字的Map集合"
      case c: Map[Int, String] => "对象是一个数字-字符串的Map集合"
      case d: Array[String] => "对象是一个字符串数组"
      case e: Array[Int] => "对象是一个数字数组"
      case f: BigInt => Int.MaxValue
      case _ => "啥也不是"
    }
      //啥也不是
    println(result)
  }
}
```

1、注意事项

（1）Map[String, Int] 和Map[Int, String]是两种不同的类型，其它类推。

（2）在进行类型匹配时，编译器会**预先检测是否有可能的匹配**，如果没有则报错。

```scala
val obj = 10
val result = obj match {
case a : Int => a
    //报错，编译器认为b这种情况不可能发生，多管闲事。
case b : Map[String, Int] => "Map集合"
case _ => "啥也不是"
}
```

（3）一个说明

```scala
//case i : Int => i 表示 将 i = obj ，然后再判断类型
val result = obj match {
	case i: Int => i
}
```

（4）如果 case _ 出现在match 中间，则表示**隐藏变量名**，即不使用,而不是表示默认匹配。

```scala
case _ : BigInt => Int.MaxValue 
```

## 5、匹配数组

1、基本介绍

（1）Array(0) 匹配只有一个元素且为0的数组。

（2）Array(x,y) 匹配数组有两个元素，并将两个元素赋值为x和y。当然可以依次类推Array(x,y,z) 匹配数组有3个元素的等等....。

（3）Array(0,_*) 匹配数组以0开始。

```scala
for (arr <- Array(Array(0), Array(1, 0), Array(0, 1, 0),
Array(1, 1, 0), Array(1, 1, 0, 1))) {
    val result = arr match {
    case Array(0) => "0"
    case Array(x, y) => x + "=" + y
    case Array(0, _*) => "以0开头和数组"
    case _ => "什么集合都不是"
}
	println("result = " + result)
} 
```

## 6、匹配列表

```scala
object MatchListDemo01 {
  def main(args: Array[String]): Unit = {
    for (list <- Array(List(0),List(88),List(1, 0), List(0, 0, 0), List(1, 0, 0))) {
      val result = list match {
        case 0 :: Nil => "0" //匹配只有一个元素且为0的list
        case x :: Nil => x  //匹配 List(88) 这样的只含有一个元素的列表,并原值返回
        case x :: y :: Nil => x + " " + y //list有两个元素
        case 0 :: tail => "0 ..." // 如果list是以0开头，后面有任意元素
        case _ => "something else"
      }

      /**
       * 0
       * 88
       * 1 0
       * 0 ...
       * something else
       */
      println(result)
    }
  }
}
```

## 7、匹配元组

```scala
object MatchTuple01 {
  def main(args: Array[String]): Unit = {
    for (pair <- Array((0, 1), (1, 0), (1, 1), (1, 0, 2))) {
      val result = pair match {
        case (0, _) => "0 ..." //匹配以0打头的二元组，第二个元素忽略
        case (y, 0) => y // 匹配第二个元素为0的二元组，并将值赋给y
        case _ => "other" //
      }

      /**
       * 0 ...
       * 1
       * other
       * other
       */
      println(result)
    }
  }
}
```

## 8、对象匹配

1、规则

（1）`case`中对象的`unapply`方法(对象提取器)返回`Some`集合则为匹配成功。

（2）返回`none`集合则为匹配失败。

2)返回none集合则为匹配失败

2、案例一-单个参数

```scala
object MatchObjectDemo01 {
  def main(args: Array[String]): Unit = {

    // 模式匹配使用：
    val number: Double = 36.0
    number match {
      /**
       * case Square(n)是运行的机制
       * 1、当匹配到case Square(n)
       * 2、会调用Square的 unapply(z: Double) 方法
       * z的值就是number的值unapply(z: Double)
       * 3、如果对象提取器返回的是some(6),
       * 则表示匹配成功，同时将some(6)中的6放到Square(n)中的n中。
       */
      case Square(n) => println("匹配成功："+n)
      case _ => println("nothing matched")
    }
  }

  object Square {
    /**
     * 1、unapply方法为对象提取器
     * 2、接收z: Double类型
     * 3、返回一个Option[Double]集合
     * 4、Some(math.sqrt(z))将传入的z进行开平方，并放入到Some(x)
     */
    def unapply(z: Double): Option[Double] = {
      println("unapply被调用 z = "+ z)
      Some(math.sqrt(z))
    }

    def apply(z: Double): Double = z * z
  }
}
```

控制台输出：

```ini
unapply被调用 z = 36.0
匹配成功：6.0
```

小结：

+ 构建对象时apply会被调用 ，比如 val n1 = Square(5)。

+ 当将 Square(n) 写在 case 后时[case Square(n) => xxx]，会默认调用unapply 方法(**对象提取器**)。

+ number 会被 传递给def unapply(z: Double) 的 z 形参。

+ 如果返回的是Some集合，则unapply提取器返回的结果会返回给 n 这个形参。
+ case中对象的unapply方法(提取器)返回some集合则为匹配成功。

5)case中对象的unapply方法(提取器)返回some集合则为匹配成功

+ 返回none集合则为匹配失败。

3、案例二-多个参数



```scala
object MatchObjectDemo02 {
  def main(args: Array[String]): Unit = {
    val namesString = "Alice,Bob,Thomas"

    /**
     * 当执行 case Names(first, second, third)时
     * 1、会调用unapplySeq(str: String)，并把"Alice,Bob,Thomas"
     * 传入 str
     * 2、如果返回的时some("Alice","Bob","Thomas"),分别给(first, second, third)
     * 注意，此时返回值的个数和Names中的参数格式要保持一致，否则匹配失败
     * 3、返回none表示匹配失败
     */
    namesString match {
      case Names(first, second, third) => {
        println("the string contains three people's names")
        // 打印字符串
        println(s"$first $second $third")
      }
      case _ => println("nothing matched")
    }
  }

  object Names {
    /**
     * 1、当构造器是多个参数时，就会触发unapply对象提取器
     */
    def unapplySeq(str: String): Option[Seq[String]] = {
      if (str.contains(",")) Some(str.split(","))
      else None
    }
  }

}
```

小结：

+ 当case 后面的对象提取器方法的参数为多个，则会默认调用def unapplySeq() 方法

+ 如果unapplySeq返回是Some，获取其中的值,判断得到的sequence中的元素的个数是否是三个,如果是三个，则把三个元素分别取出，赋值给first，second和third。

## 9、变量声明中的模式

match中每一个case都可以单独提取出来。

```scala
val (x, y) = (1, 2)

val (q, r) = BigInt(10) /% 3  //说明  q = BigInt(10) / 3 r = BigInt(10) % 3

val arr = Array(1, 7, 2, 9)
val Array(first, second, _*) = arr // 提出arr的前两个元素
println(first, second)
```

## 10、for表达式中的模式

for循环也可以进行模式匹配。

```scala
val map = Map("A"->1, "B"->0, "C"->3)
//传统方式
for ( (k, v) <- map ) {
	println(k + " -> " + v)
}

//for中模式匹配，只要value = 10的键值对。
for ((k, 0) <- map) {
	println(k + " --> " + 0)
}

//使用守卫
for ((k, v) <- map if v == 0) {
	println(k + " ---> " + v)
}
```

## 11、样例类

1、入门案例

```
object CaseDemo01 {
  def main(args: Array[String]): Unit = {
    println("xxx")
  }
}


abstract class Amount

//下面三个都是样例类
case class Dollar(value: Double) extends Amount

case class Currency(value: Double, unit: String) extends Amount

case object NoAmount extends Amount
```

基本介绍：

+ 样例类仍然是类。

+ 样例类用case关键字进行声明。

+ 样例类是为模式匹配而优化的类。

+ 构造器中的每一个参数都成为val——除非它被显式地声明为var（不建议这样做）。

+ 在样例类对应的伴生对象中提供apply方法让你不用new关键字就能构造出相应的对象。

+ 提供unapply方法让模式匹配可以工作。

+ 将自动生成toString、equals、hashCode和copy方法(有点类似模板类，直接给生成，供程序员使用)。

+ 除上述外，样例类和其他类完全一样。你可以添加方法和字段，扩展它们。

2、案例1

​		当我们有一个类型为`Amount`的对象时，可以用模式匹配来匹配他的类型，并将属性值绑定到变量(即：把样例类对象的属性值提取到某个变量,该功能有用)。

```scala
object CaseDemo01 {
  def main(args: Array[String]): Unit = {
    /**
     * 1、该案例的作用就是体验使用样例类方式进行对象匹配的间接性
     * 2、不需要写unapply方法
     */
    for (amt <- Array(Dollar(1000.0), Currency(1000.0, "RMB"), NoAmount)) {
      val result = amt match {
        //说明
        case Dollar(v) => "$" + v
        //说明
        case Currency(v, u) => v + " " + u
        case NoAmount => ""
      }

      /**
       * Dollar(1000.0): $1000.0
       * Currency(1000.0,RMB): 1000.0 RMB
       * NoAmount:
       */
      println(amt + ": " + result)
    }
  }
}


abstract class Amount

//下面三个都是样例类
case class Dollar(value: Double) extends Amount

case class Currency(value: Double, unit: String) extends Amount

case object NoAmount extends Amount
```

3、案例2

​		样例类在创建的时候带有copy方法。我们可以使用`copy`创建一个与现有对象值相同的新对象，并可以通过带名参数来修改某些属性。

```scala
object CaseDemo02 {
  def main(args: Array[String]): Unit = {
    val amt = Currency(29.95, "RMB")

    /**
     * 1、创建了一个新的对象，但是属性值一样
     * 2、类似于克隆
     * 3、copy()没有传参数的原因是因为Currency已经
     * 有参数了
     */
    val amt1 = amt.copy()
    //创建了一个新对象，但是修改了value
    val amt2 = amt.copy(value = 19.95)
    val amt3 = amt.copy(unit = "英镑") 

    /**
     * Currency(29.95,RMB)
     * Currency(19.95,RMB)
     * Currency(29.95,英镑)
     */
    println(amt)
    println(amt2)
    println(amt3)
  }
}
```

3、case语句的中置(缀)表达式

`1 + 2`，就是一个中置表达式。比如可以匹配一个List序列。

```scala
object CaseDemo03 {
  def main(args: Array[String]): Unit = {
    List(1, 3, 5, 9) match { 
      //1.两个元素间::叫中置表达式,至少first，second两个匹配才行.
      //2.first 匹配第一个 second 匹配第二个, rest 匹配剩余部分(5,9)
      //1 + 3 + 2 = 6
      case first :: second :: rest => println(first + second + rest.length)
      case _ => println("匹配不到...")
    }
  }
}
```

## 12、匹配嵌套结构

操作原理类似于正则表达式。

1、入门案例

​		现在有一些商品，请使用Scala设计相关的样例类，完成商品捆绑打折出售。要求：

（1）商品捆绑可以是单个商品，也可以是多个商品。

（2）打折时按照折扣x元进行设计。

（3）能够统计出所有捆绑商品打折后的最终价格。

2、分析

（1）创建的样例类

```scala
abstract class Item // 项

case class Book(description: String, price: Double) extends Item

//Bundle 捆 ， discount: Double 折扣 ， item: Item* ,可变参数
case class Bundle(description: String, discount: Double, item: Item*) extends Item
```

（2）匹配嵌套结构(就是Bundle的对象)

```scala
//给出案例表示有一捆数，单本漫画（40-10） +文学作品(两本书)（80+30-20） = 30 + 90 = 120.0
    val sale = Bundle("书籍", 10, Book("漫画", 40),
      Bundle("文学作品", 20, Book("《阳关》", 80), Book("《围城》", 30)))
```

完整代码：

```scala
object SalesDemo01 {
  def main(args: Array[String]): Unit = {
    //给出案例表示有一捆数，单本漫画（40-10） +文学作品(两本书)（80+30-20） = 30 + 90 = 120.0
    val sale = Bundle("书籍", 10, Book("漫画", 40),
      Bundle("文学作品", 20, Book("《阳关》", 80), Book("《围城》", 30)))

    //知识点补充1：将desc绑定到第一个Book的描述。
    val res = sale match {
      //如果我们进行对象匹配时，不想接受某些值，则使用_ 忽略即可，_* 表示所有
      case Bundle(_, _, Book(desc, _), _*) => desc
    }
    //res = 漫画
    println("res = " + res)

    println("---------------------------")
    /**
     * 1、知识点补充2：通过@表示法将嵌套的值绑定到变量。
     * 2、Book("漫画", 40),
     * Bundle("文学作品", 20, Book("《阳关》", 80), Book("《围城》", 30))
     * 绑定到指定变量（变量名任意），并以元组返回
     */
    val result2 = sale match {
      case Bundle(_, _, art@Book(_, _), rest@_*) => (art, rest)
    }

    /**
     * (Book(漫画,40.0),WrappedArray(Bundle(文学作品,20.0,WrappedArray(Book(《阳关》,80.0), Book(《围城》,30.0)))))
     * art =Book(漫画,40.0)
     * rest=WrappedArray(Bundle(文学作品,20.0,WrappedArray(Book(《阳关》,80.0), Book(《围城》,30.0))))
     */
    println(result2)
    println("art =" + result2._1)
    println("rest=" + result2._2)

    println("---------------------------")
    /**
     * 知识点补充3：不使用_*绑定剩余Item到rest
     */
    val result3 = sale match {
      //说明因为没有使用 _* 即明确说明没有多个Bundle,所以返回的rest，就不是WrappedArray了。
      case Bundle(_, _, art @ Book(_, _), rest) => (art, rest)
    }

    /**
     * (Book(漫画,40.0),Bundle(文学作品,20.0,WrappedArray(Book(《阳关》,80.0), Book(《围城》,30.0))))
     * art =Book(漫画,40.0)
     * Bundle前面的WrappedArray拿掉了
     * rest=Bundle(文学作品,20.0,WrappedArray(Book(《阳关》,80.0), Book(《围城》,30.0)))
     */
    println(result3)
    println("art =" + result3._1)
    println("rest=" + result3._2)
  }
}

//设计的样例类
abstract class Item // 项

case class Book(description: String, price: Double) extends Item

//Bundle 捆 ， discount: Double 折扣 ， item: Item* ,可变参数
case class Bundle(description: String, discount: Double, item: Item*) extends Item
```

3、实现

```scala
object SalesDemo02 {
  def main(args: Array[String]): Unit = {
    val sale = Bundle("书籍", 10, Book("漫画", 40),
      Bundle("文学作品", 20, Book("《阳关》", 80), Book("《围城》", 30)))
    var res = price(sale)
    println("res = "+res) //120.0
  }

  def price(it: Item): Double = {
    it match {
      case Book(_, p) => p
      /**
       * 1、生成一个新的集合,_是将its中每个循环的元素传递到price中it中。
       * 递归操作。
       */
      case Bundle(_, disc, its@_*) => its.map(price _).sum - disc
    }
  }
}
```

<img src="D:\04桌面\Spring Boot学习\03学习文件\scala学习4.assets\image-20200802180627952.png" alt="image-20200802180627952" style="zoom:80%;" />

## 13、密封类

（1）如果想让case类的所有子类都必须在申明该类的**相同的源文件中定义**，可以将样例类的通用超类声明为`sealed`，这个超类称之为密封类。

（2）密封就是不能在其他文件中定义子类。

<img src="D:\04桌面\Spring Boot学习\03学习文件\scala学习4.assets\image-20200802180934665.png" alt="image-20200802180934665" style="zoom:80%;" />