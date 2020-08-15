# 1 泛型

1、案例1

编写一个Message类，可以构建Int类型和String类型的Message（不能使用Any）。

```scala
object GenericDemo01 {
  def main(args: Array[String]): Unit = {

    //传入参数时，类型已经确定了，不能乱传了
    val mes1 = new StrMessage[String]("10")
    println(mes1.get)
    val mes2 = new IntMessage[Int](20)
    println(mes2.get)
  }
}

// 在 Scala 定义泛型用[T]， s 为泛型的引用
abstract class Message[T](s: T) {
  def get: T = s
}

/**
 * 1、子类扩展的时候，约定了具体的类型
 * 2、Message(msg)中的msg类型不用在写了
 * 在父类中已经定义了
 */
class StrMessage[String](msg: String) extends Message(msg)

class IntMessage[Int](msg: Int) extends Message(msg)
```

2、案例2

（1）设计一个EnglishClass (英语班级类)，在创建EnglishClass的一个实例时，需要指定[ 班级开班季节(spring,autumn,summer,winter)、班级名称、班级类型]。

（2）开班季节只能是指定的，班级名称为String, 班级类型是(字符串类型 "高级班", "初级班"..) 或者是 Int 类型(1, 2, 3 等)。

```scala
object GenericDemo02 {
  def main(args: Array[String]): Unit = {
    val class1 = new EnglishClass[SeasonEm, String, String](SeasonEm.spring, "001班", "高级班")
    println(class1.classSeason + " " + class1.className + " " + class1.classType)

    val class2 = new EnglishClass[SeasonEm, String, Int](SeasonEm.spring, "002班", 1)
    println(class2.classSeason + " " + class2.className + " " + class2.classType)
  }
}

// 季节是枚举类型
object SeasonEm extends Enumeration {
  type SeasonEm = Value //自定义SeasonEm，是Value类型,这样才能使用
  val spring, summer, winter, autumn = Value
}

// 定义一个泛型类
class EnglishClass[A, B, C](val classSeason: A, val className: B, val classType: C)
```

3、案例3

定义一个函数，可以获取各种类型的 List 的中间index的值。

```scala
object GenericDemo03 {
  def main(args: Array[String]): Unit = {
    // 定义一个函数，可以获取各种类型的 List 的中间index的值
    val list1 = List("jack", 100, "tom")
    val list2 = List(1.1, 30, 32, 41)

    println(getMidEle(list1)) //100
    println(getMidEle(list2)) //32
  }

  // 定义一个方法接收任意类型的 List 集合
  def getMidEle[A](l: List[A]) = {
    l(l.length / 2)
  }
}
```

## 1.1 上界

1、java中的上界

​		在 Java 泛型里表示某个类型是 A 类型的子类型，使用 extends 关键字，这种形式叫 upper bounds(上限或上界)，语法如下：

```java
<T extends A>
//或用通配符的形式：
<? extends A>
```

2、scala中的上界

​		在 scala 里表示某个类型是 A 类型的子类型，也称上界或上限，使用 **<:** 关键字，语法如下：

```scala
[T <: A]
//或用通配符:
[_ <: A]
```

3、案例1

```scala
object UpperBoundsDemo01 {
  def main(args: Array[String]): Unit = {
    /**
     * 1、new CompareComm(20, 30)并没有实现Comparable接口，会报错
     * 2、Integer.valueOf(20) 中的 Integer实现了Comparable接口（是其子类型），
     * 可以进行比较
     */
    //    val compareComm1 = new CompareComm(20, 30)
    //    println(compareComm1.greater)

    val compareComm2 =
    new CompareComm(Integer.valueOf(20), Integer.valueOf(30))
    println(compareComm2.greater)

    val compareComm3 =
      new CompareComm(java.lang.Float.valueOf(20.1f), java.lang.Float.valueOf(30.1f))
    println(compareComm3.greater)

    //简单的语法（隐式转换）
    val compareComm4 =
      new CompareComm[java.lang.Float](201.9f, 30.1f)
    println(compareComm4.greater)
  }
}

/**
 * 1、传统方法实现比较
 * 2、你可以写Int和Float来完成
 */
class CompareInt(n1: Int, n2: Int) {
  //返回较大的值
  def greater = if (n1 > n2) n1 else n2
}

/**
 * 泛型方式实现
 * 1、T <: Comparable[T] 表示输入的类型T实现了 Comparable[T]接口的类型，
 * 即你传入的T类型要继承Comparable接口
 * 2、obj1.compareTo(obj2) ，obj1实现了Comparable，所以可以直接调用
 * compareTo方法
 * 3、该方法通用性比传统方法要好
 */
class CompareComm[T <: Comparable[T]](obj1: T, obj2: T) {
  def greater = if (obj1.compareTo(obj2) > 0) obj1 else obj2
}
```

4、案例2

```scala
object UpperBoundsDemo02 {
  def main(args: Array[String]): Unit = {
    /**
     * bird sounds
     * bird sounds
     */
    biophony(Seq(new Bird, new Bird))
    println("--------------------------------")

    /**
     * animal sound
     * animal sound
     */
    biophony(Seq(new Animal, new Animal))
    println("--------------------------------")

    /**
     * animal sound
     * bird sounds
     */
    biophony(Seq(new Animal, new Bird)) //?
    println("--------------------------------")
    // 报错
    //    biophony(Seq(new Earth, new Earth)) 
  }

  /**
   * 1、定义了一个泛型
   * 2、_ 表示从things集合中取出的一个个元素
   */
  def biophony[T <: Animal](things: Seq[T]) = things map (_.sound)
}

class Earth { //Earth 类
  def sound() { //方法
    println("hello !")
  }
}

class Animal extends Earth {
  override def sound() = { //重写了Earth的方法sound()
    println("animal sound")
  }
}

class Bird extends Animal {
  override def sound() = { //将Animal的方法重写
    println("bird sounds")
  }
}
```

## 1.2 下界

1、java中下界

在 Java 泛型里表示某个类型是 A类型的父类型，使用 super 关键字。

```scala
<T super A>
//或用通配符的形式：
<? super A>
```

2、scala中的下界

在 scala 的下界或下限，使用 `>:`关键字，语法如下：

```scala
[T >: A]
//或用通配符:
[_ >: A]
```

3、案例

```scala
object lowerbounds {
  def main(args: Array[String]): Unit = {
    /**
     * hello !
     * hello !
     * 传入和Animal直系的，是Animal父类的还是父类处理。
     */
    biophony(Seq(new Earth, new Earth)).map(_.sound())
    biophony(Seq(new Animal, new Animal)).map(_.sound())

    /**
     * bird sounds
     * bird sounds
     * 1、虽说Bird是Animal的子类，但是不会报错。
     * 不能使用上界的思路来类推下界的含义
     * Animal子类的按照Animal处理(子类覆盖率父类的方法，所以是bird sounds 当将子类覆盖方法给注释时，输出animal sounds)。
     * 也就是下界，可以随便传，只是处理是方式不一样
     */
    biophony(Seq(new Bird, new Bird)).map(_.sound())

    /**
     * 1、不会报错，和Animal无关的，一律按照Object处理
     */
    biophony(Seq(new Moon))
  }

  /**
   * 上界案例返回：things map (_.sound)
   * 1、返回的是things，因为不能保证父类是否有方法sound()
   */
  def biophony[T >: Animal](things: Seq[T]) = things

}

class Earth { //Earth 类
  def sound() { //方法
    println("hello !")
  }
}

class Animal extends Earth {
  override def sound() = { //重写了Earth的方法sound()
    println("animal sound")
  }
}

class Bird extends Animal {
  override def sound() = { //将Animal的方法重写
    println("bird sounds")
  }
}

class Moon {}
```

<img src="D:\04桌面\Spring Boot学习\03学习文件\scala学习-泛型.assets\image-20200804100144598.png" alt="image-20200804100144598" style="zoom:80%;" />

可以看出将子类引用交给父类去处理。

## 1.3 视图界定

1、介绍

​		`<%` 的意思是`view bounds`(视界)，它比`<:`适用的范围更广，除了所有的子类型，还允许隐式转换类型。

```scala
def method [A <% B](arglist): R = ... 等价于:
def method [A](arglist)(implicit viewAB: A => B): R = ... 

或等价于:
implicit def conver(a:A): B = …
```

​		`<%` 除了方法使用之外，class 声明类型参数时也可使用：

```scala
class A[T <% Int]
```

2、案例1

```scala
object ViewBoundsDemo01 {
  def main(args: Array[String]): Unit = {
    //简洁写法
    val compareComm1 = new CompareComm(20, 30)
    println(compareComm1.greater)
  }
}

/**
 * 1、[T <% Comparable[T]] 说明 T是Comparable子类型
 * 2、视图界定和[T <: Comparable[T]]区别：视图界定支持隐式转换。
 * 即视图界定支持[T <: Comparable[T]]写法，还支持简洁的写法
 */
class CompareComm[T <% Comparable[T]](obj1: T, obj2: T) {
  def greater = if (obj1.compareTo(obj2) > 0) obj1 else obj2
}
```

3、案例2

```scala
object ViewBoundsDemo02 {
  def main(args: Array[String]): Unit = {
    val p1 = new Person("tom", 10)
    val p2 = new Person("jack", 20)
    val compareComm2 = new CompareComm2(p1, p2)
    println(compareComm2.getter) //jack 20
  }
}

/**
 * 1、对象的比较
 * 2、Person继承Ordered
 */
class Person(val name: String, val age: Int) extends Ordered[Person] {
  //重写了Ordered的compare方法，进行对象的比较。（我的年龄比你大，返回大于0）
  override def compare(that: Person): Int = this.age - that.age

  //显示方便
  override def toString: String = this.name + "\t" + this.age
}

//使用视图界定的方式，比较两个Person对象的年龄大小。
class CompareComm2[T <% Ordered[T]](obj1: T, obj2: T) {
  //回去调用Person中的compare方法
  def getter = if (obj1 > obj2) obj1 else obj2

  def geatter2 = if (obj1.compareTo(obj2) > 0) obj1 else obj2
}
```

## 1.4 上下文界定

​		与 `view bounds` 一样 `context bounds`(上下文界定)也是隐式参数的语法糖。

```scala
object contextboundsDemo01 {
  //定义了一个隐式值 Ordering[Person]类型
  implicit val personComparetor = new Ordering[Person] {
    override def compare(p1: Person, p2: Person): Int =
      p1.age - p2.age
  }

  def main(args: Array[String]): Unit = {
    val p1 = new Person("mary", 30)
    val p2 = new Person("smith", 35)

    val compareComm4 = new CompareComm4(p1, p2)
    println(compareComm4.geatter) //smith 35

    val compareComm5 = new CompareComm5(p1, p2)
    println(compareComm5.geatter) //smith  35

    val compareComm6 = new CompareComm6(p1, p2)
    println(compareComm6.geatter) //smith  35
  }
}

//方式1
//implicit comparetor: Ordering[T] 隐式参数
class CompareComm4[T: Ordering](obj1: T, obj2: T)(implicit comparetor: Ordering[T]) {
  def geatter = if (comparetor.compare(obj1, obj2) > 0) obj1 else obj2
}

//方式2,将隐式参数放到方法内
class CompareComm5[T: Ordering](o1: T, o2: T) {
  def geatter = {
    //返回一个数字 p1.age - p2.age
    def f1(implicit cmptor: Ordering[T]) = cmptor.compare(o1, o2)
    if (f1 > 0) o1 else o2
  }
}

//方式3,使用implicitly语法糖，最简单(推荐使用)
class CompareComm6[T: Ordering](o1: T, o2: T) {
  def geatter = {
    //这句话就是会发生隐式转换，获取到隐式值 personComparetor
    val comparetor = implicitly[Ordering[T]]
    println("CompareComm6 comparetor：" + comparetor.hashCode())
    if (comparetor.compare(o1, o2) > 0) o1 else o2
  }
}

//一个普通的Person类
class Person(val name: String, val age: Int) {
  override def toString = this.name + "\t" + this.age
}
```

## 1.5  协变、逆变和不变

1、介绍

（1）Scala的协变(+)，逆变(-)，协变`covariant`、逆变`contravariant`、不可变`invariant`。

（2）对于一个带类型参数的类型，比如 List[T]，如果对A及其子类型B，满足 List[B]也符合List[A]的子类型，那么就称为covariance(协变) ，如果 List[A]是 List[B]的子类型，即与原来的父子关系正相反，则称为contravariance(逆变)。

​		如果一个类型支持协变或逆变，则称这个类型为variance(翻译为可变的或变型)，否则称为invariance(不可变的)。

（3）在Java里，泛型类型都是invariant，比如List 并不是 List 的子类型。而scala支持，可以在定义类型时声明(用加号表示为协变，减号表示逆变)。

```scala
//在类型定义时声明为协变这样会把List[String]作为List[Any]的子类型。
trait List[+T]
```

2、案例

​		在这里引入关于这个符号的说明，在声明Scala的泛型类型时，“+”表示协变，而“-”表示逆变 。

+ C[+T]：如果A是B的子类，那么C[A]是C[B]的子类，称为协变 。

+ C[-T]：如果A是B的子类，那么C[B]是C[A]的子类，称为逆变 。

+ C[T]：无论A和B是什么关系，C[A]和C[B]没有从属关系。称为不变。

```scala
class CovariantDemo01 {
  def main(args: Array[String]): Unit = {
    //1、Temp3[A]不变测试
    //    val t1: Temp3[Super11] = new Temp3[Sub11]("hello world1") //报错，因为类型是不变
    //    var t2:Temp3[Sub11] = new Temp3[Sub11]("hello") //ok
    //2、Temp3[+A]协变测试
    //    val t3: Temp3[Super11] = new Temp3[Sub11]("hello world1") //ok
    //    var t4: Temp3[Sub11] = new Temp3[Sub11]("hello") //ok
    //    val t5: Temp3[Sub11] = new Temp3[Super11]("hello world1") //报错,不支持逆变

    //3、Temp3[-A]逆变测试
    //    val t6: Temp3[Super11] = new Temp3[Sub11]("hello world1") //报错
    var t7: Temp3[Sub11] = new Temp3[Sub11]("hello") //ok
    val t8: Temp3[Sub11] = new Temp3[Super11]("hello world1") //ok,支持逆变
  }
}

//不变
//class Temp3[A](title: String) { //Temp3[+A] //Temp[-A]
//  override def toString: String = {
//    title
//  }
//}

//协变
//class Temp3[+A](title: String) {
//  override def toString: String = {
//    title
//  }
//}

//逆变
class Temp3[-A](title: String) {
  override def toString: String = {
    title
  }
}

//支持协变
class Super11 //父类
class Sub11 extends Super11
```