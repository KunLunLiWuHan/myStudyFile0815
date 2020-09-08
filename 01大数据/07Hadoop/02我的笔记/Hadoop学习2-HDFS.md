# 1 介绍

1、产生背景

随着数据量的越来越大，一个操作系统中存不下所有的数据，需要分配到更多的操作系统管理的磁盘上，但是不方便管理和维护，此时，我们可以使用一种分布式文件管理系统（HDFS，HDFS只是分布式管理系统中的一种）来管理多台机器上的文件。

2、定义

HDFS（Hadoop Distributed File System）是一个系统，用于存储文件（海量数据存储的），通过目录树来定位文件，其次，它也是分布式的，由很多服务器联合起来实现其功能，集群中的服务器各有各的角色。

HDFS使用场景：适合一次写入，多次读出的场景，且不支持文件的修改。

3、HDFS的设计思想

（1）分而治之

对很大的文件进行分块存储，这个时候需要切分标准。假如切分输入的数据块太大，容易造成集群中节点的存储`datanode`的负载不均衡；反之，会造成一个文件切过多的块，造成`namenode`的压力过大。

总之，hdfs进行文件存储的时候，将文件进行切块的时候，默认每一个块大小为128M。假设一个文件为400M,切块为：

​	0-128M blk01

   128-256M blk02

​	256-384M blk03

​	384-400M blk04 （独立成一个块，不会和其他的文件的数据块合并）

假设在上面的基础上，又存储一个文件10M（即使不够128），该文件会单独一个块。

（2）备份冗余存储

解决数据丢失的问题。用空间去换取数据安全。在HDFS中没哟个数据块都会备份存储。

其中数据中副本都是同等地位的，没有优先级，互为副本。对外提供服务的时候，那个副本处于空闲就去提供。

一个节点上可以存储多个数据块，但是同一个节点上没有相同的数据块。

问题1：（真实副本 < 设置值）

假设集群3个节点，副本配置2个，有一个块的一个节点宕机了，会造成这个块的副本数低于配置的值，这个时候数据块会复制出来这个块的一个副本到另一个节点上。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200725223842097.png" alt="image-20200725223842097" style="zoom:80%;" />

问题2：（集群节点 < 副本）

假设集群3个节点，副本配置4个，此时会存储3份，另外一份namenode会进行记账，当集群中添加节点的时候，会将这个欠的副本复制出来，假设有一个块的一个节点宕机了，此时副本不会拷贝出来。

问题3：（真实副本 > 设置值）

假设集群3个节点，副本配置2个，集群中某一个节点宕机，这个节点存储的所有副本会拷贝一份，过了一段时间之后，这个节点活了，会造成这个节点上存储的数据块的副本多了（真实的副本>设置的副本个数），等待1h左右将多余的副本删除。

3、优点

（1）高容错性

数据自动保存多个副本，通过多个副本提高容错性，某一个副本丢失后，会自动恢复。

（2）适合处理大数据

能够处理百万规模以上的文件数量，数量相当之大。

（3）可搭建在廉价机器上。

（4）高可用性

7*24连续使用。

4、缺点

（1）不适合低延时的数据访问，比如毫秒级的存储数据，无法实现。

（2）无法高效地对大量小文件进行存储。

一个数据块对应一条元数据（150byte），存储小文件会大量占用NameNode的内存来存储文件的目录和块信息。此外，寻址时间远远大于读取数据的时间，不划算。

（3）不支持并发写入，文件随即修改。仅支持数据追加（append）（虽然支持文件追加，但是不建议使用），不支持文件的随机修改（一次写入，多次读取）。

5、HDFS组成架构

![image-20200723171515264](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200723171515264.png)

（1）NameNode（nn）:就是Master，它是一个主管，管理者。

​		主要功能是存储元数据信息，所谓元数据就是：用于描述原始数据的数据，如下所示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200725224442882.png" alt="image-20200725224442882" style="zoom:67%;" />

所以，这里的元数据就是指描述datanode上存储的真实的数据的数据（**存储数据块的对应关系**）。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200725225058681.png" alt="image-20200725225058681" style="zoom:67%;" />

里面的fsimage是核心的元数据信息。

namenode记录的元数据，包含3部分的内容：

（1）抽象目录树（目录结构），该目录树不属于任何一个具体节点，假设集群中有三个从节点，/指的是3个节点中共同组成的一个存储系统的抽象/目录。

通过该目录树，我们可以查看文件系统中存储的文件内容。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200725230114892.png" alt="image-20200725230114892" style="zoom: 67%;" />

（2）目录树中的文件（下面的文件会被切成两个块）和数据块的对应关系。

（存储文件：/jdk.tar.gz  180M）

​	0-128M   blk_1073741825

​	128-180M blk_1073741826

说明：在hadoop集群中，每一个数据块都会有一个唯一编号，全局唯一。

（3）数据块的存储位置（数据块和存储节点的对应关系）

​	0-128M   blk_1073741825    [hadoop101,hadoop103]

​	128-180M blk_1073741826 

另一个功能就是接收客户端的读写请求，所谓客户端**，就是客户操作的窗口**。即客户端所有的读写请求，先经过namenode。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200725231500642.png" alt="image-20200725231500642" style="zoom:80%;" />

概述如下：

+ 管理HDFS的名称空间。
+ 配置副本策略。
+ 管理数据块（Block）映射信息。
+ 处理客户端读写请求。

（2）DataNode：就是Slave。NameNode下达命令，DataNode执行实际的操作。

+ 存储实际的数据块。
+ 执行数据块的读写操作。

（3）Client：客户端。

+ 文件切分。文件上传HDFS时，Client将文件切分成一个一个的Block,然后进行上传。
+ 与NameNode交互，获取文件的位置信息。
+ 与DateNode交互，读取或者写入数据。
+ Client提供了一些命令来管理HDFS，比如NameNode格式化。
+ Client可以通过一些命令来访问HDFS，比如对HDFS增删改查操作。

（4）Secondary NameNode：并非NameNode的热备。当NameNode挂掉的时候，不是直接的马上替换NameNode提供服务。

+ 辅助NameNode，分担其工作量，比如定期合并Fsimage和Edits，并推送到NameNode。
+ 在紧急的情况下，可辅助恢复NameNode。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200726101002045.png" alt="image-20200726101002045" style="zoom: 67%;" />

6、HDFS文件块大小

​		HDFS中的文件在物理上是分块存储（Block）的，块的大小可以通过配置参数（dfs.blocksize）来规定，默认大小在Hadoop2.x版本中是128M，老版本为64M。HDFS块的大小设置主要取决于磁盘传输速率。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200723175539238.png" alt="image-20200723175539238" style="zoom:80%;" />

HDFS的块设置太小，会增加寻址时间，程序一直在找块的开始位置；反之，程序处理数据变慢。

# 2 HDFS的Shell操作

1、基本语法

```ini
#这个路径下：使用（hadoop fs 具体命令）也可以
[zookeeper@hadoop101 hadoop-2.7.2]$ bin/hadoop fs 具体命令   

#dfs是fs的实现类
bin/hdfs dfs 具体命令
```

其中hadoop是启动hadoop的客户端，然后连接hadop集群。fs表示打开hadoop的（文件系统）filesystem的客户端。

2、具体命令

（1）-help：帮助

```ini
hadoop fs -help rm
```

（2）-ls：显示目录信息

```ini
hadoop fs -ls /
```

（3）-cat：显示文件内容

```ini
hadoop fs -cat /xiaolun/kongming.txt
```

（4）-du：统计文件夹的大小信息

```ini
hadoop fs -du -s -h /xiaolun/test
```

（5）-mkdir：在HDFS上创建目录（文件）

```ini
hadoop fs -mkdir -p /xiaolun/test
```

（6）-rm：删除文件或者文件夹

```ini
hadoop fs -rm /xiaolun/test.txt

#-rmdir：删除空目录
hadoop fs -rmdir /test
```

（7）-copyFromLocal：从本地文件系统中拷贝文件到HDFS路径去

```ini
hadoop fs -copyFromLocal test.txt /xiaolun

#-copyToLocal：从HDFS拷贝到本地
hadoop fs -copyToLocal  /xiaolun/1.txt  ./

#-cp ：从HDFS的一个路径拷贝到HDFS的另一个路径
hadoop fs -cp /xiaolun/kongming.txt  /xiaolun/test

#-moveFromLocal：从本地剪切粘贴到HDFS
hadoop fs  -moveFromLocal  ./test.txt  /xiaolun
```

（8）-mv：在HDFS目录中移动文件

```ini
hadoop fs -mv  /test.txt   /xiaolun
```

（9）-appendToFile：（HDFS层面）追加一个文件到已经存在的文件末尾

```ini
hadoop fs -appendToFile   1.txt      /xiaolun/2.txt
```

（10）-tail：显示一个文件的末尾

```ini
hadoop fs -tail /xiaolun/test.txt
```

（11）-get：等同于copyToLocal，就是从HDFS下载文件到本地

```ini
hadoop fs -get  /xiaolun/1.txt  ./
```

（12）-getmerge：合并下载多个文件

```ini
hadoop fs -getmerge /user/atguigu/test/* ./zaiyiqi.txt
```

（13）-put：等同于copyFromLocal（推荐）

​		在hdfs中不能先创建一个空文件，再进行编辑，而是在本地编辑完成文件，然后上传到hdfs中进行存储。

注意：文件上传的时候，如果给定的是hdfs的目录，将本地文件传到hdfs的给定目录下；如果给的是一个hdfs不存在的文件/目录，将本地文件上传到hdfs后，会重命名为给定的文件名。比如：下面的文件就是将test.txt文件上传后以xiaolun（该文件或者目录不存在的情况下）命令。

```ini
hadoop fs -put test.txt /xiaolun
```

（14）-setrep：设置HDFS中文件的副本数量

​		配置副本个数的加载顺序：

​		hdfs-default 3 -> hdfs-site 2 -> setrep  10，最后加载的最终生效。

```ini
hadoop fs -setrep 10 /xiaolun/kongming.txt
```

​		这里设置的副本数只是记录在NameNode的元数据中，是否真的会有这么多副本，还得看DataNode的数量。因为目前只有3台设备，最多也就3个副本，只有节点数的增加到10台时，副本数才能达到10。

说明：

（1）使用`-touch`命令创建空文件（0kb）的时候且不进行实际存储的，此时，只在namenode中进行元数据存储。

（2）hdfs的所有路径权限定名：

​		namenode的访问路径+需要访问的路径。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200726105415698.png" alt="image-20200726105415698" style="zoom:67%;" />

因此，当我们使用下面的命令进行查看目录，有这样的情况

```ini
#  其中的“/” 等价于 hdfs://hadoop101:9000/，下面的是简写。
hadoop fs -ls /
```

（3）hdfs有两个端口

9000：是namenode和datanode通信访问的内部接口（rpc协议）。

50070：是客户端访问web界面的http协议端口。

# 3 HDFS客户端操作

1、环境准备

（1）将windows下的hadoop的jar包（hadoop-2.7.2）拷贝到指定环境中。

```ini
D:\03Enviroment\12hadoop\01install\hadoop-2.7.2
```

（2）配置环境变量

```ini
#HADOOP_HOME
D:\03Enviroment\12hadoop\01install\hadoop-2.7.2

#Path路径下添加
%HADOOP_HOME%\bin
```

2、测试连接

（1）导入依赖

```xml
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
		<!--hadoop客户端-->
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
        <dependency>
            <groupId>jdk.tools</groupId>
            <artifactId>jdk.tools</artifactId>
            <version>1.8</version>
            <scope>system</scope>
            <!--换成绝对路径-->
            <systemPath>D:/03Enviroment/0Java/01install/01jdk1.8/lib/tools.jar</systemPath>
        </dependency>
```

maven创建工程优缺点：

优点：工程比较繁琐时，可以解决jar包冲突的问题。

缺点：代码移动的时候，需要重新构建依赖。

（2）在resources目录下创建log4j.properties

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

（3）测试

```java
public class HdfsClient {
    @Test
    public void testMkdirs() throws Exception{
       /**
         * 1 获取HDFS客户端对象（获取文件系统）
         * 2、该Configuration对象，就是加载hadoop的配置文件。
         * 这个对象获取的配置文件不是Linux中集群的，事实上加载的是hdfs-default.xml（依赖的jar包中）
         * 位置：D:\03Enviroment\04maven\apache-maven-3.6.3\maven_repo\org\apache\hadoop
         *                  \hadoop-hdfs\2.7.2\hadoop-hdfs-2.7.2.jar!\hdfs-default.xml
         * 3、我们可以在代码中进行修改：
         * configuration.set("dfs.replication", "2");
         * 4、configuration对象加载配置文件对象顺序：
         * jar包中hdfs-default.xml -> src中hdfs-site.xml->加载代码中的
         */
        Configuration configuration = new Configuration();
        //使用addResource()方法加载resources目录下的配置文件，优先级别与加载代码中的一致
//        configuration.addResource("config/hdfs-default.xml");

        /**
         * 1、fs对象是hdfs的文件系统的抽象目录树的入口
         * 等价于（hadooop fs）
         * 2、获取hdfs文件系统入口（namenode）
         * 3、FileSystem是hdfs的文件系统类
         * 4、配置在集群上运行（访问地址,配置,用户）
         */
        FileSystem fs = FileSystem.get(new URI("hdfs://hadoop101:9000"), configuration, "zookeeper");

        // 2 在HDFS中创建路径（创建目录）
        fs.mkdirs(new Path("/1108/daxian/banzhang2"));

        // 3 关闭资源
        fs.close();
        System.out.println("------------执行完毕-------");
    }
}
```

文件目录结构（主要用于说明加载配置文件的位置）：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200726155317220.png" alt="image-20200726155317220" style="zoom:80%;" />

在网址：http://hadoop101:50070/下有文件被创建：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200723203001659.png" alt="image-20200723203001659" style="zoom:80%;" />

# 3 API操作

1、文件上传

```java
/**
 * 1、上传文件本地磁盘文件到集群
 * 2、下面文件路径两种书写方式都可以上传到HDFS中
 * 反斜杠D:/03Enviroment/12hadoop/02doc/test01.txt
 * D:\03Enviroment\12hadoop\02doc\test01.txt
 * 3、本地磁盘的文件test01.txt上传后会被该重新命名为test2.txt,当HDFS上路径设置为"/user/"时
 * 该文件会被放到该路径下
 */
fs.copyFromLocalFile(new Path("D:\\03Enviroment\\12hadoop\\02doc\\test01.txt"), new Path("/test2.txt"));
```

（1）参数优先级说明

首先，将hdfs-site.xml拷贝到resources目录下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
	<property>
		<name>dfs.replication</name>
        <value>1</value>
	</property>
</configuration>
```

参数优先级如下：

客户端代码中设置的值 >ClassPath下的用户自定义配置文件（类似上文配置的hdfs-site.xml文件） >服务器的默认配置

2、文件下载

```java
/**
 * 将HDFS中的文件(可以是文件也可以是文件夹)下载到本地
 * boolean delSrc 指是否将原文件删除
 * Path src 指要下载的文件路径
 * Path dst 指将文件下载到的路径
 * boolean useRawLocalFileSystem 是否开启文件校验
 */
fs.copyToLocalFile(true,new Path("/test2.txt"), new Path("C:\\Users\\34938\\Desktop"),true);
```

3、文件删除

```java
 /**
         * 1、将HDFS中的文件(可以是文件也可以是文件夹)下载到本地
         * arg1:boolean delSrc 指是否将原文件删除
         * arg2:Path src 指要下载的文件路径
         * arg3:Path dst 指将文件下载到的路径
         * arg4:boolean useRawLocalFileSystem 是否开启文件crc校验，主要校验文件的数据块是否损坏
         * 如果一个文件的一个数据块损坏，下载会失败。
         * 2、如何判断文件损坏 和 文件上传的时候进行比较
         * 文件上传的后,有两个文件：blk_xx1（原始文件），blk_xx1_1029.meta （原始文件块的元数据）
         * 其中，块的元数据的目的是为了数据块的校验
         * 校验过程：
         * （1）多个副本中只要有一个是正常的，就可以下载成功，因为hadoop会从正常的数据块中下载。
         *      一旦发现有损坏的块的时候，立即复制正确的块，并将损坏的块删除。
         * （2）进行在每一个块的副本的，同一行末尾追加的时候，校验不通过（123xx）;
         *      但在末尾的下一行追加，可以正常下载
         *         123
         *         xx
         *      说明校验的范围是：只会检验从起始行到上传末尾行之间的数据。
         */
fs.delete(new Path("/user/dir1"), true);
```

4、HDFS中文件名更改

```ini
// 修改文件名称
fs.rename(new Path("/user/dir1/dir2/test2.txt"), new Path("/user/dir1/dir2/test1.txt"));
```

5、HDFS文件详情查看

主要是查看文件名称、权限、长度、块信息。

```java
// 获取文件详情
RemoteIterator<LocatedFileStatus> listFiles = fs.listFiles(new Path("/"), true);
//遍历输出
while (listFiles.hasNext()) {
    LocatedFileStatus status = listFiles.next();
    // 输出详情：文件名称
    System.out.println(status.getPath().getName());
    // 长度
    System.out.println(status.getLen());
    // 权限
    System.out.println(status.getPermission());
    // 分组
    System.out.println(status.getGroup());

    // 获取存储的块信息
    BlockLocation[] blockLocations = status.getBlockLocations();
    for (BlockLocation blockLocation : blockLocations) {

        // 获取块存储的主机节点
        String[] hosts = blockLocation.getHosts();
        for (String host : hosts) {
            System.out.println(host);
        }
    }
}
```

6、HDFS文件和文件夹判断

```java
FileStatus[] listStatus = fs.listStatus(new Path("/"));
for (FileStatus fileStatus : listStatus) {
    // 如果是文件
    if (fileStatus.isFile()) {
        System.out.println("文件名：" + fileStatus.getPath().getName());
    } else {
        System.out.println("文件夹：" + fileStatus.getPath().getName());
    }
}
```

# 4 HDFS的数据流

## 4.1 HDFS写数据流程

### 1、剖析文件写入

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200723213131371.png" alt="image-20200723213131371" style="zoom:80%;" />

1、客户端通过`Distributed FileSystem`模块向`NameNode`请求上传文件，`NameNode`检查目标文件是否已存在，父目录是否存在。

2、`NameNode`返回是否可以上传。

3、客户端请求第一个 `Block`上传到哪几个`DataNode`服务器上。

4、`NameNode`返回3个`DataNode`节点，分别为`dn1`、`dn2`、`dn3`。

5、客户端通过`FSDataOutputStream`模块请求`dn1`上传数据，`dn1`收到请求会继续调用`dn2`，然后`dn2`调用`dn3`，将这个通信管道建立完成。

6、`dn1、dn2、dn3`逐级应答客户端。

7、客户端开始往`dn1`上传第一个`Block`（先从磁盘读取数据放到一个本地内存缓存），以`Packet`为单位（64KB），`dn1`收到一个`Packet`就会传给`dn2`，`dn2`传给`dn3`；`dn1`每传一个`packet`会放入一个应答队列等待应答。

8、当一个`Block`传输完成之后，客户端再次请求`NameNode`上传第二个`Block`的服务器。（重复执行3-7步）。

#### 1.1、文件上传流程

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200726182636114.png" alt="image-20200726182636114" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200726182659673.png" alt="image-20200726182659673" style="zoom:80%;" />

1、文件上传中的问题

（1）进行数据过程中或者构建pipline过程中，出现某一个datanode宕机了，需要立即重试一次，仍然通信有问题，就会将这个datanode剔除pipline，重新构建一个新的pipline。

（2）整个文件上传过程中，只保证每一个数据块1个副本成功就认为成功。

#### 1.2、文件下载流程

![image-20200726205318765](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200726205318765.png)

1、文件读取的时候，发现某一个数据块的节点有故障。

（1）先将这个故障的节点汇报给namenode，namenode会做这个节点标识，用户再次请求下载这个数据块的时候不会优先返回这个节点。

（2）客户端立即从其他的存储这个数据块的节点重新读取。

#### 1.3、元数据文件解析

文件分为5种类型：

（1）edits_***：历史日志文件，记录的是元数据的所有执行的操作日志。

（2）edits_inprogress_xx：正在编辑的日志文件，目前正在接收客户端写元数据请求的文件。

（3）**fsimage_xx**：镜像文件，真正的元数据文件，内存中元数据的一个镜像。

​		 fsimage_xx.md5：镜像文件的加密。

（4）seen_txid：当前日志文件的最大编号。

（5）version：记录集群版本信息。

#### 1.4、元数据的写入过程分析

请求中需要修改元数据的时候，会发生写入操作。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200726210417493.png" alt="image-20200726210417493" style="zoom:80%;" />

（1）先将元数据写入到内存中，内存中永远是最新的，最全的元数据信息。

（2）将元数据的操作日志写入磁盘的edits_inprogress_xx文件，磁盘中的fsimage文件，不是最新的，和内存中的有偏差，每隔一段时间磁盘上的fsimage文件和磁盘的edits文件会进行合并，这个合并工作就是secondarynamenode执行的。

#### 1.5、namenode的元数据合并

1、合并触发的条件

（1）dfs.namenode.checkpoint.period（时间间隔：1h合并一次）和dfs.namenode.checkpoint.txns（日志数据条数达到100万）的配置。该条件满足一个就会触发元数据合并合作。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200726211521214.png" alt="image-20200726211521214" style="zoom:80%;" />

#### 1.6、元数据加载过程

1、edits编号从1开始顺序递增，主要是记录edits文件的顺序。

2、fsimage文件编号从0开始的，后续编号根据每次合并的edits文件的最大编号来的。

fsimage_000000257

=fsimage00000000+edits_0000001~edits_00000257

fsimage_000000350

=fsimage00000257+edits_000000258~edits_00000350

3、每次集群进行关闭的时候内存中的元数据释放了，下次启动的时候内存中的元数据重新从磁盘上加载。

edits_000000255~edits_00000257

edits_inprogress_000000258

fsimage_000000257

加载的元数据是id最大的fsimage文件以及edits。

### 2、网络拓扑-节点距离计算

​		在`HDFS`写数据的过程中，`NameNode`会选择距离待上传数据最近距离的`DataNode`接收数据。那么这个最近距离怎么计算呢？

节点距离：两个节点到达最近的共同祖先的距离总和。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200723214026760.png" alt="image-20200723214026760" style="zoom:80%;" />

### 3、机架感知（副本存储节点选择）

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200723214612517.png" alt="image-20200723214612517" style="zoom:80%;" />

1、机架策略

​		机架策略又叫副本存放策略。这是决定每一个数据块的多个副本是如何存储的。hdfs默认的每一块副本有三个，存在不同的节点上。

2、默认副本3个，机架2个，10个节点。多个副本的存放策略如下：

（1）第一个副本存放在客户端所在节点，目的是为了防止数据块的副本上传不成功，最大程度的保证每一个副本上传成功。如果客户端不是集群中的节点（IDEA中的客户端，文件在windows中），此时会随机选择一个节点。

（2）第二个副本放在与第一个副本不同机架的任意节点，目的是保证数据安全，防止机架整体断网，断电。

（3）第三个副本存放到与第二个副本相同机架的不同节点上，目的是便于传输，提升数据传输的效率。

当然，实际可能中，会遇到各种各样的情况，比如不同机房、不同机架等。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200726165106899.png" alt="image-20200726165106899" style="zoom:50%;" />

### 4、负载均衡

（1）hadoop集群中多个datanode中每一个datanode存储数据的占比相当。

​		对于hadoop集群来说，namenode会定期检查集群的负载均衡，如果发现集群中datanode节点的负载不均衡的情况下，会自动启动负载均衡。即将存储占比较大的datanode节点上的数据，移动到存储占比小的datanode的节点上。

比如：将hadoop101（占比大）中的数据经过网络传输传递个hadoop103（占比小）,再将hadoop101中的数据删除。

（2）默认情况下带宽1M/S，如果集群中节点很少的，默认的负载均衡没有问题，反之，如果集群中节点很多的时候，默认的负载均衡就很难达到要求了。

速度为1MB/S（在`dfs.datanode.balabce.bandwidthPerSec`配置中）

（3）手动启动负载：start-balancer.sh -t 10

+ 该命令不会立即执行，类似于jvm垃圾回收机制。提醒集群空闲的时候，立即执行，加快执行效率。
+ -t 10 代表负载均衡最总达到的要求。我们讲的负载均衡，都是占比差在可接受的范围。-t 指定的就是节点存储的最大-最小的占比差（10%）。
+ 该命令在手动执行时候需要配合带宽调整。

## 4.2 HDFS读数据流程

读数据流程图：

![image-20200723214930017](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200723214930017.png)

1、客户端通过`Distributed FileSystem`向Nam`e`Node请求下载文件，`NameNode`通过查询元数据，找到文件块所在的`DataNode`地址。

2、挑选一台`DataNode`（就近原则，然后随机）服务器，请求读取数据。

3、`DataNode`开始传输数据给客户端（从磁盘里面读取数据输入流，以`Packet`为单位来做校验）。

4、客户端以`Packet`为单位接收，先在本地缓存，然后写入目标文件。

# 5 NameNode和SecondaryNameNode

1、SecondaryNameNode介绍

（1）帮助namenode备份元数据，防止namenode元数据丢失。当namenode元数据损坏或者丢失的时候可以通过SecondaryNameNode进行恢复。

（2）减轻namenode的压力。帮助namenode进行元数据合并。

## 5.1 NN和2NN工作机制

1、NameNode中的元数据存储位置

​		首先，我们做个假设，如果存储在NameNode节点的磁盘中，因为经常需要进行随机访问，还有响应客户请求，必然是效率过低。因此，元数据需要存放在内存中。但如果只存在内存中，一旦断电，元数据丢失，整个集群就无法工作了。因此产生在磁盘中备份元数据的`FsImage`。

​		这样又会带来新的问题，当在内存中的元数据更新时，如果同时更新FsImage，就会导致效率过低，但如果不更新，就会发生一致性问题，一旦NameNode节点断电，就会产生数据丢失。因此，引入`Edits`文件(只进行追加操作，效率很高)。每当元数据有更新或者添加元数据时，修改内存中的元数据并追加到Edits中。这样，一旦NameNode节点断电，可以通过FsImage和Edits的合并，合成元数据。

​		但是，如果长时间添加数据到Edits中，会导致该文件数据过大，效率降低，而且一旦断电，恢复元数据需要的时间过长。因此，需要定期进行FsImage和Edits的合并，如果这个操作由NameNode节点完成，又会效率过低。**因此，引入一个新的节点SecondaryNamenode，专门用于FsImage和Edits的合并。**

2、NameNode工作机制

![image-20200724084410430](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200724084410430.png)

 第一阶段：NameNode启动

（1）第一次启动NameNode格式化后，创建Fsimage和Edits文件。如果不是第一次启动，直接加载编辑日志和镜像文件到内存。

（2）客户端对元数据进行增删改的请求。

（3）NameNode记录操作日志，更新滚动日志。

（4）NameNode在内存中对数据进行增删改。

第二阶段：Secondary NameNode工作

（1）Secondary NameNode询问NameNode是否需要CheckPoint。直接带回NameNode是否检查结果。

（2）Secondary NameNode请求执行CheckPoint。

（3）NameNode滚动正在写的Edits日志。

（4）将滚动前的编辑日志和镜像文件拷贝到Secondary NameNode。

（5）Secondary NameNode加载编辑日志和镜像文件到内存，并合并。

（6）生成新的**镜像文件**fsimage.chkpoint。

（7）拷贝fsimage.chkpoint到NameNode。

（8）NameNode**将fsimage.chkpoint重新命名成fsimage**。

**NN和2NN工作机制详解：**

Fsimage：NameNode内存中元数据序列化后形成的文件。

Edits：记录客户端更新元数据信息的每一步操作（可通过Edits运算出元数据）。

NameNode启动时，先滚动Edits并生成一个**空的edits.inprogress**，然后加载Edits和Fsimage到内存中，此时NameNode内存就持有最新的元数据信息。Client开始对NameNode发送元数据的**增删改**的请求，这些请求的操作首先会被记录到edits.inprogress中（查询元数据的操作不会被记录在Edits中，因为查询操作不会更改元数据信息），如果此时NameNode挂掉，重启后会从Edits中读取元数据的信息。然后，NameNode会在内存中执行元数据的增删改的操作。

由于Edits中记录的操作会越来越多，Edits文件会越来越大，导致NameNode在启动加载Edits时会很慢，所以需要对Edits和Fsimage进行合并（**所谓合并，就是将Edits和Fsimage加载到内存中，照着Edits中的操作一步步执行，最终形成新的Fsimage**）。SecondaryNameNode的作用就是帮助NameNode进行Edits和Fsimage的合并工作。

SecondaryNameNode首先会询问NameNode是否需要CheckPoint（**触发CheckPoint需要满足两个条件中的任意一个，定时时间到和Edits中数据写满了**）。直接带回NameNode是否检查结果。SecondaryNameNode执行CheckPoint操作，首先会让NameNode滚动Edits并生成一个空的edits.inprogress，滚动Edits的目的是给Edits打个标记，以后所有新的操作都写入edits.inprogress，其他未合并的Edits和Fsimage会拷贝到SecondaryNameNode的本地，然后将拷贝的Edits和Fsimage加载到内存中进行合并，生成fsimage.chkpoint，然后将fsimage.chkpoint拷贝给NameNode，重命名为Fsimage后替换掉原来的Fsimage。NameNode在启动时就只需要加载之前**未合并的Edits和Fsimage**即可，因为合并过的Edits中的元数据信息已经被记录在Fsimage中。

## 5.2 Fsimage和Edits解析

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200724085204048.png" alt="image-20200724085204048" style="zoom:80%;" />

1、查看Fsimage文件

（1）基本语法

```ini
hdfs oiv -p 文件类型 -i镜像文件 -o 转换后文件输出路径

#举例，将显示的xml文件内容拷贝到Eclipse中创建的xml文件中，并格式化。
hdfs oiv -p XML -i fsimage_0000000000000000025 -o /opt/module/hadoop-2.7.2/fsimage.xml
```

2、查看Edits文件

（1）基本语法

```ini
hdfs oev -p 文件类型 -i编辑日志 -o 转换后文件输出路径

#举例
hdfs oev -p XML -i edits_0000000000000000012-0000000000000000013 -o /opt/module/hadoop-2.7.2/edits.xml
```

​		可以看出，Fsimage中没有记录块所对应DataNode，主要是在集群启动后，要求DataNode动态上报数据块信息，并间隔一段时间后再次上报。

## 5.3 CheckPoint时间设置

在hdfs-default.xml文件中：

（1）通常情况下，SecondaryNameNode每隔一小时执行一次。

```xml
<property>
  <name>dfs.namenode.checkpoint.period</name>
  <value>3600</value>
</property>
```

（2）一分钟检查一次操作次数，3当操作次数达到1百万时，SecondaryNameNode执行一次。

```xml
<property>
  <name>dfs.namenode.checkpoint.txns</name>
  <value>1000000</value>
<description>操作动作次数</description>
</property>

<property>
  <name>dfs.namenode.checkpoint.check.period</name>
  <value>60</value>
<description> 1分钟检查一次操作次数</description>
</property >
```

## 5.4 NameNode故障处理

NameNode故障后，可以采用如下两种方法恢复数据。

1、方法一：将SecondaryNameNode中数据拷贝到NameNode存储数据的目录。

（1）杀死NameNode进程

（2）删除NameNode存储的数据

```ini
rm -rf /opt/module/hadoop-2.7.2/data/tmp/dfs/name/*
```

（3）拷贝SecondaryNameNode中数据到原NameNode存储数据目录

```ini
scp -r atguigu@hadoop104:/opt/module/hadoop-2.7.2/data/tmp/dfs/namesecondary/* ./name/
```

（4）重新启动NameNode

```ini
sbin/hadoop-daemon.sh start namenode
```

2、方法2：使用-importCheckpoint选项启动NameNode守护进程，从而将SecondaryNameNode中数据拷贝到NameNode目录中。

## 5.5 安全模式

1、介绍

![image-20200724092038807](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200724092038807.png)

（1）集群启动的时候进入安全模式

集群启动顺序：namenode > datanode > secondarynamenode。

集群处于安全模式的时候，对元数据相关信息

符合以下要求的时候，才会结束安全模式：

+ 数据块的回报率（最小副本条件）>=99.9%
+ datanode的节点个数达到配置要求的时候，默认0个。
+ 当上面两个都满足的时候，停留安全模式30s，保证集群数据汇报稳定。
+ 保证namenode元数据的文件夹的存储预留大小达到100M以上。

（2）集群运行过程中也有可能进入安全模式。

2、基本语法

​		集群处于安全模式，不能执行重要操作（写操作）。集群启动完成后，自动退出安全模式。

```ini
#查看安全模式状态
bin/hdfs dfsadmin -safemode get

#进入安全模式状态
bin/hdfs dfsadmin -safemode enter 

#离开安全模式状态
bin/hdfs dfsadmin -safemode leave

#等待安全模式状态
bin/hdfs dfsadmin -safemode wait	
```

## 5.6 NameNode多目录配置

NameNode的本地目录可以配置成多个，且每个目录存放内容相同，增加了可靠性。

1、配置步骤

（1）在hdfs-site.xml文件中增加如下内容

```xml
<property>
    <name>dfs.namenode.name.dir</name>
<value>file:///${hadoop.tmp.dir}/dfs/name1,file:///${hadoop.tmp.dir}/dfs/name2</value>
</property>
```

（3）停止集群，删除data和logs中所有数据。

```ini
#hadoop101、hadoop102、hadoop10三台中数据都删除
rm -rf data/ logs/
```

（3）格式化集群并启动

（4）查看结果

# 6 DataNode

1、介绍

（1）datanode的主要作用就是负责存储真正的数据，每一个节点将对应的数据块存储到自己的本地。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200726085947070.png" alt="image-20200726085947070" style="zoom: 80%;" />

里面的块池文件夹存储namenode管理的所有数据块信息。

其次，负责处理客户端真正的读写请求。

（2）测试：将数据合并

![image-20200726091021433](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200726091021433.png)

补充：>（覆盖）  >>（重定向）

```ini
cat	source >|>> destination
```



## 6.1 DataNode工作机制

![image-20200724093500847](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200724093500847.png)



（1）一个数据块在DataNode上以文件形式存储在磁盘上，包括两个文件，一个是数据本身，一个是元数据包括数据块的长度，块数据的校验和，以及时间戳。

（2）DataNode启动后向NameNode注册，通过后，周期性（1小时）的向NameNode上报所有的块信息。

（3）心跳是每3秒一次，心跳返回结果带有NameNode给该DataNode的命令（如复制块数据到另一台机器，或删除某个数据块）。如果超过10分钟没有收到某个DataNode的心跳，则认为该节点不可用。

（4）集群运行中可以安全加入和退出一些机器。

1、心跳机制

（1）namenode如何获取哪一个datanode是可用的及可用空间？datanode会定时向namenode发送心跳报告，发送心跳报告具体做什么？

+ 告诉namenode自己的存活状态以及可用空间。
+ 向namenode发送块报告，包含每一个datanode存储的块信息。

namenode存储元数据的时候，按照存储空间：

+ 硬盘

  里面主要包含三部分内容：（1）抽象目录树（2）数据和块的对应关系（3）数据块的存储位置。

+ 内存

  真正的读写操作的时候操作的元数据是在内存中进行的，内存中包含的元数据的信息，只包含硬盘中的（1）（2）部分。

  /xx.tar.gz(文件) [blk01:[] blk02:[] ]

  用户读取的时候，需要块的存储位置的块信息是datanode向namenode发送心跳报告（块报告）的时候获取的。

  hadoop101 -> blk01 blk02 ->namnode

  hadoop103 -> blk01 blk02 ->namnode

  /xx.tar.gz(文件) [blk01:[hadoop101,hadoop103] blk02:[hadoop101,hadoop103] ]

（2）namenode确定一个datanode是否宕机需要的时间？

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200726162740132.png" alt="image-20200726162740132" style="zoom:80%;" /> 

默认连续10次接收不到心跳（不间断的），namenode断定datanode可能宕机了（这中间这要有一次接收到了，namenode重新记录心跳）。然后，namenode主动向datanode发送检查，namenode会开启后台的守护进程，等待检查结果。默认检查两次，每次5分钟。

## 6.2 数据完整性

DataNode节点保证数据完整性的方法。

（1）当DataNode读取Block的时候，它会计算CheckSum。

（2）如果计算后的CheckSum，与Block创建时值不一样，说明Block已经损坏。

（3）Client读取其他DataNode上的Block。

（4）DataNode在其文件创建后周期验证CheckSum。

## 6.3 掉线时限参数设置

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200724101806080.png" alt="image-20200724101806080" style="zoom:80%;" />

需要注意的是`hdfs-site.xml` 配置文件中的heartbeat.recheck.interval的单位为毫秒，dfs.heartbeat.interval的单位为秒。

```xml
<property>
    <name>dfs.namenode.heartbeat.recheck-interval</name>
    <value>300000</value>
</property>
<property>
    <name>dfs.heartbeat.interval</name>
    <value>3</value>
</property>
```

## 6.4 服务新数据节点

随着公司业务的增长，数据量越来越大，原有的数据节点的容量已经不能满足存储数据的需求，需要在原有集群基础上动态添加新的数据节点。

1、配置过程

（1）在hadoop104（我的是hadoop103）主机上再克隆一台hadoop105主机

（2）修改IP地址和主机名称

（3）删除原来HDFS文件系统留存的文件（/opt/module/hadoop-2.7.2/data和log）

（4）source一下配置文件

```ini
source /etc/profile
```

2、具体步骤

（1）直接启动DataNode，即可关联到集群

```ini
[atguigu@hadoop105 hadoop-2.7.2]$ sbin/hadoop-daemon.sh start datanode
[atguigu@hadoop105 hadoop-2.7.2]$ sbin/yarn-daemon.sh start nodemanager
```

（2）如果数据不均衡，可以用命令实现集群的再平衡

```ini
[atguigu@hadoop102 sbin]$ ./start-balancer.sh
```
