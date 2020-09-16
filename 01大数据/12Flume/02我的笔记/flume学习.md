## 1、介绍

1、概述

Flume 是 Cloudera 提供的一个高可用的，高可靠的，分布式的海量日志采集、聚合和传 输的系统。Flume 基于流式架构，灵活简单。

选用Flume的原因是Flume能够实时读取服务器本地磁盘的数据，将数据写入到HDFS。如下图所示：

![image-20200916095050769](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200916095050769.png)

2、组成架构

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200916095205716.png" alt="image-20200916095205716" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200916095226216.png" alt="image-20200916095226216" style="zoom:80%;" />

（1）Agent

Agent 是一个 JVM 进程，它以事件的形式将数据从源头送至目的，是 Flume 数据传输 的基本单元。 Agent 主要有 3 个部分组成，Source、Channel、Sink。

（2）Source

Source 是负责接收数据到 Flume Agent 的组件。Source 组件可以处理各种类型、各种格 式的日志数据，包括 avro、thrift、exec、jms、spooling directory、netcat、sequence generator、 syslog、http、legacy。

（3）Channel

Channel 是位于 Source 和 Sink 之间的缓冲区。因此，Channel 允许 Source 和 Sink 运作 在不同的速率上。Channel 是线程安全的，可以同时处理几个 Source 的写入操作和几个 Sink 的读取操作。 Flume 自带两种 Channel：Memory Channel 和 File Channel。 

Memory Channel 是内存中的队列。Memory Channel 在不需要关心数据丢失的情景下适 用。如果需要关心数据丢失，那么 Memory Channel 就不应该使用，因为程序死亡、机器宕 机或者重启都会导致数据丢失。 

File Channel 将所有事件写到磁盘。因此在程序关闭或机器宕机的情况下不会丢失数据。

（4）Sink

Sink 不断地轮询 Channel 中的事件且批量地移除它们，并将这些事件批量写入到存储或 索引系统、或者被发送到另一个 Flume Agent。 

Sink 是完全事务性的。在从 Channel 批量删除数据之前，每个 Sink 用 Channel 启动一 个事务。批量事件一旦成功写出到存储系统或下一个 Flume Agent，Sink 就利用 Channel 提 交事务。事务一旦被提交，该 Channel 从自己的内部缓冲区删除事件。 Sink 组件目的地包括 hdfs、logger、avro、thrift、ipc、file、null、HBase、solr、自定义。

（5）Event

传输单元，Flume 数据传输的基本单元，以事件的形式将数据从源头送至目的地。

2、拓扑结构

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200916095601532.png" alt="image-20200916095601532" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200916095611165.png" alt="image-20200916095611165" style="zoom:67%;" />



<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200916095644593.png" alt="image-20200916095644593" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200916095656877.png" alt="image-20200916095656877" style="zoom:67%;" />

3、内部原理

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200916095749976.png" alt="image-20200916095749976" style="zoom: 67%;" />

## 2、安装和部署

Flume 官网地址：

```http
 http://flume.apache.org/ 
```

文档查看地址：

```http
 http://flume.apache.org/FlumeUserGuide.html
```

下载地址 ：

```http
http://archive.apache.org/dist/flume/
```

1、安装和部署

（1）将 apache-flume-1.7.0-bin.tar.gz 上传到 linux 的/myflume 目录下

（2）将 flume/conf 下的 flume-env.sh.template 文件修改为 flume-env.sh，并配置 flumeenv.sh 文件

```shell
export JAVA_HOME=/opt/jdk1.8.0_251
```

2、测试-官方案例

（1）需求

首先，Flume 监控本机 44444 端口，然后通过 telnet 工具向本机 44444 端口发送消息，最后 Flume 将监听的数据实时显示在控制台。

（2）安装telnet工具

将 rpm 软件包(xinetd-2.3.14-40.el6.x86_64.rpm、telnet-0.17-48.el6.x86_64.rpm 和 telnetserver-0.17-48.el6.x86_64.rpm)拷入/opt/software 文件夹下面。

在该文件下执行 RPM 软件包安装命令：

```
rpm -ivh xinetd-2.3.14-40.el6.x86_64.rpm
rpm -ivh telnet-0.17-48.el6.x86_64.rpm
rpm -ivh telnet-server-0.17-48.el6.x86_64.rpm
```

（3）查看本机的44444端口是否被占用

```shell
netstat -tunlp | grep 44444
```

参数介绍：

```
基本语法：netstat [选项]

选项参数：
t 或--tcp：显示 TCP 传输协议的连线状况；
-u 或--udp：显示 UDP 传输协议的连线状况；
-n 或--numeric：直接使用 ip 地址，而不通过域名服务器；
-l 或--listening：显示监控中的服务器的 Socket；
-p 或--programs：显示正在使用 Socket 的程序识别码和程序名称；
```

（4）在 flume 目录下创建 job 文件夹，并在该文件夹中创建配置文件flume-telnet-logger.conf，并进行如下的配置：

```ini
# Name the components on this agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1
# Describe/configure the source
a1.sources.r1.type = netcat
a1.sources.r1.bind = localhost
a1.sources.r1.port = 44444
# Describe the sink
a1.sinks.k1.type = logger
# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100
# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200916100813507.png" alt="image-20200916100813507" style="zoom:80%;" />

（5）先开启flume监听44444端口

```shell
bin/flume-ng agent --conf conf/ --name a1 --conf-file job/flume-telnet-logger.conf -Dflume.root.logger=INFO,console
```

参数说明：

```
--conf conf/ ：表示配置文件存储在 conf/目录
--name a1 ：表示给 agent 起名为 a1
--conf-file job/flume-telnet.conf ：flume 本次启动读取的配置文件是在 job 文件夹下的 flume-telnet.conf 文件。

-Dflume.root.logger==INFO,console ： -D 表 示 flume 运 行时动态修改flume.root.logger 参数属性值，并将控制台日志打印级别设置为 INFO 级别。日志级别包括:log、info、warn、error。
```

（6）重新开启一个hadoop101端口，使用telnet工具向本机的44444端口发送内容

```shell
telnet localhost 44444
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200916101234274.png" alt="image-20200916101234274" style="zoom:80%;" />

（7）在 Flume 监听页面观察接收数据情况

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200916101254128.png" alt="image-20200916101254128" style="zoom:67%;" />

## 3、案例

1、实时读取本地文件到 HDFS

（1）需求

实时监控 Hive 日志，并上传到 HDFS 中。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200916104227819.png" alt="image-20200916104227819" style="zoom:67%;" />

（2）向flume/lib添加hadoop的jar包

```
commons-configuration-1.6.jar、
hadoop-auth-2.7.2.jar、
hadoop-common-2.7.2.jar、
hadoop-hdfs-2.7.2.jar、
commons-io-2.4.jar、
htrace-core-3.1.0-incubating.jar
```

（3）在flume/job目录下创建配置文件flume-file-hdfs.conf

```ini
# Name the components on this agent
a2.sources = r2
a2.sinks = k2
a2.channels = c2
# Describe/configure the source
a2.sources.r2.type = exec
# 要监控的文件
a2.sources.r2.command = tail -F /tmp/zookeeper/hive.log
a2.sources.r2.shell = /bin/bash -c
# Describe the sink
a2.sinks.k2.type = hdfs
a2.sinks.k2.hdfs.path = hdfs://hadoop101:9000/flume/%Y%m%d/%H
#上传文件的前缀
a2.sinks.k2.hdfs.filePrefix = logs-
#是否按照时间滚动文件夹
a2.sinks.k2.hdfs.round = true
#多少时间单位创建一个新的文件夹
a2.sinks.k2.hdfs.roundValue = 1
#重新定义时间单位
a2.sinks.k2.hdfs.roundUnit = hour
#是否使用本地时间戳
a2.sinks.k2.hdfs.useLocalTimeStamp = true
#积攒多少个 Event 才 flush 到 HDFS 一次
a2.sinks.k2.hdfs.batchSize = 1000
#设置文件类型，可支持压缩
a2.sinks.k2.hdfs.fileType = DataStream
#多久生成一个新的文件
a2.sinks.k2.hdfs.rollInterval = 600
#设置每个文件的滚动大小
a2.sinks.k2.hdfs.rollSize = 134217700
#文件的滚动与 Event 数量无关
a2.sinks.k2.hdfs.rollCount = 0
#最小冗余数
a2.sinks.k2.hdfs.minBlockReplicas = 1
# Use a channel which buffers events in memory
a2.channels.c2.type = memory
a2.channels.c2.capacity = 1000
a2.channels.c2.transactionCapacity = 100
# Bind the source and sink to the channel
a2.sources.r2.channels = c2
a2.sinks.k2.channel = c2
```

（4）启动hadoop

（5）启动监控配置

```shell
[zookeeper@hadoop101 flume]$ bin/flume-ng agent --conf conf/ --name a2 --conf-file job/flume-file-hdfs.conf
```

（6）在HDFS上查看文件

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200916104527340.png" alt="image-20200916104527340" style="zoom:67%;" />

2、单数据源多出口案例(选择器)

（1）需求

使用 Flume-1 监控文件变动，Flume-1 将变动内容传递给 Flume-2，Flume-2 负 责存储到 HDFS。同时 Flume-1 将变动内容传递给 Flume-3，Flume-3 负责输出到 Local FileSystem。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200916203107480.png" alt="image-20200916203107480" style="zoom:67%;" />

（2）在job/group1中创建配置文件flume-file-flume.conf

配置 1 个接收日志文件的 source 和两个 channel、两个 sink，分别输送给 flume-flumehdfs 和 flume-flume-dir。

```ini
# Name the components on this agent
a1.sources = r1
a1.sinks = k1 k2
a1.channels = c1 c2
# 将数据流复制给所有 channel
a1.sources.r1.selector.type = replicating
# Describe/configure the source
a1.sources.r1.type = exec
a1.sources.r1.command = tail -F /tmp/zookeeper/hive.log
a1.sources.r1.shell = /bin/bash -c
# Describe the sink
a1.sinks.k1.type = avro
a1.sinks.k1.hostname = hadoop101
a1.sinks.k1.port = 4141
a1.sinks.k2.type = avro
a1.sinks.k2.hostname = hadoop101
a1.sinks.k2.port = 4142
# Describe the channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100
a1.channels.c2.type = memory
a1.channels.c2.capacity = 1000
a1.channels.c2.transactionCapacity = 100
# Bind the source and sink to the channel
a1.sources.r1.channels = c1 c2
a1.sinks.k1.channel = c1
a1.sinks.k2.channel = c2
```

Avro 是由 Hadoop 创始人 Doug Cutting 创建的一种语言无关的数据序列化和 RPC 框 架。RPC（Remote Procedure Call）—远程过程调用，它是一种通过网络从远程计算机程 序上请求服务，而不需要了解底层网络技术的协议。

（3）创建 flume-flume-hdfs.conf

配置上级 Flume 输出的 Source，输出是到 HDFS 的 Sink。

```ini
# Name the components on this agent
a2.sources = r1
a2.sinks = k1
a2.channels = c1
# Describe/configure the source
a2.sources.r1.type = avro
a2.sources.r1.bind = hadoop101
a2.sources.r1.port = 4141
# Describe the sink
a2.sinks.k1.type = hdfs
a2.sinks.k1.hdfs.path = hdfs://hadoop101:9000/flume2/%Y%m%d/%H
#上传文件的前缀
a2.sinks.k1.hdfs.filePrefix = flume2-
#是否按照时间滚动文件夹
a2.sinks.k1.hdfs.round = true
#多少时间单位创建一个新的文件夹
a2.sinks.k1.hdfs.roundValue = 1
#重新定义时间单位
a2.sinks.k1.hdfs.roundUnit = hour
#是否使用本地时间戳
a2.sinks.k1.hdfs.useLocalTimeStamp = true
#积攒多少个 Event 才 flush 到 HDFS 一次
a2.sinks.k1.hdfs.batchSize = 100
#设置文件类型，可支持压缩
a2.sinks.k1.hdfs.fileType = DataStream
#多久生成一个新的文件
a2.sinks.k1.hdfs.rollInterval = 600
#设置每个文件的滚动大小大概是 128M
a2.sinks.k1.hdfs.rollSize = 134217700
#文件的滚动与 Event 数量无关
a2.sinks.k1.hdfs.rollCount = 0
#最小冗余数
a2.sinks.k1.hdfs.minBlockReplicas = 1
# Describe the channel
a2.channels.c1.type = memory
a2.channels.c1.capacity = 1000
a2.channels.c1.transactionCapacity = 100
# Bind the source and sink to the channel
a2.sources.r1.channels = c1
a2.sinks.k1.channel = c1
```

（4）创建 flume-flume-dir.conf

配置上级 Flume 输出的 Source，输出是到本地目录的 Sink

```ini
# Name the components on this agent
a3.sources = r1
a3.sinks = k1
a3.channels = c2
# Describe/configure the source
a3.sources.r1.type = avro
a3.sources.r1.bind = hadoop101
a3.sources.r1.port = 4142
# Describe the sink
a3.sinks.k1.type = file_roll
a3.sinks.k1.sink.directory =/home/zookeeper/software/flumeLog
# Describe the channel
a3.channels.c2.type = memory
a3.channels.c2.capacity = 1000
a3.channels.c2.transactionCapacity = 100
# Bind the source and sink to the channel
a3.sources.r1.channels = c2
a3.sinks.k1.channel = c2
```

输出的本地目录必须是已经存在的目录，如果该目录不存在，并不会创建新的目录。

（5）依次执行配置文件

```ini
bin/flume-ng agent --conf conf/ --name a3 --conf-file job/group1/flume-flume-dir.conf

bin/flume-ng agent --conf conf/ --name a2 --conf-file job/group1/flume-flume-hdfs.conf

bin/flume-ng agent --conf conf/ --name a1 --conf-file job/group1/flume-file-flume.conf
```

（6）启动hadoop和hive，查看相应的日志信息。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200916205539555.png" alt="image-20200916205539555" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200916205601927.png" alt="image-20200916205601927" style="zoom:67%;" />





