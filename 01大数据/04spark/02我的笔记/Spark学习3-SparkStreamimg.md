# 1、介绍

1、概述

Spark Streaming用于流式数据的处理。Spark Streaming支持的数据输入源很多，例如：Kafka、Flume、Twitter、ZeroMQ和简单的TCP套接字等等。数据输入后可以用Spark的高度抽象原语如：map、reduce、join、window等进行运算。而结果也能保存在很多地方，如HDFS，数据库等。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201011140113180.png" alt="image-20201011140113180" style="zoom:80%;" />

和Spark基于RDD的概念很相似，Spark Streaming使用离散化流(discretized stream)作为抽象表示，叫作DStream。DStream 是随时间推移而收到的数据的序列。在内部，每个时间区间收到的数据都作为 RDD 存在，而DStream是由这些RDD所组成的序列(因此得名“离散化”)。

2、架构

（1）架构图

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201011140147382.png" alt="image-20201011140147382" style="zoom:80%;" />

（2）采集数据图

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201011140333595.png" alt="image-20201011140333595" style="zoom: 80%;" />

# 2、Dstream入门

## 1、WordCount案例

1、需求

使用netcat工具向9999端口不断的发送数据，通过SparkStreaming读取端口数据并统计不同单词出现的次数

2、编写代码

（1）添加依赖

```xml
<dependency>
    <groupId>org.apache.spark</groupId>
    <artifactId>spark-streaming_2.11</artifactId>
    <version>2.1.1</version>
</dependency>
```

（2）添加代码

```scala
package chapter04

import org.apache.spark.SparkConf
import org.apache.spark.streaming.dstream.{DStream, ReceiverInputDStream}
import org.apache.spark.streaming.{Seconds, StreamingContext}

/**
 * @time: 2020-10-11 14:05 
 * @author: likunlun 
 * @description: 统计单词个数
 */
object SparkStreaming01_WordCount {
  def main(args: Array[String]): Unit = {
    val conf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkStreaming-wordcount")
    //实时数据采集环境对象
    //采集周期：以指定的时间为周期采集实时数据
    val streamingContext = new StreamingContext(conf, Seconds(3))
    //从指定的端口中采集数据
    val socketLineDStream: ReceiverInputDStream[String] = streamingContext.socketTextStream("hadoop101", 9999)

    //将采集过来的数据进行分解（扁平化）
    val wordDStream: DStream[String] = socketLineDStream.flatMap(line => {
      line.split(" ")
    })
    //将数据进行结构的转换方便分析
    val mapDStream: DStream[(String, Int)] = wordDStream.map((_, 1))
    //将转换结构的数据进行聚合处理
    val word2SumDStream: DStream[(String, Int)] = mapDStream.reduceByKey(_ + _)

    //将结果打印出来
    word2SumDStream.print()

    //启动采集器
    streamingContext.start()
    //Driver等待采集器执行
    streamingContext.awaitTermination()
  }
}
```

（3）启动上面的程序，然后再Linux中通过NetCat向端口9999发送数据。

```shell
nc -lk 9999
```

（4）在类路径下配置日志输出信息

从spark的/conf路径中下载得到，如果程序运行时，log日志太多，可以将spark conf目录下的log4j文件里面的日志级别改成WARN。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201011201209435.png" alt="image-20201011201209435" style="zoom:67%;" />

# 3、Dstream创建

## 1、介绍

Spark Streaming原生支持一些不同的数据源。一些“核心”数据源已经被打包到Spark Streaming 的 Maven 工件中，而其他的一些则可以通过 spark-streaming-kafka 等附加工件获取。每个接收器都以 Spark 执行器程序中一个长期运行的任务的形式运行，因此会占据分配给应用的 CPU 核心。

此外，我们还需要有可用的 CPU 核心来处理数据。这意味着如果要运行多个接收器，就必须至少有和接收器数目相同的核心数，还要加上用来完成计算所需要的核心数。例如，如果我们想要在流计算应用中运行 10 个接收器，那么至少需要为应用分配 11 个 CPU 核心。所以如果在本地模式运行，不要使用local[1]。

## 2、自定义数据源

1、需求

自定义数据源，实现监控某个端口号（9999），获取该端口号内容（统计单词个数）。

2、代码

```scala
package chapter04

import java.io.{BufferedReader, InputStreamReader}
import java.net.Socket

import org.apache.spark.SparkConf
import org.apache.spark.storage.StorageLevel
import org.apache.spark.streaming.dstream.{DStream, ReceiverInputDStream}
import org.apache.spark.streaming.receiver.Receiver
import org.apache.spark.streaming.{Seconds, StreamingContext}

//自定义接收器
object SparkStreaming02_DiyCollector {
  def main(args: Array[String]): Unit = {
    val conf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkStreaming-Collector")
    val streamingContext = new StreamingContext(conf, Seconds(5))
    //使用自定义的采集器，从端口处采集数据
    val receiverDStream: ReceiverInputDStream[String] = streamingContext.receiverStream(new MyReceiver("hadoop101", 9999))

    //将采集过来的数据进行分解（扁平化）
    val wordDStream: DStream[String] = receiverDStream.flatMap(line => {
      line.split(" ")
    })
    //将数据进行结构的转换方便分析
    val mapDStream: DStream[(String, Int)] = wordDStream.map((_, 1))
    //将转换结构的数据进行聚合处理
    val word2SumDStream: DStream[(String, Int)] = mapDStream.reduceByKey(_ + _)

    //将结果打印出来
    word2SumDStream.print()

    //启动采集器
    streamingContext.start()
    //Driver等待采集器执行
    streamingContext.awaitTermination()
  }
}

/**
 * 声明采集器
 * 1、继承Receiver
 */
class MyReceiver(host: String, port: Int) extends Receiver[String](StorageLevel.MEMORY_ONLY) {
  //这一行放在这里会报错：java.io.NotSerializableException: java.net.Socket
  //  private var socket: Socket = new Socket(host, port)
  private var socket: Socket = _

  def receive(): Unit = {
    socket = new Socket(host, port)
    val reader = new BufferedReader(new InputStreamReader(socket.getInputStream, "UTF-8"))
    var line: String = null

    while ((line = reader.readLine()) != null) {
      //将采集的数据存储到采集器的内部进行转换
      if ("END".equals(line)) {
        return
      } else {
        this.store(line)
      }
    }
  }

  override def onStart(): Unit = {
    new Thread(new Runnable {
      override def run(): Unit = {
        receive()
      }
    }).start()
  }

  override def onStop(): Unit = {
    if (socket != null) {
      socket.close()
      socket = null
    }
  }
}
```

## 3、Kafka数据源

1、介绍

在工程中需要引入 Maven 工件 spark- streaming-kafka_2.10 来使用它。包内提供的 KafkaUtils 对象可以在 StreamingContext 和 JavaStreamingContext 中以你的 Kafka 消息创建出 DStream。由于 KafkaUtils 可以订阅多个主题，因此它创建出的 DStream 由成对的主题和消息组成。

2、需求

通过SparkStreaming从Kafka读取数据，并将读取过来的数据做简单计算(WordCount)，最终打印到控制台。

3、代码

（1）导入依赖

```xml
<dependency>
    <groupId>org.apache.spark</groupId>
    <artifactId>spark-streaming-kafka-0-8_2.11</artifactId>
    <version>2.1.1</version>
</dependency>
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka-clients</artifactId>
    <version>0.11.0.2</version>
</dependency>
```

（2）测试类

```scala
package chapter04

import org.apache.spark.SparkConf
import org.apache.spark.storage.StorageLevel
import org.apache.spark.streaming.dstream.{DStream, ReceiverInputDStream}
import org.apache.spark.streaming.kafka.KafkaUtils
import org.apache.spark.streaming.{Seconds, StreamingContext}

/**
 * @time: 2020-10-11 15:04 
 * @author: likunlun 
 * @description: 从kafka中采集数据并消费
 */
object SparkStreaming03_Kafka {
  def main(args: Array[String]): Unit = {
    val conf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkStreaming-wordcount")
    //实时数据采集环境对象
    //采集周期：以指定的时间为周期采集实时数据
    val streamingContext = new StreamingContext(conf, Seconds(5))

    //从kafka中采集数据
    val kafkaDStream: ReceiverInputDStream[(String, String)] = KafkaUtils.createStream(
      streamingContext,
      "hadoop101:2181",
      "A-groupId",
      Map("A-topic" -> 3),
      StorageLevel.MEMORY_AND_DISK_SER_2
    )
    //将采集过来的数据进行分解（扁平化）
    val wordDStream: DStream[String] = kafkaDStream.flatMap(
      t => {
        t._2.split(" ")
      }
    )
    //将数据进行结构的转换方便分析
    val mapDStream: DStream[(String, Int)] = wordDStream.map((_, 1))
    //将转换结构的数据进行聚合处理
    val word2SumDStream: DStream[(String, Int)] = mapDStream.reduceByKey(_ + _)

    //将结果打印出来
    word2SumDStream.print()

    //启动采集器
    streamingContext.start()
    //Driver等待采集器执行
    streamingContext.awaitTermination()
  }
}
```

（3）从kafka中采集数据并消费

```shell
# 创建一个主题A-topic
[zookeeper@hadoop102 kafka]$ bin/kafka-topics.sh --zookeeper hadoop102:2181 --create --topic A-topic --replication-factor 3 --partitions 2 

# 创建一个生产者生产数据，在IDEA端可以接收到
[zookeeper@hadoop102 kafka]$ bin/kafka-console-producer.sh --broker-list hadoop102:9092 --topic A-topic
```

（4）结果展示

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201011155059374.png" alt="image-20201011155059374" style="zoom: 50%;" />

# 4、DStream转换

## 1、介绍

DStream上的原语与RDD的类似，分为Transformations（转换）和Output Operations（输出）两种。

此外转换操作中还有一些比较特殊的原语，如：updateStateByKey()、transform()以及各种Window相关的原语。

## 2、无状态转化操作

1、介绍

无状态转化操作就是把简单的RDD转化操作应用到每个批次上，也就是转化DStream中的每一个RDD。部分无状态转换如下表所示：
<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201011202226848.png" alt="image-20201011202226848" style="zoom:67%;" />

需要记住的是，尽管这些函数看起来像作用在整个流上一样，但事实上每个DStream在内部是由许多RDD(批次)组成，且无状态转化操作是分别应用到每个RDD上的。例如，reduceByKey()会归约每个时间区间中的数据，但不会归约不同区间之间的数据。 

比如：在之前的wordcount程序中，我们只会统计5秒内接收到的数据的单词个数，而不会累加。 

无状态转化操作也能在多个DStream间整合数据，不过也是在各个时间区间内。

## 3、有状态转换

1、介绍

UpdateStateByKey原语用于记录历史记录，有时，我们需要在 DStream 中跨批次维护状态(例如流计算中累加wordcount)。针对这种情况，updateStateByKey() 为我们提供了对一个状态变量的访问，用于键值对形式的 DStream。给定一个由(键，事件)对构成的 DStream，并传递一个指定如何根据新的事件 更新每个键对应状态的函数，它可以构建出一个新的 DStream，其内部数据为(键，状态) 对。 

updateStateByKey() 的结果会是一个新的 DStream，其内部的 RDD 序列是由每个时间区间对应的(键，状态)对组成的。

updateStateByKey操作使得我们可以在用新信息进行更新时保持任意的状态。为使用这个功能，你需要做下面两步： 

（1）定义状态，状态可以是一个任意的数据类型。 

（2）定义状态更新函数，用此函数阐明如何使用之前的状态和来自输入流的新值对状态进行更新。

2、代码

（1）需求

记录跨批次的单词个数信息，对单词个数进行累加。

（2）代码

```scala
package chapter04

import org.apache.spark.SparkConf
import org.apache.spark.storage.StorageLevel
import org.apache.spark.streaming.dstream.{DStream, ReceiverInputDStream}
import org.apache.spark.streaming.kafka.KafkaUtils
import org.apache.spark.streaming.{Seconds, StreamingContext}

/**
 * 累加性地统计单词个数信息
 */
object SparkStreaming04_UpdateState {
  def main(args: Array[String]): Unit = {
    val conf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkStreaming-wordcount")
    //实时数据采集环境对象
    //采集周期：以指定的时间为周期采集实时数据
    val streamingContext = new StreamingContext(conf, Seconds(5))
    //保存数据的状态，需要设定检查点路径
    streamingContext.sparkContext.setCheckpointDir("cp")

    //从kafka中采集数据
    val kafkaDStream: ReceiverInputDStream[(String, String)] = KafkaUtils.createStream(
      streamingContext,
      "hadoop101:2181",
      "A-groupId",
      Map("A-topic" -> 3),
      StorageLevel.MEMORY_AND_DISK_SER_2
    )
    //将采集过来的数据进行分解（扁平化）
    val wordDStream: DStream[String] = kafkaDStream.flatMap(
      t => {
        t._2.split(" ")
      }
    )
    //将数据进行结构的转换方便分析
    val mapDStream: DStream[(String, Int)] = wordDStream.map((_, 1))

    //将转换结构后的数据进行聚合处理
    val stateDStream: DStream[(String, Int)] = mapDStream.updateStateByKey {
      case (seq, buffer) => {
        var sum = buffer.getOrElse(0) + seq.sum
        Option(sum)
      }
    }
    //将结果打印出来
    stateDStream.print()

    //启动采集器
    streamingContext.start()
    //Driver等待采集器执行
    streamingContext.awaitTermination()
  }
}
```

# 5、窗口函数

1、介绍

（1）概述

Window Operations可以设置窗口的大小和滑动窗口的间隔来动态的获取当前Steaming的允许状态。基于窗口的操作会在一个比 StreamingContext 的批次间隔更长的时间范围内，通过整合多个批次的结果，计算出整个窗口的结果。 

![image-20201011203313739](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201011203313739.png)

所有基于窗口的操作都需要两个参数，分别为窗口时长以及滑动步长，两者都必须是 StreamContext 的批次间隔的整数倍。

如果有一个以 10 秒为批次间隔的源 DStream，要创建一个最近 30 秒的时间窗口(即最近 3 个批次)，就应当把 windowDuration 设为 30 秒。而滑动步长的默认值与批次间隔相等，用来控制对新的 DStream 进行计算的间隔。如果源 DStream 批次间隔为 10 秒，并且我们只希望每两个批次计算一次窗口结果， 就应该把滑动步长设置为 20 秒。 

（2）函数介绍

+ window(windowLength, slideInterval): 基于对源DStream窗化的批次进行计算返回一个新的Dstream。

2、入门介绍

```scala
package chapter04

/**
 * @time: 2020-10-11 20:47 
 * @author: likunlun 
 * @description: 窗口函数介绍
 */
object SparkStreaming05_windowsFuncIntro {
  def main(args: Array[String]): Unit = {
    val list = List(1, 2, 3, 4, 5, 6)
    /**
     * 滑动窗口函数
     * sliding有两个参数：
     * 参数1：尺寸
     * 参数2：步长
     */
    val iterator: Iterator[List[Int]] = list.sliding(3, 3)

    /**
     * 输出：
     * 1,2,3
     * 4,5,6
     */
    for (elem <- iterator) {
      println(elem.mkString(","))
    }
  }
}
```

3、窗口滑动

（1）需求

窗口函数结合kafka的一个使用。

```
//有三个窗口的数据，如下，每个窗口耗时为3秒。程序中windows的大小为9S，移动步长为3秒。
2   3   1

//控制台打印数据，从进来到出去。
2 5 6 4 1
```

（2）代码

```scala
package chapter04

import org.apache.spark.SparkConf
import org.apache.spark.storage.StorageLevel
import org.apache.spark.streaming.dstream.{DStream, ReceiverInputDStream}
import org.apache.spark.streaming.kafka.KafkaUtils
import org.apache.spark.streaming.{Seconds, StreamingContext}

/**
 * @time: 2020-10-11 20:47 
 * @author: likunlun 
 * @description: 窗口函数的使用
 */
object SparkStreaming06_windowsFuncUse {
  def main(args: Array[String]): Unit = {
    val conf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkStreaming-wordcount")
    //实时数据采集环境对象
    //采集周期：以指定的时间为周期采集实时数据
    val streamingContext = new StreamingContext(conf, Seconds(3))

    //从kafka中采集数据
    val kafkaDStream: ReceiverInputDStream[(String, String)] = KafkaUtils.createStream(
      streamingContext,
      "hadoop101:2181",
      "A-groupId",
      Map("A-topic" -> 3),
      StorageLevel.MEMORY_AND_DISK_SER_2
    )

    //窗口大小应该为采集周期的整数倍，窗口滑动的步长也应该为采集周期的整数倍
    val windowDStream: DStream[(String, String)] = kafkaDStream.window(Seconds(9), Seconds(3))
    //将采集过来的数据进行分解（扁平化）
    val wordDStream: DStream[String] = windowDStream.flatMap(
      t => {
        t._2.split(" ")
      }
    )
    //将数据进行结构的转换方便分析
    val mapDStream: DStream[(String, Int)] = wordDStream.map((_, 1))

    //将转换结构后的数据进行聚合处理
    //将转换结构的数据进行聚合处理
    val word2SumDStream: DStream[(String, Int)] = mapDStream.reduceByKey(_ + _)
    //将结果打印出来
    word2SumDStream.print()

    //启动采集器
    streamingContext.start()
    //Driver等待采集器执行
    streamingContext.awaitTermination()
  }
}
```

（3）测试

启动Kafka，并开启一个主题为A-topic的生产者，用于向IDEA传送数据。然后启动IDEA，接收数据。假设在生产者端传送数据A一段时间后停止，在IDEA控制台可以看到数据先增加后减少。

# 6、其他操作

1、Transform

（1）概述

Transform可以允许DStream上执行任意的RDD-to-RDD函数。即使这些函数并没有在DStream的API中暴露出来，通过该函数可以方便的扩展Spark API。该函数每一批次调度一次，其实也就是对DStream中的RDD应用转换。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201011211655748.png" alt="image-20201011211655748" style="zoom: 67%;" />





































































































































































































