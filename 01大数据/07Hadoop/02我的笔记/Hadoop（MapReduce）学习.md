

# 1 介绍

​		MapReduce是一个**分布式运算程序的编程框架和模型**，是用户开发“基于Hadoop的数据分析应用”的核心框架。Map（映射）和Reduce（归约）是核心思想。

​		MapReduce核心功能是将**用户编写的业务逻辑代码**和**自带默认组件**整合成一个完整的分布式运算程序，并发运行在一个Hadoop集群上。

## 1、优点

（1）MapReduce易于编程

​		它简单的实现一些接口，就可以完成一个分布式程序，这个分布式程序可以分布到大量廉价的PC机器上运行。

（2）良好的扩展性

​		当你的计算资源不能得到满足的时候，你可以通过简单的增加机器来扩展它的运算能力。

（3）高容错性

​		MapReduce设计的初衷就是将程序可以部署到廉价的PC机器上，这就要求它就有很高的容错性。比如：当一台机器挂掉之后，它上面的计算任务可以转移到另外一个节点上运行，不至于这个任务运行失败。这个过程不需要人工参与，完全由Hadoop内部完成。

（4）适合PB以上海量数据的离线处理

​		可以是自安上千台服务器集群并发工作，提供数据处理能力。

## 2、缺点

（1）不擅长实时计算

​		MapReduce无法像MySql一样，在毫秒或者秒级返回计算结果。

（2）不擅长流式计算

​		流式计算的输入数据是动态的，而MapReduce的输入数据集是静态的，不能动态计算。

（3）不擅长有向图（DAG）计算

​		对于多个应用程序存在依赖关系，后一个程序的输入为前一个程序的输出的情况，MapReduce并不是不能做，而是使用后，每个MapReduce作业的输出结果都会写入磁盘，造成大量的磁盘IO，导致性能低下。

## 3、MapReduce核心思想

<img src="Hadoop（MapReduce）学习.assets\image-20200724104935932.png" alt="image-20200724104935932" style="zoom:80%;" />

（1）分布式的运算程序往往需要分成至少2个阶段。

（2）第一个阶段的MapTask并发实例，完全并行运行，互不相干。

（3）第二个阶段的ReduceTask并发实例互不相干，但是他们的数据依赖于上一个阶段的所有MapTask并发实例的输出。

（4）MapReduce编程模型只能包含一个Map阶段和一个Reduce阶段，如果用户的业务逻辑非常复杂，那就只能多个MapReduce程序，**串行运行**。

## 4、MapReduce进程

<img src="Hadoop（MapReduce）学习.assets\image-20200724105042548.png" alt="image-20200724105042548" style="zoom:80%;" />

## 5、常用数据序列化类型

| Java类型 | Hadoop Writable类型 |
| -------- | ------------------- |
| boolean  | BooleanWritable     |
| byte     | ByteWritable        |
| int      | IntWritable         |
| float    | FloatWritable       |
| long     | LongWritable        |
| double   | DoubleWritable      |
| String   | Text                |
| map      | MapWritable         |
| array    | ArrayWritable       |

## 6、MapReduce编程规范

用户编写的程序分成三个部分：Mapper、Reducer和Driver。

<img src="Hadoop（MapReduce）学习.assets\image-20200724105759260.png" alt="image-20200724105759260" style="zoom:80%;" />

<img src="Hadoop（MapReduce）学习.assets\image-20200724105810836.png" alt="image-20200724105810836" style="zoom:80%;" />

# 2 入门案例

## 1、需求

​		在给定的文本文件中统计输出每一个单词出现的总次数。其中，输入数据为hello.txt文件，输出数据：统计文件中数据出现的次数。

<img src="Hadoop（MapReduce）学习.assets\image-20200724110524528.png" alt="image-20200724110524528" style="zoom:80%;" />

## 2、环境搭建

### 2.1、本地测试

（1）添加依赖

```xml
 <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-core</artifactId>
            <version>2.8.2</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-common</artifactId>
            <version>2.7.2</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.2</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-hdfs</artifactId>
            <version>2.7.2</version>
        </dependency>
    </dependencies>
```

（2）在resources目录下创建log4j.properties文件。

```properties
log4j.rootLogger=INFO, stdout
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d %p [%c] - %m%n
log4j.appender.logfile=org.apache.log4j.FileAppender
log4j.appender.logfile.File=target/spring.log
log4j.appender.logfile.layout=org.apache.log4j.PatternLayout
log4j.appender.logfile.layout.ConversionPattern=%d %p [%c] - %m%n
```

（3）编写Mapper类

```java
/**
 * map阶段
 * arg1：输入数据的key 每一行的起始偏移量类型 long
 * arg2：输入数据的value 每一行内容的类型  String
 * arg1：输出数据的key的类型：（atguigu,1）（每一个单词的类型）String
 * arg1：输出数据的value类型 （次数的类型）int
 * 1、hadoop底层已经帮助我们实现类文件读取的流，并且帮助我们进行文件读取
 * 然后将最终流的读取文件结果传送给Mapper类 map函数
 * 传递读取结果的时候 key value形式传递的。
 * mapreduece框架底层读取数据的时候，底层的流不是字符流，使用的字节流。
 * 2、整个输入和输出过程，经过网络传输和固化磁盘
 * 要求我们所有的输入和输出的类型必须具备序列化和反序列化的能力。
 * 3、在java的序列化反序列化接口Serializable 比较重，序列化和反序列化过程将整个
 * 类结构一边给序列化和反序列化。
 * 4、hadoop抛弃了上者，使用了轻量级的序列化和反序列化的Writable接口。
 * int -> IntWritable
 * short->ShortWritable
 * byte -> ByteWritable
 * long ->LongWritable
 * boolean -> BooleanWritable
 * Float -> FloatWritable
 * Double -> DoubleWritable
 * String -> Text
 */
public class WordcountMapper extends Mapper<LongWritable, Text, Text, IntWritable> {
    Text k = new Text();
    IntWritable v = new IntWritable(1);

     /**
     *
     * @param key LongWritable 每一行的偏移量
     * @param value Text 每一行的内容
     * @param context 对上连接框架流，对下传送给reduce
     * 框架流 -> map -> reduce -> 输出结果
     * 1、map函数：每一行调用一次。
     */
    @Override
    protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
         /**
         * 1、每次到map函数中，都是拿到一行内容
         * （1）获取一行内容
         * （2）切分一个单词
         * （3）发给reduce进行统计   key(单词) value （1）
         * Text, IntWritable 比如：
         * hello 1
         * jack 1
         */
        
        // 1 获取一行（数据模拟：atguigu atguigu）
        String line = value.toString();
        // 2 切割
        String[] words = line.split(" ");
        // 3 输出
        for (String word : words) {
            k.set(word);
            context.write(k, v);
        }
    }
}
```

（2）编写Reducer类

```java
/**
 * 1、map发过来的数据：hello 1
 * 2、arg1和arg2对应mapper阶段输出的key和value
 * reduce输出参数为arg3和arg4，对应的是最终处理结果
 * arg3:reduce输出key类型 指的单词 Text
 * arg4:  value类型       指的单词总次数  IntWritable
 */
public class WordcountReducer extends Reducer<Text, IntWritable, Text, IntWritable> {
    int sum;
    IntWritable v = new IntWritable();
	
     /**
     * 1、map -> 分组 -> reduce
     * 分组：按照map输出的key（单词）进行分组。
     * 2、@param key map输入的key,单词。
     * @param values 迭代器，里面多个元素，每一组中所有的value值（1，1，1）。
     * @param context 对上对map,对下输出
     * 3、一组调用一次
     */
    @Override //汇总统计
    protected void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException {
		
           /**
         * 逻辑
         * 只需要将values中的1累加
         */
        
        // 1 累加求和(数据模拟：atguigu,1)
        sum = 0;
        for (IntWritable count : values) {
            sum += count.get();
        }
        // 2 输出
        v.set(sum);
        context.write(key, v);
    }
}
```

<img src="Hadoop（MapReduce）学习.assets\image-20200727085200570.png" alt="image-20200727085200570" style="zoom:67%;" />

（3）编写Driver驱动类

```java
/**
 * 1、封装main函数的
 * （1）组装mapper reduce
 * （2）提交执行
 */
public class WordcountDriver {
    public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException {
		    /**
         * job
         * 1、在mapreduce中将一个map-reduce封装的一个任务 叫做job
         * 对应的类叫Job
         */
        
        // 1 获取配置信息以及封装任务
        Configuration configuration = new Configuration();
           /**
         * 启动一个job,封装mapper reduce
         */
        Job job = Job.getInstance(configuration);
		    /**
         * 2 设置jar加载路径
         * 设置job的主类入口
         */
        job.setJarByClass(WordcountDriver.class);

        // 3 设置该job对应的map和reduce类（设置map和reduce类）
        job.setMapperClass(WordcountMapper.class);
        job.setReducerClass(WordcountReducer.class);

        // 4 设置map输出
        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(IntWritable.class);

        // 5 设置最终输出kv类型
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);

        // 6 设置输入和输出路径
        FileInputFormat.setInputPaths(job, new Path("D:\\input"));
        FileOutputFormat.setOutputPath(job, new Path("D:\\output"));

        // 7 提交（是否打印日志）
        boolean result = job.waitForCompletion(true);
        System.exit(result ? 0 : 1);
    }
}
```

​		我们可以在`D:\output`文件夹中看到输出的数据。

说明：

​		遇到报错时：将hadoop安装路径下的hadoop.dll文件拷贝到`C:\Windows\System32`路径下（windows环境下解析Linux的组件，需要和D:\03Enviroment\12hadoop\01install\hadoop-2.7.2\bin\winutils.exe联合后一起解析）。

```ini
#hadoop jar jar包路径 需要运行主类的全限定名 程序中的参数（如果有多个空格隔开）该程序中的路劲为：hdfs路径（输入路径  输出路径）
hadoop jar  wc.jar com.xiaolun.mapreduce.WordcountDriver /user/zookeeper/input /user/zookeeper/output
```

### 2.2、集群上测试

1、将本地测试的文件输入路径和上传路径进行更改

```java
#args[0]程序中的参数
FileInputFormat.setInputPaths(job, new Path(args[0]));
FileOutputFormat.setOutputPath(job, new Path(args[1]));
```

2、pom中添加jar包依赖

```xml
<build>
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>2.3.2</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <plugin>
                <artifactId>maven-assembly-plugin </artifactId>
                <configuration>
                    <descriptorRefs>
                        <descriptorRef>jar-with-dependencies</descriptorRef>
                    </descriptorRefs>
                    <archive>
                        <manifest>
    <!--运行时，将下面的内容换成运行主类-->                <mainClass>com.xiaolun.mapreduce.WordcountDriver</mainClass>
                        </manifest>
                    </archive>
                </configuration>
                <executions>
                    <execution>
                        <id>make-assembly</id>
                        <phase>package</phase>
                        <goals>
                            <goal>single</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
```

3、maven中clean -> compile->package,将其打成jar包，然后将没有依赖的jar包拷贝到虚拟环境中，假设名称叫`wc.jar`。

4、在Hadoop启动后，执行wc.jar程序。

```ini
#hadoop jar +jar包名称 +mainClass + 输入路径 + 输出路径
hadoop jar wc.jar com.xiaolun.mapreduce.WordcountDriver /user/zookeeper/input  /user/zookeeper/output
```

注意：为了更好的观察输出结果，首先在`/user/zookeeper/input`路径下放置要测试的文件，路径`/user/zookeeper/output`要事先删除。

该目录下有两个文件_SUCCESS（0kb，标志文件，标志job执行成功）、part-r-00000（最终结果文件）。

## 3、mapreduce的设计

1、map端key是谁，决定reduce中接受的数据是如何的，目前阶段，mapkey的设计的唯一依据是分组。

因此，mapkey**可以通过reduce中的分组**确定，

（1）求每一个部门中的平均年龄

​	map:

​			key：部门（**分组**）

​			value：剩下的要求的字段 年龄

​	reduce：

​		key：部门

​		value：一个部门中的所有年龄。（年龄的累计个数，累计总和）

（2）每一个部门中相同年龄的都有谁

map:

​			key：部门+年龄

​			value：姓名

​	reduce：

相同部门，相同年龄，所有姓名

​		key：部门+年龄

​		value：所有人的姓名

2、mapreduce编程中的分工

​	mapper (无逻辑)

​		**一行数据调用一次**

​		获取一行内容 切分 发送给reduce

​	reduce

​		根据map输过来的数据进行分组和**逻辑运算**。

​		**一组一次**。

### 2.1 mapreduce编程套路

<img src="Hadoop（MapReduce）学习.assets\image-20200727094712973.png" alt="image-20200727094712973" style="zoom:80%;" />

<img src="Hadoop（MapReduce）学习.assets\image-20200727094749680.png" alt="image-20200727094749680" style="zoom:80%;" />

<img src="Hadoop（MapReduce）学习.assets\image-20200727094831205.png" alt="image-20200727094831205" style="zoom:80%;" />

<img src="Hadoop（MapReduce）学习.assets\image-20200727094912826.png" alt="image-20200727094912826" style="zoom:80%;" />

## 4、自定义类

1、数据格式

```
15271868851 1 10 200
15271868852 2 10 200
15271868853 3 10 200
15271868851 1 10 200
15271868852 2 10 200
15271868853 3 10 200
```

2、统计结果

```ini
15271868851	2,20,22
15271868852	4,20,24
15271868853	6,20,26
```

3、自定义类

```java
//实现Writable接口，以便自定义类可以序列化
public class FlowBean implements Writable {
    //上行 下行 总
    private int upflow;
    private int downflown;
    private int sumflow;

    //一定要重写
    //统计输出：15271868853	6,20,26
    public FlowBean() {
    }
    public FlowBean(int upflow, int downflown) {
        this.upflow = upflow;
        this.downflown = downflown;
        this.sumflow = this.upflow + this.downflown;
    }

    public int getUpflow() {
        return upflow;
    }

    public void setUpflow(int upflow) {
        this.upflow = upflow;
    }

    public int getDownflown() {
        return downflown;
    }

    public void setDownflown(int downflown) {
        this.downflown = downflown;
    }

    public int getSumflow() {
        return sumflow;
    }

    public void setSumflow(int sumflow) {
        this.sumflow = sumflow;
    }

    //toString 代表的是map输出 reduce输出，如果不重写这个方法，reduce会输出对象的地址
    @Override
    public String toString() {
        return  upflow + "," +downflown + "," + sumflow ;
    }

    /**
     * 1、序列化  String -> 二进制 参数 输出流
     * 2、hadoop的序列化和反序列化只针对需要的
     */
    @Override
    public void write(DataOutput out) throws IOException {
            //针对属性进行序列化
        out.writeInt(upflow);
        out.writeInt(downflown);
        out.writeInt(sumflow);
    }

    /**
     * 1、反序列化 二进制 -> String 参数 输出流
     * 2、注意反序列化和序列化的顺序要保持一致
     * 先是upflow -》 downflown -》sumflow
     */
    @Override
    public void readFields(DataInput in) throws IOException {
        this.upflow = in.readInt();
        this.downflown = in.readInt();
        this.sumflow = in.readInt();
    }
}
```

4、核心代码

```java
public class FlowCount {
    /**
     * map端
     * key 手机号
     * value 上行 + 下行 + 总流量 自定义类
     * 数据格式：15271868851 1 10 200
     */
    static class FlowMapper extends Mapper<LongWritable, Text, Text, FlowBean> {
        @Override
        protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
            Text mk = new Text();
            String line = value.toString();
            String[] datas = line.split(" ");
            System.out.println("datas.length---->"+datas.length);
            for (String data : datas) {
                System.out.println(data);
            }

            if (datas.length == 4) {
                mk.set(datas[0]);
                int up = Integer.parseInt(datas[datas.length - 3].trim());
                int down = Integer.parseInt(datas[datas.length - 2].trim());
                FlowBean fb = new FlowBean(up, down);
                context.write(mk, fb);
            }
        }
    }

    /**
     * reduce端
     */
    static class FlowReduce extends Reducer<Text, FlowBean, Text, FlowBean> {
        @Override
        protected void reduce(Text key, Iterable<FlowBean> values, Context context) throws IOException, InterruptedException {
            int up_sumflow = 0;
            int down_sumflow = 0;
            for (FlowBean fb : values) {
                up_sumflow += fb.getUpflow();
                down_sumflow += fb.getDownflown();
            }
            FlowBean res_fb = new FlowBean(up_sumflow, down_sumflow);
            context.write(key, res_fb);
        }
    }

    public static void main(String[] args) throws Exception {
        System.setProperty("HADOOP_USER_NAME", "zookeeper");
        Configuration conf = new Configuration();
        conf.set("fs.defaultFS", "hdfs://hadoop101:9000");
        //启动job
        Job job = Job.getInstance(conf);

        //设置jar主类
        job.setJarByClass(FlowCount.class);

        //设置map reduce类
        job.setMapperClass(FlowMapper.class);
        job.setReducerClass(FlowReduce.class);

        //设置map reduce的输出
        /**
         * 如果map输出的k v 和 reduce 输出的k v 一致，只需要设置一个就可以
         */
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(FlowBean.class);

        //输入/出路径
        FileInputFormat.setInputPaths(job, new Path("/flow_in01"));
        FileOutputFormat.setOutputPath(job, new Path("/flow_out01"));

        //提交任务
        job.waitForCompletion(true);
    }
}
```

**总结**

（1）自定义一个类实现writable

（2）重写readFields和write方法

（3）无参构造方法必须提供，toString方法必须重写，反序列化和序列化顺序一定一致。

（4）目前这种形式的自定义类，不能放在map的输出的key的位置，其他位置都可以。

# 3 hadoop序列化

## 1、介绍

![image-20200724154759584](Hadoop（MapReduce）学习.assets\image-20200724154759584.png)

![image-20200724154827642](Hadoop（MapReduce）学习.assets\image-20200724154827642.png)



##  2、切片与MapTask并行度决定机制

​		MapTask的并行度决定Map阶段的任务处理并发度，进而影响到整个Job的处理速度。

1、MapTask并行度决定机制

数据块：Block是HDFS物理上把数据分成一块一块。

数据切片：数据切片只是在逻辑上对输入进行分片，并不会在磁盘上将其切分成片进行存储。

<img src="Hadoop（MapReduce）学习.assets\image-20200724155826935.png" alt="image-20200724155826935" style="zoom:80%;" />

# 4 maptask的并行度

​		mapTask执行mapper端代码的任务。并行度指的是一个job中（一个完整的mapreduce的过程）运行的maptask的个数以及reducetask的个数。

​		maptask(mapper) --> 直接面向的是文件输入（大量的数据），因为map端接收的数据量可能很大，map端的任务需要分布式的运行，这里的maptask的个数指的就是map分布式运行几个任务。

​		map端的任务需要切分几个小任务和切片的大小有关。

​		map端接收的数据就是hdfs上的存储的数据，hdfs数据存储，分块存储，一个块128M，假设mapreduce任务的输入文件为500M，

存储：

blk0 0 -128

blk1 128 -256

blk2 256 -384

blk3 384 -500

​		计算任务和存储相互匹配时比较合理的。一个maptask的计算任务  -> 一个数据块的大小（一个maptask --> 一个逻辑切片的大小）。对于上面的存储，只需要启动4个maptask。

​		逻辑切片：针对计算，为了便于每一个maptask任务的划分，将数据分成不同的区域，仅仅是一个范围的划分。

​		源码实现，切片的大小：mapper代码执行FileInputFormat getSplits  进行逻辑的切片。

​	注意：

（1）一个文件如果不够一个切片的大小，单独成一个切片。

（2）切分过程中，最后一个切片的大小可能会大于128（128*1.1 = 最大140.8M）

比如：140M的数据块只会分配一个maptask。最后一个切片可能跨块，即使这样，也比启动多个maptask划算。

（3）逻辑切片的修改，

方式1：我们需要修改mapred-site.xml文件，当我们想要切块大于128M时，修改xx.split.minsize属性，小于128M，修改xx.split.maxsize属性。不推荐使用，因为它是全局生效的，对所有的job生效。

方式2：代码中修改

```ini
/**
* 调整逻辑切片的大小 100byte
*/
//maxSize
FileInputFormat.setMaxInputSplitSize(job,100);
FileInputFormat.setMinInputSplitSize();
```

# 5 mapreduce的排序

1、mapreduce计算框架中，默认按照map输出的key进行排序

（1）如果map输出的key是字符型类型 Text,按照字符串的字典顺序进行排序。

（2）如果map输出的key是数值类型 IntWritable LongWritable，按照数值的大小进行排序。

（3）自定义类。

## 5.1 案例1

需求：

按照单词出现的词频进行排序（降序），我们可以将单词词频作为map输出的key。

​		map端：

​			key: 次数 IntWritable

​			value:单词

​		shuffle:

​			先按照map key进行排序（默认升序排序）此时我们可以为次数添加负号，再按照map输出的key进行分组。

​		reduce端：

​			将次数相同的单词分到一组中，循环遍历迭代器中的每一个单词，进行输出

​				key：单词（反序）

​				value：词频（添加负号）

1. **mapper类**

```java
public class WordcountMapper extends Mapper<LongWritable, Text, IntWritable, Text> {
    IntWritable mk = new IntWritable();
    Text mv = new Text();

        @Override
    protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
        String line = value.toString();
        //   hello 5
        String[] words = line.split(" ");
        mk.set(-Integer.parseInt(words[1].trim()));  //-5
        mv.set(words[0]); //hello
        context.write(mk, mv);
    }
}
```

2. **reduce类**

```java
public class WordcountReducer extends Reducer<IntWritable, Text, Text, IntWritable> {
    IntWritable v = new IntWritable();

    /**
     * shuffle
     * -5 hadoop
     * -5 hello
     * -10 ww
     *
     * reduce :
     * groud1:
     * -10 ww
     * g-2
     * -5 hadoop
     * -5 hello
     */
    @Override
    protected void reduce(IntWritable key, Iterable<Text> values, Context context) throws IOException, InterruptedException {
        //循环遍历每一个单词
        for (Text w:values){
            v.set(-key.get());  //反序
            context.write(w, v); //每一条数据进行输出一次
        }
    }
}
```

3. 测试类

```java

FileInputFormat.setInputPaths(job, new Path("/flow_in01/test01.txt"));
Path out = new Path("/flow_out02");
FileSystem fileSystem = FileSystem.get(configuration);
if(fileSystem.exists(out)){
fileSystem.delete(out, true);
}
FileOutputFormat.setOutputPath(job, out);
```



## 5.2 自定义排序

​		mapreduce编程中，只要放在map输出的key的位置的类型，都必须具备序列胡能力和比较能力。下面的过程又叫做mapreduce的二次排序。

需求：将统计结果先按照总流量降序排序，再按照手机号升序排序。

文件类型：

```ini
#手机号 总流量
15271868852	4,20,24
15271868851	2,20,22
```

分析：

要求排序的字段：总流量+手机号

map端：

​	key: 总流量 + 手机号 上行 下行 自定义类（需要实现WritableComparable）

​			排序规则：先按总流量降序  再手机号升序

​	value: null

shuffle:

​	按照定义的规则排序 

reduce端：

​	输出结果

1. **自定义类**

```java
public class FlowBeanSort implements WritableComparable<FlowBeanSort> {
    //手机号 上行 下行 总
    private String phonenum;
    private int upflow;
    private int downflown;
    private int sumflow;

    //一定要重写
    //统计输出：15271868853 6,20,26
    public FlowBeanSort() {
    }
    public FlowBeanSort(String phonenum,int upflow, int downflown,int sumflow) {
        this.phonenum = phonenum;
        this.upflow = upflow;
        this.downflown = downflown;
        this.sumflow = sumflow;
    }

	//set/get方法

    //toString 代表的是map输出 reduce输出，如果不重写这个方法，reduce会输出对象的地址
    @Override
    public String toString() {
        return  phonenum + "," + upflow + "," +downflown + "," + sumflow ;
    }

    /**
     * 1、序列化  String -> 二进制 参数 输出流
     * 2、hadoop的序列化和反序列化只针对需要的
     */
    @Override
    public void write(DataOutput out) throws IOException {
            //针对属性进行序列化
        out.writeUTF(phonenum);
        out.writeInt(upflow);
        out.writeInt(downflown);
        out.writeInt(sumflow);
    }

    /**
     * 1、反序列化 二进制 -> String 参数 输出流
     * 2、注意反序列化和序列化的顺序要保持一致
     * 先是upflow -》 downflown -》sumflow
     */
    @Override
    public void readFields(DataInput in) throws IOException {
        this.phonenum = in.readUTF();
        this.upflow = in.readInt();
        this.downflown = in.readInt();
        this.sumflow = in.readInt();
    }

    /**
     * 比较规则
     * @param o 用于比较的对象
     * 和 this(本对象) 比较
     */
    @Override
    public int compareTo(FlowBeanSort o) {
        //先按照总流量，降序
        int tmp = o.getSumflow() - this.getSumflow();
        if(tmp == 0){
            //比较手机号
            tmp = this.getPhonenum().compareTo(o.getPhonenum());
        }
        return tmp;
    }
}
```

2. mapper类

```java
 static class FlowMapper extends Mapper<LongWritable, Text, FlowBeanSort, NullWritable> {
        FlowBeanSort fsb = new FlowBeanSort();
        @Override
        protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
            Text mk = new Text();
            String line = value.toString();
            String[] datas = line.split("\t");
            String[] flows = datas[1].split(",");
            fsb.setPhonenum(datas[0]);
            fsb.setUpflow(Integer.parseInt(flows[0]));
            fsb.setDownflown(Integer.parseInt(flows[1]));
            fsb.setSumflow(Integer.parseInt(flows[2]));
            context.write(fsb,NullWritable.get());
        }
    }
```

3. reduce类

```java
  /**
     * shuffle：按照定义的规则 总流量降序 + 手机号升序
     * reduce端
     * 流量和手机号 相同的分到一组中
     */
    static class FlowReduce extends Reducer<FlowBeanSort, NullWritable, FlowBeanSort, NullWritable> {
        @Override
        protected void reduce(FlowBeanSort key, Iterable<NullWritable> values, Context context) throws IOException, InterruptedException {
            context.write(key, NullWritable.get());
        }
    }
```

4. 测试类

```java
//设置map reduce的输出
/**
* 如果map输出的k v 和 reduce 输出的k v 一致，只需要设置一个就可以
*/
job.setOutputKeyClass(FlowBeanSort.class);
job.setOutputValueClass(NullWritable.class);
```

文件输出：

```ini
15271868853,6,20,26
15271868852,4,20,24
15271868851,2,20,22
```

# 6 分区讲解

<img src="Hadoop（MapReduce）学习.assets\image-20200727230211071.png" alt="image-20200727230211071" style="zoom:80%;" />

1、 引入reducetask的并行度。

2、maptask的数据最终输入到reducetask中，reducetask最终要接收所有的maptask中的数据。此时，reducetask不能只运行一个任务，压力很大，reducetask这一端也会进行分布式计算，将一个大的reducertask任务分割成每一个小任务-reducetask，一旦reducetask有多个，maptask的输出数据如何分配给reducetask呢？为了解决这个问题，在shuffle过程中将maptask输出的数据分成不同的数据区域，每一个区域对应一个reducetask任务，此时，每一个区域，就叫做一个分区。

​	分区是为reducetask准备任务的，有几个reducetask就有几个分区。

3、每一个分区中数据的特点

（1）不同分区中的数据，不存在重复的。

（2）相同maptask的数据在一个分区中，（同一个分组的数据到一个分区中）。

4、实现方式

（1）mapkey.hash % 分区个数

（2）默认情况下，通过文件中属性：mapreduce.job.reduces可以看到，分区为一个。我们可以通过更改配置文件来更改分区个数，不推荐，可以使用下面改动代码的方式来改动代码：

```java
//设置分区分数，参数=个数 
job.setNumReduceTasks(task);
```

（3）文件命名解释

part0-----reducetask0-----------part-r-00000

part1-----reducetask1-----------part-r-00001

part2-----reducetask2-----------part-r-00002

part：分区；r：reducetask；00000：编号。

同时，分区编号和reducetask编号一一对应。

5、底层分区实现

Partitioner  -》 mapkey.hash % redcuetask个数。

<img src="Hadoop（MapReduce）学习.assets\image-20200727231746339.png" alt="image-20200727231746339" style="zoom:80%;" />

<img src="Hadoop（MapReduce）学习.assets\image-20200727231824184.png" alt="image-20200727231824184" style="zoom:80%;" />

## 6.1 自定义分区

分区只能按照map输出的key。

1、自定义分区算法

（1）继承patitioner

（2）重写getPartition,实现核心代码。

（3）设置reducetask的个数

```java
 job.setNumReduceTasks(2);
```

（4）设置分区算法

```java
 job.setPartitionerClass(FlowPartitioner.class);
```

### 6.1.1 案例

#### 1、需求

根据手机归属地不同，返回不同的分区编号。

人为规定：

​	134-136 北京

​	137-138 深圳

​	139 武汉

此时将有三个分区。

map:

​	key: 手机号

​	value:其他

shuffle:

​	自定义分区

reduce:

​	有几个分区，就启动几个reducetask任务。

#### 2、创建项目

1. 自定义分区

```java
public class FlowPartitioner extends Partitioner<Text, Text> {

    @Override
    public int getPartition(Text key, Text value, int numPartitions) {
        String prefix = key.toString().substring(0, 3);
        int _prefix = Integer.parseInt(prefix);

        //返回分区
        if (_prefix >= 134 && _prefix <= 136){
            return 0;
        }else {
            return 1;
        }
    }
}
```

2. mapper类

```java
static class FlowMapper extends Mapper<LongWritable, Text,Text,Text>{
        /**
         * 15271868851	2,20,22
         */
        @Override
        protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
            String[] datas = value.toString().split("\t");
            Text mk = new Text();
            Text mv = new Text();
            mk.set(datas[0]);
            mv.set(datas[1]);
            context.write(mk, mv);
        }
```

3. reduce类

```java
static class FlowReducer extends Reducer<Text,Text,Text,Text>{
        @Override
        protected void reduce(Text key, Iterable<Text> values, Context context) throws IOException, InterruptedException {
            for (Text value : values) {
                /**
                 * key=13071868853,value=6,20,26
                 * key=15271868851,value=2,20,22
                 */
                System.out.println("key="+key+",value="+value);
                context.write(key, value);
                System.out.println("------------------------");
            }
        }
    }
```

4. 测试类

```java
//设置reducetask的个数
job.setNumReduceTasks(2);
//设置分区算法
job.setPartitionerClass(FlowPartitioner.class);
```

# 7 分组

1、整个shuffle的最后一个阶段，这个阶段的结果数据，会给reduce。

2、默认情况下的分组：按照map输出的key进行分组的，将map相同的放到一组中。

3、底层分组的默认实现，对排完序的mapkey进行对比，关心的是否相同（只关心compare == 0），相同的就是一组，反之不是一组。

4、map输出的数据 key- value,reduce端接收的数据，仍然是键值对。每当循环遍历一个value的时候，value的指针发生变化指向下一个，此时，key的指针也执行下一个。

<img src="Hadoop（MapReduce）学习.assets\image-20200728103540498.png" alt="image-20200728103540498" style="zoom:80%;" />这是mapreduce中的对象重用问题，在reduce端key value底层对应了两个对象。

## 7.1 自定义分组

### 1、需求

求学生成绩中每个科目平均分最高的前两个。

### 2、分析

排序：按照平均分,map

分组：按照科目进行分组，map输出的key

map端：

​	key：平均分+科目 自定义类

​	value：null

shuffle：

​	排序：自定义类（comparaTo），平均分降序排列。

​	分组：自定义分组中，compare 科目 （只关心返回值为0 ）。

reduce端：

​	理想状态 接收的数据

​	相同科目的所有数据，按照平均分降序排序

​	参数：

​		key：自定义类 一组中的第一个

​		values：null

​		循环遍历values 输出2个key。

### 3、创建项目

1. 自定义类（排序）

```java
public class ScoreBean implements WritableComparable<ScoreBean> {
    //课程
    private String course;
    //姓名
    private String name;
    //平均成绩
    private Double avg;

    public ScoreBean() {
    }

    public ScoreBean(String course, String name, Double avg) {
        this.course = course;
        this.name = name;
        this.avg = avg;
    }

    @Override
    public String toString() {
        return course + "\t" + name + "\t" + avg;
    }


    @Override
    public void write(DataOutput out) throws IOException {
        out.writeUTF(course);
        out.writeUTF(name);
        out.writeDouble(avg);
    }

    @Override
    public void readFields(DataInput in) throws IOException {
        this.course = in.readUTF();
        this.name = in.readUTF();
        this.avg = in.readDouble();
    }

    //按照平均分排，降序
    @Override
    public int compareTo(ScoreBean o) {
        int tmp = this.getCourse().compareTo(o.getCourse());
        if (tmp == 0){
            double tmp1 = o.getAvg() - this.getAvg();
            if (tmp1 ==0){
                return 0;
            }else if(tmp1 > 0){
                return 1;
            }else {
                return -1;
            }
        }
        return tmp;
    }
}
```

2. 自定义分组

```java
//自定义分组
public class CourseGroup extends WritableComparator {

    /**
     * 自定义构造方法，调用父类构造方法，第三个参数为true
     * 不然会有空指针异常的错误
     */
    public CourseGroup(){
        super(ScoreBean.class,true);
    }

    @Override
    public int compare(WritableComparable a, WritableComparable b) {
        //按照科目
        //将参数强转为本身的类型
        ScoreBean a1 = (ScoreBean)a;
        ScoreBean b1 = (ScoreBean)b;
        return a1.getCourse().compareTo(b1.getCourse());
    }
}
```

3. mapper类

```java
static class CourseTopNMapper extends Mapper<LongWritable, Text, ScoreBean, NullWritable>{
        ScoreBean sb = new ScoreBean();
        @Override
        protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
            //math,xiaohei,81,82
            String[] datas = value.toString().split(",");
            int sum=0;
            for (int i = 2;i < datas.length;i++){
                sum += Integer.parseInt(datas[i].trim());
            }
            double _avg = sum*1.0/(datas.length - 2);
            DecimalFormat df = new DecimalFormat("#.00");
            double avg = Double.parseDouble(df.format(_avg));

            sb.setCourse(datas[0]);
            sb.setName(datas[1]);
            sb.setAvg(avg);
            context.write(sb,NullWritable.get());
        }
```

4. reduce类

```java
static class CourseTopNReduce extends Reducer<ScoreBean, NullWritable, ScoreBean, NullWritable>{
    @Override
    protected void reduce(ScoreBean key, Iterable<NullWritable> values, Context context) throws IOException, InterruptedException {
        int i = 0;
        for (NullWritable value : values) {
            i++;
            context.write(key, NullWritable.get());
            if (i == 2){
                break;
            }
        }
    }
}
```

5. 测试类

```java
 //指定分组
job.setGroupingComparatorClass(CourseGroup.class);
```

文件输出：

```ini
#输入
eglish,xiaohei,81,70
alg,xiaohei,81,50
math,xiaohei,81,40
eglish,xiaohei,81,70
alg,xiaohei,81,80
math,xiaohei,81,82
math,xiaohei,81,93
eglish,xiaohei,81,94


#排序后输出
alg	xiaohei	80.5
alg	xiaohei	65.5
eglish	xiaohei	87.5
eglish	xiaohei	75.5
math	xiaohei	87.0
math	xiaohei	81.5
```

说明：

1、自定义分组写法

（1）定义一个类继承`WritableComparator`

（2）重写`compare(WritableComparable a, WritableComparable b)`方法

重写构造方法，调用父类的2个参数，参数2为true。

（3）修正排序，将分组字段放在排序的最前面。

（4）在job中指定分组

```java
job.setGroupingComparatorClass(CourseGroup.class);
```

2、相关介绍

（1）分组发生在排序之后，分组是在排序的结果上进行的，分组中只会比较排序结果相邻的对象的分组字段。

（2）

```
math	xiaohei	87.0
alg	xiaohei	80.5
alg	xiaohei	65.5
eglish	xiaohei	87.5
eglish	xiaohei	75.5
```

修正

在排序阶段，将项目相同的先排在一起，在原来只按照平均分排序之前，先按照科目（分组字段）进行排序。

# 8 combiner组件

1、局部聚合的组件，在mapreduce中默认没有。

2、这个组件是优化组件，不会影响map和reduce中的执行逻辑。作用：对每一个maptask的数据结果做聚合，然后将聚合的结果发送给reduce；目的：减少shuffle过程中的数据量，进而减少reducetask接收的数据量，提升效率，在一定程度上减少数据倾斜（combiner帮助reducetask减少数据量，帮他提前做一部分工作）。

数据倾斜：mapreduce进行分布式计算的时候，由于数据分布均匀，造成某一个task任务中的数据量很大，其他的task中的数据量相对平均。

reducetask相对于maptask来说，容易产生数据倾斜。

3、combiner中的逻辑和reduce中的逻辑是一样。

4、实现方法

（1）定义一个类，继承Reducer

（2）重写reduce函数

mapper --- combiner --- reducer

前两个泛型----map输出的k,v

后两个泛型-----reduce输入的k,v

（3）在job中指定

```java
job.setCombinerClass(xx.class)
```

5、combiner适用的场景是最大值、最小值、求和，不适用场景为：求平均值。

（1）maptask0  2,3

（2）maptask1 3,2,7

正确：17/5=3.4

combiner：

​		maptask0 2.5

​		maptask1 4

​		最终结果：(2.5 + 4)/2 = 3.25

求平均值的方案：在combiner中累计求总和和总次数，在reducer端求最终平均。

## 8.1 测试

### 1、需求

使用combiner优化wordcount程序。

### 2、创建项目

1. 创建combiner类

```java
public class MyCombiner extends Reducer<Text, IntWritable, Text, IntWritable> {
    /**
     * 1、一个combiner对应的是一个maptask的输出结果
     * arg1:每一个maptask的一组key
     * arg2:每一个maptask 一组所有value 相同单词的所有value
     * 2、调用频率
     * 每一个maptask调用一次
     */
    int sum;
    IntWritable mv = new IntWritable();

    @Override
    protected void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException {
        sum = 0;
        for (IntWritable count : values) {
            sum += count.get();
        }
        mv.set(sum);
        context.write(key, mv);
    }
}
```

2. 指定combiner类

```java
job.setCombinerClass(MyCombiner.class);
```