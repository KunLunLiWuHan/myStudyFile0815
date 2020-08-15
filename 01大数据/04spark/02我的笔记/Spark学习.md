# 1 介绍

​		Spark是一种快速、通用、可扩展的大数据分析引擎，2009年诞生于加州大学伯克利分校AMPLab，2010年开源，2013年6月成为Apache孵化项目，2014年2月成为Apache顶级项目。目前，Spark生态系统已经发展成为一个包含多个子项目的集合，其中包含SparkSQL、Spark Streaming、GraphX、MLlib等子项目，Spark是基于内存计算的大数据并行计算框架。

​		Spark基于内存计算，提高了在大数据环境下数据处理的实时性，同时保证了高容错性和高可伸缩性，允许用户将Spark部署在大量廉价硬件之上，形成集群。

​		Spark是MapReduce的替代方案，而且兼容HDFS、Hive，可融入Hadoop的生态系统，以弥补MapReduce的不足。

![image-20200729085739092](Spark学习.assets\image-20200729085739092.png)

## 1.1 Spark特点

1、快

​		与Hadoop的MapReduce相比，Spark基于内存的运算要快100倍以上，基于硬盘的运算也要快10倍以上。Spark实现了高效的DAG执行引擎，可以通过基于内存来高效处理数据流。

2、易用

​		Spark支持Java、Python和Scala的API，还支持超过80种高级算法，使用户可以快速构建不同的应用。而且Spark支持交互式的Python和Scala的shell，可以非常方便地在这些shell中使用Spark集群来验证解决问题的方法。

3、通用

​		Spark提供了统一的解决方案。Spark可以用于批处理、交互式查询（Spark SQL）、实时流处理（Spark Streaming）、机器学习（Spark MLlib）和图计算（GraphX）。这些不同类型的处理都可以在同一个应用中无缝使用。Spark统一的解决方案非常具有吸引力，毕竟任何公司都想用统一的平台去处理遇到的问题，减少开发和维护的人力成本和部署平台的物力成本。

4、兼容性

​		Spark可以非常方便地与其他的开源产品进行融合。比如，Spark可以使用Hadoop的YARN和Apache Mesos作为它的资源管理和调度器，器，并且可以处理所有Hadoop支持的数据，包括HDFS、HBase和Cassandra等。这对于已经部署Hadoop集群的用户特别重要，因为不需要做任何数据迁移就可以使用Spark的强大处理能力。Spark也可以不依赖于第三方的资源管理和调度器，它实现了Standalone作为其内置的资源管理和调度框架，这样进一步降低了Spark的使用门槛，使得所有人都可以非常容易地部署和使用Spark。此外，Spark还提供了在EC2上部署Standalone的Spark集群的工具。

# 2 安装

## 2.1 单机版安装

**说明**

local模式就是运行在一台计算机上的模式。

local：所有计算都运行在一个线程中，没有任何并行计算。

local[k]：指定使用几个线程来计算，比如Local[4]就是运行4个Worker进程，通常我们的cpu有几个core，就指定几个线程，最大化的利用cpu的计算能力。

local[*]：该模式直接按照cpu最多cores来设置线程数。

参看博客：

https://www.cnblogs.com/dintalk/p/12234718.html

1、解压后重命名

```shell
tar -xzvf spark-2.1.1-bin-hadoop2.7
mv spark-2.1.1-bin-hadoop2.7 spark
```

2、修改配置文件

（1）配置spark-env.sh

```shell
#1，在sprk/conf/文件夹下
cp spark-env.sh.template spark-env.sh

#2
vim spark-env.sh

#3
export JAVA_HOME=/opt/jdk1.8.0_251
export SPARK_MASTER_HOST=localhost
export SPARK_MASTER_PORT=7077
#租的阿里云的服务器，配置如下
export SPARK_WORKER_CORES=1
export SPARK_WORKER_MEMORY=2g
```

（2） 配置slaves

```shell
# 复制并重命名
cp slaves.template slaves

#进行下面的修改
vim slaves
localhost
```

3、启动spark-shell

```shell
[xiaolun@iZ2zehzqu74u7m655akuk5Z spark]$ bin/spark-shell
```

可以看到，进入到了scala的编辑界面。

4、日志设置

```shell
#1 conf目录下
cp log4j.properties.template log4j.properties
vim log4j.properties

log4j.rootCategory=INFO, console
#改成下面的日志级别，减少日志信息输出
log4j.rootCategory=WARN, console
```

5、测试-读取文件

![image-20200808113231316](Spark学习.assets\image-20200808113231316.png)

## 2.2 集群安装

1、架构图

<img src="Spark学习.assets\图片.png" alt="图片" style="zoom:80%;" />

2、安装步骤

（1）准备环境

（2）安装JDK

（3）上传spark安装包

（4）解压spark并修改配置文件

进入conf目录，修改spark-env.sh文件

```ini
#1。重命名文件
mv spark-env.sh.template spark-env.sh

#2.修改文件
vi spark-env.sh
#3.添加如下配置
export JAVA_HOME=/opt/jdk1.8.0_251
export SPARK_MASTER_HOST=node-4
export SPARK_MASTER_PORT=7077
```

其次，修改slaves的文件

```ini
#1.重命名文件
mv slaves.template slaves

#2.修改文件
vim slaves
#3.在文件中添加子节点
node-6
node-7
node-8
```

（5）将配置好的spark安装程序拷贝到其他机器上

```ini
#spark-2.4.6-bin-hadoop2.7/ 后面的反斜杠可以将整个目录拷贝到对应节点上
for i in {5..8}; do scp -r ./spark-2.4.6-bin-hadoop2.7/ node-$i:/home/zookeeper/bigdata ;done
```

（6）启动spark

```ini
sbin/start-all.sh
```

（7）通过web界面访问spark管理界面

```ini
#master所在机器地址+8080端口
http://node-4:8080/
```

<img src="Spark学习.assets\image-20200729144423554.png" alt="image-20200729144423554" style="zoom:80%;" />		

​		此外，也可以执行jps命令，主节点上（node-4,node-5）有Master进程，其他子节点上有Work进行。

​		到此为止，Spark集群安装完毕，但是有一个很大的问题，那就是Master节点存在单点故障，要解决此问题，就要借助zookeeper，并且启动至少两个Master节点来实现高可靠，配置方式比较简单：

Spark集群规划：node-4，node-5是Master；node-6，node-7，node-8是Worker

安装配置zookeeper集群，并启动zookeeper集群，暂时将集群配置到node-6，node-7，node-8上。

（8）停止spark所有服务，修改配置文件spark-env.sh

```ini
#将这两个配置注释掉
#export SPARK_MASTER_HOST=node-4
#export SPARK_MASTER_PORT=7077

#zookeeper集群，防止master单点故障
export SPARK_DAEMON_JAVA_OPTS="-Dspark.deploy.recoveryMode=ZOOKEEPER -Dspark.deploy.zookeeper.url=node-6:2181,node-7:2181,node-8:2181 -Dspark.deploy.zookeeper.dir=/sparkDir"

#仅在worker上生效
export SPARK_WORKER_CORES=8
export SPARK_WORKER_MEMORY=6g
```

（9）再次启动集群

```ini
#1.首先在node-4中启动集群，对应状态ALIVE
sbin/start-all.sh

#2.其次在node-5其中第二个Master，对应状态STANDBY
sbin/start-master.sh
```

当node-4挂掉时，zookeeper能够通知node-5接替Master的位置。

## 2.3 提交第一个spark程序

1、利用蒙特·卡罗算法求PI（官方给的例子）

```ini
#提交第一个spark应用到集群中运行
#路径：/home/zookeeper/bigdata/spark-2.4.6-bin-hadoop2.7/examples/jars
bin/spark-submit --master spark://node-4:7077 --class org.apache.spark.examples.SparkPi examples/jars/spark-examples_2.11-2.2.0.jar 100

#自定义内存大小和核数
bin/spark-submit --master spark://node-4:7077 --class org.apache.spark.examples.SparkPi --executor-memory 2048mb --total-executor-cores 12  examples/jars/spark-examples_2.11-2.2.0.jar 1000
```

```ini
#指定通信的master和协议
--master spark://node-4:7077

#每个executor使用的内存大小
--executor-memory  内存大小
#整个application使用的核数
--total-executor-cores 核数
#提交任务可以指定多个master地址，为了提交任务高可用
--master spark://node-4:7077,node-5:7077
```

2、提交一个spark程序到spark集群，会产生的进程。

（1）SparkSubmint（Driver）提交任务。

（2）Executor 执行真正的计算任务的。

## 2.4 运行wordcount

### 1、spark shell方式

1、Spark Shell是一个交互式的命令行，里面可以写spark程序，方便学习和测试，它也是一个客户端，用于提交spark应用程序。

```shell
#进入shell编程界面
[zookeeper@node-4 spark-2.1.1-bin-hadoop2.7]$ bin/spark-shell 
```

2、提交任务并运行

```ini
/bigdata/spark-2.2.0-bin-hadoop2.7/bin/spark-shell --master spark://node-4:7077,node-5:7077
```

​		上面是指定了master的地址，那么就会将任务提交到集群，开始时sparksubmit（客户端）要连接Master，并申请计算资源（内存和核数），Master进行资源调度（就是让那些Worker启动Executor），在准备工作时，这些进程都已经创建好了。

3、用spark Shell完成WordCount计算

```scala
#wc指hdfs上的一个文件，将其排序后输出
sc.textFile("hdfs://node-4:9000/wc").flatMap(_.split(" ")).map((_, 1)).reduceByKey(_+_).sortBy(_._2, false).collect
```

启动HDFS(上传数据到hdfs)，sc是spark core（RDD）的执行入口。

说明：

在运行的时候，我们应该启动zookeeper、Hadoop和spark。

### 2、IDEA方式

目录结构

<img src="Spark学习.assets\image-20200808202459925.png" alt="image-20200808202459925" style="zoom:50%;" />

1、导入依赖

```xml
<dependencies>
    <!-- 导入spark的依赖 -->
    <dependency>
        <groupId>org.apache.spark</groupId>
        <artifactId>spark-core_2.11</artifactId>
        <version>${spark.version}</version>
    </dependency>
</dependencies>

<build>
        <plugins>
            <!-- 编译scala的插件 -->
            <plugin>
                <groupId>net.alchim31.maven</groupId>
                <artifactId>scala-maven-plugin</artifactId>
                <version>3.2.2</version>
                <executions>
                    <execution>
                        <id>scala-compile-first</id>
                        <phase>process-resources</phase>
                        <goals>
                            <goal>compile</goal>
                            <goal>testCompile</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
```

2、测试

```scala
object ScalaWordCount {
  def main(args: Array[String]): Unit = {
    /**
     * 部署spark计算框架的运行环境 yarn或者本地
     * setAppName:设置Spark应用的名称，会在集群的web界面里显示出来。
     * setMaster("local[4]")：以四个核在本地运行。
     */
    val conf = new SparkConf().setAppName("ScalaWordCount").setMaster("local[4]")
    //创建spark执行的入口
    val sc = new SparkContext(conf)
    //读取本目录下的文件(一行一行读出来)
    val lines: RDD[String] = sc.textFile("in/1.txt")

    /**
     * 1、切分压平
     * 数据：
     * hello hello
     * xiaolun
     * world
     */
    val words: RDD[String] = lines.flatMap(_.split(" "))
    println("words -->" + words.collect())
    //将单词和一组合
    val wordAndOne: RDD[(String, Int)] = words.map((_, 1))
    println("wordAndOne ---> " + wordAndOne.collect())
    /**
     * 按key进行聚合
     * (作用就是对相同key的数据进行处理，最终每个key只保留一条记录)
     * reduceByKey会寻找相同key的数据，当找到这样的两条记录时会对其value(分别记为x,y)做(x,y) => x+y的处理
     */
    val reduced: RDD[(String, Int)] = wordAndOne.reduceByKey(_ + _)
    println("reduced ---> " + reduced)
    //排序
    val sorted: RDD[(String, Int)] = reduced.sortBy(_._2, false)
	 val res: Array[(String, Int)] = sorted.collect()
    res.foreach(println)
    sc.stop() //释放资源
  }
}
```

控制台输出：

<img src="Spark学习.assets\image-20200808202612951.png" alt="image-20200808202612951" style="zoom:50%;" />

## 2.5 Spark任务执行流程讲解

![spark任务执行过程简介](Spark学习.assets\spark任务执行过程简介.png)

## 2.6 Yarn和spark的对比

 Yarn和Spark的StandAlone调度模式对比

| Yarn                              | Spark                                          | 描述                                                      |
| --------------------------------- | ---------------------------------------------- | --------------------------------------------------------- |
| ResouceManager                    | Master                                         | 管理子节点、资源调度、接收任务请求                        |
| NodeManger                        | Worker                                         | 管理当前节点，并管理子进程                                |
| YarnChild                         | Executor                                       | 运行真正的计算逻辑的（Task）                              |
| Client（客户端）ApplicaitonMaster | SparkSubmit（=（Client + ApplicaitonMaster）） | 提交app，管理该任务的Executor，并将Task提交到（Executor） |

# 3、RDD

## 3.1 介绍

1、RDD（弹性分布式数据集）是一个基本的抽象，它里面并不存储真正要计算的数据，你对RDD进行操作，它会在Driver端转换成Task，下发到Executor来计算多台集群上的数据，操作RDD就像操作一个本地集合一样（类似代理），降低了编程的复杂度。

​		代码中是一个抽象类，代码一个不可变（map等算子并不能改变RDD，只是创建了一个RDD），可分区（和后面的并行计算有关系），里面的元素可并行计算的集合。

（并行：多个cpu核并行计算；并发：线程指并发来抢占资源）

2、Java Io回顾

![image-20200808172629625](Spark学习.assets\image-20200808172629625.png)

引入下面的IO：

![image-20200808175134936](Spark学习.assets\image-20200808175134936.png)

<img src="Spark学习.assets\image-20200808174639042.png" alt="image-20200808174639042" style="zoom:80%;" />

3、RDD体现了装饰者设计模式（包装后有功能的扩展），将数据处理的逻辑进行封装（控制抽象，将封装好的逻辑传递给其他对象或函数）。

![image-20200808175349475](Spark学习.assets\image-20200808175349475.png)

4、RDD属性

（1）一组分区（Partion）,即数据集的基本组成单位。

（2）一个计算每个分区的函数。

（3）RDD之间的依赖关系

（4）一个Partioner,即RDD的分片函数。

（5）一个列表，存储每个Partion的优先位置（preferred location）。

<img src="Spark学习.assets\image-20200808181232122.png" alt="image-20200808181232122" style="zoom: 67%;" />

5、RDD的特点

​		RDD表示只读的分区的数据集，对RDD进行改动，只能通过RDD的转换操作，由一个RDD得到一个新的RDD，新的RDD包含了从其他RDD衍生所必须的信息。RDD之间存在依赖，RDD的执行是按照血缘关系延时计算的，如果血缘关系比较长，可以通过持久化来切断血缘关系。

（1）分区

​		RDD逻辑上是分区的，每个分区的数据是抽象存在的，计算的时候会通过一个compute函数得到每个分区的数据。

​		如果RDD是通过已有的文件系统构建，则compute函数是读取指定文件系统中的数据；如果RDD是通过其他RDD转换而来，则compute函数是执行转换逻辑将其他RDD的数据进行转换。

<img src="Spark学习.assets\image-20200808182045280.png" alt="image-20200808182045280" style="zoom: 67%;" />

（2）只读

<img src="Spark学习.assets\image-20200808182305128.png" alt="image-20200808182305128" style="zoom:80%;" />

​		由一个RDD转换成另一个RDD，可以通过丰富的操作算子（解决问题其实就是将问题的初始状态，通过一系列的操作（算子）对问题状态进行转换，然后达到完成状态。spark中的算子分为两类：转换算子和行动算子。）来实现，不再像MapReduce那样只能写map和reduce了，如下图所示：

​		<img src="Spark学习.assets\image-20200808183208395.png" alt="image-20200808183208395" style="zoom:80%;" />

（3）依赖

​		依赖分为两种，一种是窄依赖，RDD之间是一一对应的；一种是宽依赖，下游RDD的每个分区与上游RDD（也称父RDD）的每个分区都有关，是多对多的关系。

<img src="Spark学习.assets\image-20200808183354611.png" alt="image-20200808183354611" style="zoom:80%;" />

（4）缓存

<img src="Spark学习.assets\image-20200808183541324.png" alt="image-20200808183541324" style="zoom:80%;" />

### 创建RDD

​		在Spark中创建RDD有三种方式：从集合中创建；从外部存储中创建；从其他RDD创建。

1、从外部存储中创建

包括本地文件系统，还有Hadoop支持的数据集，比如HDFS,HBase等。

<img src="Spark学习.assets\image-20200807185555976.png" alt="image-20200807185555976" style="zoom:80%;" />2、从集合中创建

​		将Driver的Scala集合通过并行化的方式编程RDD（做实验测试的时候用），Spark主要提供了两种函数：parallelize和makeRDD。

<img src="Spark学习.assets\image-20200807185658091.png" alt="image-20200807185658091" style="zoom:80%;" />

<img src="Spark学习.assets\image-20200807185959344.png" alt="image-20200807185959344" style="zoom:80%;" />

并行化的第二种方式makeRDD：

![image-20200807190143371](Spark学习.assets\image-20200807190143371.png)

使用IDEA创建：

```scala
 def main(args: Array[String]): Unit = {
    val conf: SparkConf = new SparkConf().setMaster("local[4]").setAppName("My Master")
    val sc = new SparkContext(conf)

    //创建RDD
    //1、从集合中创建 makeRDD (Int类型)
    val listRDD: RDD[Int] = sc.makeRDD(List(1, 2, 3, 4))
    listRDD.collect().foreach(println)
    val arrayRDD: RDD[Int] = sc.parallelize(List(1, 2, 3, 4))
    arrayRDD.collect().foreach(println)

    /**
     * 2、从外部存储中创建
     * 默认情况下，可以读取项目路径，也可以读取其他路径：HDFS
     * 默认从文件中读取的数据都是字符串类型
     */
      println("-----------------------------------------")
    val value: RDD[String] = sc.textFile("in")
//    val lines: RDD[String] = sc.textFile("hdfs://node-6:9000/user/zookeeper/in01/wc01.txt")
    value.collect().foreach(println)
  }
```

3、调用一个已经存在了的RDD的Transformation，会生成一个新的RDD。

RDD分区的数据取决于哪些因素：
（1）如果是将Driver端的Scala集合并行化创建RDD，并且没有指定RDD的分区，RDD的分区就是为该app分配的中的核数。

（2）如果是从hdfs中读取数据创建RDD，并且设置了最新分区数量是1，那么RDD的分区数据即使输入切片的数据，如果不设置最小分区的数量，即spark调用textFile时会默认传入2，那么RDD的分区数量会大于等于输入切片的数量。

### RDD分区

1、使用从集合中创建进行文件保存

```scala
//核数设置成4的结果
val listRDD: RDD[Int] = sc.makeRDD(List(1, 2, 3, 4))
listRDD.saveAsTextFile("output")

//可以使用下面的方式指定分区，文件目录只会出现两个分区
val listRDD: RDD[Int] = sc.makeRDD(List(1, 2, 3, 4),2)
```

可以看到文件目录：

<img src="Spark学习.assets\image-20200808211847070.png" alt="image-20200808211847070" style="zoom:50%;" />

接下来我们来确认一下分区数为4个的原因：

（1）、点击makeRDD

<img src="Spark学习.assets\image-20200808211315985.png" alt="image-20200808211315985" style="zoom:80%;" />

(2)、点击并行度defaultParallelism

<img src="Spark学习.assets\image-20200808211337622.png" alt="image-20200808211337622" style="zoom:80%;" />

(3)、taskScheduler.defaultParallelism

<img src="Spark学习.assets\image-20200808211423779.png" alt="image-20200808211423779" style="zoom:67%;" />

(4)、查看抽象类具体实现

![image-20200808211532698](Spark学习.assets\image-20200808211532698.png)

(5)、找到函数

<img src="Spark学习.assets\image-20200808211643308.png" alt="image-20200808211643308" style="zoom:67%;" />

2、从外部存储中创建保存文件

```scala
 val lines: RDD[String] = sc.textFile("in/1.txt")
    lines.saveAsTextFile("output1")

//可以使用下面的方式指定分区
// //读取文件时，传递的分区参数为最小分区数，但是不一定是这个分区数，取决于hadoop读取文件时的分片规则
 val lines: RDD[String] = sc.textFile("in/1.txt",3)
```

分区数为2个的原因如下：

（1）点击textFile

<img src="Spark学习.assets\image-20200808212742595.png" alt="image-20200808212742595" style="zoom:80%;" />

![image-20200808212816777](Spark学习.assets\image-20200808212816777.png)

<img src="Spark学习.assets\image-20200808212845156.png" alt="image-20200808212845156" style="zoom: 80%;" />

分区最少是两个，但是可以比2个多。

（2）向分区存数据探究

```scala
val lines: RDD[String] = sc.textFile("in/2.txt")
lines.saveAsTextFile("output2")
```

假设文件1.txt中，存入下面的数据，spark会产生3个分区:

```
12345

```

​		计算分区和向分区里面存数据是两个不同的操作，将数据放到那个分区里面对应另外一套规则（hadoop切片规则）。

​		计算分区规则：5/2 = 2，2，1。因此，对应三个分区。

​		规则：首先按照行来读取，此时读取数据为“12345”，然后将数据存到part-0000，接着读取下一行，后面的数据没有了，所以后面的数据存储的为空。

## 3.2  算子

**Driver和Excutor的关系**

<img src="Spark学习.assets\image-20200808231755967.png" alt="image-20200808231755967" style="zoom:80%;" />

<img src="Spark学习.assets\image-20200808232009393.png" alt="image-20200808232009393" style="zoom:80%;" />

1、mapPartitionsWithIndex 	<img src="Spark学习.assets\image-20200808224215682.png" alt="image-20200808224215682" style="zoom:80%;" />

```scala
val listRDD: RDD[Int] = sc.makeRDD(1 to 10)
/**
 * 需求：创建一个RDD,使每个元素所在分区，形成一个
 * 元组进而组成一个新的RDD （数据，分区）
 * 1、使用模式匹配
 * case (num,datas) 中 num 是分区号，datas分区中的数据集合
 * 即传入的数据
 */
val value: RDD[(Int, String)] = listRDD.mapPartitionsWithIndex {
  case (num, datas) => {
    //先考虑语法，后考虑实现逻辑。数据原样数据
    //datas
    // _表示datas中的每一条数据（1 -》 10），后面是分区号
    datas.map((_, "分区号：" + num))
  }
}
value.collect().foreach(println)
```



2、aggregate 

3、aggregateByKey

4、collect方法

5、foldByKey方法

6、foreach 和 foreachPartition 

7、mapPartions(func)

<img src="Spark学习.assets\image-20200808221026304.png" alt="image-20200808221026304" style="zoom:80%;" />

​		下面是map和mapPartions的区别：

![image-20200808221744471](Spark学习.assets\image-20200808221744471.png)

<img src="Spark学习.assets\image-20200808221922816.png" alt="image-20200808221922816" style="zoom:80%;" />

```scala
val lsitRDD: RDD[Int] = sc.makeRDD(1 to 10)
/**
 * 实现lsitRDD数据扩大2倍
 * 1、lsitRDD.mapPartitions(datas => datas) 返回1 - 10
 * 因为只有两个分区，所以(datas => datas)直走两次。
 * 2、但是我们想要每个数据乘以2后返回，所以对于每个分区中的数据需要遍历
 * 3、mapPartitions和map实现功能一致，但效率优于map，将一个分区中的数据，全部传给一个Executor，
 * 减少了数据传输交互次数
 * 4、缺点：可能会出现内存溢出情况。
 */
//    val value: RDD[Int] = lsitRDD.mapPartitions(datas => datas)
val value: RDD[Int] = lsitRDD.mapPartitions(datas => {
  //datas 是 可迭代的集合。
  //下面是一个整体发给Executor,进行计算
  datas.map(data => data*2)
})
value.collect().foreach(println)
```

8、flatmap

```scala
val listRDD: RDD[List[Int]] = sc.makeRDD(Array(List(1, 2), List(3, 4)))
//把 List(1, 2)，List(3, 4)两个元素，当成datas,进行拆解后返回1，2，3，4
val value: RDD[Int] = listRDD.flatMap(datas => datas)
value.collect().foreach(println)
```

9、glom

```scala
val ListRDD: RDD[Int] = sc.makeRDD(List(1, 2, 3, 4, 5), 2)
val glomRDD: RDD[Array[Int]] = ListRDD.glom()
//glom：将一个分区数据形成一个数组，形成新的类型RDD[Array[T]]
glomRDD.collect().foreach(array => {
  /**
   * 1,2
   * 3,4,5
   */
  println(array.mkString(","))
```

10、groupBy & filter

<img src="Spark学习.assets\image-20200808235138346.png" alt="image-20200808235138346" style="zoom:80%;" />

<img src="Spark学习.assets\image-20200808235229414.png" alt="image-20200808235229414" style="zoom:80%;" />

```scala
val ListRDD: RDD[Int] = sc.makeRDD(List(1, 2, 3, 4, 5))
//(Int, Iterable[Int])  :前者i % 2的值，后者数据的结果
val value: RDD[(Int, Iterable[Int])] = ListRDD.groupBy(i => i % 2)
//形成一个元组（k,v），k表示分组的key,v表示分组后的数据集合
//(0,CompactBuffer(2, 4))
//(1,CompactBuffer(1, 3, 5))
value.collect().foreach(println)
```

```scala
//将2的倍数取出
val value: RDD[Int] = ListRDD.filter(x => (x % 2 == 0))
```

