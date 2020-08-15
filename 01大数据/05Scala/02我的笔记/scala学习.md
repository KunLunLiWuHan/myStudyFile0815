# 1 介绍

​		Scala是一门以java虚拟机（JVM）为运行环境并将面向对象和函数式编程的最佳特性结合在一起的静态类型编程语，**语言特点如下**：

1. Scala 是 `Scalable Language` 的简写，是一门多范式 (multi-paradigm) 的编程语言，Scala支持面向对象和函数式编程。

2. Scala源代码(.scala)会被编译成Java字节码(.class)，然后运行于JVM之上，并可以调用现有的Java类库，实现两种语言的无缝对接。

3. scala 单作为一门语言来看， 非常的简洁高效 （三元运算， ++， --）。

4. Scala 在设计时，马丁·奥德斯基 是参考了Java的设计思想，可以说Scala是源于java，同时马丁·奥德斯基 也加入了自己的思想，将函数式编程语言的特点融合到JAVA中。

​		Spark新一代内存级大数据计算框架，是大数据的重要内容。Spark就是使用Scala编写的。因此为了更好的学习Spark,需要掌握Scala这门语言。

快速有效掌握Scala的建议 ：

1. 学习scala 特有的语法。
2.  搞清楚 scala 和java 区别。
3. 如何规范的使用scala。

# 2 安装和配置

## 2.1 Windows下搭建开发环境

​		下载地址：http://www.scala-lang.org/ 。

​		Scala需要Java运行时库，安装Scala需要首先安装JVM虚拟机并配置好，推荐安装JDK1.8。

安装步骤：

（1）配置Jdk的环境变量。

（2）配置SCALA_HOME

```ini
SCALA_HOME= D:\03Enviroment\11scala\01install\scala-2.11.8
```

（3）将Scala安装目录下的bin目录加入到PATH环境变量

```ini
#在PATH变量中添加：
%SCALA_HOME%\bin
```

（4）测试

​		在终端中输入下面命令打开scala解释器，有下面的显示时说明配置成功。

```ini
scala
```

<img src="scala学习.assets\image-20200719232839336.png" alt="image-20200719232839336" style="zoom:50%;" />

## 2.2 Linux下搭建开发环境

（1）将scala压缩文件解压并转移到指定目录

```ini
tar -zxvf scala-2.11.8.tgz  && mv scala-2.11.8 /usr/local/scala
```

（2）在root权限下，配置环境变量。

```ini
#进入配置目录
vim /etc/profile

#添加配置
export SCALA_HOME=/usr/local/scala/scala-2.11.8
export PATH=$PATH:$SCALA_HOME/bin
```

（3）使配置文件生效

```ini
source /etc/profile
```

（4）在其他目录下输入下面指令，会有下面的输出信息。

```ini
[root@hadoop02 ~]# scala
```

<img src="scala学习.assets\image-20200720084551037.png" alt="image-20200720084551037" style="zoom:80%;" />

## 2.3 快速入门

<img src="scala学习.assets\image-20200720090225646.png" alt="image-20200720090225646" style="zoom:80%;" />

<img src="scala学习.assets\image-20200720090153271.png" alt="image-20200720090153271" style="zoom:80%;" />

scala执行流程如下：

<img src="scala学习.assets\image-20200720103503066.png" alt="image-20200720103503066" style="zoom:80%;" />

1、IDEA开发Scala

（1）搭建项目

<img src="scala学习.assets\image-20200720091002974.png" alt="image-20200720091002974" style="zoom:80%;" />

​		在增加scala框架的过程中，如果我们是第一次开发scala项目，上面的`use library`是空，我们需要将其目录配置到我们scala安装的主目录（D:\03Enviroment\11scala\01install\scala-2.11.8）即可。

（2）书写入门代码

```scala
/**
 * 看到object，我们有这样的认识：
 * 1.object TestScala对应的是一个TestScala$的一个静态对象，
 * 2.在整个程序中，是一个单例。
 */
object TestScala {
  def main(args: Array[String]): Unit = {
    println("hello,world!")
  }
}
```

java中的模拟代码：

```java
//我们可以理解scala 在运行时，做了一个包装。
public class TestScala {
    public static void main(String[] paramArrayOfString) {
        TestScala$.MODULE$.main(paramArrayOfString);
    }
}

final class TestScala$ {
    public static final TestScala$ MODULE$;

    static {
        MODULE$ = new TestScala$();
    }

    public void main(String[] args) {
        System.out.println("hello,world!");
    }
}
```

2、scala程序开发注意事项

+ Scala源文件以 “.scala" 为扩展名。

+ Scala程序的执行入口是main()函数。
+ Scala语言严格区分大小写。
+ Scala方法由一条条语句构成，每个语句后不需要分号(**Scala语言会在每行后自动加分号)**，这也体现出Scala的简洁性。如果在同一行有多条语句，除了最后一条语句不需要分号，其它语句需要分号。

3、常用转义字符

|       字符       | 描述                          |
| :--------------: | :---------------------------- |
|        \t        | 一个制表位，实现对齐的功能    |
|        \n        | 换行符                        |
| "\\"(两个反斜杠) | 一个\                         |
| \"（反斜杠\+“）  | 一个"                         |
|    反斜杠\+r     | 一个回车 println("hello\rk"); |

4、scala语言输出的三种方式

（1）字符串通过+号连接（类似java）。

（2）printf用法 （类似C语言）字符串通过 `%` 传值进行填充（格式化输出）。

（3）字符串通过$引用(类似PHP）（编译器会去解析$对应变量）。

```scala
val name = "ApacheCN"
val age  = 1
val url  = "www.atguigu.com"
println("name=" + name + " age=" + age + " url=" + url)
printf("name=%s, age=%d, url=%s \n", name, age, url)
println(s"name=$name, age=$age, url=$url")

//如果下列字符串中出现了类似${age+10}，表示{}是一个表达式。
println(s"name=$name, age=${age+10}, url=$url")
```

5、关联scala源码

​		在使用scala过程中，为了搞清楚scala底层的机制，需要查看源码，下面看看如果关联和查看Scala的源码包

（1）查看源码, 选择要查看的方法或者类, 输入 **ctrl + B**

（2）将源文件`scala-sources-2.12.4`拷贝到scala的lib目录下，然后解压，关联就可以了。

6、注释

（1）单行注释： //注释文字

（2）多行注释： /* 注释文字 */

（3）文档注释

​		注释内容可以被工具 `scaladoc` 所解析，生成一套以网页文件形式体现的该程序的说明文档。

```scala
object Hello {
  /**
    * @deprecated xxx
    * @example testing coding(举出例子)
    * @param args
    */
  def main(args: Array[String]): Unit = {
     println("helllo")
  }
}
```

7、规范的代码风格

正确的缩进和空白：

（1）使用一次tab操作，实现缩进,默认整体向右边移动，时候用`shift+tab`整体向左移。

（2）使用 ctrl + alt + L 来进行代码格式化 。

（3）**运算符**两边习惯性各加一个空格。比如：2 + 4 * 5。

（4）一行最长不超过80个字符，超过的请使用换行展示，尽量保持格式优雅。

# 3 变量

## 3.1 介绍

​		变量是程序的基本组成单位。

​		变量相当于内存中一个数据存储空间的表示，你可以把变量看做是一个房间的门牌号，通过门牌号我们可以找到房间，而通过变量名可以访问到变量(值)。

1、变量使用的基本步骤

（1）声明/定义变量 (scala要求：变量声明时要初始化) 

（2）使用

```scala
object VarDemo01 {
  def main(args: Array[String]): Unit = {
      //声明变量，告诉计算机开一房间，并赋值。
    var age: Int = 10
      //定义一个Double类型，取名为sal，并赋值为10.9
    var sal: Double = 10.9
    var name: String = "tom"
    var isPass:Boolean = true
    //在scala中，小数默认为Double,整数默认为Int
    var score: Float = 70.9f;

    println(s"${age} ${isPass}")
  }
}
```

变量声明基本语法

```ini
var | val 变量名 [: 变量类型] = 变量值
```

+ 声明变量时，类型可以省略（编译器自动推导,即类型推导，但需要初始值。

+ 类型确定后，就不能修改，说明Scala 是强数据类型语言。

+ 在声明/定义一个变量时，可以使用var 或者 val 来修饰， var 修饰的变量**可改变**，val 修饰的变量不可改 。val修饰的变量在编译后，等同于加上final， 通过反编译看下底层代码（val没有线程安全问题，效率高）。
+  var 修饰的对象引用可以改变，val 修饰的则不可改变，但对象的状态(值)却是可以改变的。

```java
class VarDemo02 {
  def main(args: Array[String]): Unit = {
    //情况1
    var age = 10
    age = 30

    val num = 10
    //报错：Reassignment to val
//    num = 30

    //情况2
    val dog = new Dog
    dog.age = 90
    dog.name = "小花"
  }
}

class Dog{
  //使用var去声明一个age属性
  var age: Int = 0
  //声明名字
  var name: String = ""

  //val声明的时候，dog.name = "小花"会失效。
//  val name: String = ""
}
```

## 3.2 数据类型

​		Scala 与 Java有着相同的数据类型，在Scala中数据类型都是对象。

​		Scala数据类型分为两大类 AnyVal(值类型) 和 AnyRef(引用类型)， 注意：不管是AnyVal还是AnyRef 都是对象。

<img src="scala学习.assets\image-20200720152809166.png" alt="image-20200720152809166" style="zoom:80%;" />

| 数据类型 | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| Byte     | 8位有符号补码整数。数值区间为 -128  到 127                   |
| Short    | 16位有符号补码整数。数值区间为 -32768  到 32767              |
| Int      | 32位有符号补码整数。数值区间为 -2147483648  到 2147483647    |
| Long     | 64位有符号补码整数。数值区间为 -9223372036854775808  到 9223372036854775807 |
| Float    | 32 位, IEEE 754标准的单精度浮点数                            |
| Double   | 64 位 IEEE 754标准的双精度浮点数                             |
| Char     | 16位无符号Unicode字符, 区间值为  U+0000  到 U+FFFF           |
| String   | 字符序列                                                     |
| Boolean  | true或false                                                  |
| Unit     | 表示无值，和其他语言中void等同。用作不返回任何结果的方法的结果类型。Unit只有一个实例值，写成()。 |
| Null     | null                                                         |
| Nothing  | Nothing类型在Scala的类层级的最低端；它是任何其他类型的子类型。 |
| Any      | Any是所有其他类的超类                                        |
| AnyRef   | AnyRef类是Scala里所有引用类(reference class)的基类           |

从上面的数据类型一览图我们可以看出：

（1）在scala中有一个根类型Any,他是所有类的子类。		

（2）Null类型是scala的特别类型，它只是一个值null，它是bottom class，是所有AnyRef类型的子类。

（3）Nothing 类型是bottom class，是所有类的子类，在开发中，通常可以将Nothing类型的值返回给任意变量或者函数，抛出异常用的较多。比如：

```scala
object TypeDemo01 {
  def main(args: Array[String]): Unit = {
    print(sayHello)
  }

  /**
   * 在开发中，有一个方法会异常中断，我们可以将其返回Nothing
   * 即使用Nothing做返回值，就是明确说明该方法没有正常返回值。
   */
  def sayHello: Nothing={
    throw new Exception("抛出异常")
  }
}
```

（4）在scala中仍然遵守，低精度的值向高精度的值自动转换（implicit conversion）隐式转换。		

相对于java的类型系统，scala要复杂些！也正是这复杂多变的类型系统才让面向对象编程和函数式编程完美的融合在了一起。

### 1、scala整型

 Scala的整数类型就是用于存放整数值的，比如 12 , 30等等。

| 数据类型   | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| Byte  [1]  | 8位有符号补码整数。数值区间为 -128  到 127                   |
| Short  [2] | 16位有符号补码整数。数值区间为 -32768  到 32767              |
| Int  [4]   | 32位有符号补码整数。数值区间为 -2147483648  到 2147483647  （2^31-1） |
| Long  [8]  | 64位有符号补码整数。数值区间为 -9223372036854775808  到 9223372036854775807  = 2的(64-1)次方-1 |

1、使用细节

（1）Scala各整数类型有固定的表数范围和字段长度，不受具体OS（操作系统）的影响，以保证Scala程序的可移植性。

（2）查看最大/小值。

```scala
def main(args: Array[String]): Unit = {
  println("Long的最大值"+ Int.MaxValue + "Long的最小值" + Int.MinValue)
}
```

### 2、浮点类型

Scala的浮点类型可以表示一个小数，比如 123.4f，7.8 ，0.12等等。

1、使用细节

（1）与整数类型类似，Scala 浮点类型也有固定的表数范围和字段长度，不受具体OS的影响。

（2）浮点型常量有两种表示形式

```ini
#十进制数形式
5.12       
512.0f    
.512 (必须有小数点）

#科学计数法形式
5.12e2  = 5.12乘以10的2次方     
5.12E-2  = 5.12除以10的2次方   
```

（3）Float精确到小数点后大致7位。

### 3、字符类型

字符类型可以表示单个字符,字符类型是Char。

1、使用细节

（1）字符常量是用单引号(‘ ’)括起来的单个字符。

```scala
var c1 = 'a‘  
var c2 = '中‘ 
var c3 = '9' 
```

（2）Scala 也允许使用转义字符‘\’来将其后的字符转变为特殊字符型常量。

```scala
var c3 = ‘\n’   // '\n'表示换行符
```

 （3）可以直接给Char赋一个整数，然后输出时，会按照对应的unicode 字符输出 ['\u0061' 97]。

（4）Char类型是可以进行运算的，相当于一个整数，因为它都对应有Unicode码。字符和码值的对应关系是通过字符编码表决定的(是规定好)， 这一点和Java一样。

```scala
//报错，int - >char错误。
var c1:Char = 'a' + 1

//正确（98并没有超出Char的范围）
var c2:Char = 98
```

（5）字符型 存储到 计算机中，需要将字符对应的码值（整数）找出来
 存储：字符——>码值——>二进制——>存储
 读取：二进制——>码值——> 字符——>读取

### 4、布尔类型

（1）布尔类型也叫Boolean类型，Booolean类型数据**只允许取值**true和false。

（2）boolean类型占1个字节。

（3）boolean 类型适于**逻辑运算**，一般用于程序流程控制。

### 5、Unit类型、Null类型和Nothing类型

| 类型    | 描述                                                         |
| ------- | ------------------------------------------------------------ |
| Unit    | 表示无值，和其他语言中void等同。用作不返回任何结果的方法的结果类型。Unit只有一个实例值，写成()。 |
| Null    | null  , Null 类型只有一个实例值  null                        |
| Nothing | Nothing类型在Scala的类层级的最低端；它是任何其他类型的子类型。  当一个函数，我们确定没有正常的返回值，可以用Nothing 来指定返回类型，这样有一个好处，就是我们可以把返回的值（异常）赋给其它的函数或者变量（兼容性） |

1、使用细节

（1）Null类只有一个实例对象，null，类似于Java中的null引用。null可以赋值给任意引用类型(AnyRef)，但是不**能赋值给值类型(AnyVal 比如 Int, Float, Char, Boolean, Long, Double, Byte, Short)**

（2）Unit类型用来标识过程，也就是没有明确返回值的函数。由此可见，Unit类似于Java里的void。Unit只有一个实例，()，这个实例也没有实质的意义。

（3）Nothing，可以作为没有正常返回值的方法的返回类型，非常直观的告诉你这个方法不会正常返回，而且由于Nothing是其他任意类型的子类，他还能跟要求返回值的方法兼容。

```scala
object UnitNullNothing {
  def main(args: Array[String]): Unit = {
    var res = sayHello()
    //输出为()
    println("res=" + res)

    //null可以赋值给AnyRef
    val dog: Dog = null
    //报错，不能赋值给AnyVal
    //    val char1:Char =null
  }

  def sayHello(): Unit = {
  }
}

class Dog {
}
```

## 3.3 值类型的转换

1、隐式转换

​		Scala程序在进行赋值或者运算时，精度小的类型自动转换为精度大的数据类型，这个就是自动类型转换(**隐式转换**)。

转换细节：

（1）有多种类型的数据混合运算时，系统首先自动将所有数据转换成容量最大的那种数据类型，然后再进行计算。（ 5.6 + 10 = 》double）

（2）当我们把精度(容量)大 的数据类型赋值给精度(容量)小 的数据类型时，就会报错，反之就会进行自动类型转换。

（3）(byte, short) 和 char之间不会相互自动转换。

（4）byte，short，char 他们三者可以计算，在计算时首先转换为int类型。

```scala
 var b: Byte = 10
    var c: Char =90
    //报错，int不能转换为long
//    var s: Short = b + c
//    var s2: Short = 10 + 90
    //正确
    var s3: Short = 100
```

（5）自动提升原则： 表达式结果的类型自动提升为操作数中最大的类型。

2、强制类型转换

​		自动类型转换的逆过程，将容量大的数据类型转换为容量小的数据类型。使用时要加上强制转函数，**但可能造成精度降低或溢出**，格外要注意。

转换细节：

（1）当进行数据的 从 大——>小，就需要使用到强制转换。

（2）强转符号只针对于最近的操作数有效，往往会使用小括号提升优先级。

```scala
var num1: Int = 10 * 3.5.toInt + 6*1.5.toInt
var num2: Int = (10 * 3.5 + 6*1.5).toInt
println(num1 + "-----" + num2)
```

（3）Char类型可以保存 Int的常量值，但不能保存Int的变量值，需要强转。

```scala
var char2: Char = 1
var num3:Int = 1
//报错
var char3: Char = num3
```

（4）Byte和Short类型在进行运算时，当做Int类型处理。

3、值类型和String类型的转换

​		在程序开发中，我们经常需要将基本数据类型转成String 类型。或者将String类型转成基本数据类型。

（1）基本类型转String类型

语法： 

```ini
基本类型的值+""
```

（2）String类型转基本数据类型

使用String的toXxx方法即可。

```scala
var s1 = "str"
s1.toInt
```

（3）注意事项

​		在将String 类型转成 基本数据类型时，要确保String类型能够转成有效的数据。

​		比如：可以把 "123" , 转成一个整数，但是不能把 "hello" 转成一个整数。把 "12.5" （String）转成 Int也是错误的。 

## 3.4 标识符

1、概念

（1）Scala 对各种变量、方法、函数等命名时使用的字符序列称为标识符

（2）凡是自己可以起名字的地方都叫标识符

2、命名规则

Scala中的标识符声明，基本和Java是一致的，但是细节上会有所变化。

（1）首字符为字母，后续字符任意字母和数字，美元符号，可后接下划线_。

（2）数字不可以开头。

（3）**首字符为操作符(比如+ - \* / )，后续字符也需跟操作符 ,至少一个(反编译)**。

```scala
//-+可以，-a报错
var ++ = "hell0";
```

（4）操作符(比如+-*/)不能在标识符中间和最后。

（5）用反引号**`` ....` `**包括的任意字符串，即使是关键字(39个)也可以 （true）。

```scala
var `true` = "hello2";
```

3、标识符举例

```scala
//ok，在scala中，Int 不是关键字，而是预定义标识符,可以用，但是不推荐
Int 
//不可以，因为在scala中，_ 有很多其他的作用，因此不能使用
_
```

4、scala的39个关键字

```ini
package, import, class, object, trait, extends, with, type, forSome
private, protected, abstract, sealed, final, implicit, lazy, override
try, catch, finally, throw 
if, else, match, case, do, while, for, return, yield
def, val, var 
this, super
new
true, false, null
```

# 4 运算符

## 4.1 介绍

运算符是一种特殊的符号，用以表示数据的运算、赋值和比较等。

+ 算术运算符

+ 赋值运算符 

+ 比较运算符(关系运算符)

+ 逻辑运算符

+ 位运算符

### 1、算术运算符

​		算术运算符(arithmetic)是对数值类型的变量进行运算的，在Scala程序中使用的非常多。

| **运算符** | **运算**   | **范例**   | **结果** |
| ---------- | ---------- | ---------- | -------- |
| **+**      | 正号       | +3         | 3        |
| **-**      | 负号       | b=4;  -b   | -4       |
| **+**      | 加         | 5+5        | 10       |
| **-**      | 减         | 6-4        | 2        |
| *****      | 乘         | 3*4        | 12       |
| **/**      | 除         | 5/5        | 1        |
| **%**      | 取模(取余) | 7%5        | 2        |
| **+**      | 字符串相加 | “He”+”llo” | “Hello”  |

1、/ % 的使用

（1）对于除号“/”，它的整数除和小数除是有区别的：整数之间做除法时，只保留整数部分而舍弃小数部分。 例如：var x : Int = 10/3 ,结果是 3

（2）当对一个数取模时，可以等价 a%b=a-a/b*b ， 这样我们可以看到取模的一个本质运算(和java 的取模规则一样)。

```scala
def main(args: Array[String]): Unit = {
  // /使用
  var r1 : Int = 10 / 3  //3
  println("r1=" + r1)
  var r2 : Double = 10 / 3 //3.0
  println("r2=" + r2)

  //有一个为double类型，故可以保留小数部分
  var r3 : Double = 10.0 / 3
  println("r3=" + r3 )
  //格式化输出
  println("r3=" + r3.formatted("%.2f") )

  //%的使用,a%b=a-a/b*b
  println(10 % 3) //1
  // -10 -(-10)/3*3 = -10-(-3)*3 = -10 + 9 =-1
  println(-10 % 3)
}
```

2、细节说明

Scala中没有操作符 ++、--，需要通过+=、-=来实现同样的效果。

### 2、关系运算符

（1）关系运算符的结果都是boolean型，也就是要么是true，要么是false。

（2）关系表达式经常用在 if 结构的条件中或循环结构的条件中。

（3）关系运算符的使用和java一样。

| **运算符** | **运算                 范例                     结果**    |
| ---------- | --------------------------------------------------------- |
| **==**     | 相等于                4==3                          false |
| **!=**     | 不等于                4!=3                          true  |
| **<**      | 小于                  4<3                           false |
| **>**      | 大于                  4>3                           true  |
| **<=**     | 小于等于              4<=3                          false |
| **>=**     | 大于等于              4>=3                          true  |

1、细节说明

（1）比较运算符“==”不能误写成“=”。

（2）使用陷阱: 如果两个浮点数进行比较，应当保证数据类型一致。

### 3、逻辑运算符

​		用于连接多个条件（一般来讲就是关系表达式），最终的结果也是一个Boolean值。

假定变量 A 为 true，B 为 false

| 运算符 | 描述   | 实例                         |
| ------ | ------ | ---------------------------- |
| &&     | 逻辑与 | (A  && B) 运算结果为  false  |
| \|\|   | 逻辑或 | (A  \|\| B) 运算结果为  true |
| !      | 逻辑非 | !(A  && B) 运算结果为  true  |

### 4、赋值运算符

赋值运算符就是将某个运算后的值，赋给指定的变量。

| 运算符 | 描述                                           | 实例                                     |
| :----- | ---------------------------------------------- | ---------------------------------------- |
| =      | 简单的赋值运算符，将一个表达式的值赋给一个左值 | C =  A + B 将 A +  B 表达式结果赋值给  C |
| +=     | 相加后再赋值                                   | C  += A 等于 C = C + A                   |
| -=     | 相减后再赋值                                   | C  -= A 等于 C = C - A                   |
| *=     | 相乘后再赋值                                   | C  *= A 等于 C = C * A                   |
| /=     | 相除后再赋值                                   | C  /= A 等于 C = C / A                   |
| %=     | 求余后再赋值                                   | C  %= A 等于 C = C % A                   |
| <<=    | 左移后赋值                                     | C  <<= 2  等于 C = C << 2                |
| >>=    | 右移后赋值                                     | C  >>= 2  等于 C = C >> 2                |
| &=     | 按位与后赋值                                   | C  &= 2  等于 C = C & 2                  |
| ^=     | 按位异或后赋值                                 | C  ^= 2   等于 C = C ^ 2                 |
| \|=    | 按位或后赋值                                   | C  \|= 2   等于 C = C \| 2               |

1、运算符特点

（1）运算顺序从右往左。

（2）赋值运算符的左边只能是变量,右边可以是变量、表达式、常量值/字面量。

### 5、位运算符

| 运算符 | 描述           | 实例                                                         |
| ------ | -------------- | ------------------------------------------------------------ |
| &      | 按位与运算符   | (a  & b) 输出结果  12 ，二进制解释： 0000 1100               |
| \|     | 按位或运算符   | (a  \| b) 输出结果  61 ，二进制解释： 0011 1101              |
| ^      | 按位异或运算符 | (a  ^ b) 输出结果  49 ，二进制解释： 0011 0001               |
| ~      | 按位取反运算符 | (~a  ) 输出结果  -61  ，二进制解释：  1100  0011，  在一个有符号二进制数的补码形式。 |
| <<     | 左移动运算符   | a  << 2 输出结果  240  ，二进制解释：  1111  0000            |
| >>     | 右移动运算符   | a  >> 2 输出结果  15 ，二进制解释： 0000 1111                |
| >>>    | 无符号右移     | A  >>>2 输出结果  15,  二进制解释:  0000 1111                |

1、细节说明

Scala不支持三目运算符 **,** 在Scala中使用**if – else**的方式实现。

```scala
//不存在第一种
val num = 5 > 4 ? 5 : 4 
val num = if (5>4) 5 else 4
```

### 6、运算符优先级

| 类别 | 运算符                              | 关联性     |
| ---- | ----------------------------------- | ---------- |
| 1    | ()  []                              | 左到右     |
| 2    | ! ~                                 | **右到左** |
| 3    | * / %                               | 左到右     |
| 4    | + -                                 | 左到右     |
| 5    | >>  >>> <<                          | 左到右     |
| 6    | >  >= < <=                          | 左到右     |
| 7    | ==  !=                              | 左到右     |
| 8    | &                                   | 左到右     |
| 9    | ^                                   | 左到右     |
| 10   | \|                                  | 左到右     |
| 11   | &&                                  | 左到右     |
| 12   | \|\|                                | 左到右     |
| 13   | =  += -= *= /= %= >>= <<= &= ^= \|= | **右到左** |
| 14   | ,                                   | 左到右     |

（1）运算符有不同的优先级，所谓优先级就是表达式运算中的运算顺序。如右表，上一行运算符总优先于下一行。 

（2）只有单目运算符、赋值运算符是从右向左运算的。

（3）运算符的优先级和Java一样。

小结：运算符的优先级

1. () []

2. 单目运算

3. 算术运算符

4. 移位运算

5. 比较运算符(关系运算符)

6. 位运算

7. 关系运算符

8. 赋值运算

## 4.2 从键盘输入

在编程中，需要接收用户输入的数据，就可以使用键盘输入语句来获取。

1、java中步骤

（1）导入该类的所在包。

（2）创建该类对象（声明变量）。

（3）调用里面的功能。

2、使用StdIn即可

```scala
  def main(args: Array[String]): Unit = {
    println("请输入姓名：")
    var name = StdIn.readLine()
    printf("用户的姓名为：name =%s", name)
  }
```

# 5 程序流程控制

## 5.1 分支控制

1、使用

（1）单分支

基本语法

```scala
if (条件表达式) {
	执行代码块
}
```

（2）双分支

基本语法

```scala
if (条件表达式) {
	执行代码块1
} else {
	执行代码块2
}
```

（3）多分支

```scala
if (条件表达式1) {
	执行代码块1
}
else if (条件表达式2) {
	执行代码块2
}
……
else {
	执行代码块n
}
```

​		当条件表达式1成立时，即执行代码块1，如果表达式1不成立，才去判断表达式2是否成立，如果表达式2成立，就执行代码块2，以此类推，如果所有的表达式都不成立，则执行else 的代码块。

​	注意：只能有一个执行入口。

（4）嵌套编程

基本语法

```scala
if(){
	if(){  
    	代码1
	}else{   
    	代码2
	}
}
```

​		在一个分支结构中又完整的嵌套了另一个完整的分支结构，里面的分支的结构称为内层分支外面的分支结构称为外层分支。嵌套分支不要超过3层。

2、使用细节

（1）如果大括号{}内的逻辑代码只有一行，大括号可以省略, 这点和java 的规定一样。

（2）Scala中任意**表达式都是有返回值的**，也就意味着if else表达式其实是有返回结果的，具体返回结果的值取决于满足条件的代码体的最后一行内容。

（3）Scala中是没有三元运算符，因为可以这样简写。

```scala
//因为 scala 的if-else 是有返回值的，因此，本身这个语言也不需要三元运算符了，并且可以写在同一行，类似三元运算。
val result = if (flg) 1 else 0   
```

```scala
def main(args: Array[String]): Unit = {
  var sumVal = 9
  val result = if (sumVal > 20) {
    "结果大于20"
  }
  //输出为()(Unit)
  println(result)
}
```

3、switch分支结构

在scala中没有switch,而是使用**模式匹配**来处理。

## 5.2 for循环控制

​		Scala 也为for 循环这一常见的控制结构提供了非常多的特性，这些for 循环的特性被称为**for** **推导式**（for comprehension）或**for** **表达式**（for expression）。

1、**范围数据循环方式**

```scala
def main(args: Array[String]): Unit = {
  var start = 1
  var end = 3
    //换成until
  for(i <- start to  end){
    println(i + "你好")
  }

  //for推导式也可以直接对集合进行遍历
  var list = List("hello",10,30)
  for (item <- list) {
    println("item="+item)
  }
}
```

说明：

​		其中，i表示循环的变量， `<- 、 to 、until`是系统规定的关键字，to关键字i将会从1-3 循环， 前后闭合的范围；until关键字是前不和后台的范围。

2、**循环守卫**

```scala
for(i <- 1 to 3 if i != 2) {
    print(i + " 循环守卫")
}
```

说明：

​		循环守卫，即循环保护式（也称条件判断式，守卫）。保护式为`true`则进入循环体内部，为`false`则跳过，类似于`continue`。

3、**引入变量**

```scala
//没有关键字，所以范围后一定要加；来隔断逻辑
for(i <- 1 to 3; j = 4 - i) {
  println(j + " ")
}
```

4、**嵌套循环**

```scala
for(i <- 1 to 3; j <- 1 to 3) {
  println(" i =" + i + " j = " + j)
}
```

等价于：

```java
for (i <- 1 to 3) {
    for (j <- 1 to 3) {
        println(i + " " + j + " ")
    }
}
```

5、**循环返回值**

```scala
val res = for(i <- 1 to 10) yield {
      i
}
//控制台输出为：Vector(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
println(res)
```

​		将遍历过程中处理的结果返回到一个新**Vector**集合中，使用**yield**关键字。`i`这里我们可以看成一个代码块，意味着我们可以对`i`的逻辑进行处理，然后返回给集合。

4、**使用花括号{}代替小括号()**

```scala
for(i <- 1 to 3; j =  i * 2) {
  println(" i= " + i + " j= " + j)
}
```

等价于：

```scala
for{
      i <- 1 to 3
      j = i * 2} {
      println(" i= " + i + " j= " + j)
 }
```

（1）{}和()对于for表达式来说都可以。

（2）for 推导式有一个不成文的约定：当for 推导式仅包含单一表达式时使用圆括号，当其包含多个表达式时使用大括号。

（3）当使用**{}** 来换行写表达式时，分号就不用写了。

5、**步长控制**

```scala
//步长控制（开始，结束，步长）
for(i <- Range(1,10,2)){
    //来源于：Range = new Range(start, end, step)
    //输出结果：1，3，5，7，9
	println("i=" + i)
}
```

## 5.3 while循环

1、基本语法

```scala
while (循环条件) {
    循环体(语句)
    循环变量迭代
}
```

2、细节说明

（1）循环条件是返回一个布尔值的表达式

（2）while循环是先判断再执行语句

（3）与If语句不同，While语句本身没有值，即整个While语句的结果是Unit类型的()

（4）因为while中没有返回值,所以当要用该语句来计算并返回结果时,就不可避免的使用变量 ，而变量需要声明在while循环的外部，那么就等同于循环的内部对外部的变量造成了影响，所以**不推荐使用**，而是推荐使用for循环。

2、do..while循环控制

1、语法

```scala
do{
    循环体(语句)
    循环变量迭代
} while(循环条件)
```

2、细节说明

（1）循环条件是返回一个布尔值的表达式

（2）`do..while`循环是先执行，再判断

3)和`while` 一样，因为`do…while`中没有返回值,所以当要用该语句来计算并返回结果时,就不可避免的使用变量 ，而变量需要声明在`do...while`循环的外部，那么就等同于循环的内部对外部的变量造成了影响，所以**不推荐使用**，而是推荐使用for循环。

3、**多重循环控制**

（1）将一个循环放在另一个循环体内，就形成了嵌套循环。其中，`for ,while ,do…while`均可以作为外层循环和内层循环。**建议一般使用两层，最多不要超过3层**

（2）实质上，嵌套循环就是把内层循环当成外层循环的循环体。当只有内层循环的循环条件为false时，才会完全跳出内层循环，才可结束外层的当次循环，开始下一次的循环。

（3）设外层循环次数为m次，内层为n次， 则内层循环体实际上需要执行m*n=mn次。

4、**while循环的中断**

​		Scala内置控制结构特地去掉了break和continue，是为了更好的适应函数化编程，推荐使用**函数式的风格**解决break和contine的功能，而不是一个关键字。

​		Scala内置控制结构特地也去掉了continue，是为了更好的适应函数化编程，可以使用`if – else`或是 循环守卫实现continue的效果。

1. break的应用实例

```scala
def main(args: Array[String]): Unit = {
  var n = 1

  breakable {
    while (n < 10) {
      n += 1
      println("n=" + n)
      if (n == 8) {
        
        //import util.control.Breaks._
        //中断
        break()
      }
    }
  }

  //通过breakable函数捕获异常，可以让程序执行输出。
  println("-------ok------")
}
```

对于breakable函数的定义：

```scala
def breakable(op: => Unit) {
  try {
    op
  } catch {
    case ex: BreakControl =>
      if (ex ne breakException) throw ex
  }
}
```

说明：

（1）breakable是一个高阶函数，可以接收函数的函数就是高阶函数。

（2）op: => Unit表示接收的参数是一个没有输入和返回值的函数，可以简单理解为接收一段代码块。

（3）breakable对break()抛出的异常做出了处理，代码会继续执行。

（4）当我们传入的是代码块，breakable后面的()会变成{}。

2. continue的应用实例

​		`Scala`内置控制结构特地也去掉了`continue`，是为了更好的适应函数化编程，可以使用 `if – else` 或是 循环守卫实现`continue`的效果。

```scala
for (i <- 1 to 10 if (i != 2 && i != 3)) {
println("i=" + i)
}
```

# 6 函数式编程

## 6.1 介绍

### 1、函数式编程内容

（1）函数式编程基础

+ 函数定义/声明

+ 函数运行机制

+ 递归//难点 [最短路径，邮差问题，迷宫问题, 回溯]

+ 过程

+ 惰性函数和异常

（2）函数式编程高级

+ 值函数(函数字面量) 

+ **高阶函数** 

+ 闭包 

+ 应用函数 

+ 柯里化函数，抽象控制...

​		在scala中，函数式编程和面向对象编程融合在一起，学习函数式编程式需要oop的知识，同样学习oop需要函数式编程的基础。

授课顺序： 函数式编程基础->面向对象编程->函数式编程高级。

### 2、几个概念的说明

在学习Scala中将方法、函数、函数式编程和面向对象编程明确一下：

它们的关系分析图如下：

<img src="scala学习.assets\image-20200720223530852.png" alt="image-20200720223530852" style="zoom:80%;" />

（1）在scala中，方法和函数几乎可以等同(比如他们的定义、使用、运行机制都一样的)，只是函数的使用方式更加的灵活多样。

（2）函数式编程是从编程方式(范式)的角度来谈的，可以这样理解：函数式编程把函数当做一等公民，充分利用函数、 支持的函数的多种使用方式。

​		比如： 在Scala当中，函数是一等公民，像变量一样，既可以作为函数的参数使用，也可以将函数赋值给一个变量. ，函数的创建不用依赖于类或者对象，而在Java当中，函数的创建则要依赖于类、抽象类或者接口。

方法转函数示例：

```scala
object Method2Function {
  def main(args: Array[String]): Unit = {
    //1.使用方法，我们首先要创建一个对象
    val cat = new Cat
    println(cat.sum(10, 20))

    //2.方法转成函数
    val f1 = cat.sum _
    println("f1=" + f1)
    println("f1=" + f1(10, 30))

    //3.函数，求两个数的和
    val f2 = (n1: Int, n2: Int) => {
      n1 + n2
    }
    println("f2=" + f2)
    println("f2=" + f2(10, 40))
  }
}

class Cat {
  //方法
  def sum(n1: Int, n2: Int): Int = {
    n1 + n2
  }
}
```

控制台输出：

```scala
30
f1=<function2>
f1=40
f2=<function2>
f2=50
```

（3）面向对象编程是以对象为基础的编程方式。

（4）在scala中函数式编程和面向对象编程融合在一起了 。

### 3、函数介绍

为完成某一功能的程序指令(语句)的集合,称为函数。

### 4、函数定义

```ini
def 函数名 ([参数名: 参数类型], ...)[: 返回值类型] =] {
	语句...
	return 返回值
}
```

（1）函数声明关键字为def  (definition)。

（2）[参数名: 参数类型], ...：表示函数的输入(就是参数列表), 可以没有。 如果有，多个参数使用逗号间隔。

（3）函数中的语句：表示为了实现某一功能代码块。

（4）函数可以有返回值，也可以没有。

（5）返回值形式1:  **: 返回值类型** = 

（6）返回值形式2:  = 表示返回值类型不确定，使用类型推导完成。

（7）返回值形式3:   表示没有返回值，return 不生效。

（8）如果没有**return** ,默认以执行到最后一行的结果作为返回值。

### 5、递归调用

一个函数在函数体内又调用了本身，我们称为递归调用。

（1）函数递归需要遵守的重要原则

+ 程序执行一个函数时，就创建一个新的受保护的独立空间(**新函数栈**)。

+ 函数的局部变量是独立的，不会相互影响。

+ 递归必须向退出递归的条件逼近，否则就是无限递归。

+ 当一个函数执行完毕，或者遇到return，就会返回，遵守谁调用，就将结果返回给谁。

（2）案例

```scala
object Demo01 {
  def main(args: Array[String]): Unit = {
    test(4)
  }
  def test (n: Int) {
    if (n > 2) {
        //退出递归的条件
      test (n - 1)
    }
    println("n=" + n) 
  }
}
```

控制台输出：

```ini
n=2
n=3
n=4
```

<img src="scala学习.assets\image-20200721100300012.png" alt="image-20200721100300012" style="zoom:67%;" />

### 6、函数注意事项

（1）函数的形参列表可以是多个, 如果函数没有形参，调用时 可以不带()。

（2）形参列表和返回值列表的数据类型可以是值类型和引用类型。

（3）Scala中的函数可以根据函数体最后一行代码自行推断函数返回值类型。那么在这种情况下，return关键字可以省略。

（4）因为Scala可以自行推断，所以在省略return关键字的场合，返回值类型也可以省略。

（5）如果函数明确使用return关键字，那么函数返回就不能使用自行推断了,这时要明确写成 **:** **返回类型** **=** ，当然如果你什么都不写，即使有return 返回值也为()。

```scala
def getSum(n1: Int, n2: Int): Int = {
//因为这里有明确的return , 这时 getSum 就不能省略 : Int = 的 Int了
    return n1 + n2
  }
```

（6）如果函数明确声明无返回值（声明Unit），那么函数体中即使使用return关键字也不会有返回值。

```scala
//Unit也不写
def getsum2(n1: Int,n2: Int):Unit = {
	return n1 + n2
}
//和上面等价
def getsum2(n1: Int,n2: Int){
	return n1 + n2
}
//输出“()”
println(getsum2(1,2))
```

（7）如果明确函数无返回值或不确定返回值类型，那么返回值类型可以省略(或声明为Any)。

```scala
def f3(s: String) = {
    if(s.length >= 3)
      s + "123"
    else
      3
}

def f4(s: String): Any = {
    if(s.length >= 3)
      s + "123"
    else
      3
}
```

（8）Scala语法中任何的语法结构都可以嵌套其他语法结构(灵活)，即：函数中可以再声明/定义函数，类中可以再声明类 ，方法中可以再声明/定义方法。

（9）Scala函数的形参，在声明参数时，直接赋初始值(默认值)，这时调用函数时，如果没有指定实参，则会使用默认值。如果指定了实参，则实参会覆盖默认值。

```scala
//name的默认值为jack。
def sayOk(name : String = "jack"): String = {
      return name + " ok! "
 }
```

（10）如果函数存在多个参数，每一个参数都可以设定默认值，那么这个时候，传递的参数到底是覆盖默认值，还是赋值给没有默认值的参数，就不确定了(默认按照声明顺序[**从左到右**])。在这种情况下，可以采用**带名参数**。

```scala
object DetailParameter01 {
  def main(args: Array[String]): Unit = {
//    mysqlCon()

    //从左向右覆盖参数
//    mysqlCon("127.0.0.1",7777)
    
    //指定覆盖某个默认值，使用带名参数即可。
    mysqlCon(user = "xiaolun",pwd = "123")
  }

  def mysqlCon(add:String = "localhost",port : Int = 3306,
               user: String = "root", pwd : String = "root"): Unit = {
    println("add=" + add)
    println("port=" + port)
    println("user=" + user)
    println("pwd=" + pwd)
  }
}
```

（11）scala函数的形参默认是**val**的，因此不能在函数中进行修改。

（12）递归函数未执行之前是无法推断出来结果类型，在使用时**必须有明确的返回值类型**。

```scala
//错误，递归不能使用类型推断，必须指定返回的数据类型
//返回值形式2：:Int去掉会报错 
  def f8(n: Int): Int = { 
    if (n <= 0)
      1
    else
      n*f8(n - 1)
  }
```

（13）Scala函数支持可变参数。

```scala
//支持0到多个参数
def sum(args :Int*) : Int = { 
}
//支持1到多个参数
def sum(n1: Int, args:  Int*) : Int  = { 
}
```

### 7、过程

​		将函数的返回类型为Unit的函数称之为过程(procedure)，如果明确函数没有返回值，那么等号可以省略。

细节说明：

（1）注意区分: 如果函数声明时没有返回值类型，但是有 = 号，可以进行类型推断最后一行代码。这时这个函数实际是有返回值的，该函数并不是过程。

（2）开发工具的自动代码补全功能，虽然会自动加上Unit，但是考虑到Scala语言的简单，灵活，最好不加。

```
def test(): Unit ={  
}

//(过程)推荐这个
def test(){  
}
```

### 8、惰性函数

（1）场景

​		惰性计算（**尽可能延迟表达式求值**）是许多函数式编程语言的特性。惰性集合在需要时提供其元素，无需预先计算它们，这带来了一些好处。

​		首先，您可以将耗时的计算推迟到绝对需要的时候。

​		其次，您可以创造无限个集合，只要它们继续收到请求，就会继续提供元素。函数的惰性使用让您能够得到更高效的代码。Java 并没有为惰性提供原生支持，Scala提供了。

```scala
object LazyDemo01 {
  def main(args: Array[String]): Unit = {
    //如果没有lazy关键字，sum()求和函数会被执行。
    lazy val res = sum(10, 20)
    println("-----------------")
    //当要使用res 时，sun才执行
    println("res=" + res)
  }

  //求和函数
  def sum(n1: Int, n2: Int): Int = {
    //证明函数又没有执行，输出一句话
    println("sum() 执行了..")
    n1 + n2
  }
}
```

（2）介绍

​		当函数返回值被声明为**lazy**时，函数的执行将被推迟，直到我们首次对此取值，该函数才会执行。这种函数我们称之为惰性函数，在Java的某些框架代码中称之为懒加载(延迟加载)。

（3）细节说明

+ lazy 不能修饰 var 类型的变量。
+ 不但是 在调用函数时，加了 **lazy** ,会导致函数的执行被推迟，我们在声明一个变量时，如果给声明了 lazy ,那么变量值得分配也会推迟。 比如：

```scala
lazy val i = 10
```

### 9、异常

（1）介绍

​		Scala提供try和catch块来处理异常。try块用于包含可能出错的代码。catch块用于处理try块中发生的异常。可以根据需要在程序中有任意数量的try...catch块。

（2）案例

```scala
object ExceptionDemo01 {
  def main(args: Array[String]): Unit = {
    try {
      val r = 10 / 0
    } catch {
      /**
       * 1、只有一个catch
       * 2、一个catch中有多个case,一个case就可以匹配发生的一种异常。
       * 3、=>表示关键字，不能改动。后面是对异常的处理代码（也可以是代码块）。
       * 4、finally为最终要执行的代码。
       */
      case ex: ArithmeticException => println("捕获了除数为零的算数异常")
      case ex: Exception => println("捕获了异常")
    } finally {
      // 最终要执行的代码,一般用来释放资源。
      println("scala finally...")
    }
  }
}
```

（3）细节说明

+ 我们将可疑代码封装在try块中。 在try块之后使用了一个catch处理程序来捕获异常。如果发生任何异常，catch处理程序将处理它，程序将不会异常终止。

+ Scala的异常的工作机制和Java一样，但是Scala没有“checked(编译期)”异常，即Scala没有编译异常这个概念，**异常都是在运行的时候捕获处理**。

+ 用throw关键字，抛出一个异常对象。所有异常都是Throwable的子类型。throw表达式是有类型的，就是Nothing，因为Nothing是所有类型的子类型，所以throw表达式可以用在需要类型的地方

```scala
object ThrowDemo01 {
  def main(args: Array[String]): Unit = {
    //情况1：不处理异常
//    val res = test()
//    println(res.toString)
//
//    //异常还没处理时，下面代码不会执行
//    println("-----ok------")

    //情况2：如果我们希望test()抛出异常后，代码可以继续执行，我们需要对其进行处理
    try{
      test()
    }catch {
      case ex:Exception => println("捕获到了异常："+ex.getMessage)
    }
    println("-----ok------")
  }

  //自定义的异常
  def test(): Nothing = {
    throw new Exception("------异常出现-----")
  }
}
```

+ 在Scala里，借用了模式匹配的思想来做异常的匹配，因此，在catch的代码里，是一系列case子句来匹配异常。，当匹配上后 => 有多条语句可以换行写（类似 java 的 switch case x: 代码块..）。
+ 异常捕捉的机制与其他语言中一样，如果有异常发生，catch子句是按次序捕捉的。因此，在catch子句中，**越具体的异常**越要靠前，越普遍的异常越靠后，如果把越普遍的异常写在前，把具体的异常写在后，在scala中也不会报错，但这样是非常不好的编程风格。

+ `finally`子句用于执行不管是正常处理还是有异常发生时都需要执行的步骤，一般用于对象的清理工作，这点和Java一样。

+ Scala提供了throws关键字来声明异常。可以使用方法定义声明异常。 它向调用者函数提供了此方法可能引发此异常的信息。 它有助于调用函数处理并将该代码包含在try-catch块中，**以避免程序异常终止**。在scala中，可以使用**throws**注释来声明异常。

```scala
object ThrowsDemo01 {
  def main(args: Array[String]): Unit = {
    f11()
  }

  /**
   *1、等同于Java中的 NumberFormatException.class
   * 指定抛出的异常类型是NumberFormatException.class
   */
  @throws(classOf[NumberFormatException])
  def f11() = {
    "abc".toInt
  }
}
```