# 1、SparkSQL介绍

1、介绍

（1）概述

Spark SQL是Spark用来处理结构化数据的一个模块，它提供了2个编程抽象：DataFrame和DataSet，并且作为分布式SQL查询引擎的作用。

Hive是将Hive SQL转换成MapReduce然后提交到集群上执行，大大简化了编写MapReduc的程序的复杂性，由于MapReduce这种计算模型执行效率比较慢。所有Spark SQL的应运而生，它是将Spark SQL转换成RDD，然后提交到集群执行，执行效率非常快。

（2）特点

+ 易整合
+ 统一的数据访问方式
+ 兼容Hive
+ 标准的数据连接

2、DataFrame介绍

（1）DataFrame更像传统数据库的二维表格，除了数据以外，还记录数据的结构信息，即schema。

（2）与Hive类似，DataFrame也支持嵌套数据类型（struct、array和map）。从API易用性的角度上看，DataFrame API提供的是一套高层的关系操作，比函数式的RDD API要更加友好，门槛更低。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201010164258033.png" alt="image-20201010164258033" style="zoom:67%;" />

左侧的RDD[Person]虽然以Person为类型参数，但Spark框架本身不了解Person类的内部结构。右侧的DataFrame却提供了详细的结构信息，使得Spark SQL可以清楚地知道该数据集中包含哪些列，每列的名称和类型各是什么。

（3）DataFrame是为数据提供了Schema的视图。可以把它当做数据库中的一张表来对待，DataFrame也是懒执行的。性能上比RDD要高，主要原因：优化的执行计划：查询计划通过Spark catalyst optimiser进行优化。

3、DataSet介绍

（1）是Dataframe API的一个扩展，是Spark最新的数据抽象。用户友好的API风格，既具有类型安全检查也具有Dataframe的查询优化特性。

（2）样例类被用来在Dataset中定义数据的结构信息，样例类中每个属性的名称直接映射到DataSet中的字段名称。

（3）Dataframe是Dataset的特例，DataFrame=Dataset[Row] ，所以可以通过as方法将Dataframe转换为Dataset。Row是一个类型，跟Car、Person这些的类型一样，所有的表结构信息我都用Row来表示。

（4）DataFrame只是知道字段，但是不知道字段的类型，所以在执行这些操作的时候是没办法在编译的时候检查是否类型失败的，比如你可以对一个String进行减法操作，在执行的时候才报错，而DataSet不仅仅知道字段，而且知道字段类型，所以有更严格的错误检查。

# 2、SparkSQL编程

## 1、介绍

SparkSession是Spark最新的SQL查询起始点，实质上是SQLContext和HiveContext的组合，所以在SQLContext和HiveContext上可用的API在SparkSession上同样是可以使用的。

SparkSession内部封装了sparkContext，所以计算实际上是由sparkContext完成的。

## 2、DataFrame

### 1、创建

1、介绍

在Spark SQL中SparkSession是创建DataFrame和执行SQL的入口，创建DataFrame有三种方式：

（1）通过Spark的数据源进行创建。

（2）从一个存在的RDD进行转换。

（3）还可以从Hive Table进行查询返回。

2、从Spark的数据源创建

（1）导入user.json数据

```python
{"name": "a","age": 20}
{"name": "b","age": 24}
{"name": "c","age": 28}
```

（2）创建DataFrame

```scala
scala> var df = spark.read.json("/myspark/test/user.json")

df: org.apache.spark.sql.DataFrame = [age: bigint, name: string]
```

（3）展示结果

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201010172008960.png" alt="image-20201010172008960" style="zoom:67%;" />

3、SQL风格语法(主要)

（1）为DataFrame创建一个临时表

```scala
var df = spark.read.json("/myspark/test/user.json")

scala> df.createOrReplaceTempView("user")
```

（2）通过SQL语句实现查询全表

```scala
spark.sql("select * from user").show()
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201010172239427.png" alt="image-20201010172239427" style="zoom:67%;" />

临时表是Session范围内的，Session退出后，表就失效了。如果想应用范围内有效，可以使用全局表。注意使用全局表时需要全路径访问，如：

```
global_temp.user
```

（3）为DataFrame创建一个全局表

```scala
scala> df.createGlobalTempView("people")
```

（4）通过SQL语句实现查询全表

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201010172356610.png" alt="image-20201010172356610" style="zoom:67%;" />

4、DSL风格（次要）

（1）只查看”name”列数据

```scala
scala> df.select("age").show()
```

（2）查看”name”列数据以及”age+1”数据

```scala
scala> df.select($"name", $"age" + 1).show()
```

### 2、RDD转换为DateFrame

如果需要RDD与DF或者DS之间操作，那么都需要引入 import spark.implicits._（spark不是包名，而是sparkSession对象的名称）。

1、方式1：手动转换

（1）导入隐式转换

```scala
scala> import spark.implicits._
```

2、转换

```scala
scala> var rdd = sc.makeRDD(List(1,2,3,4))
scala> var df = rdd.toDF("id")
```

2、方式2：反射转换

（1）创建一个样例类

```scala
scala> case class People(name:String, age:Int)
```

（2）转换

```scala
scala> var rdd = sc.makeRDD(List(("zs",10),("ls",20)))
scala> var peopleRDD = rdd.map( t => {People(t._1,t._2)})

peopleRDD: org.apache.spark.rdd.RDD[People] = MapPartitionsRDD[39] at map at <console>:31
```

### 3、DateFrame转换为RDD

1、直接调用rdd进行转换

```scala
scala> peopleRDD.toDF.rdd

res17: org.apache.spark.rdd.RDD[org.apache.spark.sql.Row] = MapPartitionsRDD[46] at rdd at <console>:34
```

## 3、DataSet

Dataset是具有强类型的数据集合，需要提供对应的类型信息。

### 4、DataFrame转DataSet

1、导入隐式转换

```scala
import spark.implicits._
```

2、创建样例类

```scala
case class People(name:String, age:Int)
```

3、转换

```scala
var df = spark.read.json("/myspark/test/user.json")

scala> df.as[Person].show()
+---+----+
|age|name|
+---+----+
| 20|   a|
| 24|   b|
| 28|   c|
+---+----+
```



### 1、创建

（1）创建一个样例类

```scala
case class Person(name: String, age: Long)
```

（2）创建DataSet

```scala
scala> val caseClassDS = Seq(Person("Andy", 32)).toDS()

caseClassDS: org.apache.spark.sql.Dataset[Person] = [name: string, age: bigint]
```

### 2、RDD转换为DataSet

参考创建DataSet的方式，使用toDS()的方式。

### 3、DataSet转换为RDD

```scala
scala> caseClassDS.rdd

res0: org.apache.spark.rdd.RDD[Person] = MapPartitionsRDD[3] at rdd at <console>:28
```

### 4、DataSet转DataFrame

1、导入隐式转换

```scala
import spark.implicits._
```

2、转换

```scala
scala> caseClassDS.toDF

res2: org.apache.spark.sql.DataFrame = [name: string, age: bigint]
```

# 3、IDEA编写SparkSQL程序

## 1、创建DataFrame读取数据

1、代码

（1）json数据

```json
{"name": "a","age": 20}
{"name": "b","age": 24}
{"name": "c","age": 28}
```

（2）导入依赖

```xml
<dependency>
  <groupId>org.apache.spark</groupId>
  <artifactId>spark-sql_2.11</artifactId>
  <!--版本应该是2.1.1，不然会报错-->
  <!-- <version>2.4.4</version>-->
  <version>2.1.1</version>
</dependency>
```

（3）测试

```scala
import org.apache.spark.SparkConf
import org.apache.spark.sql.{DataFrame, SparkSession}

/**
 * @time: 2020-10-10 18:47 
 * @author: likunlun 
 * @description: 创建DataFrame读取数据
 */
object SparkSQL01_CreateDataFrame {
  def main(args: Array[String]): Unit = {
    val sparkConf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkSql01")
    val spark: SparkSession = SparkSession.builder().config(sparkConf).getOrCreate()
    //读取数据
    val frame: DataFrame = spark.read.json("in/user.json")
    //展示数据
//    frame.show()

    //将DataFrame展示成一张表
    frame.createOrReplaceTempView("user")
    //采用sql的方式访问数据
    spark.sql("select * from user").show()

    /**
     * +---+----+
     * |age|name|
     * +---+----+
     * | 20|   a|
     * | 24|   b|
     * | 28|   c|
     * +---+----+
     */
    spark.stop()
  }
}
```

## 2、RDD、DataFrame、DataSet转换

1、代码

```scala
import org.apache.spark.SparkConf
import org.apache.spark.rdd.RDD
import org.apache.spark.sql.{DataFrame, Dataset, Row, SparkSession}

/**
 * @time: 2020-10-10 18:47 
 * @author: likunlun 
 * @description: RDD、DataFrame、DataSet之间的相互转换
 */
object Spark01_sqlTransform {
  def main(args: Array[String]): Unit = {
    val sparkConf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkSql01")
    val spark: SparkSession = SparkSession.builder().config(sparkConf).getOrCreate()

    //创建RDD
    val rdd: RDD[(Int, String, Int)] = spark.sparkContext.makeRDD(List((1, "zs", 10), (2, "ls", 20)))

    /**
     * 转换之前，需要引入隐式转换
     * 这里的spark不是包名的含义，是SparkSession对象的名字
     */
    import spark.implicits._

    //1、Rdd转换成DF
    val df: DataFrame = rdd.toDF("id", "name", "age")

    //2、DF转换为DS
    val ds: Dataset[User] = df.as[User]
    //3、DS转换为DF
    val df1: DataFrame = ds.toDF()
    //4、DF转换为rdd
    val rdd1: RDD[Row] = df1.rdd
    rdd1.foreach(row => {
      //通过索引获取数据
      println(row.getString(1))
    })

    //5、RDD和DataSet之间的相互转换
    val UserRDD: RDD[User] = rdd.map {
      case (id, name, age) => {
        User(id, name, age)
      }
    }
    val userDS: Dataset[User] = UserRDD.toDS()
    val rdd2: RDD[User] = userDS.rdd
    rdd2.foreach(println)

    spark.stop()
  }
}

case class User(id: Int, name: String, age: Int)
```

## 3、用户自定义弱聚合函数

强类型的Dataset和弱类型的DataFrame都提供了相关的聚合函数， 如 count()，countDistinct()，avg()，max()，min()。除此之外，用户可以设定自己的自定义聚合函数。

弱类型用户自定义聚合函数：通过继承UserDefinedAggregateFunction来实现用户自定义聚合函数。下面展示一个求年龄的自定义聚合函数。

1、代码

```scala
package chapter03

import org.apache.spark.SparkConf
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{DataType, DoubleType, LongType, StructType}
import org.apache.spark.sql.{DataFrame, Row, SparkSession}

/**
 * @time: 2020-10-10 19:59 
 * @author: likunlun 
 * @description: 自定义弱聚合函数
 */
object SparkSQL03_DiyAggregationFunc_Weak {
  def main(args: Array[String]): Unit = {
    val sparkConf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkSql01")
    val spark: SparkSession = SparkSession.builder().config(sparkConf).getOrCreate()

    //自定义聚合函数
    //创建聚合函数
    val udf1 = new MyAgeAvgFunction()
    spark.udf.register("avgAge", udf1)
    //使用聚合函数
    val frame: DataFrame = spark.read.json("in/user.json")
    frame.createOrReplaceTempView("user")
    spark.sql("select avgAge(age), avgAge(grade) from user").show()
    spark.stop()
  }
}

/**
 * 声明用户自定义聚合函数
 * 1、继承UserDefinedAggregateFunction
 * 2、实现方法
 */
class MyAgeAvgFunction extends UserDefinedAggregateFunction {
  //函数输入的数据结构
  override def inputSchema: StructType = {
    /**
     * 1、age在这里只是一个变量，定义成xxx也没有关系。
     * 2、该输入参数只会在更新函数update()中的input参数用到。
     * 3、传入两个需要操作的数据进行求平均值，步骤一致。
     */
    //    new StructType().add("age", LongType) //只计算年龄的平均值
    new StructType().add("age", LongType).add("grade", LongType)

  }

  //计算时的数据结构
  override def bufferSchema: StructType = {
    new StructType().add("num", LongType).add("count", LongType)
  }

  //函数返回的数据结构
  override def dataType: DataType = DoubleType

  //函数是否稳定，即相同输入是否有相同输出
  override def deterministic: Boolean = true

  //计算之前的缓冲区初始化
  /**
   * 1、分区内的初始化操作
   * 2、即给sum和count赋的初始值
   */
  override def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = 0L
    buffer(1) = 0L
  }

  //根据查询结果更新缓存区数据(分区)
  /**
   * 1、分区内的更新操作
   * 2、buffer是临时变量
   * 3、input是自定义函数调用时传入的值，一行一行的进行操作
   */
  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    buffer(0) = buffer.getLong(0) + input.getLong(0) //sum += i
    print(buffer.getLong(0))
    buffer(1) = buffer.getLong(1) + 1 //count += 1
  }

  //将多个节点的缓冲区合并（分区合并）
  /**
   * 1、分区间的合并操作
   *
   * @param buffer1 假设有三个分区，其表示将前面的1，2，分区合并后的结果
   * @param buffer2 第三个分区的数据
   */
  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    //sum
    buffer1(0) = buffer1.getLong(0) + buffer2.getLong(0)
    //count
    buffer1(1) = buffer1.getLong(1) + buffer2.getLong(1)
    println(1)
  }

  //最后进行计算后输出
  override def evaluate(buffer: Row): Any = {
    buffer.getLong(0).toDouble / buffer.getLong(1)
  }
}
```

## 4、用户自定义强聚合函数

强类型用户自定义聚合函数：通过继承Aggregator来实现强类型自定义聚合函数，同样是求平均年龄。

1、代码

```scala
package chapter03

import org.apache.spark.SparkConf
import org.apache.spark.sql.expressions.Aggregator
import org.apache.spark.sql._

object SparkSQL04_DiyAggregationFunc_Strong {
  def main(args: Array[String]): Unit = {
    val sparkConf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkSql01")
    val spark: SparkSession = SparkSession.builder().config(sparkConf).getOrCreate()
    import spark.implicits._
    //创建聚合对象
    val udf2 = new MyAgeAvgFunction2
    //将聚合函数转换为查询列
    val avgCol: TypedColumn[UserBean, Double] = udf2.toColumn.name("avgAge")
    val frame: DataFrame = spark.read.json("in/user2.json")
    val userDS: Dataset[UserBean] = frame.as[UserBean]
    //应用函数
    userDS.select(avgCol).show()

    spark.stop()
  }
}

case class UserBean(name: String, age: Long)

case class AvgBuffer(var sum: Long, var count: Long)

/**
 * UserBean:输入参数类型
 * AvgBuffer:缓冲区
 * Double：输出类型
 */
class MyAgeAvgFunction2 extends Aggregator[UserBean, AvgBuffer, Double] {
  //初始化
  override def zero: AvgBuffer = {
    AvgBuffer(0, 0)
  }

  /**
   * 分区内的聚合数据
   *
   * @param b
   * @param a
   * @return
   */
  override def reduce(b: AvgBuffer, a: UserBean): AvgBuffer = {
    b.sum = b.sum + a.age
    b.count = b.count + 1
    b
  }

  /**
   * 分区间的合并数据
   *
   * @param b1
   * @param b2
   * @return
   */
  override def merge(b1: AvgBuffer, b2: AvgBuffer): AvgBuffer = {
    b1.sum = b1.sum + b2.sum
    b1.count = b1.count + b2.count
    b1
  }

  //完成计算
  override def finish(reduction: AvgBuffer): Double = {
    reduction.sum.toDouble / reduction.count
  }

  //转码对象，自定义类
  override def bufferEncoder: Encoder[AvgBuffer] = Encoders.product

  override def outputEncoder: Encoder[Double] = Encoders.scalaDouble
}
```

# 4、不同数据源的读取

## 1、介绍

1、文件保存选项

可以采用SaveMode执行存储操作，SaveMode定义了对数据的处理模式。需要注意的是，这些保存模式不使用任何锁定，不是原子操作。此外，当使用Overwrite方式执行时，在输出新数据之前原数据就已经被删除。

SaveMode详细介绍如下表：

| Scala/Java                      | Any Language     | Meaning              |
| ------------------------------- | ---------------- | -------------------- |
| SaveMode.ErrorIfExists(default) | "error"(default) | 如果文件存在，则报错 |
| SaveMode.Append                 | "append"         | 追加                 |
| SaveMode.Overwrite              | "overwrite"      | 覆写                 |
| SaveMode.Ignore                 | "ignore"         | 数据存在，则忽略     |

2、Json文件

Spark SQL 能够自动推测 JSON数据集的结构，并将它加载为一个Dataset[Row]. 可以通过SparkSession.read.json()去加载一个 一个JSON 文件。

注意：这个JSON文件不是一个传统的JSON文件，每一行都得是一个JSON串。

3、Parquet文件

Parquet是一种流行的列式存储格式，可以高效地存储具有嵌套字段的记录。Parquet格式经常在Hadoop生态圈中被使用，它也支持Spark SQL的全部数据类型。Spark SQL 提供了直接读取和存储 Parquet 格式文件的方法。

4、操作

（1）使用追加模式将数据写入到指定文件夹中

```scala
scala> df.write.format("json").mode("append").save("file:///myspark/test")
```

spark默认读取的是一个Parquet文件，我们可以指定格式让其读取指定类型（json）文件。

## 2、JDBC

1、介绍

Spark SQL可以通过JDBC从关系型数据库中读取数据的方式创建DataFrame，通过对DataFrame一系列的计算后，还可以将数据再写回关系型数据库中。

注意:需要将相关的数据库驱动放到spark的类路径（/myspark/spark/jars）下。

```
//需要使用的jar包
/myhive/hive/lib/mysql-connector-java-5.1.35.jar
```

2、操作

（1）启动spark-shell

```shell
bin/spark-shell
```

（2）连接Mysql数据库

```scala
val jdbcDF = spark.read
.format("jdbc")
.option("url","jdbc:mysql://hadoop101:3306/rdd")
.option("dbtable", "user")
.option("user", "root")
.option("password", "123")
.load()
```

Navicat中需要有数据库rdd和数据表user，将上面代码连接数据库时，需要将其写成一行。

（3）测试连接

```scala
scala> jdbcDF.show
+----+---+
|name|age|
+----+---+
| tom| 10|
|jack| 20|
+----+---+
```

（4）将数据写入Mysql

```scala
jdbcDF.write
.format("jdbc")
.option("url","jdbc:mysql://hadoop101:3306/rdd")
.option("dbtable", "user")
.option("user", "root")
.option("password", "123")
.save()
```

## 3、Hive

1、介绍

SparkSQL中有一个内置的Hive数据库，因此，即使没有部署好Hive，Spark SQL也可以运行。 但是，需要注意的是，如果你没有部署好Hive，Spark SQL会在当前的工作目录中创建出自己的Hive 元数据仓库，叫作 metastore_db。

此外，如果你尝试使用 HiveQL 中的 CREATE TABLE (并非 CREATE EXTERNAL TABLE)语句来创建表，这些表会被放在你默认的文件系统中的 /user/hive/warehouse 目录中(如果你的 classpath 中有配好的 hdfs-site.xml，默认的文件系统就是 HDFS，否则就是本地文件系统)。

如果你要把Spark SQL连接到一个部署好的Hive上，你必须把hive-site.xml复制到 Spark的配置文件目录中($SPARK_HOME/conf)。

2、内置Hive应用

（1）查询表数据

```scala
scala> spark.sql("show tables").show()

+--------+---------+-----------+
|database|tableName|isTemporary|
+--------+---------+-----------+
+--------+---------+-----------+
```

（2）创建表

```scala
scala> spark.sql("create table testTable(id int)")

20/10/11 16:08:28 WARN HiveMetaStore: Location: file:/myspark/spark/jars/spark-warehouse/testtable specified for non-external table:testtable
```

可以看到在路径：/myspark/spark/jars/spark-warehouse下，的确有一张表。

（3）查看表结构

```scala
scala> spark.sql("show tables").show()

+--------+----------+-----------+
|database| tableName|isTemporary|
+--------+----------+-----------+
| default| testtable|      false|
|        |      user|       true|
+--------+----------+-----------+
```

（4）使用hive语法向hive中加载文件

```scala
scala> spark.sql("load data local inpath '/myspark/test/user.json' into table testtable")

//文件内容
{"name": "a","age": 20}
{"name": "b","age": 24}
{"name": "c","age": 28}
```

在/myspark/spark/jars/spark-warehouse路径下，的确有文件user.json。

（5）查询文件

```scala
scala> spark.sql("select * from testtable").show

+----+
|  id|
+----+
|null|
|null|
|null|
+----+
```

3、外置Hive应用

（1）将Hive中的hive-site.xml拷贝或者软连接到Spark安装目录下的conf目录下。

（2）测试连接操作

```scala
scala> spark.sql("show databases").show

+------------+
|databaseName|
+------------+
|        db03|
|     default|
+------------+
```

此时，无论hive启动与否，spark都可以读到它的配置。











































































