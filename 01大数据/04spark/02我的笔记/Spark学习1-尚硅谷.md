# 1、Spark运行模式

## 1、重要角色

1、Driver（驱动器）

Spark的驱动器是执行开发程序中的main方法的进程。它负责开发人员编写的用来创建SparkContext、创建RDD，以及进行RDD的转化操作和行动操作代码的执行。如果你是用spark shell，那么当你启动Spark shell的时候，系统后台自启了一个Spark驱动器程序，就是在Spark shell中预加载的一个叫作 sc的SparkContext对象。如果驱动器程序终止，那么Spark应用也就结束了。主要负责：

（1）把用户程序转为作业（JOB）。

（2）跟踪Executor的运行状况。

（3）为执行器节点调度任务。

（4）UI展示应用运行状况。

2、 Executor（执行器）

Spark Executor是一个工作进程，负责在 Spark 作业中运行任务，任务间相互独立。Spark 应用启动时，Executor节点被同时启动，并且始终伴随着整个 Spark 应用的生命周期而存在。如果有Executor节点发生了故障或崩溃，Spark 应用也可以继续执行，会将出错节点上的任务调度到其他Executor节点上继续运行。主要负责：

（1）负责运行组成 Spark 应用的任务，并将结果返回给驱动器进程。

（2）通过自身的块管理器（Block Manager）为用户程序中要求缓存的RDD提供内存式存储。RDD是直接缓存在Executor进程内的，因此任务可以在运行时充分利用缓存数据加速运算。

## 2、Local模式

1、介绍

Local模式就是运行在一台计算机上的模式，通常就是通过在本机上测试和练手，可以通过下面的集中方式设置Master。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201002151803239.png" alt="image-20201002151803239" style="zoom:67%;" />

2、官方求PI具体案例

（1）指令

```shell
[zookeeper@hadoop101 spark]$ bin/spark-submit \
--class org.apache.spark.examples.SparkPi \
--executor-memory 1G \
--total-executor-cores 2 \
./examples/jars/spark-examples_2.11-2.1.1.jar \
100
```

```shell
基本语法：
bin/spark-submit \
--class <main-class>
--master <master-url> \
--deploy-mode <deploy-mode> \
--conf <key>=<value> \

... # other options
<application-jar> \
[application-arguments]

参数说明：
--master 指定Master的地址，默认为Local

--class: 你的应用的启动类 (如 org.apache.spark.examples.SparkPi)

--deploy-mode: 是否发布你的驱动到worker节点(cluster) 或者作为一个本地客户端 (client) (default: client)*

--conf: 任意的Spark配置属性， 格式key=value. 如果值包含空格，可以加引号“key=value” 
application-jar: 打包好的应用jar,包含依赖. 这个URL在集群中全局可见。 
比如 hdfs:// 共享存储系统，如果是 file:// path，那么所有的节点的path都包含同样的jar

application-arguments: 传给main()方法的参数

--executor-memory 1G 指定每个executor可用内存为1G

--total-executor-cores 2 指定每个executor使用的cup核数为2个
```

（2）结果展示

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201002154753003.png" alt="image-20201002154753003" style="zoom: 50%;" />

3、Spark通用运行简易流程

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201002160400210.png" alt="image-20201002160400210" style="zoom:67%;" />



## 3、yarn模式

1、介绍

Spark客户端直接连接Yarn，不需要额外构建Spark集群。有yarn-client和yarn-cluster两种模式，主要区别在于：Driver程序的运行节点。

yarn-client：Driver程序运行在客户端，适用于交互、调试，希望立即看到app的输出。

yarn-cluster：Driver程序运行在由RM（ResourceManager）启动的AP（APPMaster）适用于生产环境。

![image-20201002173820833](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201002173820833.png)

2、安装部署

（1）修改hadoop配置文件yarn-site.xml,添加如下内容

```xml
 <!--是否启动一个线程检查每个任务正使用的物理内存量，如果任务超出分配值，则直接将其杀掉，默认是true -->
<property>
  <name>yarn.nodemanager.pmem-check-enabled</name>
  <value>false</value>
</property>
<!--是否启动一个线程检查每个任务正使用的虚拟内存量，如果任务超出分配值，则直接将其杀掉，默认是true -->
<property>
  <name>yarn.nodemanager.vmem-check-enabled</name>
  <value>false</value>
</property>
```

如果没有进行这样的配置，将会有下面的报错：

```
Failed to send RPC XXX to / XXX: java.nio.channels.ClosedChannel
```

（2）修改spark-env.sh，添加如下配置

```ini
YARN_CONF_DIR=/home/zookeeper/software/hadoop-2.7.2/etc/hadoop
```

（3）分发配置文件yarn-site.xml，spark-env.sh

（4）启动HDFS以及YARN集群，然后执行测试程序

```shell
bin/spark-submit \
--class org.apache.spark.examples.SparkPi \
--master yarn \
--deploy-mode client \
./examples/jars/spark-examples_2.11-2.1.1.jar \
100
```

（5）结果展示

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201002185730710.png" alt="image-20201002185730710" style="zoom:67%;" />

## 4、Yarn和spark的对比

| Yarn                              | Spark                                          | 描述                                                      |
| --------------------------------- | ---------------------------------------------- | --------------------------------------------------------- |
| ResouceManager                    | Master                                         | 管理子节点、资源调度、接收任务请求                        |
| NodeManger                        | Worker                                         | 管理当前节点，并管理子进程                                |
| YarnChild                         | Executor                                       | 运行真正的计算逻辑的（Task）                              |
| Client（客户端）ApplicaitonMaster | SparkSubmit（=（Client + ApplicaitonMaster）） | 提交app，管理该任务的Executor，并将Task提交到（Executor） |

# 2、RDD的转换

## 1、Value类型

1、map(func)

（1）作用

返回一个新的RDD，该RDD由每一个输入元素经过func函数转换后组成。

（2）需求

创建一个1-10数组的RDD，将所有元素*2形成新的RDD。

```scala
var rdd1 = sc.parallelize(1 to 10)
var rdd2 = rdd1.map(_*2)
 
 //打印
scala> rdd2.collect

//结果显示
res1: Array[Int] = Array(2, 4, 6, 8, 10, 12, 14, 16, 18, 20)
```

2、mapPartitions(func) 

（1）作用

类似于map，该算子可以独立地在RDD的每一个分片上运行，因此在类型为T的RDD上运行时，func的函数类型必须是Iterator[T] => Iterator[U]。

假设有N个元素，有M个分区，那么map的函数的将被调用N次，而mapPartitions被调用M次，一个函数一次处理所有分区。

（2）需求

创建一个RDD，使每个元素*2组成新的RDD。

```scala
var rdd2=sc.parallelize(Array(1,2))
var res = rdd2.mapPartitions(x => x.map(_*2))
```

（3）map()和mapPartition()的区别

 map()：每次处理一条数据。

 mapPartition()：每次处理一个分区的数据，这个分区的数据处理完后，原RDD中分区的数据才能释放，可能导致OOM。当内存空间较大的时候建议使用mapPartition()，以提高处理效率。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200808221744471.png" alt="image-20200808221744471" style="zoom: 50%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200808221922816.png" alt="image-20200808221922816" style="zoom: 50%;" />

3、mapPartitionsWithIndex(func)

（1）作用

类似于mapPartitions，但func带有一个整数参数表示分片的索引值，因此在类型为T的RDD上运行时，func的函数类型必须是(Int, Interator[T]) => Iterator[U]。

（2）需求

创建一个RDD，使每个元素跟所在分区形成一个元组组成一个新的RDD。

```scala
scala> var rdd3 = sc.parallelize(1 to 4)

scala> var indexRdd = rdd3.mapPartitionsWithIndex((index,items)=>(items.map((index,_))))

//输出结果，可以发现分区以0开头（0-3）
scala> indexRdd.collect
res11: Array[(Int, Int)] = Array((0,1), (1,2), (2,3), (3,4))
```

4、flatMap(func)

（1）作用

类似于map，但是每一个输入元素可以被映射为0或多个输出元素（所以func应该返回一个序列，而不是单一元素）

（2）需求

创建一个元素为1-4的RDD，输出结果为阶梯状：

```
Array(1, 1, 2, 1, 2, 3, 1, 2, 3, 4)
```

```scala
var flatmap01 = rdd.flatMap(1 to _)

//输出
scala> flatmap01.collect
res13: Array[Int] = Array(1, 1, 2, 1, 2, 3, 1, 2, 3, 4)
```

5、glom()

（1）作用

将每一个分区形成一个数组，形成新的RDD类型时RDD[Array[T]]。

（2）需求

将每个分区中的数据放到一个数组中。

```scala
scala> var rdd = sc.parallelize(1 to 4)

//输出，可以看到有4个数组，每个数组中包含一个分区的元素
scala> rdd.glom().collect
res15: Array[Array[Int]] = Array(Array(1), Array(2), Array(3), Array(4))
```

（3）查看rdd中的分区数量

```scala
scala> rdd2.partitions.size
```

6、 groupBy(func)

（1）作用

分组，按照传入函数的返回值进行分组。将相同的key对应的返回值的元素放入一个迭代器。

（2）需求

创建一个RDD，按照元素模以2的值进行分组。

```scala
scala> var rdd = sc.parallelize(1 to 4)

var res = rdd.groupBy(_%2)

//控制台输出
scala> res.collect
res16: Array[(Int, Iterable[Int])] = Array((0,CompactBuffer(2, 4)), (1,CompactBuffer(1, 3)))
```

7、filter(func)

（1）作用

过滤。返回一个新的RDD，该RDD由经过func函数计算后返回值为true的输入元素组成。

（2）需求

创建一个RDD（由字符串组成），过滤出一个新RDD（包含”a”的子串）

```scala
var rdd = sc.parallelize(Array("aa","bb","ac")

var res = rdd.filter(_.contains("a"))
//输出
scala> res.collect
res1: Array[String] = Array(aa, ac)
```

8、 sample(withReplacement, fraction, seed) 

（1）介绍

以指定的随机种子随机抽样出数量为fraction的数据，withReplacement表示是抽出的数据是否放回，true为有放回的抽样，false为无放回的抽样，seed用于指定随机数生成器种子。

（2）需求

创建一个RDD（1-10），从中选择不放回抽样。

（3）代码

```java
package operator01_valueType

import org.apache.spark.rdd.RDD
import org.apache.spark.{SparkConf, SparkContext}

object operator01_valueType {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("Operator").setMaster("local[4]")
    val sc = new SparkContext(conf)

    /**
     * 1、从指定的数据集合中进行抽样处理。
     * 生成数据，按照指定规则进行过滤。
     * 2、fraction ：抽出多少，这是一个double类型的参数,0-1之间，eg:0.3表示抽出30%
     */
    val listRDD: RDD[Int] = sc.makeRDD(1 to 10)
    //false表示无放回的抽样
    val sampleRDD: RDD[Int] = listRDD.sample(false, 1, 1)
    sampleRDD.collect().foreach(println)
  }
}
```

（4）spark代码

```scala
val rdd = sc.parallelize(1 to 10)

//不放回抽样
var sample1 = rdd.sample(false,0.4,2)
```

9、distinct([numTasks]))

（1）作用

对源RDD进行去重后返回一个新的RDD。默认情况下，只有8个并行任务来操作，但是可以传入一个可选的numTasks参数改变它。

（2）需求

创建一个RDD，使用distinct()对其去重。

```scala
var rdd = sc.parallelize(List(1,2,3,1,2))

//也可以指定并行度为2,rdd.distinct(2)
scala> var res = rdd.distinct()

//输出
scala> res.collect
res10: Array[Int] = Array(1, 2, 3)
```

10、 coalesce(numPartitions)

（1）作用

缩减分区数，用于大数据集过滤后，提高小数据集的执行效率。

（2）需求

创建一个4个分区的RDD，对其缩减分区。

```scala
val rdd = sc.parallelize(1 to 4,4)
val coalesceRDD = rdd.coalesce(3)

//缩减分区，查看结果
scala> coalesceRDD.partitions.size
res21: Int = 3
```

11、 repartition(numPartitions) 

（1）作用

根据分区数，重新通过随机洗牌所有数据。

（2）需求

创建一个4个分区的RDD，对其重新分区。

```scala
var rdd = sc.parallelize(1 to 4)
var reRdd = rdd.repartition(3)

scala> reRdd.partitions.size
res4: Int = 3
```

（3）coalesce和repartition的区别

coalesce重新分区，可以选择是否进行shuffle过程。由参数shuffle: Boolean = false/true决定。

repartition实际上是调用的coalesce，默认是进行shuffle的。

12、 sortBy(func,[ascending], [numTasks]) 

（1）作用

使用func先对数据进行处理，按照处理后的数据比较结果排序，默认为正序。

（2）需求

创建一个RDD，按照不同的规则进行排序。

```scala
var rdd = sc.parallelize(1 to 4)

//按照自生大小进行排序
scala> rdd.sortBy(x => x).collect
res10: Array[Int] = Array(1, 2, 3, 4)

//按照与3余数的大小排序
scala> rdd.sortBy(_%3).collect
res11: Array[Int] = Array(3, 1, 4, 2)
```

## 2、双Value类型

1、union(otherDataset)

（1）作用

对源RDD和参数RDD求并集后返回一个新的RDD。

（2）需求

创建两个RDD，求并集。

```scala
var rdd = sc.parallelize(1 to 4)
var rdd2 = sc.parallelize(3 to 6)
var rdd3 = rdd.union(rdd2)

//结果显示，可以发现相交的数据并没有去重
scala> rdd3.collect
res12: Array[Int] = Array(1, 2, 3, 4, 3, 4, 5, 6)
```

2、 subtract (otherDataset)

（1）作用

计算差的一种函数，去除两个RDD中相同的元素，不同的元素将会被保留下来。

（2）需求

创建两个RDD，求第一个RDD与第二个RDD的差集

```scala
//数据显示，使用交集中的rdd和rdd2
scala> rdd.subtract(rdd2).collect
res13: Array[Int] = Array(1, 2)
```

3、 intersection(otherDataset) 

（1）作用

对源RDD和参数RDD求交集后返回一个新的RDD。

（2）需求

创建两个RDD，求两个RDD的交集。

```scala
scala> rdd.intersection(rdd2).collect
res14: Array[Int] = Array(4, 3)
```

4、 cartesian(otherDataset)

（1）作用

笛卡尔积（尽量避免使用）。

（2）需求

创建两个RDD，计算两个RDD的笛卡尔积。

```scala
scala> rdd.cartesian(rdd2).collect
res16: Array[(Int, Int)] = Array((1,3), (1,4), (1,5), (1,6), (2,3), (2,4), (2,5), (2,6), (3,3), (3,4), (3,5), (3,6), (4,3), (4,4), (4,5), (4,6))
```

5、zip(otherDataset)

（1）作用

将两个RDD组合成Key/Value形式的RDD,这里默认两个RDD的partition数量以及元素数量都相同，否则会抛出异常。

（2）需求

创建两个RDD，并将两个RDD组合到一起形成一个(k,v)RDD。

```scala
scala> rdd.zip(rdd2).collect
res17: Array[(Int, Int)] = Array((1,3), (2,4), (3,5), (4,6))
```

## 3、key-value类型

1、partitionBy

（1）作用

对pairRDD进行分区操作，如果原有的partionRDD和现有的partionRDD是一致的话就不进行分区， 否则会生成ShuffleRDD，即会产生shuffle过程。

（2）创建一个4个分区（我的虚拟机默认为4个分区）的RDD，对其重新分区。

```scala
val rdd = sc.parallelize(Array((1,"a"),(2,"b"),(3,"c"),(4,"d")),4)

//对RDD重新分区
var res = rdd.partitionBy(new org.apache.spark.HashPartitioner(2))

//结果展示
scala> res.glom().collect
res21: Array[Array[(Int, String)]] = Array(Array((2,b), (4,d)), Array((1,a), (3,c)))
```

2、groupByKey

（1）需求

groupByKey也是对每个key进行操作，但只生成一个sequence。

（2）需求

创建一个pairRDD，将相同key对应值聚合到一个sequence中，并计算相同key对应值的相加结果。

```scala
val words = Array("one", "two", "two", "three", "three", "three")
val wordPairsRDD = sc.parallelize(words).map(word => (word, 1))

//wordPairsRDD数据结果如下
scala> wordPairsRDD.collect
res23: Array[(String, Int)] = Array((one,1), (two,1), (two,1), (three,1), (three,1), (three,1))

//求和并展示
scala> group.collect
res24: Array[(String, Iterable[Int])] = Array((two,CompactBuffer(1, 1)), (one,CompactBuffer(1)), (three,CompactBuffer(1, 1, 1)))

scala>  group.map(t => (t._1, t._2.sum)).collect
res25: Array[(String, Int)] = Array((two,2), (one,1), (three,3))
```

3、 reduceByKey(func, [numTasks])

（1）作用

在一个(K,V)的RDD上调用，返回一个(K,V)的RDD，使用指定的reduce函数，将相同key的值聚合到一起，reduce任务的个数可以通过第二个可选的参数来设置。

（2）需求

创建一个pairRDD，计算相同key对应值的相加结果。

reduceByKey会寻找相同key的数据，当找到这样的两条记录时会对其value(分别记为x,y)做`(x,y) => x+y`的处理，即只保留求和之后的数据作为value。反复执行这个操作直至每个key只留下一条记录。

```scala
val rdd = sc.parallelize(List(("female",1),("male",3),("female",4),("male",2)))

 val reduce = rdd.reduceByKey((x,y) => x+y)

//结果展示
scala> reduce.collect
res0: Array[(String, Int)] = Array((female,5), (male,5))
```

（3）reduceByKey和groupByKey的区别

reduceByKey：按照key进行聚合，在shuffle之前有combine（预聚合）操作，返回结果是RDD[k,v]。

 groupByKey：按照key进行分组，直接进行shuffle。reduceByKey比groupByKey效率更高，建议使用。但是需要注意是否会影响业务逻辑

4、aggregateByKey

```scala
//参数
(zeroValue:U,[partitioner: Partitioner]) (seqOp: (U, V) => U,combOp: (U, U) => U)

//参数描述
（1）zeroValue：给每一个分区中的每一个key一个初始值；
（2）seqOp：函数用于在每一个分区中用初始值逐步迭代value；
（3）combOp：函数用于合并每个分区中的结果。
```

（1）作用

在kv对的RDD中，按key将value进行分组合并，合并时，将每个value和初始值作为seq函数的参数，进行计算，返回的结果作为一个新的kv对，然后再将结果按照key进行合并，最后将每个分组的value传递给combine函数进行计算（先将前两个value进行计算，将返回结果和下一个value传给combine函数，以此类推），将key与计算结果作为一个新的kv对输出。

（2）需求

创建一个pairRDD，取出每个分区相同key对应值的最大值，然后将两个分区中的最大值进行相加。

（3）分析

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201004172804207.png" alt="image-20201004172804207" style="zoom: 50%;" />

```scala
val rdd = sc.parallelize(List(("a",3),("a",2),("c",4),("b",3),("c",6),("c",8)),2)

//math.max(_,_)区间内，_+_区间外
scala> rdd.aggregateByKey(0)(math.max(_,_),_+_).collect
res2: Array[(String, Int)] = Array((b,3), (a,3), (c,12))
```

5、foldByKey

```scala
//参数
(zeroValue: V)(func: (V, V) => V): RDD[(K, V)]
```

（1）作用

aggregateByKey的简化操作，seqop和combop相同。

（2）需求

创建一个pairRDD，计算相同key对应值的相加结果（并没有求每个分区中不同key的最大值）。

```scala
 val rdd = sc.parallelize(List((1,3),(1,2),(1,4),(2,3),(3,6),(3,8)),3)
 
 //结果输出
scala> rdd.foldByKey(0)(_+_).collect
res3: Array[(Int, Int)] = Array((3,14), (1,9), (2,3))
//等价于上面的结果（此时分区内逻辑和分区间逻辑相同）
scala> rdd.aggregateByKey(0)(_+_,_+_).collect
res5: Array[(Int, Int)] = Array((3,14), (1,9), (2,3))
```

6、combineByKey[C]

```scala
//参数
(createCombiner: V => C,  mergeValue: (C, V) => C,  mergeCombiners: (C, C) => C)

//参数描述
（1）createCombiner: combineByKey() 会遍历分区中的所有元素，因此每个元素的键要么还没有遇到过，要么就和之前的某个元素的键相同。如果这是一个新的元素,combineByKey()会使用一个叫作createCombiner()的函数来创建那个键对应的累加器的初始值。
（2）mergeValue: 如果这是一个在处理当前分区之前已经遇到的键，它会使用mergeValue()方法将该键的累加器对应的当前值与这个新的值进行合并。
（3）mergeCombiners: 由于每个分区都是独立处理的， 因此对于同一个键可以有多个累加器。如果有两个或者更多的分区都有对应同一个键的累加器， 就需要使用用户提供的 mergeCombiners() 方法将各个分区的结果进行合并。
```

（1）作用

对相同K，把V合并成一个集合。

（2）需求

创建一个pairRDD，根据key计算每种key的均值。（先计算每个key出现的次数以及可以对应值的总和，再相除得到结果）。

（3）分析

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201004190217522.png" alt="image-20201004190217522" style="zoom:67%;" />





```scala
val input = sc.parallelize(Array(("a", 88), ("b", 95), ("a", 91), ("b", 93), ("a", 95), ("b", 98)),2)

//将相同key对应的值相加，同时记录该key出现的次数，放入一个二元组
val combine = input.combineByKey(
(_,1),
(acc:(Int,Int),v)=>(acc._1+v,acc._2+1),
(acc1:(Int,Int),acc2:(Int,Int))=>(acc1._1+acc2._1,acc1._2+acc2._2)
)
res5: Array[(String, (Int, Int))] = Array((b,(286,3)), (a,(274,3)))

//输出结果
combine.map{case (key,value) => (key,value._1/value._2.toDouble)}.collect
res33: Array[(String, Double)] = Array((b,95.33333333333333), (a,91.33333333333333))
```

7、sortByKey([ascending], [numTasks])

（1）作用

在一个(K,V)的RDD上调用，K必须实现Ordered接口，返回一个按照key进行排序的(K,V)的RDD。

（2）需求

创建一个pairRDD，按照key的正序和倒序进行排序。

```scala
val rdd = sc.parallelize(Array((3,"aa"),(6,"cc"),(2,"bb"),(1,"dd")))
 
//结果输出-正序
scala> rdd.sortByKey(true).collect()
res0: Array[(Int, String)] = Array((1,dd), (2,bb), (3,aa), (6,cc))
```

8、mapValues

（1）作用

针对于(K,V)形式的类型只对V进行操作。

（2）需求

创建一个pairRDD，并将value添加字符串"|||"

```scala
val rdd = sc.parallelize(Array((3,"aa"),(6,"cc"),(2,"bb"),(1,"dd")))

//结果显示
scala> rdd.mapValues(_+"|||").collect()
res1: Array[(Int, String)] = Array((3,aa|||), (6,cc|||), (2,bb|||), (1,dd|||))
```

9、join(otherDataset, [numTasks])

（1）作用

在类型为(K,V)和(K,W)的RDD上调用，返回一个相同key对应的所有元素对在一起的(K,(V,W))的RDD。

（2）需求

创建两个pairRDD，并将key相同的数据聚合到一个元组。

```scala
val rdd = sc.parallelize(Array((1,"a"),(2,"b"),(3,"c")))
val rdd1 = sc.parallelize(Array((1,4),(2,5),(3,6)))

//结果展示
scala> rdd.join(rdd1).collect()
res2: Array[(Int, (String, Int))] = Array((1,(a,4)), (2,(b,5)), (3,(c,6)))
```

10、cogroup(otherDataset, [numTasks])

（1）作用

在类型为(K,V)和(K,W)的RDD上调用，返回一个(K,(Iterable<V>,Iterable<W>))类型的RDD。

（2）需求

创建两个pairRDD，并将key相同的数据聚合到一个迭代器。

```scala
val rdd = sc.parallelize(Array((1,"a"),(2,"b"),(3,"c")))
val rdd1 = sc.parallelize(Array((1,4),(2,5),(3,6)))

//结果显示
scala>  rdd.cogroup(rdd1).collect()
res3: Array[(Int, (Iterable[String], Iterable[Int]))] = Array((1,(CompactBuffer(a),CompactBuffer(4))), (2,(CompactBuffer(b),CompactBuffer(5))), (3,(CompactBuffer(c),CompactBuffer(6))))
```

# 3、RDD的Action

1、reduce(func)

（1）作用

通过func函数聚集RDD中的所有元素，先聚合分区内数据，再聚合分区间数据。

（2）需求

创建一个RDD，将所有元素聚合得到结果。

```scala
val rdd1 = sc.makeRDD(1 to 10,2)
//聚合RDD[Int]所有元素-结果展示
scala> rdd1.reduce(_+_)
res5: Int = 55

val rdd2 = sc.makeRDD(Array(("a",1),("a",3),("c",3),("d",5)))
//聚合RDD[String]所有数据-结果展示
scala> rdd2.reduce((x,y)=>(x._1 + y._1,x._2 + y._2))
res51: (String, Int) = (adca,12)
```

2、collect()

（1）作用

在驱动程序中，以数组的形式返回数据集的所有元素。

（2）需求

创建一个RDD，并将RDD内容收集到Driver端打印。

```scala
val rdd = sc.parallelize(1 to 10)

//结果展示
scala> rdd.collect
res0: Array[Int] = Array(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)  
```

3、count()

（1）作用

返回RDD中元素的个数。

（2）需求

创建一个RDD，统计该RDD的条数。

```scala
val rdd = sc.parallelize(1 to 10)
scala> rdd.count
res1: Long = 10
```

4、first()

（1）作用

返回RDD中的第一个元素。

（2）需求

创建一个RDD，返回该RDD中的第一个元素。

```scala
val rdd = sc.parallelize(1 to 10)

//结果展示
scala> rdd.first
res6: Int = 1
```

5、take(n)

（1）作用

返回一个由RDD的前n个元素组成的数组。

（2）需求

创建一个RDD，将前3个数据以数组的形式返回。

```scala
val rdd = sc.parallelize(1 to 10)

scala> rdd.take(3)
res7: Array[Int] = Array(1, 2, 3)
```

6、takeOrdered(n)

（1）作用

返回该RDD排序后的前n个元素组成的数组。

（2）需求

将RDD中的排序号的前三个数据返回。

```scala
val rdd = sc.parallelize(Array(2,5,4,6,8,3))

//结果展示
scala> rdd.takeOrdered(3)
res8: Array[Int] = Array(2, 3, 4)
```

7、aggregate

```scala
//参数
(zeroValue: U)(seqOp: (U, T) ⇒ U, combOp: (U, U) ⇒ U)
```

（1）作用

该函数将每个分区里面的元素通过seqOp和初始值进行聚合，然后用combine函数将每个分区的结果和初始值(zeroValue)进行combine操作。这个函数最终返回的类型不需要和RDD中元素类型一致。

（2）需求

创建一个RDD，将所有元素相加得到结果。

```scala
val rdd = sc.parallelize(1 to 10)

//结果展示
scala> rdd.aggregate(0)(_+_,_+_)
res9: Int = 55
```

8、fold(num)(func)

（1）作用

折叠操作，aggregate的简化操作，seqop和combop一样。

（2）需求

创建一个RDD，将所有元素相加得到结果。

```scala
scala> rdd.fold(0)(_+_)
res24: Int = 55
```

9、saveAsTextFile(path)

（1）作用

将数据集的元素以textfile的形式保存到HDFS文件系统或者其他支持的文件系统，对于每个元素，Spark将会调用toString方法，将它装换为文件中的文本。

10、saveAsSequenceFile(path) 

（1）作用

将数据集中的元素以Hadoop sequencefile的格式保存到指定的目录下，可以使HDFS或者其他Hadoop支持的文件系统。

11、saveAsObjectFile(path) 

（1）作用

用于将RDD中的元素序列化成对象，存储到文件中。

12、countByKey()

（1）作用

针对(K,V)类型的RDD，返回一个(K,Int)的map，表示每一个key对应的元素个数。

（2）需求

创建一个PairRDD，统计每种key的个数。

```scala
val rdd = sc.parallelize(List((1,3),(1,2),(1,4),(2,3),(3,6),(3,8)),3)

//结果展示
scala>  rdd.countByKey
res10: scala.collection.Map[Int,Long] = Map(3 -> 2, 1 -> 3, 2 -> 1)
```

13、foreach(func)

（1）作用

在数据集的每一个元素上，运行函数func进行更新。

（2）需求

创建一个RDD，对每个元素进行打印。

```scala
var rdd = sc.makeRDD(1 to 5,2)

//数据展示
scala> rdd.foreach(println(_))
3
4
5
1
2
```

# 4、RDD中的函数传递

1、介绍

在实际开发中我们往往需要自己定义一些对于RDD的操作，那么此时需要主要的是，初始化工作是在Driver端进行的，而实际运行程序是在Executor端进行的，这就涉及到了跨进程通信，是需要序列化的。

2、传递一个方法

（1）创建一个类

```scala
import org.apache.spark.rdd.RDD

//class Search(query: String)  extends Serializable {
class Search(query: String) {
  //过滤出包含字符串的数据
  def isMatch(s: String): Boolean = {
    s.contains(query)
  }

  //过滤出包含字符串的RDD
  def getMatch1(rdd: RDD[String]): RDD[String] = {
    rdd.filter(isMatch)
  }

  //过滤出包含字符串的RDD
  def getMatche2(rdd: RDD[String]): RDD[String] = {
    //    rdd.filter(x => x.contains(query))
    /**
     * 方法2：将类变量赋值给局部变量
     * （1）query_ 是一个Driver的局部变量。
     * （2）Driver需要向Executor端传递的是一个字符串query_，字符串本身就可以序列化。
     */
    val query_ : String = this.query
    rdd.filter(x => x.contains(query_))
  }
}
```

（2）主程序

```scala
import org.apache.spark._
import org.apache.spark.rdd._

object TestDemo01 {
  def main(args: Array[String]): Unit = {
    //1.初始化配置信息及SparkContext
    val sparkConf: SparkConf = new SparkConf().setAppName("WordCount").setMaster("local[*]")
    val sc = new SparkContext(sparkConf)

    //2.创建一个RDD
    val rdd: RDD[String] = sc.parallelize(Array("hadoop", "spark", "hive", "atguigu"))

    //3.创建一个Search对象
    val search = new Search("h");

    //4.运用第一个过滤函数并打印结果
  val match1: RDD[String] = search.getMatche1(rdd)
    match1.collect().foreach(println)
  }
}
```

在这个方法中所调用的方法isMatch()是定义在Search这个类中的，实际上调用的是this. isMatch()，this表示Search这个类的对象，程序在运行过程中需要将Search对象序列化以后传递到Executor端。因此，使实体类继承scala.Serializable即可。

```scala
class Search() extends Serializable{...}
```

3、传递一个属性

（1）代码

```scala
 val match1: RDD[String] = search.getMatche2(rdd)
```

在这个方法中所调用的方法query是定义在Search这个类中的字段，实际上调用的是this. query，this表示Search这个类的对象，程序在运行过程中需要将Search对象序列化以后传递到Executor端。

（2）解决方法

+ 使类继承scala.Serializable即可。

+ 将类变量query赋值给局部变量

```scala
  def getMatche2(rdd: RDD[String]): RDD[String] = {
    //    rdd.filter(x => x.contains(query))
    /**
     * 方法2：将类变量赋值给局部变量
     * （1）query_ 是一个Driver的局部变量。
     * （2）Driver需要向Executor端传递的是一个字符串query_，字符串本身就可以序列化。
     */
    val query_ : String = this.query
    rdd.filter(x => x.contains(query_))
  }
```

# 5、RDD的依赖关系

## 1、 Lineage

1、介绍

RDD只支持粗粒度转换，即在大量记录上执行的单个操作。将创建RDD的一系列Lineage（血统）记录下来，以便恢复丢失的分区。RDD的Lineage会记录RDD的元数据信息和转换行为，当该RDD的部分分区数据丢失时，它可以根据这些信息来重新运算和恢复丢失的数据分区。

2、案例

```scala
var rdd = sc.makeRDD(List(1,2,3,4))
var mapRDD = rdd.map((_,1))
var reduceRDD = mapRDD.reduceByKey(_+_)

//查看血缘关系
scala> reduceRDD.toDebugString
res1: String =
(4) ShuffledRDD[2] at reduceByKey at <console>:28 []
 +-(4) MapPartitionsRDD[1] at map at <console>:26 []
    |  ParallelCollectionRDD[0] at makeRDD at <console>:24 []
```

## 2、DAG

1、介绍

DAG(Directed Acyclic Graph)叫做有向无环图，原始的RDD通过一系列的转换就就形成了DAG，根据RDD之间的依赖关系的不同将DAG划分成不同的Stage，对于窄依赖，partition的转换处理在Stage中完成计算。对于宽依赖，由于有Shuffle的存在，只能在parent RDD处理完成后，才能开始接下来的计算，因此宽依赖是划分Stage的依据。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201005145438255.png" alt="image-20201005145438255" style="zoom:67%;" />

## 3、任务划分

1、介绍

RDD任务切分中间分为：Application、Job、Stage和Task

（1）Application：初始化一个SparkContext即生成一个Application

（2）Job：一个Action算子就会生成一个Job

（3）Stage：根据RDD之间的依赖关系的不同将Job划分成不同的Stage，遇到一个宽依赖则划分一个Stage。

（4）Task：Stage是一个TaskSet，将Stage划分的结果发送到不同的Executor执行即为一个Task。

Application->Job->Stage-> Task每一层都是1对n的关系。

2、运行规划图

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201005145632834.png" alt="image-20201005145632834" style="zoom:67%;" />

# 6、键值对RDD数据分区器

1、介绍

Spark目前支持Hash分区和Range分区，用户也可以自定义分区，Hash分区为当前的默认分区，Spark中分区器直接决定了RDD中分区的个数、RDD中每条数据经过Shuffle过程属于哪个分区和Reduce的个数。

注意：

(1)只有Key-Value类型的RDD才有分区器的，非Key-Value类型的RDD分区器的值是None。
 (2)每个RDD的分区ID范围：0~numPartitions-1，决定这个值是属于那个分区的。

2、获取RDD分区

```scala
scala> rdd.partitioner
res10: Option[org.apache.spark.Partitioner] = None
```

3、Hash分区

HashPartitioner分区的原理：对于给定的key，计算其hashCode，并除以分区的个数取余，如果余数小于0，则用余数+分区的个数（否则加0），最后返回的值就是这个key所属的分区ID。

# 7、数据读取与保存

## 1、介绍

1、概述

Spark的数据读取及数据保存可以从两个维度来作区分：文件格式以及文件系统。

文件格式分为：Text文件、Json文件、Csv文件、Sequence文件以及Object文件；

文件系统分为：本地文件系统、HDFS、HBASE以及数据库。

## 2、文件类数据读取与保存

1、Text文件

```scala
//数据读取 textFile(String)
val hdfsFile = sc.textFile("hdfs://hadoop102:9000/fruit.txt")

//数据保存 saveAsTextFile(String)
 hdfsFile.saveAsTextFile("/fruitOut")
```

2、Json文件

（1）概述

如果JSON文件中每一行就是一个JSON记录，那么可以通过将JSON文件当做文本文件来读取，然后利用相关的JSON库对每一条数据进行JSON解析。

注意：使用RDD读取JSON文件处理很复杂，同时SparkSQL集成了很好的处理JSON文件的方式，所以应用中多是采用SparkSQL处理JSON文件。

（2）代码

项目结构

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201005155323945.png" alt="image-20201005155323945" style="zoom:67%;" />

user.json文件中放入数据

```json
{"name": "a","age": 20}
{"name": "b","age": 24}
{"name": "c","age": 28}
```

测试类

```scala
package RddProgram

import org.apache.spark.rdd.RDD
import org.apache.spark.{SparkConf, SparkContext}
import scala.util.parsing.json.JSON

object Spark03_Json {
  def main(args: Array[String]): Unit = {
    //1.初始化配置信息及SparkContext
    val sparkConf: SparkConf = new SparkConf().setAppName("WordCount").setMaster("local[*]")
    val sc = new SparkContext(sparkConf)

    //2.创建一个RDD
    val json: RDD[String] = sc.textFile("in/user.json")
    //解析json数据
    val result: RDD[Option[Any]] = json.map(JSON.parseFull)
    result.foreach(println)

    //释放资源
    sc.stop()
  }
}
```

结果显示：

```
Some(Map(name -> c, age -> 28.0))
Some(Map(name -> a, age -> 20.0))
Some(Map(name -> b, age -> 24.0))
```

## 3、文件系统类数据读取与保存

### 1、MySQL数据库连接

支持通过Java JDBC访问关系型数据库。需要通过JdbcRDD进行

1、MySQL数据库中表的查询

（1）导入依赖

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.11</version>
</dependency>
```

（2）数据库中user表结构

```sql
CREATE TABLE `user` (
  `id` int NOT NULL AUTO_INCREMENT,
  `name` varchar(20) DEFAULT NULL,
  `age` int DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=utf8;
```

（3）业务类

```scala
package RddProgram

import java.sql.DriverManager
import org.apache.spark.rdd.JdbcRDD
import org.apache.spark.{SparkConf, SparkContext}

object Spark04_MySQLQuery {
  def main(args: Array[String]): Unit = {
    //1.初始化配置信息及SparkContext
    val sparkConf: SparkConf = new SparkConf().setAppName("Opera_Mysql").setMaster("local[*]")
    val sc = new SparkContext(sparkConf)

    //配置连接mysql的参数
    val driver = "com.mysql.cj.jdbc.Driver"
    val url = "jdbc:mysql://localhost:3306/rdd?useUnicode=true&characterEncoding=UTF-8&useSSL=false&serverTimezone=UTC"
    val userName = "root"
    val passWd = "123"

    //创建JDBCRDD，方法数据库
    var sql = "select * from `user` where `id`>=? and `id` <= ?;"
    val jdbcRDD = new JdbcRDD(
      sc,
      () => {
        Class.forName(driver)
        DriverManager.getConnection(url, userName, passWd)
      },
      sql,
      1,
      3,
      1,
      r => (r.getInt(1), r.getString(2))
    )

    //打印最后结果
    println(jdbcRDD.count())
    jdbcRDD.foreach(println)

    //释放资源
    sc.stop()
  }
}
```

2、MySQL数据库中表的插入

```scala
package RddProgram

import java.sql.{DriverManager, PreparedStatement}
import org.apache.spark.rdd.{JdbcRDD, RDD}
import org.apache.spark.{SparkConf, SparkContext}

object Spark04_MySQLInsert {
  def main(args: Array[String]): Unit = {
    //1.初始化配置信息及SparkContext
    val sparkConf: SparkConf = new SparkConf().setAppName("Opera_Mysql").setMaster("local[*]")
    val sc = new SparkContext(sparkConf)

    //插入到数据库中的数据
    val dataRDD: RDD[(String, Int)] = sc.makeRDD(List(("zs", 20), ("ls", 24)))
    //配置连接mysql的参数
    val driver = "com.mysql.cj.jdbc.Driver"
    val url = "jdbc:mysql://localhost:3306/rdd?useUnicode=true&characterEncoding=UTF-8&useSSL=false&serverTimezone=UTC"
    val userName = "root"
    val passWd = "123"

    //一次性处理一个partition的数据，那么对于每个partition，只要创建一个数据库连接即可
    dataRDD.foreachPartition(
      datas => {
        Class.forName(driver)
        var connect = DriverManager.getConnection(url, userName, passWd)
        datas.foreach {
          case (username, age) => {
            var sql = "insert into user(name,age) values (?,?);"
            val statement: PreparedStatement = connect.prepareStatement(sql)
            statement.setString(1, username)
            statement.setInt(2, age)
            statement.executeUpdate()
            statement.close()
          }
        }
        connect.close()
      }
    )
    //释放资源
    sc.stop()
  }
}
```

### 2、HBase数据库联俄籍

1、介绍

由于 org.apache.hadoop.hbase.mapreduce.TableInputFormat 类的实现，Spark 可以通过Hadoop输入格式访问HBase。这个输入格式会返回键值对数据，其中键的类型为org.apache.hadoop.hbase.io.ImmutableBytesWritable，而值的类型为org.apache.hadoop.hbase.client.Result。

2、遍历Hbase

（1）添加依赖

```xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-client</artifactId>
    <version>1.3.1</version>
</dependency>
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-server</artifactId>
    <version>1.3.1</version>
</dependency>
```

（2）启动Hbase集群

首先启动zookeeper，然后启动hadoop，最后将Hbase中的文件hbase-site.xml拷贝到项目的resources中。

（3）在hbase中新建表格

```scala
hbase(main):006:0> create 'student','info'

//插入一条数据
put 'student','1001','info:name','zs'
```

（4）从Hbase中读取数据

```scala
package RddProgram

import org.apache.hadoop.conf.Configuration
import org.apache.hadoop.hbase.{Cell, CellUtil, HBaseConfiguration}
import org.apache.hadoop.hbase.client.Result
import org.apache.hadoop.hbase.io.ImmutableBytesWritable
import org.apache.hadoop.hbase.mapreduce.TableInputFormat
import org.apache.hadoop.hbase.util.Bytes
import org.apache.spark.rdd.RDD
import org.apache.spark.{SparkConf, SparkContext}


object Spark05_HbaseQuery {
  def main(args: Array[String]): Unit = {
    //创建spark配置信息
    val sparkConf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("hbaseRDD")

    //创建SparkContext
    val sc = new SparkContext(sparkConf)

    //构建HBase配置信息
    val conf: Configuration = HBaseConfiguration.create()
    conf.set(TableInputFormat.INPUT_TABLE, "student") //设置表名 namespace:tableName

    //从HBase读取数据形成RDD
    val hbaseRDD: RDD[(ImmutableBytesWritable, Result)] = sc.newAPIHadoopRDD(
      conf,
      classOf[TableInputFormat],
      classOf[ImmutableBytesWritable], //rowkey的类型
      classOf[Result]// 查询结果集
    )

    //显示数据的条数
    val count: Long = hbaseRDD.count()
    println(count)

    //对hbaseRDD进行处理，遍历显示数据的
    hbaseRDD.foreach {
      case (rowkey, result) =>{
        val cells: Array[Cell] = result.rawCells()
        for(cell <- cells){
          println(Bytes.toString(CellUtil.cloneValue(cell)))
        }
      }
    }
    //关闭连接
    sc.stop()
  }
}
```

3、向Hbase中插入数据

```scala
package RddProgram

import org.apache.hadoop.conf.Configuration
import org.apache.hadoop.hbase.HBaseConfiguration
import org.apache.hadoop.hbase.client.{Put, Result}
import org.apache.hadoop.hbase.io.ImmutableBytesWritable
import org.apache.hadoop.hbase.mapred.TableOutputFormat
import org.apache.spark.rdd.RDD
import org.apache.spark.{SparkConf, SparkContext}
import org.apache.hadoop.hbase.util.Bytes
import org.apache.hadoop.mapred.JobConf

object Spark05_HbaseInsert {
  def main(args: Array[String]): Unit = {
    //创建spark配置信息
    val sparkConf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("hbaseRDD")

    //创建SparkContext
    val sc = new SparkContext(sparkConf)

    val dataRDD: RDD[(String, String)] = sc.makeRDD(List(("1003", "we"), ("1004", "mz")))

    //构建HBase配置信息
    val conf: Configuration = HBaseConfiguration.create()

    val putRdd: RDD[(ImmutableBytesWritable, Put)] = dataRDD.map {
      case (rowkey, name) => {
        val put = new Put(Bytes.toBytes(rowkey))
        put.addColumn(Bytes.toBytes("info"), Bytes.toBytes("name"), Bytes.toBytes(name))
        (new ImmutableBytesWritable(Bytes.toBytes(rowkey)), put)
      }
    }

    //作业的配置
    val jobConf = new JobConf(conf)
    jobConf.setOutputFormat(classOf[TableOutputFormat])
    jobConf.set(TableOutputFormat.OUTPUT_TABLE,"student")
    putRdd.saveAsHadoopDataset(jobConf)

    //关闭连接
    sc.stop()
  }
}
```

# 8、RDD编程进阶

## 1、累加器

1、介绍

（1）概述

Spark有三大数据结构，分别是：

+ RDD：分布式数据集
+ 累加器：分布式只写共享变量
+ 广播变量：分布式只读共享变量

通常在向 Spark传递函数时，比如使用 map() 函数或者用 filter() 传条件时，可以使用驱动器程序中定义的变量，但是集群中运行的每个任务都会得到这些变量的一份新的副本，更新这些副本的值也不会影响驱动器中的对应变量。如果我们想实现所有分片处理时更新共享变量的功能，可以使用累加器。

（2）分析图

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201005192706646.png" alt="image-20201005192706646" style="zoom:67%;" />

2、将RDD中的数据累计

```scala
package RddProgram

import org.apache.spark.rdd.RDD
import org.apache.spark.util.LongAccumulator
import org.apache.spark.{SparkConf, SparkContext}

/**
 * @time: 2020-10-05 19:06 
 * @author: likunlun 
 * @description: 累加器
 */
object Spark06_ShareData {
  def main(args: Array[String]): Unit = {
    //创建spark配置信息
    val sparkConf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("Accumulator")
    //创建SparkContext
    val sc = new SparkContext(sparkConf)
    val dataRDD: RDD[Int] = sc.makeRDD(List(1, 2, 3, 4), 2)
    //使用累加器来共享变量，累加数据，下面是创建累加器对象
    val accumulator: LongAccumulator = sc.longAccumulator

    dataRDD.foreach {
      case i => {
        //执行累加器的累加功能
        accumulator.add(i)
      }
    }
    //结果展示
    println("sum = " + accumulator.value)
  }
}
```

3、自定义累加器

过滤开头为字母h的单词

```scala
package RddProgram

import java.util

import org.apache.spark.rdd.RDD
import org.apache.spark.util.AccumulatorV2
import org.apache.spark.{SparkConf, SparkContext}

/**
 * @time: 2020-10-05 19:06 
 * @author: likunlun 
 * @description: 自定义累加器
 */
object Spark06_ShareData_DIY {
  def main(args: Array[String]): Unit = {
    //创建spark配置信息
    val sparkConf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("Accumulator")
    //创建SparkContext
    val sc = new SparkContext(sparkConf)
    val dataRDD: RDD[String] = sc.makeRDD(List("hadoop", "spark", "hbase"), 2)

    //创建累加器对象
    val wordAccumulator = new WordAccumulator()
    //注册累加器
    sc.register(wordAccumulator)
    //执行
    dataRDD.foreach {
      case word => {
        wordAccumulator.add(word)
      }
    }
    println("累加器的值为：" + wordAccumulator.value)
  }
}

/**
 * 自定义累加器
 * 1、继承AccumulatorV2
 * 2、实现抽象方法
 */
class WordAccumulator extends AccumulatorV2[String, util.ArrayList[String]] {
  private val list = new util.ArrayList[String]()

  //当前的累加器是否为初始化状态
  override def isZero: Boolean = list.isEmpty

  //复制累加器对象
  override def copy(): AccumulatorV2[String, util.ArrayList[String]] = {
    new WordAccumulator()
  }

  //重置累加器对象
  override def reset(): Unit = list.clear()

  //向累加器中添加数据
  override def add(v: String): Unit = {
    if (v.contains("h")) {
      list.add(v)
    }
  }

  //合并
  override def merge(other: AccumulatorV2[String, util.ArrayList[String]]): Unit = {
    list.addAll(other.value)
  }

  //获取累加器的结果
  override def value: util.ArrayList[String] = list
}
```

## 2、广播变量

1、介绍

广播变量用来高效分发较大的对象。向所有工作节点发送一个较大的只读值，以供一个或多个Spark操作使用。比如，如果你的应用需要向所有节点发送一个较大的只读查询表，甚至是机器学习算法中的一个很大的特征向量，广播变量用起来都很顺手。 在多个并行操作中使用同一个变量，但是 Spark会为每个任务分别发送。

2、代码

```scala
package RddProgram

import org.apache.spark.broadcast.Broadcast
import org.apache.spark.{SparkConf, SparkContext}

object Spark07_BroadcastVar {
  def main(args: Array[String]): Unit = {
    //创建spark配置信息
    val sparkConf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("Accumulator")
    //创建SparkContext
    val sc = new SparkContext(sparkConf)
    val rdd1 = List((1, "a"), (2, "b"), (3, "c"))

    val list = List((1, 1), (2, 2), (3, 3))

    /**
     * 可以使用广播变量减少数据的传输，因为此时使用mapshuffle过程。
     * 1、构建广播变量
     */
    val broadcast: Broadcast[List[(Int, Int)]] = sc.broadcast(list)
    val result: List[(Int, (String, Any))] = rdd1.map {
      case (key, value) => {
        var v2: Any = null
        //2、使用广播变量
        for (t <- broadcast.value) {
          if (key == t._1) {
            v2 = t._2
          }
        }
        (key, (value, v2))
      }
    }

    /**
     * (1,(a,1))
     * (2,(b,2))
     * (3,(c,3))
     */
    result.foreach(println)
  }
}
```































































































































































