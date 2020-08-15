# 1 数据压缩

## 1.1、介绍

![image-20200809090759616](D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习-YARN.assets\image-20200809090759616.png)

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习-YARN.assets\image-20200809090838206.png" alt="image-20200809090838206" style="zoom:80%;" />

1、MR支持的编码

| 压缩格式 | hadoop自带？ | 算法    | 文件扩展名 | 是否可切分 | 换成压缩格式后，原来的程序是否需要修改 |
| -------- | ------------ | ------- | ---------- | ---------- | -------------------------------------- |
| DEFLATE  | 是，直接使用 | DEFLATE | .deflate   | 否         | 和文本处理一样，不需要修改             |
| Gzip     | 是，直接使用 | DEFLATE | .gz        | 否         | 和文本处理一样，不需要修改             |
| bzip2    | 是，直接使用 | bzip2   | .bz2       | **是**     | 和文本处理一样，不需要修改             |
| **LZO**  | 否，需要安装 | LZO     | .lzo       | **是**     | 需要建索引，还需要指定输入格式         |
| Snappy   | 否，需要安装 | Snappy  | .snappy    | 否         | 和文本处理一样，不需要修改             |

为了支持多种压缩/解压缩算法，Hadoop引入了编码/解码器，如下表所示。

| 压缩格式 | 对应的编码/解码器                          |
| -------- | ------------------------------------------ |
| DEFLATE  | org.apache.hadoop.io.compress.DefaultCodec |
| gzip     | org.apache.hadoop.io.compress.GzipCodec    |
| bzip2    | org.apache.hadoop.io.compress.BZip2Codec   |
| LZO      | com.hadoop.compression.lzo.LzopCodec       |
| Snappy   | org.apache.hadoop.io.compress.SnappyCodec  |

压缩性能比较：

| 压缩算法 | 原始文件大小 | 压缩文件大小 | 压缩速度 | 解压速度 |
| -------- | :----------- | ------------ | -------- | -------- |
| gzip     | 8.3GB        | 1.8GB        | 17.5MB/s | 58MB/s   |
| bzip2    | 8.3GB        | 1.1GB        | 2.4MB/s  | 9.5MB/s  |
| LZO      | 8.3GB        | 2.9GB        | 49.3MB/s | 74.6MB/s |

## 1.2、压缩方式选择

1、gzip压缩

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习-YARN.assets\image-20200809091136900.png" alt="image-20200809091136900" style="zoom: 80%;" />

2、 Bzip2压缩

![image-20200809091216196](D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习-YARN.assets\image-20200809091216196.png)

3、lzo压缩

![image-20200809091243169](D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习-YARN.assets\image-20200809091243169.png)

4、snappy压缩

![image-20200809091303548](D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习-YARN.assets\image-20200809091303548.png)

## 1.3 压缩位置选择

压缩可以在MapReduce作用的任意阶段启用。

![image-20200809091415057](D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习-YARN.assets\image-20200809091415057.png)

## 1.4 压缩参数配置

要在Hadoop中启用压缩，可以配置如下参数：

| 参数                                                         | 默认值                                                       | 阶段        | 建议                                          |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ----------- | --------------------------------------------- |
| io.compression.codecs    （在core-site.xml中配置）           | org.apache.hadoop.io.compress.DefaultCodec,  org.apache.hadoop.io.compress.GzipCodec,  org.apache.hadoop.io.compress.BZip2Codec | 输入压缩    | Hadoop使用文件扩展名判断是否支持某种编解码器  |
| mapreduce.map.output.compress（在mapred-site.xml中配置）     | false                                                        | mapper输出  | 这个参数设为true启用压缩                      |
| mapreduce.map.output.compress.codec（在mapred-site.xml中配置） | org.apache.hadoop.io.compress.DefaultCodec                   | mapper输出  | 企业多使用LZO或Snappy编解码器在此阶段压缩数据 |
| mapreduce.output.fileoutputformat.compress（在mapred-site.xml中配置） | false                                                        | reducer输出 | 这个参数设为true启用压缩                      |
| mapreduce.output.fileoutputformat.compress.codec（在mapred-site.xml中配置） | org.apache.hadoop.io.compress.  DefaultCodec                 | reducer输出 | 使用标准工具或者编解码器，如gzip和bzip2       |
| mapreduce.output.fileoutputformat.compress.type（在mapred-site.xml中配置） | RECORD                                                       | reducer输出 | SequenceFile输出使用的压缩类型：NONE和BLOCK   |

## 1.5 压缩和解压缩案例

1、数据流的压缩和解压缩

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习-YARN.assets\image-20200809091844485.png" alt="image-20200809091844485" style="zoom:80%;" />

测试一下压缩方式：

| DEFLATE | org.apache.hadoop.io.compress.DefaultCodec |
| ------- | ------------------------------------------ |
| gzip    | org.apache.hadoop.io.compress.GzipCodec    |
| bzip2   | org.apache.hadoop.io.compress.BZip2Codec   |

代码：

```java
public class ZIpDemo01 {
    public static void main(String[] args) throws Exception {
        //执行后在D:/有一个压缩文件：D:/参考框架.txt.bz2
//        compress("D:/参考框架.txt", "org.apache.hadoop.io.compress.BZip2Codec");
        decompress("D:/参考框架.txt.bz2");
    }

    /**
     * 压缩
     *
     * @param filename
     * @param method   压缩方式
     */
    private static void compress(String filename, String method) throws Exception {

        // （1）获取输入流
        FileInputStream fis = new FileInputStream(new File(filename));

        Class codecClass = Class.forName(method);

        CompressionCodec codec = (CompressionCodec) ReflectionUtils.newInstance(codecClass, new Configuration());

        // （2）获取输出流  codec.getDefaultExtension()为文件添加后缀
        FileOutputStream fos = new FileOutputStream(new File(filename + codec.getDefaultExtension()));
        //对普通输出流进行封装
        // 对正在被写入一个输出流的数据进行压缩，将其以压缩格式写入底层
        /**
         *
         */
        CompressionOutputStream cos = codec.createOutputStream(fos);

        // （3）流的对拷
        IOUtils.copyBytes(fis, cos, 1024 * 1024 * 5, false);

        // （4）关闭资源
        cos.close();
        fos.close();
        fis.close();
    }

    // 2、解压缩
    private static void decompress(String filename) throws Exception {

        // （0）校验是否能解压缩
        CompressionCodecFactory factory = new CompressionCodecFactory(new Configuration());

        CompressionCodec codec = factory.getCodec(new Path(filename));

        if (codec == null) {
            System.out.println("cannot find codec for file " + filename);
            return;
        }

        // （1）获取输入流
        CompressionInputStream cis = codec.createInputStream(new FileInputStream(new File(filename)));

        // （2）获取输出流 （decoded 表示解压缩后的文件）
        FileOutputStream fos = new FileOutputStream(new File(filename + ".decoded"));

        // （3）流的对拷
        IOUtils.copyBytes(cis, fos, 1024 * 1024 * 5, false);

        // （4）关闭资源
        cis.close();
        fos.close();
    }
}
```

## 1.6 Map和Reduce启用压缩

​		即使你的MapReduce的输入输出文件都是未压缩的文件，你仍然可以对Map任务的中间结果输出做压缩，因为它要写在硬盘并且通过网络传输到Reduce节点，对其压缩可以提高很多性能，这些工作只要设置两个属性即可。

只需要在Driver端添加下面的配置即可：

```java
// 开启map端输出压缩
configuration.setBoolean("mapreduce.map.output.compress", true);
// 设置map端输出压缩方式
configuration.setClass("mapreduce.map.output.compress.codec", BZip2Codec.class, CompressionCodec.class);

Job job = Job.getInstance(configuration);

// 设置reduce端输出压缩开启
FileOutputFormat.setCompressOutput(job, true);
// 设置压缩的方式
FileOutputFormat.setOutputCompressorClass(job, BZip2Codec.class);
```

# 2 Yarn资源调度器

​		Yarn是一个资源调度平台，负责为运算程序提供服务器运算资源，相当于一个分布式的操作系统平台，而MapReduce等运算程序则相当于运行于操作系统之上的应用程序。

## 2.1 工作机制

![image-20200809101307449](D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习-YARN.assets\image-20200809101307449.png)

工作机制详解：

（1）MR程序提交到客户端所在的节点。

（2）YarnRunner向ResourceManager申请一个Application。

（3）RM将该应用程序的资源路径返回给YarnRunner。

（4）该程序将运行所需资源提交到HDFS上。

（5）程序资源提交完毕后，申请运行mrAppMaster。

（6）RM将用户的请求初始化成一个Task。

（7）其中一个NodeManager领取到Task任务。

（8）该NodeManager创建容器Container，并产生MRAppmaster。

（9）Container从HDFS上拷贝资源到本地。

（10）MRAppmaster向RM 申请运行MapTask资源。

（11）RM将运行MapTask任务分配给另外两个NodeManager，另两个NodeManager分别领取任务并创建容器。

（12）MR向两个接收到任务的NodeManager发送程序启动脚本，这两个NodeManager分别启动MapTask，MapTask对数据分区排序。

（13）MrAppMaster等待所有MapTask运行完毕后，向RM申请容器，运行ReduceTask。

（14）ReduceTask向MapTask获取相应分区的数据。

（15）程序运行完毕后，MR会向RM申请注销自己。

## 2.2 作业提交全过程详解

![image-20200809103132454](D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习-YARN.assets\image-20200809103132454.png)

（1）作业提交

第1步：Client调用job.waitForCompletion方法，向整个集群提交MapReduce作业。

第2步：Client向RM申请一个作业id。

第3步：RM给Client返回该job资源的提交路径和作业id。

第4步：Client提交jar包、切片信息和配置文件到指定的资源提交路径。

第5步：Client提交完资源后，向RM申请运行MrAppMaster。

（2）作业初始化

第6步：当RM收到Client的请求后，将该job添加到容量调度器中。

第7步：某一个空闲的NM领取到该Job。

第8步：该NM创建Container，并产生MRAppmaster。

第9步：下载Client提交的资源到本地。

（3）任务分配

第10步：MrAppMaster向RM申请运行多个MapTask任务资源。

第11步：RM将运行MapTask任务分配给另外两个NodeManager，另两个NodeManager分别领取任务并创建容器。

（4）任务运行

第12步：MR向两个接收到任务的NodeManager发送程序启动脚本，这两个NodeManager分别启动MapTask，MapTask对数据分区排序。

第13步：MrAppMaster等待所有MapTask运行完毕后，向RM申请容器，运行ReduceTask。

第14步：ReduceTask向MapTask获取相应分区的数据。

第15步：程序运行完毕后，MR会向RM申请注销自己。

（5）进度和状态更新

YARN中的任务将其进度和状态(包括counter)返回给应用管理器, 客户端每秒(通过mapreduce.client.progressmonitor.pollinterval设置)向应用管理器请求进度更新, 展示给用户。

（6）作业完成

除了向应用管理器请求作业进度外, 客户端每5秒都会通过调用waitForCompletion()来检查作业是否完成。时间间隔可以通过mapreduce.client.completion.pollinterval来设置。作业完成之后, 应用管理器和Container会清理工作状态。作业的信息会被作业历史服务器存储以备之后用户核查。

## 2.3 资源调度器

​		目前，Hadoop作业调度器主要有三种：FIFO、Capacity Scheduler和Fair Scheduler。Hadoop2.7.2默认的资源调度器是Capacity Scheduler。

具体设置详见：yarn-default.xml文件：

```xml
<property>
    <description>The class to use as the resource scheduler.</description>
    <name>yarn.resourcemanager.scheduler.class</name>
<value>org.apache.hadoop.yarn.server.resourcemanager.scheduler.capacity.CapacityScheduler</value>
</property>
```

1、先进先出调度器（FIFO）

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习-YARN.assets\image-20200809103401012.png" alt="image-20200809103401012" style="zoom:80%;" />

2、容量调度器（Capacity Scheduler）

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习-YARN.assets\image-20200809103456432.png" alt="image-20200809103456432" style="zoom:80%;" />

3、公平调度器（Fair Scheduler）

![image-20200809104012668](D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习-YARN.assets\image-20200809104012668.png)

## 2.3 任务的推测执行

1、作业完成时间取决于最慢的任务完成时间

一个作业由若干个Map任务和Reduce任务构成。因硬件老化、软件Bug等，某些任务可能运行非常慢。

思考：系统中有99%的Map任务都完成了，只有少数几个Map老是进度很慢，完不成，怎么办？

2、推测执行机制

发现拖后腿的任务，比如某个任务运行速度远慢于任务平均速度。为拖后腿任务启动一个备份任务，同时运行。谁先运行完，则采用谁的结果。

3、执行推测任务的前提条件

（1）每个Task只能有一个备份任务

（2）当前Job已完成的Task必须不小于0.05（5%）

（3）开启推测执行参数设置。mapred-site.xml文件中默认是打开的。

```xml
<property>
  	<name>mapreduce.map.speculative</name>
  	<value>true</value>
  	<description>If true, then multiple instances of some map tasks may be executed in parallel.</description>
</property>

<property>
  	<name>mapreduce.reduce.speculative</name>
     <value>true</value>
  	<description>If true, then multiple instances of some reduce tasks may be executed in parallel.</description>
</property>
```

4、不能启用推测执行机制情况

  （1）任务间存在严重的负载倾斜；

  （2）特殊任务，比如任务向数据库中写数据。