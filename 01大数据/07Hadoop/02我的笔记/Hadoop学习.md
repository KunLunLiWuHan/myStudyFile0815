大数据的特点（4V）

+ 容量大（Volume）：现在数据产生快，数据类型复杂。
+ 速度快（Velocity）：随时随地都在产生数据。
+ 种类多（Variety）：结构，半结构等。

+ 价值高（Value）：整体价值高，单条数据价值低。

大数据部门业务流程分析：

![image-20200719173155491](D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200719173155491.png)

大数据部门组织结构：

![image-20200719173300432](D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200719173300432.png)

## 1、hadoop介绍

​		hadoop是一个由Apache基金会所开发的分布式系统基础架构，主要解决海量数据的存储和海量数据的分析计算问题。广义上来讲，hadoop通常指一个更宽泛的概念-Hadoop生态圈。

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200719174023252.png" alt="image-20200719174023252" style="zoom:80%;" />

## 1.1 课程介绍

1、大数据主要解决的是分布式的问题，包含两个生态圈：Hadoop生态圈（Hadoop以及所有依赖Hadoop或者辅助Hadoop的组件组成的）和	Spark生态圈，大数据课程主要分为两个部分：

（1）离线：已经存在的数据（静态的）

​		Hadoop （8）

​			**hdfs 存储** （4）

​			**mapreduce 计算** （3.5）

​			yarn 资源调度 （0.5）

​		其次，会学习**zookeeper**（分布式协调服务，2）、**hive**(依赖于hadoop的数据仓库，主要学习hql语句，6)、**hbase**（依赖于hadoop分布式数据库，3）。

​		其次，学习工具型组件：**sqoop**（关系型数据库mysql的数据迁移，0.5），**flume**（数据采集工具，1），azkaben（crontab只能做单机的简单的任务调度，azkaben做复杂任务的调度，0.5）

（2）实时：实时产生的数据（动态的）

首先，**scala**（4）

其次，**spark**（12）

其次，storm，**kafka**（消息队列，2）

其次，flink（5），机器学习（3-4）

2、数据的分类

按照结构分：

（1）结构化数据

​		mysql中的数据，excel表等。是严格的二维表结构，每一行有相同的列，且每一行对应列的类型都是一致的。

（2）半结构化数据

​		有结构，但是没有严格的二维表结构。比如：html,css,xml，json（按照标签划分的）等。

（3）非结构化数据

​		视频，图片等。一般都是二进制存储的。

3、企业数据量

一般是TB,PB的数据量。

4、数据处理流程

数据采集，数据存储，数据清洗（ETL），数据计算，数据存储结果，web可视化展示。

5、来源于三篇论文

GFS-google的文件系统-HDFS

MAPREDUCE-google的分布式计算方案-MAPREDUCE

BIGADATBLE-google的分布式数据库-HBASE

## 2、Hadoop三大发行版本

Hadoop三大发行版本：Apache、Cloudera、Hortonworks。

+ Apache版本最原始（最基础）的版本，对于入门学习最好。

+ Cloudera在大型互联网企业中用的较多。

+ Hortonworks文档较好。

1. Apache Hadoop

官网地址：http://hadoop.apache.org/releases.html

下载地址：https://archive.apache.org/dist/hadoop/common/

2. Cloudera Hadoop 

官网地址：https://www.cloudera.com/downloads/cdh/5-10-0.html

下载地址：http://archive-primary.cloudera.com/cdh5/cdh/5/

3. Hortonworks Hadoop

官网地址：https://hortonworks.com/products/data-center/hdp/

下载地址：https://hortonworks.com/downloads/#data-platform

## 3、Hadoop的优势

1. 高可靠性：Hadoop底层维护多个数据副本，所以即使Hadoop某个计算元素或者存储出现故障，也不会导致数据的丢失。
2. 高扩展性：在集群间分配任务数据，可方便地扩展数以千计的节点。
3. 高效性：在MapReduce的思想下，Hadoop是并行工作的，以加快任务处理速度。
4. 高容错性：能够自动地将失败的任务进行重新分配。

## 4、Hadoop组成

1、Hadoop1.x和Hadoop2.x的区别

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200719175054920.png" alt="image-20200719175054920" style="zoom:80%;" />

2、HDFS架构概述

HDFS（Hadoop Distributed File System）的架构概述如下：

（1）NameNode(nn)：存储文件的**元数据**，如文件名，文件目录，文件属性（生成时间、副本数、文件权限），以及每个文件的块列表和块所在的DataNode等。

（2）DataNode(dn)：在本地文件系统存储文件块数据，以及**块数据的检验和**。

（3）Secondary NameNode(2nn)：用来监控HDFS状态的辅助后台程序，每隔一段时间获取HDFS元数据的快照。

3、YARN架构

YARN主要由ResourceManager、NodeManager、ApplicationMaster和Container等组件构成，如下图所示。

![image-20200719180806841](D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200719180806841.png)

4、MapReduce架构概述

MapReduce将计算过程分为两个阶段：Map和Reduce，如下图所示：

（1）Map阶段并行处理输入数据

（2）Reduce阶段对Map结果进行汇总

![image-20200719181126286](D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200719181126286.png)

5、大数据技术生态体系

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200719181356604.png" alt="image-20200719181356604"  />

图中涉及的技术名词解释如下：

（1）Sqoop：Sqoop是一款开源的工具，主要用于在Hadoop、Hive与传统的数据库(MySql)间进行数据的传递，可以将一个关系型数据库（例如 ：MySQL，Oracle 等）中的数据导进到Hadoop的HDFS中，也可以将HDFS的数据导进到关系型数据库中。

（2）Flume：Flume是Cloudera提供的一个高可用的，高可靠的，分布式的**海量日志**采集、聚合和传输的系统，Flume支持在日志系统中定制各类数据发送方，用于收集数据；同时，Flume提供对数据进行简单处理，并写到各种数据接受方（可定制）的能力。

（3）Kafka：Kafka是一种高吞吐量的分布式发布订阅消息系统，有如下特性：

+ 通过O(1)的磁盘数据结构提供消息的持久化，这种结构对于即使数以TB的消息存储也能够保持长时间的稳定性能。
+ 高吞吐量：即使是非常普通的硬件Kafka也可以支持每秒数百万的消息。
+ 支持通过Kafka服务器和消费机集群来分区消息。
+ 支持Hadoop并行数据加载。

（4）Storm：Storm用于“连续计算”，对数据流做连续查询，在计算时就将结果以流的形式输出给用户。

（5）Spark：Spark是当前最流行的开源大数据内存计算框架。可以基于Hadoop上存储的大数据进行计算。

（6）Oozie：Oozie是一个管理Hdoop作业（job）的工作流程调度管理系统。

（7）Hbase：HBase是一个分布式的、面向列的开源数据库。HBase不同于一般的关系数据库，它是一个适合于**非结构化数据存储的数据库**。

（8）Hive：Hive是基于Hadoop的一个数据仓库工具，可以将结构化的数据文件映射为一张数据库表，并提供简单的SQL查询功能，可以将SQL语句转换为MapReduce任务进行运行。 其优点是学习成本低，可以通过类SQL语句快速实现简单的MapReduce统计，不必开发专门的MapReduce应用，十分适合数据仓库的统计分析。

（9）R语言：R是用于统计分析、绘图的语言和操作环境。R是属于GNU系统的一个自由、免费、源代码开放的软件，它是一个用于统计计算和统计制图的优秀工具。

（10）Mahout：Apache Mahout是个可扩展的机器学习和数据挖掘库。

（11）ZooKeeper：Zookeeper是Google的Chubby一个开源的实现。它是一个针对大型分布式系统的可靠协调系统，提供的功能包括：配置维护、名字服务、 分布式同步、组服务等。ZooKeeper的目标就是封装好复杂易出错的关键服务，将简单易用的接口和性能高效、功能稳定的系统提供给用户。

推荐的系统项目架构如下：

![image-20200719181717213](D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200719181717213.png)

# 2 安装和部署

## 2.1 环境准备

### 1、下载地址

https://archive.apache.org/dist/hadoop/common/hadoop-2.7.2/

hadoop官网：http://hadoop.apache.org/

### 2、安装

（1）解压到/home/zookeeper/software/hadoop-2.7.2

（2）在root权限下，配置环境变量

```ini
#进入配置目录
vim /etc/profile

export HADOOP_HOME=/home/zookeeper/software/hadoop-2.7.2
export PATH=$PATH:$HADOOP_HOME/bin
```

（3）使配置文件生效

```ini
source /etc/profile
```

（4）测试是否安装成功

```ini
[zookeeper@hadoop102 ~]$ hadoop version
```

控制台输出：

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200722153434828.png" alt="image-20200722153434828" style="zoom:80%;" />

（5）重启虚拟机

```ini
sudo reboot
```

### 3、Hadoop目录结构

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200722153941157.png" alt="image-20200722153941157" style="zoom:80%;" />

（1）bin目录：存放对Hadoop相关服务（HDFS,YARN）进行操作的脚本。

（2）etc目录：Hadoop的配置文件目录，存放Hadoop的配置文件。

（3）lib目录：存放Hadoop的本地库（对数据进行压缩解压缩功能）。

（4）sbin目录：存放启动或停止Hadoop相关服务的脚本。

（5）share目录：存放Hadoop的依赖jar包、文档、和官方案例（公共资源）。

# 3 Hadoop运行模式

Hadoop运行模式包括：本地模式、伪分布式模式以及完全分布式模式。

## 3.1 本地模式

​		直接解压，无需配置配置文件。然后安装到一个节点上。没有分布式文件系统（hdfs）和后台进程，使用本地的文件进程。

### 1、官方Grep案例

（1）在hadoop-2.7.2文件下面创建一个input文件夹（zookeeper权限就行）

```ini
mkdir input
```

（2）将Hadoop的xml配置文件复制到input

```ini
[zookeeper@hadoop102 hadoop-2.7.2]$ cp etc/hadoop/*.xml input
```

（3）执行share目录下的MapReduce程序

```ini
[zookeeper@hadoop102 hadoop-2.7.2]$ bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.2.jar grep input output 'dfs[a-z.]+'
```

上面主要表达的是对一个正则表达式的处理。

（4）查看输出结果

```ini
cat output/*

#当我们之前没有新建output文件夹时，此时输出结果为：
1       dfsadmin
```

output文件夹下有两个文件：

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200722160150533.png" alt="image-20200722160150533" style="zoom:80%;" />

### 2、官方WordCount案例

（1）在hadoop-2.7.2文件下面创建一个wcinput文件夹

```
mkdir wcinput
```

（2）在wcinput文件下创建一个wc.input文件

```
 touch wc.input
```

（3）编辑wc.input文件

```ini
hadoop yarn
hadoop mapreduce
zookeeper
zookeeper
```

（4）回到hadoop-2.7.2目录执行程序

```ini
hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.2.jar  wordcount wcinput wcoutput
```

（5）查看结果

```ini
cat wcoutput/part-r-00000
```

控制台输出对应单词的统计个数：

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200722161012236.png" alt="image-20200722161012236" style="zoom: 80%;" />

## 3.2 伪分布运行模式

​		只安装到一个节点上，有分布式文件系统的，有相应的hadoop的所有进程，但是分布式文件系统仅仅在一个节点上，该节点上的有5个进程。

### 1、集群配置

（1）配置hadoop-env.sh

首先，获取JDK的安装路径：

```ini
# 路径为：/opt/jdk1.8.0_251
echo $JAVA_HOME
```

配置hadoop-env.sh中的JDK路径

```ini
[zookeeper@hadoop101 hadoop]$ vim hadoop-env.sh

//配置
export JAVA_HOME=/opt/jdk1.8.0_251
```

（2）配置：core-site.xml

类似于common公共模块。

```ini
#路径:/home/zookeeper/software/hadoop-2.7.2/etc/hadoop
[zookeeper@hadoop101 hadoop]$ vim core-site.xml
```

进行下面的配置：

```xml
<!-- 指定HDFS中NameNode的地址 -->
<property>
<name>fs.defaultFS</name>
    <value>hdfs://hadoop101:9000</value>
</property>

<!-- 指定Hadoop运行时产生文件的存储目录 -->
<property>
        <name>hadoop.tmp.dir</name>
        <value>/home/zookeeper/software/hadoop-2.7.2/data/tmp</value>
</property>
```

（3）配置：hdfs-site.xml

关于hdfs模块的配置。

```ini
vim hdfs-site.xml
```

进行下面的配置：

```ini
<!-- 指定HDFS副本的数量，默认3个 -->
<property>
	<name>dfs.replication</name>
	<value>1</value>
</property>
```

（4）启动集群

```ini
#1、格式化NameNode
[zookeeper@hadoop102 hadoop-2.7.2]$ bin/hdfs namenode -format

#2、启动NameNode
#关闭:sbin/hadoop-daemon.sh stop namenode
sbin/hadoop-daemon.sh start namenode

#3、启动DataNode
#关闭:sbin/hadoop-daemon.sh stop datanode
sbin/hadoop-daemon.sh start datanode
```

（5）查看集群状态

我们可以通过下面的指令查看集群是否启动成功：

```
jps
```

此时输出为：

```ini
13586 NameNode
13668 DataNode
13786 Jps
```

也可以在浏览器端查看HDFS系统：

网址：http://hadoop101:50070/dfshealth.html#tab-overview

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200722170859560.png" alt="image-20200722170859560" style="zoom:80%;" />

注意：

​		我们只需要在第一次启动的时候执行格式化即可，以后不要老是格式化，因为格式化NameNode，会产生新的集群id（`/home/zookeeper/software/hadoop-2.7.2/data/tmp`）,导致NameNode和DataNode的集群id不一致，集群找不到已往数据。

​		所以，格式NameNode时，一定要先删除data数据和log日志，然后再格式化NameNode。

```ini
#路径：/home/zookeeper/software/hadoop-2.7.2/data/tmp/dfs/data/current
cat VERSION

#得到dn版本号为：
clusterID=CID-b886f13f-f0dd-40c2-9f5a-7316f493a5a1 

#路径：/home/zookeeper/software/hadoop-2.7.2/data/tmp/dfs/name/current
cat VERSION

#得到版本号
clusterID=CID-b886f13f-f0dd-40c2-9f5a-7316f493a5a1
```

（6）操作集群

+ （a）在HDFS文件系统上创建一个input文件夹

```ini
#-p代表多级文件([zookeeper@hadoop101 hadoop-2.7.2]$)
bin/hdfs dfs -mkdir -p /user/zookeeper/input
```

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200722171601395.png" alt="image-20200722171601395" style="zoom:67%;" />

+ （b）将测试文件内容上传到文件系统上

```ini
#input指令是上传
bin/hdfs dfs -put wcinput/wc.input /user/zookeeper/input/
```

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200722171727848.png" alt="image-20200722171727848" style="zoom:80%;" />

+ （c）查看上传的文件是否正确

```ini
bin/hdfs dfs -ls  /user/zookeeper/input/
```

![image-20200722171759469](D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200722171759469.png)

+ （d）运行MapReduce程序

```
bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.2.jar wordcount /user/zookeeper/input/ /user/zookeeper/output
```

我们可以通过下面的指令进行查看文件内容：

```ini
bin/hdfs dfs -cat /user/zookeeper/output/*
```

也可以将测试文件下载到本地来查看：

```ini
hdfs dfs -get /user/zookeeper/output/part-r-00000 ./wcoutput/
```

还可以在文件系统中进行查看：

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200722172328577.png" alt="image-20200722172328577" style="zoom:80%;" />

### 2、启动YARN并运行MapReduce程序

（1）配置yarn-env.sh

```ini
export JAVA_HOME=/opt/jdk1.8.0_251
```

（2）配置yarn-site.xml

关于yarn模块的配置。

```xml
<!-- Reducer获取数据的方式 -->
<property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
</property>

<!-- 指定YARN的ResourceManager的地址 -->
<property>
    <name>yarn.resourcemanager.hostname</name>
    <value>hadoop101</value>
</property>
```

（3）配置mapred-env.sh

```ini
export JAVA_HOME=/opt/jdk1.8.0_251
```

（4）配置mapred-site.xml

关于mapreduce的配置。

将mapred-site.xml.template重命名为mapred-site.xml

```ini
mv mapred-site.xml.template mapred-site.xml
```

对mapred-site.xml进行配置

```xml
<!-- 指定MR运行在YARN上 -->
<property>
		<name>mapreduce.framework.name</name>
		<value>yarn</value>
</property>
```

（5）启动集群

在启动集群前，我们必须保证NameNode和DataNode已经启动。

```ini
#1.启动ResourceManager
#路径：/home/zookeeper/software/hadoop-2.7.2
sbin/yarn-daemon.sh start resourcemanager

#2.启动NodeManager
sbin/yarn-daemon.sh start nodemanager
```

（6）查看集群状态

我们可以使用下面命令：

```ini
jps
```

也可以通过YARN的浏览器页面查看，网址：http://hadoop101:8088/cluster

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200722181750018.png" alt="image-20200722181750018" style="zoom:67%;" />

（7）对集群进行操作

首先，将output这个文件删除，然后执行如下的操作：

```ini
#删除浏览器上的文件
bin/hdfs dfs -rm -r /user/zookeeper/output

#2、操作数据后输出，会在浏览器上显示
bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.2.jar wordcount /user/zookeeper/input  /user/zookeeper/output
```

此时，我们可以在浏览器界面看到进度条变化：

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200722182123009.png" alt="image-20200722182123009" style="zoom:80%;" />

### 3、配置历史服务器

为了查看程序的历史运行情况，需要配置一下历史服务器。

（1）配置mapred-site.xml

```
vi mapred-site.xml
```

在文件中添加下面的配置：

```xml
<!-- 历史服务器端地址 -->
<property>
<name>mapreduce.jobhistory.address</name>
<value>hadoop101:10020</value>
</property>
<!-- 历史服务器web端地址 -->
<property>
    <name>mapreduce.jobhistory.webapp.address</name>
    <value>hadoop101:19888</value>
</property>
```

（2）启动历史服务器

```ini
sbin/mr-jobhistory-daemon.sh start historyserver
```

（3）查看历史服务器是否启动

我们可以通过下面指令查看，此时Linux输出为：

```ini
jps

#输出为
3425 ResourceManager
3668 NodeManager
4565 Jps
2744 NameNode
4378 JobHistoryServer
2829 DataNode
```

也可以通过浏览器查看JobHistory，网址：http://hadoop101:19888/jobhistory

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200722190937000.png" alt="image-20200722190937000" style="zoom:80%;" />

### 4、配置日志的聚集

​		日志聚集概念：应用运行完成以后，将程序运行日志信息上传到HDFS系统上。

​		日志聚集功能好处：可以方便的查看到程序运行详情，方便开发调试。

注意：

开启日志聚集功能，需要重新启动**NodeManager 、ResourceManager和HistoryManager。**

（1） 配置yarn-site.xml

```ini
vim yarn-site.xml
```

在文件中添加如下配置：

```xml
<property>
    <name>yarn.log-aggregation-enable</name>
    <value>true</value>
</property>

<!-- 日志保留时间设置7天 -->
<property>
    <name>yarn.log-aggregation.retain-seconds</name>
    <value>604800</value>
</property>
```

（2）先执行关闭后开启NodeManager 、ResourceManager和HistoryManager。

```ini
sbin/yarn-daemon.sh stop resourcemanager
sbin/yarn-daemon.sh stop nodemanager
sbin/mr-jobhistory-daemon.sh stop historyserver
```

（3）删除HDFS上已存在的输出文件/user/zookeeper/output，后执行**wordcount**操作。忘记的话，可以查看上文。

（4）输入网址：http://hadoop101:8088/cluster，查看日志信息。

![image-20200722192717084](D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200722192717084.png)

点击日志链接logs：

![image-20200722192747209](D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200722192747209.png)

查看日志信息：

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200722192822480.png" alt="image-20200722192822480" style="zoom:80%;" />

##### 5、配置文件说明

​		Hadoop配置文件分两类：默认配置文件和自定义配置文件，只有用户想修改某一默认配置值时，才需要修改自定义配置文件，更改相应属性值。

（1）默认配置文件

| 要获取的默认文件     | 文件存放在Hadoop的jar包中的位置                             |
| :------------------- | :---------------------------------------------------------- |
| [core-default.xml]   | hadoop-common-2.7.2.jar/  core-default.xml                  |
| [hdfs-default.xml]   | hadoop-hdfs-2.7.2.jar/  hdfs-default.xml                    |
| [yarn-default.xml]   | hadoop-yarn-common-2.7.2.jar/  yarn-default.xml             |
| [mapred-default.xml] | hadoop-mapreduce-client-core-2.7.2.jar/  mapred-default.xml |

（2）自定义配置文件

​		`core-site.xml、hdfs-site.xml、yarn-site.xml、mapred-site.xml`四个配置文件存放在`$HADOOP_HOME/etc/hadoop`这个路径上，用户可以根据项目需求重新进行修改配置。

## 3.3 完全分布式运行模式

​		一个集群中，有多个节点共同负责存储和计算，存在真正的分布式文件系统和计算系统。

​		但是在多个节点中，只有一个namenode主节点，剩下的都是从节点datanode。此时存在一个问题：namenode存在单点故障的问题，namenode宕机，会造成整个集群不可用。极少数公司用，集群规模小（3-5台）。

**高可用的安装介绍**：

​		此时，出现了高可用的安装，主要解决namenode存在单点故障的问题。该集群中同时有两个namenode（在hadoop3.0中支持多个），同一时间只有一个对外提供服务，我们将这个对外提供服务的namenode称为active namenode，另一个namenode处于热备的状态，时刻准备接替active namenode，将其称为standby namenode。

​		一旦active的宕机，standby的会立即切换为active，如果原先的active活了，这个时候将自己的状态切换为standby。

​		存在问题：当集群规模很大的时候，同一时间对外提供服务的只有一个namenode，会造成namenode的压力很大（两个namenode是不能分担压力的）。生产上用的较多，适用于高可用集群，不适用超级大的规模集群。

**联邦模式安装介绍**：

​		解决高可用集群的namenode的压力过大的问题，同一时间，一个集群中存在多个namenode，但是这多个namenode都处于active状态的，多个namenode共同管理集群。

​		多个namenode共同管理集群中所有从节点的时候，是分工明确的。不同的namenode进行管理不同的块池（blockpoolID=BP-XX）中的数据。

超大集群：联邦模式+高可用。

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200725214818640.png" alt="image-20200725214818640" style="zoom:80%;" />

### 3.3.1 编写集群分发脚本

#### 1、scp（secure copy）安全拷贝

scp可以实现服务器与服务器之间的数据拷贝。（from server1 to server2）

（1）基本语法

```ini
#scp [可选参数] file_source file_target 
# 要拷贝的文件路径/名称    目的用户@主机:目的路径/名称
scp -r $pdir/$fname  $user@hadoop$host:$pdir/$fname
```

举例：

```ini
#将hadoop101中/opt/module目录下的软件拷贝到hadoop102上。
scp -r /opt/module  root@hadoop102:/opt/module
```

（2）修改所在组和所有者

```ini
# atguigu:atguigu（所有者：所在组）
#/opt/module 要修改的文件
#sudo:superuser do(超级用户)
sudo chown atguigu:atguigu -R /opt/module
```

（3）拷贝/etc/profile配置文件

```ini
#hadoop101到hadoop103
sudo scp /etc/profile root@hadoop103:/etc/profile

#使配置文件生效
source /etc/profile
```

总结：

（1）当远程服务器没有相同文件，只是简单的将文件从server1复制到server2。

（2）当远程服务器存在相同文件时，该命令会对原来的文件实现覆盖。比如：server1中的1.txt会覆盖server2.txt。

#### 2、rsync 远程同步工具

​		rsync主要用于备份和镜像（实现同步）。具有速度快、避免复制相同内容和支持符号链接的优点。

​		rsync和scp区别：用rsync做文件的复制要比scp的速度快，rsync只对**差异文件**做更新。scp是把所有文件都复制过去。

（1）基本语法

```ini
rsync -rvl  $pdir/$fname   $user@hadoop$host:$pdir/$fname
```

| 选项 | 功能         |
| ---- | ------------ |
| -r   | 递归         |
| -v   | 显示复制过程 |
| -l   | 拷贝符号连接 |

### 3.3.2 集群配置

#### 1、集群部署规划

|      | hadoop101              | hadoop102                    | hadoop103                       |
| ---- | ---------------------- | ---------------------------- | ------------------------------- |
| HDFS | **NameNode**  DataNode | DataNode                     | **SecondaryNameNode**  DataNode |
| YARN | NodeManager            | ResourceManager  NodeManager | NodeManager                     |

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200724233907309.png" alt="image-20200724233907309" style="zoom:80%;" />

1、HDFS分布式存储（一主多从，一助理）

（1）NameNode  ：该角色是分布式存储的老大，管理每一个节点存储数据的编号以及对应关系。

（2）DataNode：负责真正存储数据的。

（3）SecondaryNameNode：NameNode 的助理和执行备份操作。

2、MapReduce：分布式计算程序

安装完成，没有对应进程。什么时候启动，什么时候有进程。

3、yarn：（资源调度）服务于计算程序的

主从结构。有两个角色：

（1）ResourceManager ：资源调度的老大，控制那个计算任务分配多少资源（同时也决定在那个节点进行分配在资源）。

（2）NodeManager：提供资源进行运行任务。

4、修改hadoop的配置文件

（1）core-site.xml

（2）hdfs-site.xml

（3）mapred-site.xml

（4）yarn-site.xml

（5）hadoop-env.sh

（6）slaves

5、远程发送hadoop的环境变量

```ini
sudo scp /etc/profile hadoo102:/etc
sudo scp /etc/profile hadoo103:/etc

#使其生效(hadoo101，hadoo102,hadoo103节点上)
source /etc/profile
```

6、将hadoop安装包远程发送到其他节点

```ini 
scp -r /home/zookeeper/software/hadoop-2.7.2 hadoop2(3):/home/zookeeper/software
```

7、格式化namenode节点

8、群起集群

#### 2、配置集群

（1）配置core-site.xml

```xml
<!-- 指定HDFS中NameNode的地址
1、一个configuration是核心配置文件标签，一个property对应一个配置项。
2、配置形式为key-value键值对形式，key对应<name>,value对应<value>
3、fs.defaultFS是默认的，不能更改。配置的是主节点NameNode访问入口。
4、协议：//主机名：端口
-->
<property>
<name>fs.defaultFS</name>
    <value>hdfs://hadoop101:9000</value>
</property>

<!-- 指定Hadoop运行时（hdfs）产生文件的存储目录
1、配置家目录下的一个目录，这样的话，普通用户有访问权限 
2、hdfs-site.xml配置中相关数据的总目录
-->
<property>
        <name>hadoop.tmp.dir</name>
        <value>/home/zookeeper/software/hadoop-2.7.2/data/tmp</value>
</property>
```

（2）配置HDFS文件

+ （a）配置hadoop-env.sh文件

```ini
//配置，首先加载/etc/profile 启动hadoop时，会加载下面的路径中的JAVA_HOME。
export JAVA_HOME=/opt/jdk1.8.0_251
```

+ （b）配置hdfs-site.xml文件

```xml
<!-- 1、指定HDFS副本的存储个数，默认每个数据存3份 -->
<property>
	<name>dfs.replication</name>
	<value>3</value>
</property>

<!-- 2、指定Hadoop辅助名称节点主机配置的信息，和namenode不同节点 -->
<property>
      <name>dfs.namenode.secondary.http-address</name>
      <value>hadoop103:50090</value>
</property>

<!-- 3、namenode相关数据存储目录
（1）、为了保证元数据的安全，一般配置多个不同目录 
-->
<property>
        <name>hadoop.namenode.name.dir</name>
        <value>/home/zookeeper/software/hadoop-2.7.2/data/tmp/namenodeName</value>
</property>

<!-- 4、datanode的数据存储目录 -->
<property>
        <name>hadoop.datanode.data.dir</name>
        <value>/home/zookeeper/software/hadoop-2.7.2/data/tmp/datanodeData</value>
</property>
```

（3）YARN配置文件

+ （a）配置yarn-env.sh

```ini
export JAVA_HOME=/opt/jdk1.8.0_251
```

+ （b）配置yarn-site.xml

```xml
<!-- 1、（Reducer获取数据的方式）yarn上允许的计算任务的配置
（1）、yarn集群为mapreduce程序提供shuffle服务
-->
<property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
</property>

<!-- 2、指定YARN的ResourceManager的地址 
（1）配置yarn的主节点-->
<property>
    <name>yarn.resourcemanager.hostname</name>
    <value>hadoop102</value>
</property>
```

（4）MapReduce配置文件

+ （a）配置mapred-env.sh

```ini
export JAVA_HOME=/opt/jdk1.8.0_251
```

+ 配置mapred-site.xml

```ini
#1、首先重命名文件（赋值一份）
cp mapred-site.xml.template mapred-site.xml

#2、对重命名的mapred-site.xml进行配置
<!-- 指定MR运行在Yarn上，配置mapreduce的计算任务的做资源调度框架 -->
<property>
		<name>mapreduce.framework.name</name>
		<value>yarn</value>
</property>
```

（4）然后对集群中的其他机器进行配置。（推荐分发配置）

#### 3、集群单点启动

**说明：**启动集群的方式2方式1：各个服务组件逐一启动/停止。

（1）如果集群是第一次启动，需要格式化NameNode。（此时把关于此次启动的所有的后台服务给关闭）

首先，删除目录下的日志和数据文件，为了方便起见，我们需要将hadoop101,hadoop102,hadoop103上的data/,logs/文件全部删除。

```ini
[zookeeper@hadoop101 hadoop-2.7.2]$ rm -rf data/ logs/
```

其次，格式化NameNode:

```ini
[zookeeper@hadoop101 hadoop-2.7.2]$ bin/hdfs namenode -format
```

（2）在hadoop101上启动NameNode

```ini
sbin/hadoop-daemon.sh start namenode
```

（3）分别在hadoop101,hadoop102,hadoop103上启动DataNode

```ini
sbin/hadoop-daemon.sh start datanode
```

（4）检查集群启动状态

可以使用下面指令：

```ini
jps
```

也可以登录网址http://hadoop101:50070/dfshealth.html#tab-overview查看：

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200722213131166.png" alt="image-20200722213131166" style="zoom:67%;" />

#### 4、SSH无密登录配置

（1）基本语法

```ini
#此时我们需要输入hadoop102的密码才能够登录
ssh hadoop102(另一台电脑的ip地址)
```

（2）免密登录原理

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200722222252544.png" alt="image-20200722222252544" style="zoom:80%;" />

+ （a）首先是hadoop101生成公钥和私钥

```ini
#cd .ssh即可进入下面的目录
[zookeeper@hadoop101 .ssh]$ ssh-keygen -t rsa
```

​		然后敲（三个回车），就会生成两个文件id_rsa（私钥）、id_rsa.pub（公钥）。

然后将公钥拷贝到要免密的三台目标机器上：

```ini
#hadoop101自身登录自己也要密码输入。
[zookeeper@hadoop101 .ssh]$ ssh-copy-id hadoop101
[zookeeper@hadoop101 .ssh]$ ssh-copy-id hadoop102
[zookeeper@hadoop101 .ssh]$ ssh-copy-id hadoop103
```

+ （b）还需要在hadoop102上采用zookeeper账号配置一下无密登录到hadoop101、hadoop102、hadoop103服务器上。这和hadoop102作为 ResourceManager  有关系。

```ini
#1、生成公钥和密钥
[zookeeper@hadoop102 .ssh]$ ssh-keygen -t rsa
#2、向三台机器配置公钥
[zookeeper@hadoop102 .ssh]$ ssh-copy-id hadoop101（2，3）
```

+ （c）hadoop101上采用root账号，配置一下无密登录到hadoop101、hadoop102、hadoop103。参考（b）方式即可。

（3）.ssh文件夹下的文件功能说明

| known_hosts     | 记录ssh访问过计算机的公钥(public  key) |
| --------------- | -------------------------------------- |
| id_rsa          | 生成的私钥                             |
| id_rsa.pub      | 生成的公钥                             |
| authorized_keys | 存放授权过得无密登录服务器公钥         |

#### 5、群起集群

（1）配置slaves文件

主要是配置从节点信息。

```ini
#路径:/home/zookeeper/software/hadoop-2.7.2/etc/hadoop/slaves
[zookeeper@hadoop101 hadoop]$ vim slaves
```

​		在该文件中增加下面的内容（localhost删除），同时也在hadoop102、
hadoop103文件中也这样配置。主要是配置（datanode和nodemanager的关系）

```ini
hadoop101
hadoop102
hadoop103
```

注意：该文件中添加的内容结尾不允许有空格和空行。

（2）启动集群

​		如果是第一次启动集群，需要格式化NameNode（只能再namenode节点上 hadoop101）。此时，**一定要先停止上次启动的所有namenode和datanode进程，然后再删除data和log数据**。不然会出现namenode节点启动失败的情况。

**说明：**启动集群的方式2：采用各个模块分开启动/停止（其中配置ssh是前提），是比较常用的一种方式。

+ （a）NameNode格式化

成功的格式化只能一次。

```ini
bin/hdfs namenode -format
```

+ （b）启动HDFS文件

```ini
#只需要在hadoop101中启动一次就可以
[zookeeper@hadoop101 hadoop-2.7.2]$ sbin/start-dfs.sh

#通过指令jps查看进程，和上表对应
hadoop101:NameNode、DataNode
hadoop102:DataNode
hadoop103:DataNode、SecondaryNameNode
```

+ （c）启动YARN

```ini
#只能在hadoop102机器上启动，hadoop101（NameNode）,hadoop103会报错。应该在hadoop102(ResourceManager主节点上执行)上启动。
[zookeeper@hadoop102 hadoop-2.7.2]$ sbin/start-yarn.sh
```

​		`NameNode`和`ResourceManger`的配置如果不是同一台机器，不能在`NameNode`上启动 `YARN`，应该在`ResouceManager`所在的机器上启动`YARN`。

+ （d）查看启动状态

浏览器输入：http://hadoop103:50090/status.html，可以查看`SecondaryNameNode`的信息。

web界面访问推荐使用：rescourcemanager主机名或ip：8088

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200722230720130.png" alt="image-20200722230720130" style="zoom:67%;" />

#### 6、集群基本测试

（1）上传文件到集群

```ini
#[zookeeper@hadoop101 hadoop-2.7.2]$
#1、创建文件夹，此时会在浏览器上显示。
bin/hdfs dfs -mkdir -p /user/zookeeper/input

#2、上传文件。此时wc.input要存在
bin/hdfs dfs -put wcinput/wc.input /user/zookeeper/input/
```

web浏览器上访问：

namenode主机名或ip：50070

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200722232032255.png" alt="image-20200722232032255" style="zoom:80%;" />

细节显示：

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200722232100497.png" alt="image-20200722232100497" style="zoom:80%;" />

（2）查看HDFS文件的存储路径

```ini
/home/zookeeper/software/hadoop-2.7.2/data/tmp/dfs/data/current/BP-2075475336-192.168.10.101-1595455626144/current/finalized/subdir0/subdir0
```

（3）HDFS文件在磁盘存储文件的内容

```ini
[zookeeper@hadoop102 subdir0]$ cat blk_1073741825
hadoop yarn
hadoop mapreduce
zookeeper
zookeeper
```

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200722232344716.png" alt="image-20200722232344716" style="zoom:67%;" />

### 3.3.3 集群时间同步

​		时间同步的方式：找一个机器，作为时间服务器，所有的机器与这台集群时间进行定时的同步，比如，每隔十分钟，同步一次时间。

<img src="D:\04桌面\Spring Boot学习\03学习文件\Hadoop学习.assets\image-20200723101758376.png" alt="image-20200723101758376" style="zoom:80%;" />

1、时间服务器配置

在root用户权限下进行配置。

（1）检查ntp是否安装

```ini
[root@hadoop101 ~]# rpm -qa|grep ntp
ntp-4.2.6p5-10.el6.centos.x86_64
fontpackages-filesystem-1.41-1.1.el6.noarch
ntpdate-4.2.6p5-10.el6.centos.x86_64
```

（2）修改ntp配置文件

```ini
vim /etc/ntp.conf
```

+ （a）授权192.168.1.0-192.168.1.255网段上的所有机器可以从hadoop101机器上查询和同步时间。

```ini
restrict 192.168.10.0 mask 255.255.255.0 nomodify notrap
```

+ （b）集群在局域网中，不使用其他互联网上的时间

```ini
#将下面内容注释
#server 0.centos.pool.ntp.org iburst
#server 1.centos.pool.ntp.org iburst
#server 2.centos.pool.ntp.org iburst
#server 3.centos.pool.ntp.org iburst
```

+ （c）当该节点丢失网络连接，依然可以采用本地时间作为时间服务器为集群中的其他节点提供时间同步。

```ini
#在该文件最后添加下面的文件
server 127.127.1.0
fudge 127.127.1.0 stratum 10
```

（3）修改/etc/sysconfig/ntpd 文件，增加下面的同步内容

```ini
#让硬件时间与系统时间一起同步
SYNC_HWCLOCK=yes
```

（4）启动ntpd服务

```ini
#查看ntpd服务
service ntpd status
#启动ntpd服务
service ntpd start

#设置ntpd服务开机启动
chkconfig ntpd on
```

2、其他机器配置。

同样也需要在root权限下进行配置。

（1）配置10分钟与时间服务器同步一次

```ini
#使用定时任务命令
crontab -e

#编写定时任务
*/10 * * * * /usr/sbin/ntpdate hadoop101
```

（2）测试，查看其他机器时间是否同步

```ini
#任意设置机器时间
date -s "2017-9-11 11:11:11"

#10分钟后，查看其他机器时间是否和时间服务器同步
date
```

