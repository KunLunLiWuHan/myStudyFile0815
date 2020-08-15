# 1 数据结构上

1、scala集合基本介绍

（1）Scala同时支持不可变集合和可变集合，不可变集合可以安全的并发访问。

（2）两个主要的包：

不可变集合：scala.collection.immutable

可变集合： scala.collection.mutable

（3）Scala默认采用不可变集合，对于几乎所有的集合类，Scala都同时提供了可变(mutable)和不可变(immutable)的版本。

（4）Scala的集合有三大类：序列Seq、集Set、映射Map，所有的集合都扩展自**Iterable**特质，在Scala中集合有可变（mutable）和不可变（immutable）两种类型。 

2、可变集合和不可变集合举例

（1）不可变集合：scala不可变集合，就是这个**集合本身**不能动态变化（当然，其内容集合可以变化）。(类似java的数组，是不可以动态增长的)

（2）可变集合:可变集合，就是这个集合本身可以动态变化的。(比如:ArrayList , 是可以动态增长的) 

3、不可变集合继承层次

<img src="scala学习3.assets\image-20200722084650983.png" alt="image-20200722084650983" style="zoom:80%;" />

小结：

（1）Set、Map是Java中也有的集合。

（2）Seq是Java没有的，我们发现List归属到Seq了,因此这里的List就和java不是同一个概念了。

（3）我们前面的for循环有一个 1 to 3 ,就是IndexedSeq 下的Vector。

（4）String也是属于IndexeSeq。

```scala
object CollectionDemo01 {
  def main(args: Array[String]): Unit = {
    val str = "hello"
    //字符串在scala中就是char的集合IndexSeq
    for (item <- str){
      println(item)
    }

    println(str(2)) //索引：l
  }
}
```

（5）我们发现经典的数据结构比如Queue 和 Stack被归属到LinearSeq。

（6）大家注意Scala中的Map体系有一个SortedMap,说明Scala的Map可以支持排序。

（7）IndexSeq 和 LinearSeq 的区别IndexSeq是通过索引来查找和定位，因此速度快，比如String就是一个索引集合，通过索引即可定位，LineaSeq 是线型的，即有头尾的概念，这种数据结构一般是通过遍历来查找，它的价值在于应用到一些。
4、可变集合继承层次

![timg](scala学习3.assets\timg.jpg)

## 1.1 数组

### 1、定长数组

（1）方式1

```scala
val arr1 = new Array[Int](10)
//赋值,集合元素采用小括号访问
arr1(1) = 7 
```

​		这里的数组等同于Java中的数组,中括号的类型就是数组的类型

```scala
object ArrayDemo01 {
  def main(args: Array[String]): Unit = {
    /**
     * 1、创建了一个Array对象
     * 2、[Int]表示泛型，即该数组中，只能存放Int型
     * 3、[Any]表示该数组可以存放任务数组。
     * 4、在没有赋值的情况下，各个元素的值为0
     */
    val arr01 = new Array[Int](4)
    println(arr01.length) //4
    println("arr01(0)=" + arr01(0))//0

    //数组的遍历
    for (i <- arr01) {
      println(i)
    }
    println("--------------------")

    //数组的修改，通过下标
    arr01(3) = 10
    for (i <- arr01) {
      println(i)
    }
  }
}
```

（2）方法2

在定义数组时，直接赋值。

```scala
def main(args: Array[String]): Unit = {
  /**
   * 1、使用的是object Array的apply
   * 2、直接初始化数组，给了整数和“”，数组的泛型为Any
   */
  var arr02 = Array(1, 3, "xxx")
  for (i <- arr02) {
    println(i)
  }
   //可以使用我们传统方式遍历，使用下标的方式遍历
   for(index <- 0 until arr02.length){
       println(arr02(index))
   }
}
```

### 2、变长数组

学习集合的流程(创建,查询,修改,删除)。

（1）声明创建

```scala
object ArrayBufferDemo01 {
  def main(args: Array[String]): Unit = {
    //创建ArrayBuffer
    //也可以这样写：val arr01 = ArrayBuffer[Any]()
    val arr01 = ArrayBuffer[Any](3, 2, 5)

    //通过下标访问元素
    println("arr01(1)=" + arr01(1))
    //遍历
    for (i <- arr01) {
      println(i)
    }
    println(arr01.length) //3
    println("arr01.hash=" + arr01.hashCode())

    //使用append追加元素，append支持可变参数
    //可以理解为java数组的扩容
    arr01.append(90.0, 13)
    println("arr01.hash=" + arr01.hashCode())

    //修改
    arr01(1) = 89
    println("-------------修改-------------")
    for (i <- arr01) {
      println(i)
    }

    //删除
    arr01.remove(0)
    println("-------------删除-------------")
    for (i <- arr01) {
      println(i)
    }
    println("最新的长度=" + arr01.length)
  }
}
```

（2）小结

+ ArrayBuffer是变长数组，类似java的ArrayList。

+ val arr2 = ArrayBuffer[Int] 也是使用的apply方法构建对象。

+ **def** append(elems: A*) { appendAll(elems) } 接收的是可变参数。

+ 每append一次，arr在底层会重新分配空间，进行扩容，arr2的内存地址会发生变化，也就成为新的ArrayBuffer。

（3）定长数组与变长数组的转换

```scala
//定长数组转可变数组
//arr2.toArray 返回结果才是一个定长数组， arr2本身没有变化
arr1.toBuffer 

//可变数组转定长数组
arr2.toArray  
```

### 3、多维数组

```scala
//定义
//说明：二维数组中有三个一维数组，每个一维数组中有四个元素(3行4列)
val arr = Array.ofDim[Double](3,4)

//赋值
arr(1)(1) = 11.11
```

案例

```scala
object MultiplyArray {
  def main(args: Array[String]): Unit = {

    //创建
    val array1 = Array.ofDim[Int](3, 4)
    array1(1)(1) = 9
    //取出二维数组的各个元素（一维数组）
    for (item <- array1) {
      //一维数组遍历
      for (item2 <- item) {
        print(item2 + "\t")
      }
      println()
    }

    println("===================")
    //传统方式遍历
    for (i <- 0 to array1.length - 1) {
      for (j <- 0 to array1(i).length - 1) {
        printf("arr[%d][%d]=%d\t", i, j, array1(i)(j))
      }
      println()
    }
  }
}
```

控制台输出结果：

<img src="scala学习3.assets\image-20200722094313801.png" alt="image-20200722094313801" style="zoom:80%;" />

#### 4、Scala数组与Java的List的互转

（1）Scala数组转Java的List

```scala
object ArrayBuffer2JavaList {
  def main(args: Array[String]): Unit = {
    // Scala集合和Java集合互相转换
    val arr = ArrayBuffer("1", "2", "3")

    import scala.collection.JavaConversions.bufferAsJavaList
    //ProcessBuilder 用到 bufferAsJavaList
    val javaArr = new ProcessBuilder(arr) 
    //arrList为java中的arrList
    val arrList = javaArr.command()
    println(arrList) //输出 [1, 2, 3]
  }
}
```

（2）Java的List转Scala数组(mutable.Buffer)

```scala
import scala.collection.JavaConversions.asScalaBuffer
import scala.collection.mutable
// java.util.List ==> Buffer
val scalaArr: mutable.Buffer[String] = arrList
scalaArr.append("jack")
println(scalaArr)
```

## 1.2 元组

### 1、介绍

​		元组也是可以理解为一个**容器**，可以存放各种相同或不同类型的数据。说的简单点，就是将多个无关的数据封装为一个整体，称为元组, 最多的特点灵活和松散，对数据没有过多的约束。

注意：元组中最大只能有22个元素。

### 2、元组的创建

```scala
object TupleDemo01 {
  def main(args: Array[String]): Unit = {
    /**
     * 创建
     * 1、tuple1是一个Tuple，类型是Tuple5
     * 2、为了高效地操作元组，编译器根据元素地个数不同，
     * 会对应不同地元组类型。
     * 分别是：Tuple1....Tuple22
     */
    val tuple1 = (1, 2, 3, "hello", 4)
    println(tuple1)
  }
}
```

### 3、元组数据的访问

​		访问元组中的数据,可以采用顺序号（_顺序号），也可以通过索引（productElement）访问。

```scala
def main(args: Array[String]): Unit = {
  val t1 = (1, "a", "b", true, 2)
  println(t1._1) //访问元组的第一个元素 ，从1开始
  println(t1.productElement(0)) // 访问元组的第一个元素，从0开始
}
```

### 4、元组数据的遍历

Tuple是一个整体，遍历需要调其迭代器。

```scala
def main(args: Array[String]): Unit = {
  val t1 = (1, "a", "b", true, 2)
  
  for(item <- t1.productIterator){
    println(item)
  }
}
```

## 1.3 List

​		Scala中的List 和Java List 不一样，在Java中List是一个接口，真正存放数据是ArrayList，**而Scala的List可以直接存放数据，就是一个object**，默认情况下Scala的List是**不可变**的，List属于序列Seq。

```scala
val List = scala.collection.immutable.List
```

```scala
def main(args: Array[String]): Unit = {
  /**
   * 创建时，直接分配元素
   * 1、默认情况下List是scala.collection.immutable.List，即不可变长度
   * 2、在scala中，如果需要使用可变的list,使用ListBuffer
   */
  val list01 = List(1, 2, 3)
  println(list01)

  //空集合
  val list02 = Nil
  println(list02) //输出：List()
}
```

小结：

（1）List 在 scala包对象声明的,因此不需要引入其它包也可以使用。

（2）List 中可以放任何数据类型，比如 arr1的类型为 List[Any]。

```
 //添加数据的另一个类型 
 val list01 = List(1, 2, 3,"hello")
```

（3）如果希望得到一个空列表，可以使用**Nil**对象, 在 scala包对象声明的,因此不需要引入其它包也可以使用。

```scala
val Nil = scala.collection.immutable.Nil
```

### 1、访问List元素

```scala
// 1是索引（从0开始），表示取出第2个元素
val value1 = list1(1) 
```

### 2、元素的追加

​		向列表中增加元素, 会返回新的列表/集合对象（并没有破坏不可变的特性）。注意：Scala中List元素的追加形式非常独特，和Java不一样。

（1）方式1-在列表的最后增加数据

```scala
def main(args: Array[String]): Unit = {
var list1 = List(1, 2, 3, "abc")
    // :+运算符表示在列表的最后增加数据
    val list2 = list1 :+ 4
    println(list1) //list1没有变化
    println(list2) //新的列表结果是 [1, 2, 3, "abc", 4]
  }
```

（2）方式2-在列表的最前面增加数据

```scala
var list1 = List(1, 2, 3, "abc")
// :+运算符表示在列表的最后增加数据
val list2 = 4 +: list1
println(list1) //list1没有变化
println(list2) //List(4, 1, 2, 3, abc)
```

（3）方式3-在列表的最后增加数据

```scala
val list1 = List(1, 2, 3, "abc")
  /**
   * 1、list()
   * 2、list(List(1, 2, 3, abc))
   * 3、list(6,List(1, 2, 3, abc))
   *在原先基础上把数据放进去
   */
  val list5 = 4 :: 5 :: 6 :: list1 :: Nil
  println(list5) //List(4, 5, 6, List(1, 2, 3, abc))
}
```

:::案例：

```
val list1 = List(1, 2, 3, "abc")
/**
* 1、list()
* 2、list（1, 2, 3, abc)（将原有数据打散后放进去）
*/
val list7 = 4 :: 5 :: 6 :: list1 ::: Nil
println(list7) //List(4, 5, 6, 1, 2, 3, abc)
```

说明：

+ 符号`::`表示向集合中新建集合添加元素。
+ 运算时，集合对象一定要放置在最右边。
+ 运算规则，从右向左。
+ `:::`运算符是将集合中的每一个元素加入到空集合中去。

### 3、ListBuffer

ListBuffer是**可变的**list集合，可以添加，删除元素，ListBuffer属于seq序列。

```scala
object ListBufferDemo01 {
  def main(args: Array[String]): Unit = {
    //创建ListBuffer
    val lst0 = ListBuffer[Int](1, 2, 3)

    //访问
    println("lst0(2)=" + lst0(2)) //3
    //遍历，有序
    for (item <- lst0) {
      println("item=" + item)
    }

    //创建一个空的listbuffer
    val lst1 = new ListBuffer[Int]
    //动态的添加元素，对list1直接变化
    lst1 += 4  //ListBuffer(4)
    lst1.append(5) //ListBuffer(4, 5)
    println("lst1="+ lst1)

    //把一个集合加到另外一个集合中。
    lst0 ++= lst1  //ListBuffer(1, 2, 3, 4, 5)

    val lst2 = lst0 ++ lst1 //ListBuffer(1, 2, 3, 4, 5,4,5)
    val lst3 = lst0 :+ 5 //ListBuffer(1, 2, 3, 4, 5,5)

    println("=====删除=======")
    println("lst1=" + lst1)
    lst1.remove(1)
    for (item <- lst1) {
      println("item=" + item)
    }
  }
}
```

## 1.4 Queue

### 1、队列的说明

（1）队列是一个**有序**列表，在底层可以用数组或是链表来实现。

（2）其输入和输出要遵循**先入先出**的原则。即：先存入队列的数据，要先取出，后存入的要后取出。

（3）在Scala中，由设计者直接给我们提供队列类型使用。

（4）在scala中, 有 `scala.collection.mutable.Queue` 和 `scala.collection.immutable.Queue` ，一般来说，我们在开发中通常使用**可变集合中**的队列。 

### 2、队列的创建

```scala
def main(args: Array[String]): Unit = {
  import scala.collection.mutable
  //说明: 这里的Int是泛型，表示q1队列只能存放Int类型
  //如果希望q1可以存放其它类型，则使用 Any 即可。
  val q1 = new mutable.Queue[Int]
  println(q1)
}
```

### 3、队列元素的追加数据

```scala
    val q1 = new mutable.Queue[Int]
//    给队列添加元素
    q1 += 20
    println(q1)  //Queue(20)
//    拆开后相加，默认数据加在队列的后面
    q1 ++= List(2,4,6) //Queue(20, 2, 4, 6)
    println(q1)
```

元素为list时：

```scala
val q1 = new mutable.Queue[Any] //下面的编译可以通过
q1 += 20

//表示将List(1,2,3)作为一个元素存入到队列中
q1 += List(1,2,3) //泛型为Any才ok，Queue(20, List(1, 2, 3))
println(q1)
```

### 4、删除和加入队列元素

按照进入队列的顺序删除元素（队列先进先出）。

```scala
object QueueDemo02 {
  def main(args: Array[String]): Unit = {
    val q1 = new mutable.Queue[Int]
    q1 += 12
    q1 += 34
    q1 ++= List(2, 9)
    println(q1) //Queue(12, 34, 2, 9)

    //从队列头开始删除
    val queueElement = q1.dequeue()
    println("queueElement="+queueElement) //取出元素12
    println(q1)//Queue(34, 2, 9)
    //从尾部入队列
    val queueElement2 = q1.enqueue(20, 60)
    println("queueElement="+queueElement2) //()
    println(q1) //Queue(34, 2, 9, 20, 60)
  }
}
```

### 5、返回队列的元素

```scala
//返回队列的第一个元素,对q1没有任何影响。
println(q1.head)

//返回队列最后一个元素
println(q1.last)

//返回除了第一个以外剩余的元素， 可以级联使用，这个在递归时使用较多。
println(q1.tail)
```

案例：

```scala
println(q1) //Queue(34, 2, 9, 20, 60)
println(q1.head) //34
println(q1.last) //60
println(q1.tail) //Queue(2, 9, 20, 60)
println(q1.tail.tail) //Queue( 9, 20, 60)
```

## 1.5 Map

### 1、Scala中的Map介绍

​		HashMap 是一个散列表(数组+链表)，它存储的内容是键值对(key-value)映射，Java中的HashMap是无序的，key不能重复。

（1）Scala中的Map 和Java类似，也是一个散列表，它存储的内容也是键值对(key-value)映射，Scala中不可变的Map是有序的，可变的Map是无序的。

（2）Scala中，有可变Map (`scala.collection.mutable.Map`) 和 不可变Map(`scala.collection.immutable.Map`) 

### 2、构建Map

Scala中的不可变Map是有序，构建Map中的元素底层是Tuple2类型。

（1）构造不可变映射

```scala
 def main(args: Array[String]): Unit = {
    /**
     * 构建Map
     * 1、默认Map是immutable.Map
     * 2、key-value类型支持Any
     */
    val map1 = Map("Alice" -> 10, "Bob" -> 20, "Kotlin" -> "北京")
     //Map(Alice -> 10, Bob -> 20, Kotlin -> 北京)
    println(map1)
  }
```

从输出的结果看到，输出顺序和声明顺序一致。

（2）构造可变映射

```scala
//构建可变映射
val map2 = scala.collection.mutable.Map("Alice" -> 10, "Bob" -> 20, "Kotlin" -> 30)
/**
 * Map(Bob -> 20, Kotlin -> 30, Alice -> 10)
 * 从输出的结果看到，输出顺序和声明顺序不一致
 */
println(map2)
```

（3）创建空的映射

```scala
//创建空的映射
val map3 = new scala.collection.mutable.HashMap[String, Int]
//Map()
println(map3)
```

（4）**对偶元组**

即创建包含**键值对**的二元组， 和第一种方式等价，只是形式上不同而已。

对偶元组就是只含有两个数据的元组。

```scala
//对偶元组
val map4 = mutable.Map( ("A", 1), ("B", 2), ("C", 3),("D", 30) )
//map4=Map(D -> 30, A -> 1, C -> 3, B -> 2)
println("map4=" + map4)
println(map4("A")) //1
```

### 3、取值

（1）使用map（key）

```scala
val value1 = map2("Alice")
println(value1)
```

说明:

+ 如果key存在，则返回对应的值。

+ 如果key不存在，则抛出异常[java.util.NoSuchElementException]。

+ 在Java中,如果key不存在则返回null。

（2）使用contains方法检查是否存在key

```scala
val map4 = mutable.Map( ("A", 1), ("B", 2), ("C", 3))
//如果key存在，返回true;反之，返回false。
if( map4.contains("B") ) {
    println("key存在 值= " + map4("B"))
} else {
    println("key不存在")
}
```

使用containts先判断在取值，可以防止异常，并加入相应的处理逻辑。

（3）使用map.get(key).get取值 

​		通过 映射.get(键) 这样的调用返回一个Option对象，要么是Some，要么是None。

```scala
/**
 * 1、map.get方法会将数据进行包装
 * 2、如果 map.get(key) key存在返回some,如果key不存在，则返回None
 * 3、如果 map.get(key).get  key存在，返回key对应的值,否则，
 * 抛出异常 java.util.NoSuchElementException: None.get
 */
var map5 = mutable.Map( ("A", 1), ("B", "北京"), ("C", 3) )
println(map5.get("A")) //some(1)
println(map5.get("A").get) //得到Some然后再取出
```

（4）使用map4.getOrElse()取值

```scala
/**
 * 1、如果key存在，返回key对应的值。
 * 2、如果key不存在，返回默认值。在java中底层有很多类似的操作。
 */
val map6 = mutable.Map( ("A", 1), ("B", "北京"), ("C", 3) )
println(map6.getOrElse("A","默认")) //1
println(map6.getOrElse("D","默认值")) //默认值
```

如何选择取值方式：

（a）如果我们确定map有这个key ,则应当使用map(key), 速度快

（b）如果我们不能确定map是否有key ,**而且有不同的业务逻辑**，使用`map.contains()` 先判断在加入逻辑。

（c）如果只是简单的希望得到一个值，使用`map4.getOrElse("ip","127.0.0.1")`。

### 4、修改、添加和删除

针对的是**可变的**map元素。

（1）更新map的元素

```scala
def main(args: Array[String]): Unit = {
  val map1 = mutable.Map( ("A", 1), ("B", "北京"), ("C", 3) )
  map1("AA") = 20
  map1("B") = "上海"
  //Map(A -> 1, AA -> 20, C -> 3, B -> 上海)
  println(map1)
}
```

说明：

（a）map 是可变的，才能修改，否则报错。

（b）如果key存在：则修改对应的值,key不存在,等价于添加一个key-val。

（2）添加map元素

方式1：增加单个元素

```scala
val map2 = mutable.Map( ("A", 1), ("B", "北京"), ("C", 3) )
map2 += ( "D" -> 4 ) //添加元素
map2 += ( "B" -> "上海" ) //（已经存在的key）修改元素
//Map(D -> 4, A -> 1, C -> 3, B -> 上海)
println(map2)
```

方式2：增加多个元素

```scala
//增加多个元素
val map3 = mutable.Map( ("A", 1), ("B", "北京"), ("C", 3) )
val map4 = map3 + ("E"->1, "F"->3)
map3 += ("EE"->1, "FF"->3)
//Map(A -> 1, C -> 3, EE -> 1, B -> 北京, FF -> 3)
println(map3)
//Map(A -> 1, C -> 3, F -> 3, E -> 1, B -> 北京)
println(map4)
```

（3）删除map元素

```scala
val map5 = mutable.Map( ("A", 1), ("B", "北京"), ("C", 3) )
map5 -= ("A", "B")
//map5=Map(C -> 3)
println("map5=" + map5)
```

5、遍历

```scala
val map1 = mutable.Map(("A", 1), ("B", "北京"), ("C", 3))

/**方式1：很简洁
 * 输出：A is mapped to 1
 */
for ((k, v) <- map1) println(k + " is mapped to " + v)
/**
 * 方式2：和方式1差不多,按照元组的方式来取。
 * 1、输出：(A,1)，(C,3)
 * 2、取出方式v类型是Tuple2
 */
for (v <- map1) println(v)
//输出：(A,1),key = A,value = 1
for (v <- map1) println(v + ",key = " + v._1 + ",value = "+ v._2)

//只关心key,将key值取出来
for (k <- map1.keys) println(k)
//只关心value,将value值取出来
for (v <- map1.values) println(v)
```

## 1.6 set

1、介绍

**集是不重复元素的集合**。集不保留顺序，默认是以哈希集实现。

Java中Set的回顾

​		java中，HashSet是实现Set< E >接口的一个实体类，数据是以哈希表的形式存放的，里面的不能包含重复数据。Set接口是一种不包含重复元素的 collection，HashSet中的数据也是没有顺序的。 

Scala中Set的说明

​		默认情况下，Scala 使用的是**不可变集合**，如果你想使用可变集合，需要引用 `scala.collection.mutable.Set` 包。

2、创建

```scala
//不可变集合的创建，不需要引入包
val set = Set(1, 2, 3) //不可变
//Set(1, 2, 3)
println(set)

////可变集合的创建，需要引入包
import scala.collection.mutable.Set
val mutableSet = Set(1, 2, 3)
//Set(1, 2, 3)
println(mutableSet)
```

3、添加

```scala
/**
 * 可变集合元素的添加
 * 如果添加的对象已经存在，则不会重复添加，
 * 也不会报错
 */
mutableSet.add(4) //方式1
//Set(1, 2, 3, 4)
println(mutableSet)

mutableSet += 6  //方式2
//Set(1, 2, 6, 3, 4)
println(mutableSet)

mutableSet.+=(5) //方式3
//Set(1, 5, 2, 6, 3, 4)
println(mutableSet)
```

4、删除

```scala
/**
 * 可变集合的元素删除
 *如果删除的对象不存在，则不生效，也不会报错
 */
  println("---------删除--------")
val set02 = mutable.Set(1,2,4,"abc")
// 方式1：操作符形式
set02 -= 2
// 方式2：scala的Set可以直接删除值
set02.remove("abc")
//Set(1, 4)
println(set02)
```

5、遍历

```scala
println("---------遍历--------")
val set03 = mutable.Set(1, 2, 4, "abc")
for(x <- set03) {
  println(x)
}
```

# 2 数据结构下

## 2.1 集合元素的映射

### 1、引入

（1）需求：将List(3,5,7) 中的所有元素都 * 2 ，将其结果放到一个新的集合中返回，即返回一个新的List(6,10,14),

（2）传统方式

```scala
object Demo01 {
  def main(args: Array[String]): Unit = {
    val list1 = List(3, 5, 7)
    var list2 = List[Int]()
    for (item <- list1) { //遍历
      list2 = list2 :+ item * 2
    }
    println(list2)
  }
}
```

缺点：

（a）不够简洁和高效。

（b）没有体现函数式编程的特点。（集合 -》函数-》新的集合-》函数..）

（c）不利于处理复杂的数据处理业务

### 2、高阶函数和Map映射

（1）上面提出的问题，其实就是一个关于集合元素映射操作的问题。

​		在Scala中可以通过**map映射**操作来解决：将集合中的每一个元素通过指定功能（函数）映射（转换）成新的结果集合这里其实就是所谓的将函数作为参数传递给另外一个函数,这是函数式编程的特点。

以HashSet为例说明：

```ini
def map[B](f: (A) ⇒ B): HashSet[B]   //map函数的签名
这个就是map映射函数集合类型都有
[B] 是泛型
map 是一个高阶函数(可以接收一个函数的函数，就是高阶函数)，可以接收 函数 f: (A) => B 
HashSet[B] 就是返回的新的集合
```

（2）入门案例1

```scala
object HigOrderFunDemo01 {
  def main(args: Array[String]): Unit = {

    /**
     * 1、使用高阶函数
     * 2、test(sum, 6.0)中sum不用加()，因为传入的是一个函数
     * 若带上()表示该函数执行了。
     * 3、sum(6.0)返回什么值，高阶函数就返回什么值
     * 4、也可以将sum 换成 sum _，_表示将一个函数传递给别人
     */
    val res = test(sum, 6.0)
    println("res=" + res) //12.0

    /**
     * 1、在scala中。可以把一个函数直接赋值给一个变量，但是不执行该函数
     * 2、我们通过在函数后面添加“_”,如果无“_”函数中方法会执行。
     * 3、方法需要将()去掉
     */
    val f1 = myPrint _
    f1() //执行
  }

  /**
   * 1、test就是一个高阶函数
   * 2、f: Double（接收） => Double（返回）表示一个函数
   * 该函数可以接收一个Double参数，并返回一个Double参数。
   * 3、n1: Double表示普通参数
   * 4、f(n1)表示在test函数中执行你传入的函数
   */
  def test(f: Double => Double, n1: Double) = {
    f(n1)
  }

  //普通函数
  def sum(d: Double): Double = {
    d + d
  }

  def myPrint(): Unit = {
    println("hello world!")
  }
}
```

（3）入门案例2

```scala
object HigOrderFunDemo02 {
  def main(args: Array[String]): Unit = {
    test2(sayOK)
  }

  /**
   * 1、test2是一个高阶函数，可以接收一个没有输入且返回值为Unit的函数
   */
  def test2(f: () => Unit) = {
    f()
  }

  def sayOK() = {
    println("sayOKKK...")
  }

}
```

（3）入门案例3-解决引入问题

```scala
object HigOrderFunDemo03 {
  def main(args: Array[String]): Unit = {
    val list1 = List(3, 5, 7)

    def f1(n1: Int): Int = {
      2 * n1
    }

    //map映射
    /**
     * list1.map(f1)说明
     * 1、将list集合元素依次遍历
     * 2、将各个元素传递给f1函数
     * 3、返回一个新的int值，将得到的新的int
     * 放入到一个新的集合中并返回
     */
    val list2 = list1.map(f1)
    println(list2)

    println("----------------------------------")
    /**
     * 模拟实现map映射函数的机制
     */
    val myList = MyList()
    val myList2 = myList.map(f1)
    println("myList2=" + myList2)
  }
}

//底层模拟代码
class MyList {
  //将集合在这里写死，正常的话，应该是读取list集合
  var list1 = List(3, 5, 7)
  var list2 = List[Int]()

  def map(f: Int => Int): List[Int] = {
    for (item <- list1) {
      list2 = list2 :+ f(item)
    }
    list2
  }
}

object MyList {
  def apply(): MyList = new MyList()
}
```

### 3、集合扁平化操作

**flatmap**映射：flat即压扁，压平，扁平化映射。

flatmap：flat即压扁，压平，扁平化，效果就是将集合中的**每个元素的子元素**映射到某个**函数**并返回新的集合。

```scala
object FlatMapDemo01 {
  def main(args: Array[String]): Unit = {
    val names = List("Alice", "Bob", "Nick")

    def upper(s: String): String = {
      s.toUpperCase
    }
    //注意：每个字符串也是char集合
    /**
     * 1、需求：将List集合中的所有元素，进行扁平化操作，
     * 即把所有元素打散
     * 2、List(A, L, I, C, E, B, O, B, N, I, C, K)
     */
    println(names.flatMap(upper))

  }
}
```

4、集合元素的过滤

filter：将符合要求的数据(筛选)放置到新的集合中。

（1）案例：将 val names = List("Alice", "Bob", "Nick") 集合中首字母为'A'的筛选到新的集合。

```scala
object FilterDemo01 {
  def main(args: Array[String]): Unit = {
    val names = List("Alice", "Bob", "Nick")

    def startA(s: String): Boolean = {
      s.startsWith("A")
    }

    val names2 = names.filter(startA)
    println("names=" + names2)
  }
}
```

5、化简

化简：将二元函数引用于集合中的函数。

（1）需求

val list = List(1, 20, 30, 4 ,5) ,求出list的和。

```scala
object ReduceDemo01 {
  def main(args: Array[String]): Unit = {
    val list = List(1, 20, 30, 4, 5)

    def sum(n1: Int, n2: Int): Int = {
      n1 + n2
    }

    /**
     * 使用化简的方式来计算list集合的和
     * 1、reduceLeft(f) 接收的函数需要的形式为 op: (B, A) => B): B
     * 2、reduceleft(f) 的运行规则是 从左边开始执行将得到的结果返回给第一个参数
     * 然后继续和下一个元素运行，将得到的结果继续返回给第一个参数，继续..
     * 3、((((1 + 2)  + 3) + 4) + 5) = 15
     */
    val res = list.reduceLeft(sum)
    println("res=" + res)
  }
}
```

**reduceLefft(_ + _)**（接收一个函数时，也可以直接传入一个匿名函数；类似于Java中传入匿名对象）这个函数的执行逻辑如图：

<img src="scala学习3.assets\image-20200802092801904.png" alt="image-20200802092801904" style="zoom:80%;" />

（2）分析下面的代码，并使用化简的方法求出List(1, 2, 3, 4 ,5)最小的值

```scala
object ReduceDemo02 {
  def main(args: Array[String]): Unit = {
    val list = List(1, 2, 3, 4, 5)


    //((((1-2)-3)-4)-5) = -13
    println(list.reduceLeft(minus)) // -13

    /**
     * (4-5)将结果放到 num2
     * (1-(2-(3-(4-5)))) = 3
     */
    println(list.reduceRight(minus)) //(((-3)-2)-1)
    println(list.reduce(minus)) //-13 等价于reduceLeft
    
    println("-----------------------------")
    println(list.reduce(min))
  }

  def minus(num1: Int, num2: Int): Int = {
    num1 - num2
  }

  //求出最小值
  def min(n1: Int,n2: Int): Int={
    if(n1 > n2) n2 else n1
  }
}
```

6、折叠

​		fold函数将上一步返回的值作为函数的第一个参数继续传递参与运算，直到list中的所有元素被遍历。

（1）理解

​		可以把reduceLeft看做简化版的foldLeft。reduceLeft就是调用的foldLeft[ B ] (head)并且是默认从集合的head元素开始操作的。
​		相关函数：fold，foldLeft，foldRight，可以参考reduce的相关方法理解

```scala
object FoldDemo01 {
  def main(args: Array[String]): Unit = {
    // 折叠
    val list = List(1, 2, 3, 4)

    def minus(num1: Int, num2: Int): Int = {
      num1 - num2
    }

    /**
     * 1、list.foldLeft(5)(minus) 可以理解为
     * list(5,1,2,3,4) list.reduceleft(minus) -5
     * 2、foldRight
     * list(1,2,3,4,5) list.reduceRight(minus) 3
     */
    println(list.foldLeft(5)(minus)) // 函数的柯里化
    println(list.foldRight(5)(minus))

  }
}
```

（2）折叠缩写方式

foldLeft和foldRight缩写方法分别是：`/:和:\`。

```scala
var i6 = (1 /: list4) (minus) // =等价=> list4.foldLeft(1)(minus)
```

7 、扫描

​		扫描，即对某个集合的所有元素做fold操作，但是会把产生的所有中间结果放置于一个集合中保存。

```scala
object ScanDemo01 {
  def main(args: Array[String]): Unit = {

    /**
     * 5 (1,2,3,4,5) =>(5,4,2,-1,-5,-10)
     * 步骤：
     * （1）将5放到左边并保存
     * （2）5和1进行折叠，（5-1）=4 并将结果保存放到第二个位置上
     * （3）4-2 = 2
     * ...
     */
    val i8 = (1 to 5).scanLeft(5)(minus) // i8 类型：IndexedSeq[Int]
    println(i8)


    //5 (1,2,3,4,5) =>(5,6,8, 11,15,20)
    val i9 = (1 to 5).scanLeft(5)(add) //IndexedSeq[Int]
    println(i9)

  }

  //普通函数
  def minus(num1: Int, num2: Int): Int = {
    num1 - num2
  }

  def add(num1: Int, num2: Int): Int = {
    num1 + num2
  }
}
```

## 2.2 扩展

### 1、拉链（合并）

在开发中，当我们需要将两个集合进行对偶元组合并，可以使用拉链。

```scala
object ZipDemo01 {
  def main(args: Array[String]): Unit = {
    val list1 = List(1, 2, 3)
    val list2 = List(4, 5, 6)
    //拉链
    val list3 = list1.zip(list2)
    //对偶元组：List((1,4), (2,5), (3,6))
    println("list3=" + list3)

    //如果要取出合并后的各个对偶元组的数据，可以遍历
    for(item<-list3){
      println(item._1 + " " + item._2) //取出时，按照元组的方式取出即可
    }

  }
}
```

注意事项：

+ 拉链的本质就是两个集合的合并操作，合并后每个元素是一个 对偶元组。
+ 操作的规则如下图：

<img src="scala学习3.assets\image-20200802102232926.png" alt="image-20200802102232926" style="zoom:80%;" />

+ 如果两个集合个数不对应，会造成数据丢失。
+ 集合不限于List, 也可以是其它集合比如 Array。

### 2、迭代器

​		通过iterator方法从集合获得一个迭代器，通过while循环和for表达式对集合进行遍历。

```scala
object IteratorDemo01 {
  def main(args: Array[String]): Unit = {
    // 得到迭代器
    val iterator = List(1, 2, 3, 4, 5).iterator
    println("--------遍历方式1 while-----------------")
    while (iterator.hasNext) {
      println(iterator.next())
    }
    println("--------遍历方式2 for -----------------")

    /**
     * 1、for循环迭代要保证该集合在上面
     * 没有迭代过（类似指针到最后了）。才能够正常输出。
     */
    for (enum <- iterator) {
      println(enum)
    }
  }
}
```

小结

+  iterator 的构建实际是 AbstractIterator 的一个匿名子类，该子类提供了下面的方法

```ini
def iterator: Iterator[A] = new AbstractIterator[A] {
    var these = self
    def hasNext: Boolean = !these.isEmpty
    def next(): A =
```



```ini
def iterator: Iterator[A] = new AbstractIterator[A] {
    var these = self
    def hasNext: Boolean = !these.isEmpty
    def next(): A =
```

+ 该AbstractIterator 子类提供了 **hasNext** **next** 等方法。因此，我们可以使用 while的方式，使用hasNext next 方法变量。

### 3、流Stream

​		stream是一个集合。这个集合，可以用于存放无穷多个元素，但是这无穷个元素并不会一次性生产出来，而是需要用到多大的区间，就会动态的生产，末尾元素遵循lazy规则(即：要使用结果才进行计算的) 。

```scala
object StreamDemo01 {
  def main(args: Array[String]): Unit = {
    val stream1 = numsForm(1)
    println(stream1) //Stream(1, ?)
    //取出第一个元素
    println("head=" + stream1.head) //head=1
    println(stream1.tail) //触发操作，获取：Stream(2, ?)
    println(stream1) //Stream(1, 2, ?)

    //使用map映射stream的元素并进行一些计算
    println(numsForm(5).map(multi)) //(25,?)

  }

  /**
   * 说明；创建Stream
   * （1）Stream 集合存放的数据类型是BigInt
   * （2）numsForm 是自定义的一个函数，函数名是程序员指定的。
   * （3）创建的集合的第一个元素是 n , 后续元素生成的规则是 n + 1
   * （4）后续元素生成的规则是可以程序员指定的 ，比如 numsForm( n * 4)...
   */
  def numsForm(n: BigInt): Stream[BigInt] = n #:: numsForm(n + 1)

  //创建Stream2
  def multi(x: BigInt): BigInt = {
    x * x
  }

}
```

注意：

如果使用流集合，就不能使用last属性，如果使用last集合就会进行无限循环。

### 4、视图View

Stream的懒加载特性，也可以对其他集合应用view方法来得到类似的效果，具有如下特点：

+ view方法产出一个总是被懒执行的集合。
+ view不会缓存数据，每次都要重新计算，比如遍历View时。

1、案例

找到1-100 中，数字倒序排列 和它本身相同的所有数。(1 2, 11, 22, 33 ...)。

```scala
object ViewDemo01 {
  def main(args: Array[String]): Unit = {

    //说明: 没有使用view,常规方式
    val viewSquares1 = (1 to 100)
      .filter(eq)
    //Vector(1, 2, 3, 4, 5, 6, 7, 8, 9, 11, 22, 33, 44, 55, 66, 77, 88, 99)
    println(viewSquares1)

    println("------------------------------------")

    /**
     * 使用view
     * 1、在程序中，对集合进行map，filter..
     * 不希望立即执行，而是使用到结果时才执行，
     * 此时，可以使用view来优化
     */
    val viewSquares2 = (1 to 100)
      .view
      .filter(eq)
    println(viewSquares2) //SeqViewF(...)

    /**
     * 1、使用到结果时，能够正常输出。
     * 2、将获得参数存到缓存中，用到时
     * 将其拿出来。
     */
    for (item <- viewSquares2) {
      println("item = " + item)
    }

  }

  //如果这个数，逆序后和原来相等，返回true,否则返回false
  def eq(i: Int): Boolean = {
    i.toString.equals(i.toString.reverse)
  }
}
```

### 5、线程安全集合

所有线程安全的集合都是以Synchronized开头的集合。

```ini
SynchronizedBuffer
SynchronizedMap
SynchronizedPriorityQueue
SynchronizedQueue
SynchronizedSet
SynchronizedStack
```

### 6、并行集合

1、Scala为了充分使用多核CPU，提供了并行集合（有别于前面的串行集合），用于多核环境的并行计算。**parallel**(pærəlel并行)。

2、主要用到的算法有： 
 		Divide and conquer : 分治算法，Scala通过splitters(分解器)，combiners（组合器）等抽象层来实现，主要原理是将计算工作分解很多任务，分发给一些处理器去完成，并将它们处理结果合并返回。

​		Work stealin算法，主要用于任务调度负载均衡（load-balancing），通俗点完成自己的所有任务之后，发现其他人还有活没干完，主动（或被安排）帮他人一起干，这样达到尽早干完的目的。

3、案例

（1）打印1-5

```scala
object ParDemo01 {
  def main(args: Array[String]): Unit = {
    (1 to 5).foreach(println(_))
    println()

    /**
     * 并行计算
     * 1、将输出任务分散到不同的cpu。
     * 2、输出1,3,4,2,5，结果无序。
     */
    (1 to 5).par.foreach(println(_))
  }
}
```

### 7、操作符

​		这部分内容没有必要刻意去理解和记忆，语法使用的多了，自然就会熟练的使用，该部分内容了解一下即可。

1、如果想在变量名、类名等定义中使用语法关键字（保留字），可以配合反引号反引号 。

```scala
val `val` = 42
```

2、中置操作符

A 操作符 B 等同于 A.操作符(B）。

3、后置操作符

A操作符 等同于 `A.操作符`。

4、前置操作符

+、-、！、~等操作符A等同于`A.unary_操作符` 。

5、赋值操作符

A 操作符= B 等同于 A = A 操作符 B ，比如 A += B 等价 A = A + B。

案例：

```scala
object OperationDemo01 {
  def main(args: Array[String]): Unit = {
    val n1 = 1
    val n2 = 2
    val r1 = n1 + n2

    //中置操作符,等价于上面
    val r2 = n1.+(n2)
    println("r1=" + r1 + " r2=" + r2)
    val dog = new Dog1
    dog.+(90)
    println("dog.age=" + dog.age) // 0 + 90 =90

    // 对操作符进行后置操作
    println(dog.++)
    println("dog.age=" + dog.age)

    //对操作符进行前置操作
    !dog
    println("dog.age=" + dog.age)
  }
}

class Dog1 {
  var age: Int = 0

  //对操作符进行重载(中置操作符)
  def +(n: Int): Unit = {
    this.age += n
  }

  //对操作符进行后置操作
  def ++(): Unit = {
    this.age += 1
  }

  //  对操作符进行前置操作（一元运算符）
  def unary_!(): Unit = {
    this.age = - this.age
  }
}
```

