# 1 面向对象编程

## 1.1 介绍

1、（类和对象）使用一种新的数据类型

（1）可以管理多个不同类型的数据 - 属性。

（2）可以对属性进行操作-方法 。

2、scala语言是面向对象的

​		Java是面向对象的编程语言，由于历史原因，Java中还存在着非面向对象的内容:基本类型 ，null，静态方法等。 

## 1.2 类和对象

1、定义类

```java
[修饰符] class 类名 {
   类体
} 
```

（1）scala语法中，类并不声明为public，所有这些类都具有公有可见性(即默认就是public），一个Scala源文件可以包含多个类。

2、属性

属性是类的一个组成部分，一般是值数据类型,也可是引用类型。

**细节说明**

（1）属性的定义语法同变量，示例：

```scala
[访问修饰符] var属性名称[：类型] =属性值

var name = "xiaohong"
var name:String = "xiaohong"
```

（2）属性的定义类型可以为任意类型，包含值类型或引用类型。

（3）Scala中声明一个属性,必须显示的初始化，然后根据初始化数据的类型自动推断，属性类型可以省略。

（4）如果赋值为null,则一定要加类型，因为不加类型, 那么该属性的类型就是Null类型。

```scala
var address : String = null 
```

（5）5)如果在定义属性时，暂时不赋值，也可以使用符号_(下划线)，让系统分配默认值，此时属性必须指定类型。

| **类型**             | **_** **对应的值** |
| -------------------- | ------------------ |
| Byte  Short Int Long | 0                  |
| Float Double         | 0.0                |
| String 和  引用类型  | null               |
| Boolean              | false              |

（6）不同对象的属性是独立，互不影响，一个对象对属性的更改，不影响另外一个。

3、创建对象

（1）基本语法

```scala
val | var 对象名 [：类型]  = new 类型()
```

说明：

+ 如果我们不希望改变对象的引用(即：内存地址), 应该声明为val 性质的，否则声明为var, scala设计者推荐使用**val** ,因为一般来说，在程序中，我们只是改变对象属性的值，而不是改变对象的引用。
+ scala在声明对象变量时，可以根据创建对象的类型自动推断，所以类型声明（[：类型]去掉）可以省略，**但当类型和后面 `new` 对象类型有继承关系即多态时，就必须写了。**

```scala
object CreateObj {
  def main(args: Array[String]): Unit = {
    //emp类型就是Emp
    val emp = new Emp
    //如果我们希望子类对象交给父类的引用，这时就需要加上类型
    //此时emp2类型是Person
    val emp2:Person = new Emp

  }
}
class Person{
}
class Emp extends Person{
}
```

（2）类和对象的内存分配机制

```scala
 val p1 = new Person
 p1.name = "jack"
 p1.age = 30
 val p2 = p1 //内存布局
```

内存布局如下：

<img src="D:\04桌面\Spring Boot学习\03学习文件\scala学习2.assets\image-20200721181341265.png" alt="image-20200721181341265" style="zoom:80%;" />

```scala
//p1更改name属性的值时，p2拿到的值会改变。
p1.name="tom"
println(p1.name)
```

## 1.3 方法

Scala中的方法其实就是函数，声明规则请参考函数式编程中的函数声明。

1、基本语法

```scala
def 方法名(参数列表) [：返回值类型] = { 
	方法体
}
```

2、方法的调用机制原理

（1）) 当我们scala开始执行时，先在栈区开辟一个main栈。main栈是最后被销毁。

（2）当scala程序在执行到一个方法时，总会开一个新的栈。

（3）每个栈是独立的空间，变量（基本数据类型）是独立的，相互不影响（引用类型除外）。

（4）当方法执行完毕后，该方法开辟的栈就会被jvm机回收。

## 1.4 构造器

​		构造器(constructor)又叫构造方法，是类的一种特殊的方法，它的主要作用是完成对新对象的初始化。

1、Java构造器基本语法

```scala
[修饰符] 方法名(参数列表){
	构造方法体
}
```

2、Java构造器的特点

（1）在Java中一个类可以定义多个不同的构造方法，构造方法重载。

（2）如果程序员没有定义构造方法，系统会自动给类生成一个默认无参构造方法(也叫默认构造器)，比如 Person (){}。

（3）一旦定义了自己的构造方法（构造器）,默认的构造方法就覆盖了，就不能再使用默认的无参构造方法，除非**显式的定义一下**,即: Person(){}。

### 1.4.1 主构造器和辅助构造器

​		和Java一样，Scala构造对象也需要调用构造方法，并且可以有任意多个构造方法（即scala中构造器也支持重载）。

Scala类的构造器包括： 主构造器 和 辅助构造器。

1、基本语法

```scala
class 类名(形参列表) {  // 主构造器只有一个
   // 类体
   def  this(形参列表) {  // 辅助构造器
   }
   def  this(形参列表) {  //辅助构造器可以有多个...
   }
} 
```

（1）辅助构造器函数的名称this, 可以有多个，编译器通过不同参数（个数和类型）来区分。

```scala
object ConDemo01 {
  def main(args: Array[String]): Unit = {
    val p1 = new Person("tom",10)
    println(p1.toString)
  }
}

//没有返回值
class Person(inName: String,inAge: Int){
  var name: String = inName
  var age: Int = inAge

  override def toString: String = {
    "name = " + this.name + "\t age = " + this.age
  }
}
```

2、细节说明

（1）Scala构造器作用是完成对新对象的初始化，构造器没有返回值。

（2）主构造器的声明直接放置于类名之后。

（3）主构造器会执行类定义中的所有语句（除去里面定义的函数），这里可以体会到Scala的函数式编程和面向对象编程融合在一起，即：构造器也是方法（函数），传递参数和使用方法和前面的函数部分内容没有区别。

（4）如果**主构造器无参数**，小括号可省略，构建对象时调用的构造方法的小括号也可以省略。

（5）**辅助构造器**名称为this（这个和Java是不一样的），多个辅助构造器通过不同参数列表进行区分， 在底层就是f构造器重载。

```scala
object ConDemo01 {
  def main(args: Array[String]): Unit = {
    //1、主构造器
    val p1 = new Person("tom",10)
    println(p1.toString)

    //2、辅助构造器,会调用def this(name: String)
    val p2 = new Person("xiaohei")
    println(p2.toString)
  }
}

//主构造器私有化：class Person private(inName: String,inAge: Int)
class Person(inName: String,inAge: Int){
  var name: String = inName
  var age: Int = inAge

  override def toString: String = {
    "name = " + this.name + "\t age = " + this.age
  }

  //辅助构造器
   //辅助构造器私有化：private def this(name: String)
  def this(name: String){
    /**
     * 1、辅助构造器，必须在第一行显式调用主构造器（这点和java一样）
     * 2、（先执行主构造器的内容，后执行辅助构造器的内容，假如主构造器A继承B类，程序会先B，再主构造器A，后辅助构造器A）
     * 其根本原因是实现子类（主构造器A）和父类（B）的关系。
     */
    this("jack01",20)
    this.name = name
  }
}
```

（6）如果想让主构造器变成私有的，可以在()之前加上private，这样用户只能通过辅助构造器来构造对象了。

```scala
class Person private(inName: String,inAge: Int)
```

（7）6)辅助构造器的声明不能和主构造器的声明一致,会发生错误(即构造器名重复)。

## 1.5 属性高级

1、构造器参数

（1）Scala类的主构造器的形参未用任何修饰符修饰，那么这个参数是局部变量。

```scala
//inName此时为局部变量
class Person(inName: String){}
```

（2）如果参数使用val关键字声明，那么Scala会将参数作为类的私有的只读属性使用 。var是私有的可读写属性。

```scala
//inName此时为Test01的只读私有属性
object ConDemo02 {
  def main(args: Array[String]): Unit = {
    val test = new Test01("xiaohei")
    //不可以写
//    test.inName = "aaa"
    println(test.inName)
  }
}

class Test01(val inName: String ){
  var name = inName
}
```

（3）如果参数使用var关键字声明，那么那么Scala会将参数作为类的成员属性使用,并会提供属性对应的xxx()[类似getter]/xxx_$eq()[类似setter]方法，即这时的成员属性是私有的，但是可读写。

2、Bean属性

​		JavaBeans规范定义了Java的属性是像getXxx（）和setXxx（）的方法。许多Java工具（框架）都依赖这个命名习惯。

​		为了Java的互操作性，将Scala字段加**@BeanProperty**时，这样会自动生成规范的 `setXxx/getXxx` 方法。这时可以使用 对象.setXxx() 和 对象.getXxx() 来调用属性。

1、细节说明

（1）给某个属性加入**@BeanPropetry**注解后，会生成getXXX和setXXX的方法，并且对原来底层自动生成类似xxx(),xxx_$eq()方法，没有冲突，二者可以共存。

```scala
@BeanProperty var name: String = null
```

## 1.6 包

1、Java包的三大作用

（1）区分相同名字的类。

（2）当类很多时,可以很好的管理类。

（3）控制访问范围。

2、scala包介绍

​		和Java一样，Scala中管理项目可以使用包，但Scala中的包的**功能更加强大**，使用也相对复杂些。

​		Scala中**包名**和**源码**所在的系统文件目录结构要可以不一致，但是编译后的字节码文件路径和包名会保持一致（这个工作由编译器完成）。

3、命名规则

（1）只能包含数字、字母、下划线、小圆点.,但不能用数字开头, 也不要使用关键字。

```ini
demo.class.exec1  //错误 , 因为class是关键字
demo.12a    // 错误，因为不能以数字开头
```

（2）命名规范

一般是：小写字母+小圆点。

```java
com.公司名.项目名.业务模块名

//举例
com.sina.edu.user
```

3、Scala会自动引入的常用包。

+ java.lang.*  
+ scala包（自动引入，但是不代表其里面的所有子包可以用，还需要重新引入）
+ Predef包

4、使用细节

（1）scala进行package 打包时，可以有如下形式。

```scala
//方法1
package com.atguigu.scala
class Person{}

//方法2，和方式1完全等价
package com.atguigu
package scala

//方式3，我们可以在包中创建子包，类和trait。即scala支持，在一个文件中，可以同时创建多个包，以及给各个包创建类和object。
package com.atguigu{
    class user{
    }
    
  package scala{
    class Person{
    }
  }
    
  object TestTiger {
    def main(args: Array[String]): Unit = {
    }
  }
} 
```

（2）包也可以像嵌套类那样嵌套使用（包中有包）, 这个在前面的第三种打包方式已经讲过，在使用第三种方式时的好处是：程序员可以在同一个文件中，将类(class / object)、trait创建在不同的包中，这样就非常灵活了。

（3）作用域原则：可以直接向上访问。即: Scala中子包中直接访问父包中的内容， 大括号体现作用域。

​		在子包和父包类重名时（使用子包中的类），默认采用就近原则，如果希望指定使用某个类，则带上包名即可。

（4）父包要访问子包的内容时，需要`import`对应的类等。

```scala
package A {
  //引入子包B，以便使用Tiger类
  import A.B.Tiger

  package B {
    //Tiger类 在 A的子包中
    class Tiger {}
  }

  object Test2 {
    def main(args: Array[String]): Unit = {
      //如果要在父包使用到子包的类，需要import
      val tiger = new Tiger()
      println("tiger=" + tiger)
    }
  }
}
```

（5）可以在同一个.scala文件中，声明多个并列的package(建议嵌套的pakage不要超过3层) 

（6）包名可以相对也可以绝对，比如，访问BeanProperty的绝对路径是：
 _root_. scala.beans.BeanProperty ，在一般情况下：我们使用相对路径来引入包，只有当包名冲突时，使用绝对路径来处理。

```scala
package com.atguigu.scala2
class Manager(var name: String) {
  //第一种形式
  //@BeanProperty var age: Int = _
  //第二种形式, 和第一种一样，都是相对路径引入
  //@scala.beans.BeanProperty var age: Int = _
  //第三种形式, 是绝对路径引入，可以解决包名冲突
  @_root_.scala.beans.BeanProperty var age: Int = _
}

object TestBean {
  def main(args: Array[String]): Unit = {
    val m = new Manager("jack")
    println("m=" + m)
  }
}
```

5、包对象

​		包可以包含类、对象和特质trait，但不能包含函数/方法或变量的定义。这是Java虚拟机的局限。为了弥补这一点不足，scala提供了包对象的概念来解决这个问题。

```scala
package com.xiaolun {
  /**
   * 在包中直接
   * 1、每个包都可以有一个包对象，且只能有一个。需要在父包(com.xiaolun)中定义它,且名称与子包一样（主要用来对包的功能进行补充）。
   * 2、package object scala 表示创建一个包对象scala,对应com.xialun.scala这个包对应的包对象。
   * 3、在包对象中定义的变量和方法，就可以在对应的包中使用。
   * 4、注意包对象的位置，包对象和子包是平行的关系。
   */
  package object scala {
    var name = "jack"

    def sayOk(): Unit = {
      println("package object sayOk!")
    }
  }

  package scala {

    //在包中直接写方法或者定义变量，直接报错。我们需要使用上面定义的包对象来解决。
    //val name = "king"
    //def sayOk(){}

    class Test {
      def test(): Unit = {
        //这里的name就是包对象scala中声明的name
        println(name)
        sayOk() //这个sayOk 就是包对象scala中声明的sayOk
      }
    }

    object TestObj {
      def main(args: Array[String]): Unit = {
        val t = new Test()
        t.test()
        //因为TestObje和scala这个包对象在同一包，因此也可以使用
        println("name=" + name)
      }
    }
  }
}
```

（1）底层实现机制

​		当创建包对象后，在该包下生成 `public final class package` 和 `public final class package$`，然后通过 `package$` 的一个**静态实例**完成对包对象中的属性和方法的调用。

6、包的可见性

（1）Java访问修饰符

<img src="D:\04桌面\Spring Boot学习\03学习文件\scala学习2.assets\image-20200721215015395.png" alt="image-20200721215015395" style="zoom:80%;" />

+ 公开级别:用public 修饰,对外公开。

+ 受保护级别:用protected修饰,对子类（不要求和父类在同一个包下）和同一个包中的类公开。

+ 默认级别:没有修饰符号,向同一个包的类公开。

+ 私有级别:用**private**修饰，只有类本身可以访问，不对外公开。

（2）Scala中包的可见性介绍

​		在Scala中，你可以通过类似的修饰符达到同样的效果。但是使用上有区别。

```scala
object Testvisit {
  def main(args: Array[String]): Unit = {
    val c = new Clerk()
    c.showInfo()
    Clerk.test(c)
  }
}

//类
class Clerk {
  var name: String = "jack"
  private var sal: Double = 9999.9
  def showInfo(): Unit = {
    println(" name " + name + " sal= " + sal)
  }
}

/**
 * 1、在一个文件中出现了class Clerk 和 object Clerk
 * 前者称为伴生类，后者称为伴生对象。
 * 2、scala设计者将static关键字拿掉，进而设计了伴生类和伴生对象的概念，
 * 将静态内容放到伴生对象中，伴生类写放非静态内容。
 */
object Clerk {
  def test(c: Clerk): Unit = {
    //这里体现出在伴生对象中，可以访问私有属性c.sal
    println("test() name=" + c.name + " sal= " + c.sal)
  }
}
```

使用细节说明：

+ 当方法访问权限为默认时，默认为public访问权限，这是因为从底层看属性是private的，但是因为提供了xxx_$eq()（类似setter）/xxx()（类似getter]）方法，因此从使用效果看是任何地方都可以访问。
+ private为私有权限，只在**类的内部**和**伴生对象**中可用 。
+ protected为受保护权限，scala中受保护权限比Java中更严格，只能子类访问，同包无法访问 (编译器)。
+ 在scala中没有public关键字,即不能用public显式的修饰属性和方法。
+ 包访问权限（表示属性有了限制。同时包也有了限制），相当于给我们的访问权限扩大范围。这点和Java不一样，体现出Scala包使用的灵活性。

```scala
package com.atguigu.scala
class Person {
   // 增加包访问权限后
   //1.private同时起作用。不仅同类可以使用;
   //2. 同时com.atguigu.scala中包下其他类也可以使用
  
  private[scala] val pname="hello" 
}

 //当然，也可以将可见度延展到上层包。
 //atguigu 表示上面最后一个 com.atguigu.scala包的名字
private[atguigu] val description="zhangsan"
```

7、包的引入

（1）介绍

​		Scala引入包也是使用import, 基本的原理和机制和Java一样，但是Scala中的import功**能更加强大，也更灵活**。

​		因为Scala语言源自于Java，所以**java.lang**包中的类会自动引入到当前环境中，而Scala中的**scala**包和**Predef**包的类也会自动引入到当前环境中，即起其下面的类可以直接使用。

（2）细节说明

+ 在Scala中，`import`语句可以出现在任何地方，并不仅限于文件顶部，`import`语句的作用一直延伸到包含该语句的块末尾。

  这种语法的好处是：在需要时在引入包，缩小`import`包的作用范围，提高效率。

  ```scala
  class User {
      //该包只能在User类中使用，作用域在这个User类的大括号里面。
      import scala.beans.BeanProperty
      @BeanProperty var  name : String = ""
  }
  class Dog {
      //报错
      @BeanProperty var  name : String = "" 
  }
  ```

+ Java中如果想要导入包中所有的类，可以通过通配符`*`，Scala中采用下 `_`（下划线）。

+ 如果不想要某个包中全部的类，而是其中的几个类，可以采用选取器(大括号{})。

```scala
def test(): Unit = {
    //只引入mutable下面的HashMap, HashSet这两个类
    import scala.collection.mutable.{HashMap, HashSet}
}
```

+ 如果引入的多个包中含有相同的类，那么可以将不需要的类进行重命名进行区分，这个就是**重命名**。

```scala
//含义：将ava.util.HashMap 重命名为 JavaHashMap，避免重名。
import java.util.{ HashMap=>JavaHashMap, List}
import scala.collection.mutable._

var map = new HashMap() // 此时的HashMap指向的是scala中的HashMap
var map1 = new JavaHashMap(); // 此时使用的java中hashMap的别名
```

+ 如果某个冲突的类根本就不会用到，那么这个类可以直接**隐藏**掉。

```scala
//含义为: 引入java.util包的所有类，但是忽略 HahsMap类.
import java.util.{ HashMap=>_, _} 

// 此时的HashMap指向的是scala中的HashMap, 而且idea工具的提示也不会显示java.util的HashMaple。
var map = new HashMap() 
```

# 2 三大特征

## 1、抽象

​		实际上就是把一类事物的共有的属性和行为提取出来，形成一个物理模型(模板)。这种研究问题的方法称为抽象。

<img src="D:\04桌面\Spring Boot学习\03学习文件\scala学习2.assets\image-20200721222605029.png" alt="image-20200721222605029" style="zoom:80%;" />

## 2、封装

​		封装(encapsulation)就是把抽象出的**数据和对数据的操作**封装在一起,数据被保护在内部,程序的其它部分只有通过**被授权的操作**(成员方法),才能对数据进行操作。

1、好处

（1）隐藏实现细节。

（2）可以对数据进行验证，保证安全合理。

2、封装的实现步骤 

（1）将属性进行私有化

（2）提供一个公共的set方法，用于对属性判断并赋值。

```scala
def setXxx (参数名: 类型): Unit = {
    //待加入数据验证的业务逻辑
  	属性 = 参数名
}
```

（3）提供一个公共的get方法，用于获取属性的值

```scala
def getXxx() [: 返回类型] = {
	return 属性
}
```

3、细节说明

（1）Scala中为了简化代码的开发，当声明属性时，本身就自动提供了对应setter/getter方法，如果属性声明为private的，那么自动生成的setter/getter方法也是private的，如果属性省略访问权限修饰符，那么自动生成的setter/getter方法是public的。

（2）因此我们如果只是对一个属性进行简单的set和get ，只要声明一下该属性(属性使用默认访问修饰符) 不用写专门的getset，默认会创建，访问时，直接对象.变量。这样也是为了保持访问一致性。

（3）从形式上看 dog.food 直接访问属性，其实底层仍然是**访问的方法**, 看一下反编译的代码就明白。

（4）有了上面的特性，目前很多新的框架，在进行反射时，也支持对属性的直接反射。

## 3、继承

### 1、基本语法

```scala
class 子类名 extends 父类名  { 
    类体 
}
```

### 2、优点

（1）代码的复用性提高了。

（2）代码的扩展性和维护性提高了，即当我们修改父类时，对应的子类就会继承相应的方法和属性。

（3）子类继承了所有的属性，只是私有的属性不能直接访问，需要通过公共的方法去访问。

### 3、重写方法

​		scala明确规定，重写一个非抽象方法需要用**override**修饰符，调用超类的方法使用**super**关键字 。

```scala
object MethodOverride01 {
  def main(args: Array[String]): Unit = {
    val emp = new Emp()
    emp.printName()
  }
}

class Person {
  var name: String = "tom"

  def printName() {
    println("Person printName() " + name)
  }
}

//这里我们继承了person
class Emp extends Person {
  /**
   * 1、这里需要显式的使用override
   * 2、如果子类在方法上使用了和父类相同的名称，
   *    子类需要添加关键字override
   * 3、在子类中需要调用父类的方法，使用关键字
   * super
   */
  override def printName() {
    println("Emp printName() " + name)
    super.printName()
  }
}
```

### 4、类型检查和转换

（1）要测试某个对象是否属于某个给定的类，可以用**isInstanceOf**方法。用**asInstanceOf**方法将引用转换为子类的引用。classOf获取对象的类名。

+ classOf[String]就如同Java的 String.class 。
+ obj.isInstanceOf[T]就如同Java的obj instanceof T 判断obj是不是T类型。
+ obj.asInstanceOf[T]就如同Java的(T)obj 将obj强转成T类型。

```scala
object TypeConvert01 {
  def main(args: Array[String]): Unit = {

    // 获取对象类型
    println(classOf[String]) //java.lang.String
    val s = "zhangsan"
    println(s.getClass.getName) //java.lang.String
    println(s.isInstanceOf[String]) //true
    //将s显式转换成String
    println(s.asInstanceOf[String])

//    var p = new Person2
    //假设emp继承Person2类
//    val e = new Emp
    //将子类引用给父类（向上转型，自动）
//    p = e
      //将父类引用重新转换成子类引用（多态），向下转型，可以使用子类的方法
//    p.asInstanceOf[Emp].sayHi()
  }
}
```

（2）最佳实践

类型检查和转换的最大价值在于：可以判断传入对象的类型，然后转成对应的子类对象，进行相关操作，这里也体现出**多态**的特点。

### 5、超类的构造

（1）回顾Java中超类的构造

```java
class A {
    public A() {
        System.out.println("A()");
    }
    public A(String name) {
        System.out.println("A(String name)" + name);
    }
}
class B extends A{
    public B() {
        //这里会隐式调用super(); 就是无参的父类构造器A()
        //会优先输出父类A()的方法
        System.out.println("B()");
    }
    public B(String name) {
        super(name);
        System.out.println("B(String name)" + name);
    }
}
```

从上面的代码中可以看出：在Java中，创建子类对象时，子类的构造器总是去调用一个父类的构造器(显式或者隐式调用)。

（2）Scala超类的构造说明

（a）类有一个主构器和任意数量的辅助构造器，而每个辅助构造器都必须先调用主构造器(也可以是间接调用)，这点在前面我们说过了。

```scala
object ScalaBaseContrator {
  def main(args: Array[String]): Unit = {
    /**
     * 执行流程
     * 1、scala遵守先构建父类部分，所以，先执行父类的构造方法
     * 2、执行顺序
     * person...
     * Emp...  ->new Emp001()
     * Emp 辅助构造器 -> new Emp001("merry")
     */
    //    val emp = new Emp001()
    val emp2 = new Emp001("merry")

  }
}

class Person001(pName: String) {
  var name = "zhangsan"
  println("Person...")

  /**
   * 子类无参构造方法会到这一步，
   * 但是不会到下面三个参数的辅助构造方法。
   */
  def this() {
    this("父类名字")
  }

  /**
   * 有三个参数的辅助构造方法不会被执行
   */
  def this(arg1: String, arg2: String, arg3: String) {
    this("父类名字")
  }

}

class Emp001 extends Person001 {
  println("Emp ....")

  //辅助构造器
  def this(name: String) {
    // 必须调用主构造器，不写会报错
    this
    this.name = name
    println("Emp 辅助构造器~")
  }
}
```

输出结果：

```ini
Person...
Emp ....
Emp 辅助构造器~
```

（b）只有**主构造器可以调用父类的构造器**。辅助构造器不能直接调用父类的构造器。在Scala的构造器中，你不能调用super(params)

```scala
class Person(name: String) { //父类的构造器
}
// 将子类参数传递给父类构造器,这种写法 √
class Emp (name: String) extends Person(name) {
  
    // super(name)  (×) 没有这种语法
    def  this() {
    	super("abc") // (×)不能在辅助构造器中调用父类的构造器
    }
}
```

### 6、覆写字段

​		在Scala中，子类改写父类的字段，我们称为覆写/重写字段。覆写字段需使用 override修饰。

（a）Java中字段的覆写

​		在Java中只有方法的重写，没有属性/字段的重写，准确的讲，是隐藏字段代替了重写。在Java中，父类和子类定义了一个同名的字段，不会报错。**但是对于同一个对象，用父类的引用去取值，会取到父类字段的值，用子类的引用去取值，取到子类字段的值。**

​		在实际开发中，要尽量避免子类和父类使用相同的字段名，否则很容易引入一些不容易发现的bug。

```java
public class JavaFiledOverride {
    public static void main(String[] args) {
   
        Super c1 = new Super();  //super
        System.out.println(c1.s);

        sub1 c2 = new sub1();  //sub
        System.out.println(c2.s);

        Super c3 = new sub1(); //super
        System.out.println(c3.s);

    }
}

class Super {
    String s = "super";
}

class sub1 extends Super {
    String s = "sub";
}
```

控制台输出：

```java
super
sub
super
```

（b）动态绑定机制

```java
public class JavaDynamicBind {
    public static void main(String[] args) {
        /**
         * 1、将一个子类的对象地址，交给了A（父类）引用
         * 2、java动态绑定机制小节
         * （1）如果调用的是方法，则JVM会将该方法和对象的内存地址绑定。比如
         *  当B中的sum注释的时候，a.sum()会调用父类B中的sum()方法，但是在该方法中
         *  有一个方法getI()，此时对应的是子类A的地址，输出值为20,所以20+10=30
         * （2）如果调用的是一个属性，没有动态绑定机制，在哪里调用，就返回对应值。比如，
         *  当B中的sum1注释的时候，a.sum1()会调用父类B中的sum1()方法，但是在该方法中
         *  有一个属性值i，无绑定机制，正常输出i的值5,所以5+10=15
         *  3、没有注释B中的sum()和sum1()方法的时候输出结果为40，40;反之输出结果为30，15
         */
        A a = new B();
        System.out.println(a.sum());  //40 //30
        System.out.println(a.sum1()); //40 //15
    }
}

class A {
    public int i = 5;

    public int sum() {
        return getI() + 10;
    }

    public int sum1() {
        return i + 10;
    }

    public int getI() {
        return i;
    }
}

class B extends A {
    public int i = 20;

//    public int sum() {
//        return i + 20;
//    }

    public int getI() {
        return i;
    }

//    public int sum1() {
//        return i + 20;
//    }
}
```

（c）Scala覆写字段快速入门

```scala
object JavaFiledOverride02 {
  def main(args: Array[String]): Unit = {
    val obj1: BB= new BB
    val obj2: AA= new BB

    /**
     * 1、输出：obj1.age=20   obj2.age=20
     * 2、obj1.age=》 obj1.age()动态绑定机制
     *
     */
    println("obj1.age="+obj1.age+"\t obj2.age="+obj2.age)
  }
}

class AA {
  val age : Int = 10  //会生成public age()
}

class BB extends AA {
  override val age : Int = 20 //会生成public age()
}
```

覆写字段的使用细节：

（a）def只能重写另一个def(即：方法只能重写另一个方法)。

```scala
object FiledOverrideDetail01 {
  def main(args: Array[String]): Unit = {
    val b = new BBBB
    /**
     *  b.name = "jack" 相当于调用了父类的name_$eq()
     *  println(b.name) 相当于调用了子类的name()
     *  这样的会会出现数据设置和数据获取不一致的问题
     */
    println(b.name)
  }
}

class AAAA {
  //将类型变成val的话，可以正常运行
  val name: String = ""
//  var name: String = ""  //底层会生成public name()和 public name_$eq()
}
class BBBB extends AAAA {
  override  val name: String = "jj" //底层会生成public name()
}
```

（b）val只能重写另一个val 属性 或 **重写不带参数**的def。

```scala
object FiledOverrideDetail02 {
  def main(args: Array[String]): Unit = {
    val b = new BBB
    println(b.sal) //0

    val b2: AAA = new BBB
    println(b2.sal) //也是输出0
  }
}

class AAA {
  def sal(): Int = {
    return 10
  }
}

class BBB extends AAA {
  override val sal: Int = 0
}
```

（c）抽象属性使用细节

```scala
object FiledOverrideDetail03 {
  def main(args: Array[String]): Unit = {
      println("hello-----")
  }
}

/**
 * 在A01中有一个抽象字段（属性）
 * 1、抽象的字段或者属性就是没有初始化的字段
 * 2、当一个类含有抽象属性时，则该类需要标记为abstract
 * 3、对于抽象的属性，在底层不会生成对应的属性声明，而是生成两个对应的抽象方法（name name_$eq）
 */
abstract class A01{
  var name:String //抽象
  var age:Int = 10 //为下面的继承测试用
}

class sub_A01 extends A01{
  /**
   * 1、如果我们在子类中去重写父类的抽象方法，本质是实现了抽象方法
   * 2、因此，在这里我们可以写override，也可以不写
   */
  override var name: String = _
  //不能重写父类的定义方法
//  override var age:Int = 20
}
```

### 7、 抽象类

​		在Scala中，通过`abstract`关键字标记不能被实例化的类。方法**不用标记**`abstract`，只要省掉方法体即可。抽象类可以拥有抽象字段，抽象字段/属性就是没有初始值的字段

（1）入门案例

把Animal做成抽象类, 包含一个抽象的方法cry()

```scala
abstract class Animal{
  var name : String //抽象的字段
  var age : Int // 抽象的字段
  var color : String = "black" //普通属性
  def cry()  //抽象方法，无abstract
}
```

（2）基本语法

```scala
abstract class Person() { // 抽象类 
  var name: String // 抽象字段, 没有初始化
  def printName // 抽象方法, 没有方法体
}
```

​		抽象类的价值更多是在于设计，是设计者设计好后，让子类继承并实现抽象类(即：实现抽象类的抽象方法)

（3）使用细节

+ 抽象类不能被实例

+ 抽象类不一定要包含abstract方法。也就是说,抽象类可以没有abstract方法。

+ 一旦类包含了抽象方法或者抽象属性,则这个类必须声明为abstract

+ 抽象方法不能有主体，不允许使用abstract修饰。

+ 如果一个类继承了抽象类，则它必须实现抽象类的所有抽象方法和抽象属性，除非它自己也声明为abstract类。

+ 抽象方法和抽象属性不能使用private、final 来修饰，因为这些关键字都是和重写/实现相违背的。

+ 抽象类中可以有实现的方法。

+ 子类重写抽象方法不需要override，写上也不会错。

### 8、匿名子类

和Java一样，可以**通过包含带有定义或重写的代码块的方式**创建一个匿名的子类。

（1）java匿名子类使用

```java
abstract class A2{
    abstract public void cry();
}

//main函数下实现，在java中创建一个匿名子类对象
A2 obj = new A2() {
            @Override
            public void cry() {
                System.out.println("okook!");
            }
};
obj.cry()
```

（2）scala匿名子类案例

```scala
object NoNameDemo01 {
  def main(args: Array[String]): Unit = {
    var monster = new Monster {
      override var name: String = "牛魔王"

      override def cry(): Unit = {
        println("牛魔王哼哼叫唤..")
      }
    }
    println(monster.name)
    monster.cry()
  }

}

abstract class Monster {
  var name: String

  def cry()
}
```

### 9、继承层级

![image-20200731180536507](D:\04桌面\Spring Boot学习\03学习文件\scala学习2.assets\image-20200731180536507.png)

继承层级图小结

（1）在scala中，所有其他类都是AnyRef的子类，类似Java的Object。

（2）AnyVal和AnyRef都扩展自Any类。Any类是根节点。

（3）Any中定义了isInstanceOf、asInstanceOf方法，以及哈希方法等。

（4）Null类型的唯一实例就是null对象。可以将null赋值给任何引用，但不能赋值给值类型的变量。

（5）Nothing类型没有实例。它对于泛型结构是有用处的，举例：空列表Nil的类型是List[Nothing]，它是List[T]的子类型，T可以是任何类。

# 3 伴生对象

## 1、介绍

（1）java中静态的概念

```java
public static 返回值类型  方法名(参数列表) {方法体}
      静态属性...
```

说明：Java中静态方法并不是通过对象调用的，而是通过类对象调用的，所以静态操作并不是面向对象的。

（2）Scala中静态的概念-伴生对象

​		Scala语言是完全面向对象(万物皆对象)的语言，所以并没有静态的操作(即在Scala中没有静态的概念)。

​		但是为了能够和Java语言交互(因为Java中有静态概念)，就产生了一种特殊的对象来模拟类对象，我们称之为类的**伴生对象**。

​		这个类的所有静态内容都可以放置在它的伴生对象中声明和调用。

## 2、快速入门

```scala
object AccompanyObject01 {
  def main(args: Array[String]): Unit = {
    //在底层等价于ScalaPerson$.MODULE$.sex（）
    println(ScalaPerson.sex) //true
    ScalaPerson.sayHi() //hello world
  }
}

/**
 * 1、当在同一个文件中有class ScalaPerson 和 object ScalaPerson
 * 2、class ScalaPerson称为伴生类，将非静态的内容放到该类中
 * 3、object ScalaPerson称为伴生对象，将静态的内容放到该对象（类）中
 * 4、class ScalaPerson编译后，底层生成ScalaPerson类ScalaPerson.class
 * 5、object ScalaPerson编译后，底层生成ScalaPerson$类ScalaPerson$.class
 * 6、对于伴生对象的内容，我们可以直接通过ScalaPerson.属性或者方法调用
 */
//伴生类
class ScalaPerson {
  var name: String = _
}

//伴生对象
object ScalaPerson {
  var sex: Boolean = true

  def sayHi(): Unit = {
    println("hello world")
  }
}
```

<img src="D:\04桌面\Spring Boot学习\03学习文件\scala学习2.assets\image-20200731183404541.png" alt="image-20200731183404541" style="zoom: 67%;" />

## 3、伴生对象小结

（1）Scala中伴生对象采用object关键字声明，伴生对象中声明的全是 静态内容，可以通过伴生对象名称直接调用。

（2）伴生对象对应的类称之为伴生类，伴生对象的名称应该和伴生类名一致。

（3）伴生对象中的属性和方法都可以通过伴生对象名(类名)直接调用访问。

（4）从语法角度来讲，所谓的伴生对象其实就是类的静态方法和成员的集合。

（5）从技术角度来讲，scala还是没有生成静态的内容，只不过是将伴生对象生成了一个新的类，实现属性和方法的调用。

（6）从底层原理看，伴生对象实现静态特性是依赖于 public static final MODULE$实现的。 

（7）伴生对象的声明应该和伴生类的声明在同一个源码文件中(如果不在同一个文件中会运行错误!)，但是如果没有伴生类，也就没有所谓的伴生对象了，所以放在哪里就无所谓了。

（8）如果 class A 独立存在，那么A就是一个类， 如果 object A 独立存在，那么A就是一个"静态"性质的对象[即类对象], 在 object A中声明的属性和方法可以通过 A.属性 和 A.方法 来实现调用。

（9）当一个文件中，存在伴生类和伴生对象时，文件的图标会发生变化。

## 4、apply方法

```scala
object ApplyDemo01 {
  def main(args: Array[String]): Unit = {

    val pig1 = new Pig("小花")

    //使用apply方法来创建对象
    val pig2 = Pig("小黑猪") //自动触发apply(pName: String)
    val pig3 = Pig() // apply()

    println("pig1.name"+pig1.name)
    println("pig2.name"+pig2.name)
    println("pig3.name"+pig3.name)

  }
}

class Pig(pName: String) {
  var name: String = pName
}

object Pig {
  //编写一个apply
  def apply(pName: String): Pig = new Pig(pName)

  def apply(): Pig = new Pig("pig2")
}
```

# 4 接口

## 1、回顾Java接口

（1）声明接口

```java
interface 接口名
```

（2）实现接口

```java
class 类名 implements 接口名1，接口2
```

+ 在Java中, 一个类可以实现多个接口。

+ 在Java中，接口之间支持多继承。

+ 接口中属性都是常量。

+ 接口中的方法都是抽象的。

## 2、Scala接口的介绍

​		从面向对象来看，接口并不属于面向对象的范畴，Scala是纯面向对象的语言，在Scala中，没有接口。

​		Scala语言中，采用特质`trait`（特征）来代替接口的概念，也就是说，多个类具有相同的特征（特征）时，就可以将这个特质（特征）独立出来，采用关键字`trait`声明。 理解trait 等价于`(interface + abstract class)`。

![image-20200801095645113](D:\04桌面\Spring Boot学习\03学习文件\scala学习2.assets\image-20200801095645113.png)

## 3、特质（trait）快速入门

（1）trait的声明

```scala
//trait 命名 一般首字母大写
trait 特质名 {
	trait体
}
```

​		Serializable： 就是scala的一个特质。在scala中，java中的接口可以当做特质使用。

```java
//trait Serializable extends Any with java.io.Serializable
//在scala中，java的接口都可以当作trait来使用
object T1 extends Serializable {
}
```

（2）Scala中trait的使用

​		一个类具有某种特质（特征），就意味着这个类满足了这个特质（特征）的所有要素，所以在使用时，也采用了extends关键字，如果有多个特质或存在父类，那么需要采用with关键字连接。

语法：

```scala
//没有父类
class  类名   extends   特质1   with    特质2   with   特质3 ..

//有父类
class  类名   extends   父类   with  特质1   with   特质2   with 特质3
```

（3）入门案例

![image-20200801101327966](D:\04桌面\Spring Boot学习\03学习文件\scala学习2.assets\image-20200801101327966.png)

```scala
object TraitDemo02 {
  def main(args: Array[String]): Unit = {
    val c = new C()
    val e = new E()
    c.getConnect("root","123")
    e.getConnect("root1","1234")
  }
}

//定义一个trait
trait trait1 {

  /**
   * 1、声明方法，抽象的
   * 2、定义一个规范
   */
  def getConnect(user: String, pwd: String): Unit
}

class A {}

class B extends A {}

class C extends A with trait1 {
  override def getConnect(user: String, pwd: String): Unit = {
    println("c连接mysql")
  }
}

class D {}

class E extends D with trait1 {
  def getConnect(user: String, pwd: String): Unit = {
    println("e连接oracle")
  }
}

class F extends D {}
```

## 4、特t质trait 的再说明

（1）Scala提供了特质（trait），特质可以同时拥有抽象方法和具体方法，一
个类可以实现/继承多个特质。

（2）特质中没有实现的方法就是抽象方法。类通过extends继承特质，通过with可以继承多个特质。

（3）java接口都可以当做Scala特质使用。

（4）和Java中的接口不太一样的是特质中的方法并不一定是抽象的，也可以有非抽象方法(即：实现了的方法)。

```scala
object TraitDemo03 {
  def main(args: Array[String]): Unit = {
    val t = new Sheep()
    t.sayok()
    t.sayHello()

  }
}

/**
 * 1、当一个trait有抽象方法和非抽象方法时
 * 一个trait在底层对应两个 Trait01.class(接口)，
 * Trait01$class.class  Trait01$class 抽象类
 * 2、当在sheep类中要使用Trait01的实现的方法，通过Trait01$class
 */
trait Trait01 {
  //抽象方法
  def sayHi()

  //实现的普通方法
  def sayHello(): Unit = {
    println("Trait02 sayHello.....")
  }
}

class Sheep extends Trait01 {
  override def sayHi(): Unit = {
    println("T1 sayOk.....")
  }
}
```

<img src="D:\04桌面\Spring Boot学习\03学习文件\scala学习2.assets\image-20200801103939945.png" alt="image-20200801103939945" style="zoom:80%;" />

## 5、动态混入

（1）除了可以在类声明时继承特质以外，还可以在构建对象时**混入**特质，扩展目标类的功能。

（2）此种方式也可以应用于对抽象类功能进行扩展。

（3）动态混入是Scala特有的方式（java没有动态混入），可在不修改类声明/定义的情况下，扩展类的功能，非常的灵活，耦合性低 。

（4）动态混入可以在不影响原有的继承关系的基础上，给指定的类扩展功能。

```scala
object MixInDemo01 {
  def main(args: Array[String]): Unit = {
    /**
     * 1、在不修改类的定义的基础上，让他们可以使用trait方法
     */
    var oracle = new OracleDB with Operate3
    oracle.insert(999)

    //MySQL3为抽象类，没有抽象的方法
    val mysql = new MySQL3 with Operate3
    mysql.insert(4)

    //如果抽线类中含有抽象方法，在实现动态混入特质的过程中需要
    //按照下面的顺序实现抽象方法。
    val mysql2 = new MySQL3_ with Operate3 {
      override def say(): Unit = {
        println("say....")
      }
    }
    mysql2.insert(12)
    mysql2.say()
  }
}

trait Operate3 { //特质
  def insert(id: Int): Unit = { //方式（实现）
    println("插入数据 = " + id)
  }
}

class OracleDB {
}

abstract class MySQL3 {
}

//含有抽象方法
abstract class MySQL3_ {
  def say()
}
```

## 6、叠加特质

（1）在Scala中创建对象共有4种方式

+ new 对象
+ apply 创建
+ 匿名子类方式
+ 动态混入

（2）构建对象的同时如果混入多个特质，称之为叠加特质，那么特质声明顺序从左到右，方法执行顺序从右到左。

案例：分析叠加特质时，对象的构建顺序，和执行方法的顺序

<img src="D:\04桌面\Spring Boot学习\03学习文件\scala学习2.assets\image-20200801151847062.png" alt="image-20200801151847062" style="zoom:50%;" />

```scala
object AddTraits {
  def main(args: Array[String]): Unit = {
    /**
     * 1、创建mysql实例时，动态混入DB4和File4
     * 当我们创建一个动态混入对象时，其顺序从左到右
     * 输出：Operate4... -》 Data4 -》DB4 -》File4
     * （虽说继承了Data4但是不会走两遍父类）
     */
    val mySQL = new MySQL4 with DB4 with File4
    println("----------------")

    /**
     * 1、当我们执行一个动态混入对象的方法，其执行顺序从右到左（栈）
     * 当执行super时，是指左边的特质（动态混入特质）DB4；
     * 如果在左边无特质，则该super就是父特质
     * 向文件 -》 (执行super) 向数据库 -> (执行super,没找到) 插入数据：100
     */
    mySQL.insert(100)
  }
}

trait Operate4 {
  println("Operate4...")

  def insert(id: Int)
}

trait Data4 extends Operate4 {
  println("Data4")

  //实现insert方法
  override def insert(id: Int): Unit = {
    println("插入数据 = " + id)
  }
}

//特质，继承Data4
trait DB4 extends Data4 {
  println("DB4")

  override def insert(id: Int): Unit = {
    println("向数据库")
    super.insert(id)
  }
}

trait File4 extends Data4 {
  println("File4")

  override def insert(id: Int): Unit = {
    println("向文件")

    /**
     * 1、调用了insert方法
     * 2、这里的super在动态混入时，不一定是父类
     */
    super.insert(id)
  }
}

//普通类
class MySQL4 {}
```

​		如果想要调用具体特质的方法，可以指定：`super[特质].xxx(…)`.其中的泛型必须是该特质的直接超类类型。

```scala
trait File4 extends  Data4 {
  println("File4")
  override def insert(id : Int): Unit = {
    print("向文件")
    super[Data4].insert(id)
  }
}
```

## 7、在特质中重写抽象方法特例

```scala
object MixInDemo02 {
  def main(args: Array[String]): Unit = {
    val mysql5 = new MySQL5 with DB5  with File5
    mysql5.insert(666)

    //只有File5会报错，因为super.insert(id)找到父类，
    //发现，其抽象方法未实现。
//    val mysql5 = new MySQL5 with File5
  }
}

trait Operate5 {
  def insert(id: Int)
}

trait File5 extends Operate5 {
  /**
   * 1、重写父类的抽象方法
   * def insert(id: Int): Unit = {}
   * 其中super.insert(id) 会报错
   * 2、如果我们在子特质中重写/实现了一个父类的
   * 抽象方法，但是同时调用了super方法，没有方法的具体实现，无法编译通过，
   * 为了避免这种情况的发生。可重写抽象方法abstract override
   * 这样在使用时，就必须考虑动态混入的顺序问题。
   * 3、这样写的作用：这时super.insert(id)的调用就和动态混入顺序有密切关系
   */
  abstract override def insert(id: Int): Unit = {
    println("将数据保存到文件中..")
    super.insert(id)
  }
}

trait DB5 extends Operate5 {
  def insert(id: Int): Unit = {
    println("将数据保存到数据库中..")
  }
}

class MySQL5 {}
```

理解 abstract override 的小技巧分享：
		可以这里理解，当我们给某个方法增加了`abstract override` 后，就是明确的告诉编译器，该方法确实是重写了父特质的抽象方法，但是重写后，该方法仍然是一个抽象方法（因为没有完全的实现，需要其它特质继续实现[通过混入顺序]）。

## 8、富接口

即该特质中既有抽象方法，又有非抽象方法。

```scala
trait Operate {
    def insert( id : Int ) //抽象
    def pageQuery(pageno:Int, pagesize:Int): Unit = { //实现
        println("分页查询")
    }
}
```

## 9、动态混入对字段的处理

（1）特质中可以定义具体字段，如果初始化了就是具体字段，如果不初始化就是抽象字段。混入该特质的类就具有了该字段，字段不是继承，而是直接加入类，成为自己的字段。

```scala
trait Operate6 {
  var opertype : String
  def insert()
}

trait DB6 extends  Operate6 {
  var opertype : String = "insert"
  def insert(): Unit = {
  }
}
class MySQL6 {}

var mysql = new MySQL6 with DB6
//通过反编译，可以看到 opertype
println(mysql.opertype)
```

（2）特质中的抽象字段

特质中未被初始化的字段在具体的子类中必须被重写。

## 10、特质构造顺序

```scala
object MixInDemo03 {
  def main(args: Array[String]): Unit = {
    /**
     * 特质构造顺序
     * 1、声明类的同时混入特质（class FF extends EE with CC with DD）
     * (1)调用当前类的超类构造器 EE -》 E...
     * (2)第一个特质的父特质构造器  CC -> A.. (找到最终的父类，之后
     *                  倒过来执行) -> B..
     * (3)第一个特质构造器 -> c..
     * (4)第二个特质构造器的父特质构造器, 如果已经执行过，就不再执行 -> D..
     * (5)第二个特质构造器
     * (6).......重复(4),(5)的步骤(如果有第3个，第4个特质)
     * (7)最后执行当前类构造器  -》F..
     */
//    val ff1 = new FF()
//    println(ff1)

    /**
     * 1、在构建对象时，动态混入特质（class KK extends EE）
     * 声明：先创建new kk对象，然后再混入其他特质
     * （1）调用当前类的超类构造器  -》E..
     * （2）当前类构造器 ->K.. (之后的顺序和第一种特质构造顺序一致
     *                   A...
     *                   B....
     *                   C....
     *                   D....)
     * （3）第一个特质构造器的父特质构造器
     * （4）第一个特质构造器.
     * （5）第二个特质构造器的父特质构造器, 如果已经执行过，就不再执行
     * （6）第二个特质构造器
     * （7）.......重复5,6的步骤(如果有第3个，第4个特质)
     * （8）当前类构造器
     */
    val ff2 = new KK() with CC with DD
    println(ff2)
  }
}

trait AA {
  println("A...")
}

trait BB extends AA {
  println("B....")
}

trait CC extends BB {
  println("C....")
}

trait DD extends BB {
  println("D....")
}

class EE {
  println("E...")
}

/**
 * 先继承EE,然后在继承CC和DD
 */
class FF extends EE with CC with DD {
  println("F....")
}

/**
 * KK继承普通类EE
 */
class KK extends EE {
  println("K....")
}
```

分析两种方式对构造顺序的影响

+ 第1种方式实际是构建类对象, 在混入特质时，该对象还没有创建。

+ 第2种方式实际是构造匿名子类，可以理解成在混入特质时，对象已经创建了。

## 11、扩展类的特质

特质可以继承类，以用来拓展该类的一些功能。

```scala
object ExtendTraitDemo01 {
  def main(args: Array[String]): Unit = {
  }
}

/**
 * 1、特质可以继承类，以用来拓展该类的一些功能。
 * LoggedException继承了Exception，则
 * LoggedException可以使用Exception中的一些功能
 */
trait LoggedException extends Exception {
  def log(): Unit = {
    // 方法来自于Exception类
    println(getMessage())
  }
}

//2、所有混入该特质的类，会自动成为该特质所继承的超类的子类。
//UnhappyException 就是Exception的子类.
class UnhappyException extends LoggedException {
  // 已经是Exception的子类了，所以可以重写方法
  override def getMessage = "错误消息！"
}

/**
 * 3、如果混入该特质的类（UnhappyException2），已经继承了
 * 另一个类(IndexOutOfBoundsException)，
 * 则要求IndexOutOfBoundsException类是特质超类(Exception)的子类，
 * 否则就会出现了多继承现象，发生错误。
 */
class UnhappyException2 extends IndexOutOfBoundsException with LoggedException {
  // 已经是Exception的子类了，所以可以重写方法
  override def getMessage = "错误消息！"
}

class C1{
}
//报错，C1不是LoggedException特质的超类的子类
class UnhappyException3 extends C1 with LoggedException {
  override def getMessage = "错误消息！"
}
```

## 12、自身类型

​		自身类型：主要是为了解决特质的循环依赖问题，同时可以确保特质在不扩展某个类的情况下，依然可以做到限制混入该特质的类的类型。

```scala
object SelfType {
  def main(args: Array[String]): Unit = {

  }
}

/**
 * 1、 Logger就是自身类型特质，当这里做了自生类型后
 * 等价于trait Logger extends Exception,要求混入该特质的类
 * 也是Exception的子类
 */
trait Logger {
  // 明确告诉编译器，我就是Exception,如果没有这句话，下面的getMessage不能调用
  this: Exception =>
  def log(): Unit = {
    // 既然我就是Exception, 那么就可以调用其中的方法
    println(getMessage)
  }
}

/**
 * 1、报错，因为Console不是Exception的子类
 * 2、Logger中已声明了
 */
//class Console extends Logger {}

//先继承Exception，让其称为其子类，才能够识别。
class Console extends Exception with Logger
```