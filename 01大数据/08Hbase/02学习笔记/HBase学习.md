# 1 介绍

HBase是一种分布式，可扩展，支持海量数据村塾的NoSQL数据库。从HBase的底层物理存储结果（k-v）来看，HBase更像一个`multi-dimensional map`。

## 1.1 Hbase逻辑结构

![image-20200805111218898](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200805111218898.png)

## 1.2 Hbase逻辑结构结构

![image-20200805111502497](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200805111502497.png)

## 1.3 数据模型

1、Name Space 

（1）命名空间

类似于关系型数据库的 DatabBase 概念，每个命名空间下有多个表。HBase有两个自带的命名空间，分别是 hbase 和 default，hbase 中存放的是 HBase 内置的表，default 表是用户默认使用的命名空间。 

表和命名空间的隶属关系在创建的时候通过如下的方式决定：

```shell
#Create a namespace
create_namespace 'my_ns'

#create my_table in my_ns namespace
create ' my_ns:my_table', 'fam'

#drop namespace
drop_namespace 'my_ns'

#alter namespace
alter_namespace 'my_ns', {METHOD => 'set', 'PROPERTY_NAME' => 'PROPERTY_VALUE'}
```

（2）预定义的命名空间
有两个系统内置的预定义命名空间：
hbase：系统命名空间，用于包含hbase的内部表。
default：所有未指定命名空间的表都自动进入该命名空间(默认的)。

2、 Region  

类似于关系型数据库的表概念。不同的是，HBase 定义表时只需要声明列族即可，不需要声明具体的列。

这意味着，往 HBase 写入数据时，字段可以动态、按需指定。因此，和关系型数据库相比，HBase 能够轻松应对字段变更的场景。

一个命名空间或者一张表，可以被固定到一组RegionServers上。

3、Row  

HBase 表中的每行数据都由一个  `RowKey`  和多个  `Column`（列）组成，数据是按照 RowKey的字典顺序存储的， 并且查询数据时只能根据 `RowKey` 进行检索， 所以 `RowKey` 的设计十分重要。 

4、Column  

HBase 中的每个列都由 `Column Family`(列族)和 `Column Qualifier`（列限定符）进行限定，例如 info：name，info：age。建表时，只需指明列族，而列限定符无需预先定义。 

5、Time   Stamp 

用于标识数据的不同版本（version），每条数据写入时，如果不指定时间戳，系统会自动为其加上该字段，其值为写入 HBase 的时间。

每个单元格都保存着同一份数据的多个版本，这些版本采用时间戳进行索引。

6、 Cell

由`{rowkey, column Family：column Qualifier, time Stamp}` 唯一确定的单元。cell 中的数据是没有类型的，全部是字节码形式存贮。

## 1.4 HBase基本架构

![image-20200805112355336](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200805112355336.png)

具体的话，参考下面的博客：

```http
https://blog.csdn.net/lavorange/article/details/82775275
https://www.jianshu.com/p/53864dc3f7b4
```

1、架构角色

（1）Region   Server  

`Region Server` 为 `Region` 的管理者，其实现类为 `HRegionServer`，主要作用如下: 

（a）对于**数据**的操作：`get, put（增，改）, delete`； 

（b）对于 Region 的操作：`splitRegion（切分）、compactRegion（合并）`。 

（2）Master

Master 是所有 Region Server 的管理者，其实现类为 HMaster，主要作用如下： 

（a）对于**表**的操作：`create, delete, alter` 

（b）对于`RegionServer`的操作： 分配`regions`到每个`RegionServer`， 监控每个`RegionServer`的状态，负载均衡和故障转移。 

（3） Zookeeper  

`HBase` 通过 `Zookeeper` 来做 `Master` 的高可用、`RegionServer` 的监控、元数据的入口以及集群配置的维护等工作。 

（4） HDFS  

`HDFS` 为 `HBase` 提供最终的底层数据存储服务，同时为 `HBase` 提供高可用的支持。 

# 2 HBase安装和部署

## 1.1、安装

1、zookeeper正常部署

**首先保证 Zookeeper 集群的正常部署，并启动之**：

```ini
[zookeeper@hadoop101 zookeeper-3.4.10]$ 
 bin/zkServer.sh start
 [zookeeper@hadoop102 zookeeper-3.4.10]$ 
 bin/zkServer.sh start
 [zookeeper@hadoop103 zookeeper-3.4.10]$ 
 bin/zkServer.sh start
```

2、hadoop正常部署并启动之

```ini
[zookeeper@hadoop101 hadoop-2.7.2]$ sbin/start-dfs.sh 
[zookeeper@hadoop102 hadoop-2.7.2]$ sbin/start-yarn.sh 
```

3、HBase安装

（1）解压

```ini
#解压路径
/home/zookeeper/software/hbase-1.3.1
```

（2）修改HBase配置文件

（a）hbase-env.sh 修改内容

```ini
export JAVA_HOME=/opt/jdk1.8.0_251
export HBASE_MANAGES_ZK=false 
```

（b）hbase-site.xml修改内容

```xml
<configuration> 
  <property> 
   <name>hbase.rootdir</name> 
   <value>hdfs://hadoop101:9000/HBase</value> 
  </property> 
 
  <property> 
   <name>hbase.cluster.distributed</name> 
   <value>true</value> 
  </property> 
 
<!-- 0.98 后的新变动，之前版本没有.port,默认端口为 60000 --> 
  <property> 
   <name>hbase.master.port</name> 
   <value>16000</value> 
  </property> 
 
  <property>    
   <name>hbase.zookeeper.quorum</name> 
   <value>hadoop101,hadoop102,hadoop103</value>
  </property> 
 
  <property>    
   <name>hbase.zookeeper.property.dataDir</name> 
   <value>/home/zookeeper/software/zookeeper-3.4.10/HBaseData</value> 
  </property> 
  
  <property>
    <name>zookeeper.znode.parent</name>
    <value>/hbase</value>
	</property>
  
</configuration> 
```

（c）regionservers修改内容

```ini
hadoop102 
hadoop103 
hadoop104
```

（d）软连接 hadoop 配置文件到 HBase

```ini
ln -s /home/zookeeper/software/hadoop-2.7.2/etc/hadoop/core-site.xml  /home/zookeeper/software/hbase-1.3.1/conf/core-site.xml

ln -s /home/zookeeper/software/hadoop-2.7.2/etc/hadoop/hdfs-site.xml  /home/zookeeper/software/hbase-1.3.1/conf/hdfs-site.xml
```

（e）远程将配置发送到其他集群

```ini
scp -r ./hbase-1.3.1 zookeeper@hadoop102:/home/zookeeper/software/
```

## 1.2、启动

1、单节点启动

```ini
[zookeeper@hadoop101 hbase-1.3.1]$ bin/hbase-daemon.sh start master 
[zookeeper@hadoop102 hbase-1.3.1]$ bin/hbase-daemon.sh start regionserver
[zookeeper@hadoop103 hbase-1.3.1]$ bin/hbase-daemon.sh start regionserver
```

提示：如果集群之间的节点时间不同步，会导致 regionserver 无法启动，抛出
ClockOutOfSyncException 异常。 我们可以执行下面的操作：

（1）同步时间服务 

（2）更改属性：hbase.master.maxclockskew 设置更大的值 

```xml
<property> 
        <name>hbase.master.maxclockskew</name> 
        <value>180000</value> 
        <description>Time difference of regionserver from 
master</description> 
</property> 
```

2、群起集群

```ini
[zookeeper@hadoop101 hbase-1.3.1]$ bin/start-hbase.sh

#关闭集群
[zookeeper@hadoop101 hbase-1.3.1]$ bin/stop-hbase.sh 
```

3、查看集群状态

（1）使用下面命令

```ini
jps

#hadoop101
HMaster
HRegionServer

#hadoop102
HRegionServer

#hadoop103
HRegionServer
```

（2）通过“host:port”的方式来访问 HBase 管理页面

```http
http://hadoop101:16010
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200805145435561.png" alt="image-20200805145435561" style="zoom:80%;" />

注意：要首先启动zookeeper。

## 1.3、HBase Shell操作

1、基本操作

（1）进入HBase客户端命令行

```ini
[zookeeper@hadoop101 hbase-1.3.1]$$ bin/hbase shell
```

（2）查看帮助命令

```ini
hbase(main):001:0> help 
```

（3）查看当前数据库中有哪些表

```ini
hbase(main):002:0> list 
```

2、表的操作

（1）创建表

```ini
# 'student01','info' :表名，列族
hbase(main):002:0> create 'student01','info' 
```

（2）插入数据到表 

```ini
hbase(main):003:0> put 'student01','1001','info:sex','male' 
hbase(main):004:0> put 'student01','1001','info:age','18' 
hbase(main):005:0> put 'student01','1002','info:name','Janna' 
hbase(main):006:0> put 'student01','1002','info:sex','female' 
hbase(main):007:0> put 'student01','1002','info:age','20'
```

（3）扫描查看表结构

```ini
hbase(main):008:0> scan 'student01'
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200805151732525.png" alt="image-20200805151732525" style="zoom:80%;" />

```ini
#STARTROW,STOPROW要大写。{}为查看的附加条件，左右包含
hbase(main):003:0> scan 'student01',{STARTROW => '1001',STOPROW => '1001'}
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200805151759165.png" alt="image-20200805151759165" style="zoom:80%;" />

```ini
hbase(main):004:0> scan 'student01',{STARTROW => '1001'}
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200805151916514.png" alt="image-20200805151916514" style="zoom:80%;" />

（4）更新指定字段的数据

```ini
hbase(main):012:0> put 'student01','1001','info:name','Nick' 
hbase(main):013:0> put 'student01','1001','info:age','100'
```

（5）查看“指定行”或者“指定列族：列”的数据

```ini
hbase(main):014:0> get 'student01','1001' 
hbase(main):015:0> get 'student01','1001','info:name'
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200805152630917.png" alt="image-20200805152630917" style="zoom:80%;" />

（6）查看表结构 

```ini
hbase(main):011:0> describe ‘student01’
```

对于版本VERSIONS的理解：

（a）（VERSIONS => '1'）VERSIONS为1，也就是说，默认情况只会存取一个版本的列数据，当再次插入的时候，后面的值会覆盖前面的值。

（b）修改表结构，让Hbase表支持存储3个VERSIONS的版本列数据。

```shell
alter 'student01',{NAME=>'info',VERSIONS=>3}
```

再次查看表结构：

```shell
VERSIONS => '3'
```

插入3行数据：

```shell
# t1 即为 student01；f1 为info
hbase(main):015:0> put 't1','1002','f1:name','a'
0 row(s) in 0.5890 seconds
hbase(main):016:0> put 't1','1002','f1:name','b'
0 row(s) in 0.1900 seconds
hbase(main):017:0> put 't1','1002','f1:name','c'
0 row(s) in 0.1580 seconds
hbase(main):018:0> get 't1','1002','f1:name'

#插入了3行数据到表中，并且3行数据的rowkey一致，然后使用get命令来获取这一行数据，发现只返回了最新的一行数据。
COLUMN      CELL                  
f1:name    timestamp=1482820567560, value=c      
```

获取多行数据方法：

```shell
#假设VERSIONS=>4，获取的数据还是如下，因为，在alter中更改为3,即最大只能为3。
#假设VERSIONS=>2，获取的数据只有最新的value=c(b)两行。
hbase(main):002:0> get 't1','1002',{COLUMN=>'f1:name',VERSIONS=>3}
COLUMN                           CELL                       
f1:name timestamp=1482820567560,value=c           
f1:name timestamp=1482820541363,value=b       
f1:name timestamp=1482820503889,value=a
3 row(s) in 0.0960 seconds
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200806161248525.png" alt="image-20200806161248525" style="zoom:80%;" />

（7）统计表数据行数

```ini
hbase(main):021:0> count 'student01' 
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200805152842621.png" alt="image-20200805152842621" style="zoom:80%;" />

（8）删除数据 

删除某 rowkey 的全部数据：

```ini
hbase(main):016:0> deleteall 'student01','1001'
```

删除某 rowkey 的某一列数据： 

```ini
hbase(main):017:0> delete 'student01','1002','info:sex'
```

（9）清空表数据 

```ini
#清空表的操作顺序为先 disable，然后再 truncate。
hbase(main):018:0> truncate 'student01'
```

（10）删除表 

```ini
#首先需要先让该表为 disable 状态：
hbase(main):019:0> disable 'student01' 
#然后才能 drop 这个表(如果直接 drop 表，会报错：ERROR: Table student is enabled. Disable it first. )
hbase(main):020:0> drop 'student01'
```

（11）变更表信息 

将 info 列族中的数据存放 3 个版本： 

```ini
hbase(main):022:0> alter 'student01',{NAME=>'info',VERSIONS=>3} 
hbase(main):022:0>  get 'student01','1001',{COLUMN=>'info:name',VERSIONS=>3}
```

# 3 HBase进阶

## 3.1、架构原理

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200805154912843.png" alt="image-20200805154912843" style="zoom:80%;" />

1、StoreFile

保存实际数据的物理文件，StoreFile 以 HFile 的形式存储在 HDFS 上。每个 Store 会有一个或多个 StoreFile（HFile），数据在每个 StoreFile 中都是有序的。 

2、MemStore

写缓存，由于 HFile 中的数据要求是有序的，所以数据是先存储在 MemStore 中，排好序后，等到达刷写时机才会刷写到 HFile，每次刷写都会形成一个新的 HFile。 

3、WAL

由于数据要经 MemStore 排序后才能刷写到 HFile，但把数据保存在内存中会有很高的概率导致数据丢失，为了解决这个问题，数据会先写在一个叫做 Write-Ahead logfile （预写入日志）的文件中，然后再写入 MemStore 中。所以在系统出现故障的时候，数据可以通过这个日志文件重建。 

## 3.2、写流程

![image-20200805155747920](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200805155747920.png)

写流程：

 （1）Client 先访问 zookeeper，获取 hbase:meta 表位于哪个 Region Server。 

（2）访问对应的 Region Server，获取 hbase:meta 表，根据读请求的 namespace:table/rowkey，查询出目标数据位于哪个 Region Server 中的哪个 Region 中。并将该 table 的 region 信息以及 meta 表的位置信息缓存在客户端的 meta cache，方便下次访问。 

（3）与目标 Region Server 进行通讯； 

（4）将数据顺序写入（追加）到 WAL； 

（5）将数据写入对应的 MemStore，数据会在 MemStore 进行排序； 

（6）向客户端发送 ack； 

（7）等达到 MemStore 的刷写时机后，将数据刷写到 HFile。 

## 3.3、Flush流程

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200805163123432.png" alt="image-20200805163123432" style="zoom:80%;" />

MemStore  刷写时机： 

1、 当某个 `memstroe` 的大小达到了 `hbase.hregion.memstore.flush.size` （默认值 128M ），其所在 region 的所有 memstore 都会刷写。 
当 memstore 的大小达到了 

```ini
hbase.hregion.memstore.flush.size （默认值 128M ） 
hbase.hregion.memstore.block.multiplier （默认值 4 ） 
```

时，会阻止继续往该 memstore 写数据。 

2、当 region server 中 memstore 的总大小达到 `java_heapsize` 

```ini
hbase.regionserver.global.memstore.size （默认值 0.4 ）

hbase.regionserver.global.memstore.size.lower.limit （默认值 0.95 ）
```

region 会按照其所有 memstore 的大小顺序 （由大到小） 依次进行刷写。 直到 region server中所有 memstore 的总大小减小到上述值以下。 

当 region server 中 memstore 的总大小达到 `java_heapsize`

```ini
hbase.regionserver.global.memstore.size （默认值 0.4 ）
```

时，会阻止继续往所有的 memstore 写数据。 

3、到达自动刷写的时间，也会触发 memstore flush。自动刷新的时间间隔由该属性进行配置 

```ini
hbase.regionserver.optionalcacheflushinterval （默认 1 小时）
```

4、当 WAL 文件的数量超过 hbase.regionserver.max.logs，region 会按照时间顺序依次进行刷写，直到 WAL 文件数量减小到 hbase.regionserver.max.log 以下（该属性名已经废弃，现无需手动设置，最大值为 32）。 

## 3.4、读流程

![image-20200805164204421](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200805164204421.png)

（1）Client 先访问 zookeeper，获取 hbase:meta 表位于哪个 Region Server。 

（2）访问对应的 Region Server，获取 hbase:meta 表，根据读请求的 namespace:table/rowkey，查询出目标数据位于哪个 Region Server 中的哪个 Region 中。并将该 table 的 region 信息以及 meta 表的位置信息缓存在客户端的 meta cache，方便下次访问。 

（3）与目标 Region Server 进行通讯； 

（4）分别在 Block Cache（读缓存），MemStore 和 Store File（HFile）中查询目标数据，并将查到的所有数据进行合并。此处所有数据是指同一条数据的不同版本（time stamp）或者不同的类型（Put/Delete）。 

（5） 将从文件中查询到的数据块（Block，HFile 数据存储单元，默认大小为 64KB）缓存到 Block Cache。 

（6）将合并后的最终结果返回给客户端。 

## 3.5、 StoreFile Compaction 

​		由于memstore每次刷写都会生成一个新的HFile， 且同一个字段的不同版本 （timestamp）和不同类型 （Put/Delete） 有可能会分布在不同的HFile中， 因此查询时需要遍历所有的HFile。

​		为了减少 HFile 的个数，以及清理掉过期和删除的数据，会进行 StoreFile Compaction。 

​		Compaction 分为两种， 分别是 Minor Compaction 和 Major Compaction。 Minor Compaction会将临近的若干个较小的 HFile 合并成一个较大的 HFile，但 不会清理过期和删除的数据。Major Compaction 会将一个 Store 下的所有的 HFile 合并成一个大 HFile，并且 会清理掉过期和删除的数据。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200805164634789.png" alt="image-20200805164634789" style="zoom:80%;" />

## 3.6、Region Split

​		默认情况下，每个 Table 起初只有一个 Region，随着数据的不断写入，Region 会自动进行拆分。刚拆分时，两个子 Region 都位于当前的 Region Server，但处于负载均衡的考虑，HMaster 有可能会将某个 Region 转移给其他的 Region Server。 

Region Split 时机： 

1、当1个region中的某个Store下所有StoreFile的总大小超过hbase.hregion.max.filesize，该 Region 就会进行拆分（0.94 版本之前）。 

2、当 1 个 region 中 的某 个 Store 下所有 StoreFile 的总 大 小超过 Min(R^2 * 
"hbase.hregion.memstore.flush.size",hbase.hregion.max.filesize")，该 Region 就会进行拆分，其中 R 为当前 Region Server 中属于该 Table 的个数（0.94 版本之后）。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200805164824790.png" alt="image-20200805164824790" style="zoom:80%;" />

# 4 HBase API

## 4.1、提前工作

### 1、连接HBase服务器

1、导入依赖

```xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-client</artifactId>
    <version>1.3.1</version>
</dependency>
```

2、配置本地的hosts

```ini
C:\Windows\System32\drivers\etc\hosts
```

3、连接到HBase服务器

（1）配置属性连接服务器

```java
//测试连接
public class HBase_connect {
   public static void main(String[] args) {
      //1、获取配置对象
      Configuration conf = new Configuration();
      //2、设置连接HBase的参数
      conf.set("hbase.zookeeper.quorum", "hadoop102,hadoop103,hadoop101");

      //3、获取核心对象Admin
      HBaseAdmin admin = null;
      try {
         admin = new HBaseAdmin(conf);
         //4、通过Admin来测试（表fruit是否存在）
         boolean exists = admin.tableExists("fruit");
         System.out.println("exists --> "+ exists); //exists --> true
      } catch (IOException e) {
         e.printStackTrace();
      }finally {
         try {
            admin.close();
         } catch (IOException e) {
            e.printStackTrace();
         }
      }
   }
}
```

（2）配置文件连接服务器

（a）在resources文件中导入hbase-site.xml配置文件，该文件一般从集群中的配置文件中复制而来。

（b）连接hbase服务器

```java
package com.xiaolun.demoTest01;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.TableName;
import org.apache.hadoop.hbase.client.Admin;
import org.apache.hadoop.hbase.client.Connection;
import org.apache.hadoop.hbase.client.ConnectionFactory;

import java.io.IOException;

//测试连接
public class HBase_connect {
   public static void main(String[] args) throws Exception {

      // 0.先获取HadoopAPI中的Configuration对象
      //Configuration hconf = new Configuration();
      // 1.获取HBaseConfiguration对象
      //Configuration conf = HBaseConfiguration.create(hconf);
     
     //简单方式来连接
     Configuration conf = HBaseConfiguration.create();
      // 2.获取Connection对象
      Connection connection = ConnectionFactory.createConnection(conf);
      // 3.获取Admin对象
      Admin admin = connection.getAdmin();
      try {
         //4、通过Admin来测试（表fruit是否存在）
         boolean exists = admin.tableExists(TableName.valueOf("fruit"));
         System.out.println("exists --> " + exists); //exists --> true
      } catch (IOException e) {
         e.printStackTrace();
      } finally {
         try {
            admin.close();
         } catch (IOException e) {
            e.printStackTrace();
         }
      }
   }
}
```

### 2、设计模板类

```java
public class HBaseUtil {
   HBaseAdmin admin = null;

   @Before
   public void Before() throws Exception {
      //1、获取配置对象
      Configuration conf = new Configuration();
      //2、设置连接HBase的参数
      conf.set("hbase.zookeeper.quorum", "hadoop102,hadoop103,hadoop101");
      //3、提取核心对象
      admin = (HBaseAdmin) 			 ConnectionFactory.createConnection(conf).getAdmin();
   }

   @After
   public void after() throws Exception {
      admin.close();
   }
}
```

### 3、提取工具类

```java
public class HBaseUtil {
	private final static String HBASE_KEY = "hbase.zookeeper.quorum";
	private final static String HBASE_VALUE = "hadoop102,hadoop103,hadoop101";

	//将 static中的 connection 提取出来，以便getAdmin能够拿到该对象
	private static Connection connection;

	static {
		try {
			//获取配置对象
			Configuration conf = new Configuration();
			//设置连接HBase的参数
			conf.set(HBASE_KEY, HBASE_VALUE);
			//获取连接对象
			connection = ConnectionFactory.createConnection(conf);
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

	public static HBaseAdmin getAdmin() {
		//异常在这里进行处理，不要抛出去
		try {
			//提取核心对象 admin，使用该类，可以执行管理员任务
			HBaseAdmin admin = (HBaseAdmin) connection.getAdmin();
			return admin;
		} catch (IOException e) {
			e.printStackTrace();
		}
		return null;
	}

	public static Table getTable() {
		//获取默认的表
		return getTable("DefaultTable");
	}

	public static Table getTable(String tableName) {
		Table table = null;
		try {
			//如果为空(""或者null)，返回为null
			if (StringUtils.isEmpty(tableName)){
        return null;
      } 

			//获取table对象
			table = connection.getTable(TableName.valueOf(tableName));
		} catch (IOException e) {
			e.printStackTrace();
		}
		return table;
	}

	public static void close(Admin admin) {
		try {
			if (admin != null) {
				admin.close();
			}
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

	public static void close(Table table) {
		try {
			if (table != null) {
				table.close();
			}
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}
```

重构HBaseUtil：

```java
//Scan封装
public static void showScan(Table table, Scan scan) {
   try {
      //结果扫描器
      ResultScanner scanner = table.getScanner(scan);
      //5、迭代
      Iterator<Result> iterator = scanner.iterator();
      while (iterator.hasNext()) {
         Result result = iterator.next();
         HBaseUtil.showResult(result);
      }

   } catch (IOException e) {
      e.printStackTrace();
   }
}

//showResult
private static void showResult(Result result) {
  //表格扫描器
  CellScanner cellScanner = result.cellScanner();
  try {
    //遍历扫描器
    while (cellScanner.advance()) {
      //获取一个表格
      Cell cell = cellScanner.current();
      //行键 列族 列名 列值
      System.out.println(new String(CellUtil.cloneRow(cell), "utf-8") + "\t" +
                         new String(CellUtil.cloneFamily(cell), "utf-8")
                         + "\t" + new String(CellUtil.cloneQualifier(cell), "utf-8") + "\t" +
                         new String(CellUtil.cloneValue(cell), "utf-8"));
    }
  } catch (IOException e) {
    e.printStackTrace();
  }
}
```

再次重构HBaseUtil：

```java
/**
 *展示过滤器的输出结果
 * @param filter 过滤器
 * @param tableName 表名
 */
public static void showFilter(Filter filter,String tableName){
   //扫描整个表
   Scan scan = new Scan();
   //设置过滤器
   scan.setFilter(filter);
   //获取表 （主要）
   Table table = HBaseUtil.getTable(tableName);
   //打印
   HBaseUtil.showScan(table, scan);
   //释放资源
   HBaseUtil.close(table);
}
```

## 4.2 创建项目

### 1、导入依赖

```xml
 <!--方法1： 解决target bytecode version自动变换的问题-->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <encoding>UTF-8</encoding>
    </properties>


    <dependencies>
        <dependency>
            <groupId>org.apache.hbase</groupId>
            <artifactId>hbase-server</artifactId>
            <version>1.3.1</version>
        </dependency>

        <dependency>
            <groupId>org.apache.hbase</groupId>
            <artifactId>hbase-client</artifactId>
            <version>1.3.1</version>
        </dependency>
    </dependencies>

    <!--方法2：idea 使用maven构建项目时，
    target bytecode version经常从1.5自动变化1.8的问题（推荐）-->
<!--    <build>-->
<!--        <plugins>-->
<!--            <plugin>-->
<!--                <groupId>org.apache.maven.plugins</groupId>-->
<!--                <artifactId>maven-compiler-plugin</artifactId>-->
<!--                <configuration>-->
<!--                    <source>1.8</source>-->
<!--                    <target>1.8</target>-->
<!--                </configuration>-->
<!--            </plugin>-->
<!--        </plugins>-->
<!--    </build>-->
```

### 2、获取Configuration对象

```java
//配置文件信息
public static Configuration conf;

static {
    //使用 HBaseConfiguration 的单例方法实例化
    conf = HBaseConfiguration.create();
    conf.set("hbase.zookeeper.quorum", "hadoop102,hadoop103,hadoop101");
    conf.set("hbase.zookeeper.property.clientPort", "2181");
}
```

### 3、判断表是否存在

```java
//判断表是否存在
public static boolean isTableExist(String tableName) throws Exception {
  //在 HBase 中管理、访问表需要先创建 HBaseAdmin 对象
  Connection connection = ConnectionFactory.createConnection(conf);

  //获取管理员对象
  //  HBaseAdmin admin = new HBaseAdmin(conf); //过时方法
  HBaseAdmin admin = (HBaseAdmin) connection.getAdmin();
  boolean exist = admin.tableExists(tableName);

  //关闭连接
  admin.close();
  return exist;
}

//main测试
 //1、测试表student01是否存在
 System.out.println(isTableExist("student01"));
```

使用工具类书写：

```java
package com.xiaolun.demoTest03;

import org.apache.hadoop.hbase.client.HBaseAdmin;

import java.io.IOException;

public class Demo01 {
   public static void main(String[] args) {
      System.out.println(isTableExist("fruit"));
   }

   public static boolean isTableExist(String tableName) {
      //获取管理员对象
      boolean exist = false;
      try {
       //执行到这里的时候，就是可以进入static中的方法，那么在其他类中就不用写conf方法了
         HBaseAdmin admin = HBaseUtil.getAdmin();
         exist = admin.tableExists(tableName);
      } catch (IOException e) {
         e.printStackTrace();
      }
      return exist;
   }
}
```

### 4、创建表

```java
//创建命名空间
public static void createNameSpace(String ns) throws Exception {
    HBaseAdmin admin = new HBaseAdmin(conf);
    //1、创建命名空间
    NamespaceDescriptor namespaceDescriptor = NamespaceDescriptor.create(ns).build();

    try {
        admin.createNamespace(namespaceDescriptor);
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

```java
//创建表（columnFamily 可变形参）
public static void createTable(String tableName, String... columnFamily) throws Exception {
    HBaseAdmin admin = new HBaseAdmin(conf);
    //判断表是否存在
    if (isTableExist(tableName)) {
        System.out.println("表" + tableName + "已存在");
        //System.exit(0);
    } else {
        //创建表属性对象,表名需要转字节（创建表描述器）
        HTableDescriptor descriptor = new HTableDescriptor(TableName.valueOf(tableName));
        //循环创建多个列族
        for (String cf : columnFamily) {
            /**
             * 1、new HColumnDescriptor(cf) 创建列族描述器
             * 2、descriptor.addFamily()添加具体的列族信息
             */
            descriptor.addFamily(new HColumnDescriptor(cf));
        }
        //根据对表的配置，创建表
        admin.createTable(descriptor);
        System.out.println("表" + tableName + "创建成功！");
    }
}

//4、创建命名空间测试
createNameSpace("nameSpace0408");
 //创建表测试
createTable("student02", "info1", "info2");
//表示在nameSpace0408命名空间中创建表student02
 createTable("nameSpace0408:student02", "info1", "info2");
```

### 5、删除表

```java
//删除表
public static void dropTable(String tableName) throws Exception {
  HBaseAdmin admin = new HBaseAdmin(conf);
  if (isTableExist(tableName)) {
    admin.disableTable(tableName);
    admin.deleteTable(tableName);
    System.out.println("表" + tableName + "删除成功！");
  } else {
    System.out.println("表" + tableName + "不存在！");
  }
}

//删除表测试
dropTable("student02");
```

### 6、向表中插入多行数据

```java
/**
 * 向表中插入数据
 *
 * @param tableName    表名
 * @param rowKey       行键
 * @param columnFamily 列族
 * @param column       列
 * @param value        值
 * @throws Exception
 */
public static void addRowData(String tableName, String rowKey,
                              String columnFamily, String column, String value) throws Exception {
    //创建 HTable 对象
    HTable hTable = new HTable(conf, tableName);
    //向表中插入数据(字节数组)
    Put put = new Put(Bytes.toBytes(rowKey)); //？
    //向 Put 对象中组装数据
    put.addColumn(Bytes.toBytes(columnFamily), Bytes.toBytes(column), Bytes.toBytes(value));
    hTable.put(put);
    hTable.close();
    System.out.println("插入数据成功");
}

 //向表中插入数据测试
addRowData("student01", "1001", "info", "name", "xiaohei");
```

### 7、获取数据

```java
/**
 * 1、获取某一行数据 - get方法
 * tableName 表名
 * rowKey 行键
 */
public static void getRow(String tableName, String rowKey) throws Exception {
    HTable table = new HTable(conf, tableName);
    Get get = new Get(Bytes.toBytes(rowKey));
    //get.setMaxVersions();显示所有版本
    //get.setTimeStamp();显示指定时间戳的版本
    Result result = table.get(get);
    //解析result并打印
    for (Cell cell : result.rawCells()) {
        System.out.println(" 行 键 :" + Bytes.toString(result.getRow()));
        System.out.println(" 列 族 " + Bytes.toString(CellUtil.cloneFamily(cell)));
        System.out.println(" 列 :" + Bytes.toString(CellUtil.cloneQualifier(cell)));
        System.out.println(" 值 :" + Bytes.toString(CellUtil.cloneValue(cell)));
        System.out.println("时间戳:" + cell.getTimestamp());
    }
}

/**
 * 获取某一行指定“列族: 列”的数据 - get
 *
 * @param tableName
 * @param rowKey
 * @param family
 * @param qualifier
 * @throws Exception
 */
public static void getRowQualifier(String tableName, String rowKey,
                                   String family, String qualifier) throws Exception {
    HTable table = new HTable(conf, tableName);
    Get get = new Get(Bytes.toBytes(rowKey));
    get.addColumn(Bytes.toBytes(family),
            Bytes.toBytes(qualifier));
    Result result = table.get(get);
    for (Cell cell : result.rawCells()) {
        System.out.println(" 行 键 :" +
                Bytes.toString(result.getRow()));
        System.out.println(" 列 族 " +
                Bytes.toString(CellUtil.cloneFamily(cell)));
        System.out.println(" 列 :" +
                Bytes.toString(CellUtil.cloneQualifier(cell)));
        System.out.println(" 值 :" +
                Bytes.toString(CellUtil.cloneValue(cell)));
    }
}

/**
 * 获取全部数据 - scan
 *
 * @param tableName
 * @throws Exception
 */
public static void getAllRows(String tableName) throws Exception {
    HTable hTable = new HTable(conf, tableName);
    //得到用于扫描 region 的对象
    Scan scan = new Scan();
    //使用 HTable 得到 resultcanner 实现类的对象
    ResultScanner resultScanner = hTable.getScanner(scan);
    for (Result result : resultScanner) {
        Cell[] cells = result.rawCells();
        for (Cell cell : cells) {
            //得到 rowkey
            System.out.println(" 行 键 :" + Bytes.toString(CellUtil.cloneRow(cell)));
            //得到列族
            System.out.println(" 列 族 " + Bytes.toString(CellUtil.cloneFamily(cell)));
            System.out.println(" 列 :" + Bytes.toString(CellUtil.cloneQualifier(cell)));
            System.out.println(" 值 :" + Bytes.toString(CellUtil.cloneValue(cell)));
        }
    }
}

//获取某一行数据
getRow("student01", "1001");
getAllRows("student01");
//获取某一行指定“列族: 列”的数据
getRowQualifier("student01","1001","info","name");
```

### 8、删除多行数据

```java
public static void deleteMultiRow(String tableName, String... rowsKey) throws Exception {
        HTable hTable = new HTable(conf, tableName);
        List<Delete> deleteList = new ArrayList<Delete>();
        //构建删除对象
        for (String row : rowsKey) {
            Delete delete = new Delete(Bytes.toBytes(row));
            deleteList.add(delete);
        }
        hTable.delete(deleteList);
        hTable.close();
    }

  //删除多行数据
 deleteMultiRow("student01","1001");
```

### 9、Version的操作

```java
public static void getVersion(String tableName, byte[] rowName, String familyName, String qualifier) throws Exception {
    //new HTable(conf, tableName);推荐使用这一种方法
    Connection connection = ConnectionFactory.createConnection(conf);
    Table table = connection.getTable(TableName.valueOf(tableName));

    Scan scan = new Scan(rowName); //指定列族
    //设置一次性获取多少个版本的数据
    scan.setMaxVersions(3);
    scan.addColumn(familyName.getBytes(), qualifier.getBytes());
    ResultScanner resultScanner = table.getScanner(scan);
    for (Result result : resultScanner) {
        Cell[] cells = result.rawCells();
        for (Cell cell : cells) {
            System.out.println(" 行 键 :" + Bytes.toString(CellUtil.cloneRow(cell)));
            System.out.println(" 列 族 " + Bytes.toString(CellUtil.cloneFamily(cell)));
            System.out.println(" 列 :" + Bytes.toString(CellUtil.cloneQualifier(cell)));
            System.out.println(" 值 :" + Bytes.toString(CellUtil.cloneValue(cell)));
            System.out.println(" 时间戳 :" + cell.getTimestamp());
            System.out.println("-------------------------------------------------");
        }
    }
}

//测试
String rowName = "1002";     getVersion("student01",rowName.getBytes(),"info","name");
```

控制台输出结果：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200806161423550.png" alt="image-20200806161423550" style="zoom:80%;" />

### 10、更改表-alter

```java
/**
 * @desc modify table
 * @param tableName
 */
public static void alter(String tableName){
   HBaseAdmin admin = HBaseUtil.getAdmin();
   //创建表的描述器
   try {
      HTableDescriptor tableDescriptor = admin.getTableDescriptor(TableName.valueOf(tableName));
      //添加列族
      HColumnDescriptor hColumnDescriptor = new HColumnDescriptor("f1");
      tableDescriptor.addFamily(hColumnDescriptor);
      admin.modifyTable(tableName, tableDescriptor);
   } catch (IOException e) {
      e.printStackTrace();
   }finally {
      HBaseUtil.close(admin);
   }
}

//测试
alter("fruit");
```

可以为已经已经创建的表添加一个新的列族f1。

## 4.3 与MapReduce交互

通过 HBase 的相关 JavaAPI，我们可以实现伴随 HBase 操作的 MapReduce 过程，比如使用MapReduce 将数据从本地文件系统导入到 HBase 的表中，比如我们从 HBase 中读取一些原始数据后使用 MapReduce 做数据分析。 

### 1、官方 HBase-MapReduce

1、环境变量的导入

在/etc/profile文件中配置：

```ini
export HBASE_HOME=/home/zookeeper/software/hbase-1.3.1
export HADOOP_HOME=/home/zookeeper/software/hadoop-2.7.2
```

然后在并在 hadoop-env.sh 中配置：（注意：在 for 循环之后配）如下如所示：

```ini
export HADOOP_CLASSPATH=$HADOOP_CLASSPATH:/home/zookeeper/software/hbase-1.3.1/lib/*
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200809110205447.png" alt="image-20200809110205447" style="zoom:67%;" />

其中，上图中的`export HADOOP_CLASSPATH`写错，导致报错：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200809114631099.png" alt="image-20200809114631099" style="zoom:80%;" />

2、运行官方的 MapReduce 任务

（1）统计 stuTest表中有多少行数据

```shell
#其中表stuTest是hbase种已经创建的表，需要在hbase目录下运行
/home/zookeeper/software/hadoop-2.7.2/bin/yarn jar lib/hbase-server-1.3.1.jar rowcounter stuTest
```

输出结果：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200809114840050.png" alt="image-20200809114840050" style="zoom:67%;" />

（2）使用 MapReduce 将本地数据导入到 HBase 

（a）在本地创建一个 tsv 格式的文件：fruit.tsv 

```java
    public static void main(String[] args) {
        System.out.println("1001"+"\t"+"Apple"+"\t"+"red");
        System.out.println("1002"+"\t"+"Pear"+"\t"+"Yellow");
        System.out.println("1003"+"\t"+"Pineapple"+"\t"+"red");
    }
//制表符
1001	Apple	red
1002	Pear	Yellow
1003	Pineapple	red
```

注意：fruit.tsv需要是制表符的数据，不然在将数据从mapreduce传到hbase种会失败。

我使用一种比较笨的方法来创建的。

（b）创建fruit表

```ini
create 'fruit','info'
```

（c）在 HDFS 中创建 input_fruit 文件夹并上传 fruit.tsv 文件 

```ini
bin/hdfs fs -mkdir /input_fruit/ 
bin/hdfs  fs  -put  fruit.tsv /input_fruit/
```

（d）执行 MapReduce 到 HBase 的 fruit 表中 

```ini
#其中的 \ 表示换行
/home/zookeeper/software/hadoop-2.7.2/bin/yarn jar lib/hbase-server-1.3.1.jar \
importtsv -Dimporttsv.columns=HBASE_ROW_KEY,info:name,info:color fruit \
hdfs://hadoop101:9000//input_fruit/test.txt
```

（e）使用scan查看导入结果

```ini
scan 'fruit'
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200809154144799.png" alt="image-20200809154144799" style="zoom:80%;" />

### 2、 HBase的数据存储到HDFS

1、导入依赖

```xml
<dependencies>
    <!--hadoop依赖-->
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

    <!--HBase依赖-->
    <dependency>
        <groupId>org.apache.hbase</groupId>
        <artifactId>hbase-server</artifactId>
        <version>1.3.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hbase</groupId>
        <artifactId>hbase-client</artifactId>
        <version>1.3.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hbase</groupId>
        <artifactId>hbase-common</artifactId>
        <version>1.3.1</version>
    </dependency>
</dependencies>
```

2、编写代码

```java
public class HBase2HDFS implements Tool {

   private Configuration conf;

   @Override
   public int run(String[] args) throws Exception {
      //1、获取job
      Job job = Job.getInstance(conf);
      //2、设置jar 写上驱动的名字
      job.setJarByClass(HBase2HDFS.class);
      //3、设置输入输出 和原来的mapreduce不同
      TableMapReduceUtil.initTableMapperJob("t2",
            new Scan(), HBaseMapper.class,
            Text.class, NullWritable.class, job);
      FileOutputFormat.setOutputPath(job, new Path(args[0]));
      //4、提交
      return job.waitForCompletion(true) ? 1 : 0;
   }

   @Override
   public void setConf(Configuration conf) {
      //1、配置连接HBase的路径
      conf.set("hbase.zookeeper.quorum", "hadoop102,hadoop103,hadoop101");
      //2、配置连接HDFS
      conf.set("fs.defaultFS", "hdfs://hadoop101:9000");
      //3、配置连接mapreduce
      conf.set("mapreduce.framework.name", "yarn");
      //configuration保持一致
      this.conf = conf;
   }

   @Override
   public Configuration getConf() {
      //获取configuration
      return conf;
   }

   /**
    * 1、TableMapper<KEYOUT, VALUEOUT>
    * 2、从HBase上输出数据到HDFS(不要数据，只要一个文本)
    */
   public static class HBaseMapper extends TableMapper<Text, NullWritable> {
      private Text mk = new Text();

      @Override
      protected void map(ImmutableBytesWritable key, Result value, Context context) throws IOException, InterruptedException {
         //1、创建字符串对象
         StringBuffer stringBuffer = new StringBuffer();
         //2、获取扫描
         CellScanner cellScanner = value.cellScanner();
         //3、扫描
         //遍历扫描器（没有try-catch包围也没有报错）
         while (cellScanner.advance()) {
            //4、获取一个表格
            Cell cell = cellScanner.current();
            //5、按逗号进行拼接
            stringBuffer.append(new String(CellUtil.cloneValue(cell), "utf-8")).append(",");
         }
         //6、设置key
         mk.set(stringBuffer.toString());
         //7、写出
         context.write(mk, NullWritable.get());
      }
   }

   public static void main(String[] args) throws Exception {
      ToolRunner.run(HBaseConfiguration.create(), new HBase2HDFS(), args);
   }
}
```

3、执行顺序

（1）打包

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200809231659858.png" alt="image-20200809231659858" style="zoom:80%;" />

（2）上传到Linux服务器

（3）配置HBase环境变量

```ini
#$HBASE_HOME需要事先在环境变量中配置
#在进行下面的第（5）步之前，需要将下面的代码运行，此时，会将HBase中的所有jar包加载。
export HADOOP_CLASSPATH=$HADOOP_CLASSPATH:$HBASE_HOME/lib/*
```

（4）在HBase中创建t2表

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200809231955835.png" alt="image-20200809231955835" style="zoom:80%;" />

（5）执行

```ini
# HBase2HDFS.jar存放在hadoop-2.7.2的上一级目录中，故有 ../
# HBase2HDFS.jar 是将代码package之后改的名
[zookeeper@hadoop103 hadoop-2.7.2]$ bin/hadoop jar ../HBase2HDFS.jar com.xiaolun.HBase2HDFS /hbase_demo/hbase2hdfs
```

4、执行结果

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200809231942168.png" alt="image-20200809231942168" style="zoom:80%;" />

可以将t2表中的值拿到。

### 3、HDFS数据存储搭配HBase

1、代码

```java
public class HDFS2HBase implements Tool {

   private Configuration conf;

   @Override
   public int run(String[] args) throws Exception {
      //1、获取job
      Job job = Job.getInstance(conf);
      //2、设置jar 写上驱动的名字
      job.setJarByClass(HDFS2HBase.class);
      //3、设置其他
      job.setMapperClass(HBaseMapper2.class);
      job.setReducerClass(HBaseReducer2.class);
      job.setMapOutputKeyClass(Text.class);
      job.setMapOutputValueClass(LongWritable.class);
      FileInputFormat.setInputPaths(job, new Path(args[0]));
      TableMapReduceUtil.initTableReducerJob("t3", HBaseReducer2.class, job);
      //4、提交
      return job.waitForCompletion(true) ? 1 : 0;
   }

   @Override
   public void setConf(Configuration conf) {
      //1、配置连接HBase的路径
      conf.set("hbase.zookeeper.quorum", "hadoop102,hadoop103,hadoop101");
      //2、配置连接HDFS
      conf.set("fs.defaultFS", "hdfs://hadoop101:9000");
      //3、配置连接mapreduce
      conf.set("mapreduce.framework.name", "yarn");
      //configuration保持一致
      this.conf = conf;
   }

   @Override
   public Configuration getConf() {
      //获取configuration
      return conf;
   }

   /**
    * 1、TableMapper<KEYOUT, VALUEOUT>
    * 2、从HBase上输出数据到HDFS(不要数据，只要一个文本)
    */
   public static class HBaseMapper2 extends Mapper<LongWritable, Text, Text, LongWritable> {
      private Text mk = new Text();
      private LongWritable mv = new LongWritable(1);

      @Override
      protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
         //1、读取一行
         String line = value.toString();
         //2、切分
         String[] columns = line.split(",");
         //3、遍历
         for (String column : columns) {
            mk.set(column);
            context.write(mk, mv);
         }
      }
   }

   //TableReducer<KEYIN, VALUEIN, KEYOUT> extends Reducer<KEYIN, VALUEIN, KEYOUT, Mutation>
   public static class HBaseReducer2 extends TableReducer<Text, LongWritable, ImmutableBytesWritable> {
      @Override
      protected void reduce(Text key, Iterable<LongWritable> values, Context context) throws IOException, InterruptedException {
         //1、计数器
         long count = 0;
         Iterator<LongWritable> iterator = values.iterator();
         while (iterator.hasNext()) {
            LongWritable next = iterator.next();
            count += next.get();
         }
         //2、行键
         Put put = new Put(Bytes.toBytes(key.toString()));
         put.addColumn(Bytes.toBytes("info"), Bytes.toBytes("name"), Bytes.toBytes(key.toString()));
//       put.addColumn(Bytes.toBytes("info"), Bytes.toBytes("count"), Bytes.toBytes(count));
         //3、写回
         context.write(new ImmutableBytesWritable(Bytes.toBytes(key.toString())), put);
      }
   }

   public static void main(String[] args) throws Exception {
      ToolRunner.run(HBaseConfiguration.create(), new HDFS2HBase(), args);
   }
}
```

2、打包执行

```ini
bin/hadoop jar ../hdfs2hbase-1.0-SNAPSHOT.jar com.xiaolun.HDFS2HBase /hbase_demo/hbase2hdfs
```

# 5 Filter查询

## 5.1、SingleColumnValueFilter

```java
//select *from t1 where age <= 18
@Test
public void singleColumnvalueFilter() throws Exception {
    /**
		 * 1、创建单列值过滤器
		 * Bytes.toBytes("18") 要带上引号""，不然查不到数据
		 */
    SingleColumnValueFilter filter1 =
        new SingleColumnValueFilter(Bytes.toBytes("info"), Bytes.toBytes("age"),
                                    CompareFilter.CompareOp.LESS_OR_EQUAL, Bytes.toBytes("18"));
    /**
		 * 设置单值过滤，如果没有这个属性列，就不计算它
		 * 假如某个列键中的列族中只有name属性，没有age属性
		 * 设置下面的属性后，该列键就不参加计算，此时不会输出，
		 * 否则会输出
		 */
    filter1.setFilterIfMissing(true);
    //2、扫描整个表
    Scan scan = new Scan();
    //3、设置过滤器
    scan.setFilter(filter1);
    //4、获取表
    Table table = HBaseUtil.getTable("t1");
    //5、打印
    HBaseUtil.showScan(table, scan);
    //6、释放资源
    HBaseUtil.close(table);
}
```

使用再次重构的HBaseUtil：

```java
public void singleColumnvalueFilter() throws Exception {
   SingleColumnValueFilter filter1 =
         new SingleColumnValueFilter(Bytes.toBytes("info"), Bytes.toBytes("age"),
               CompareFilter.CompareOp.LESS_OR_EQUAL, Bytes.toBytes("18"));
   filter1.setFilterIfMissing(true); 
   HBaseUtil.showFilter(filter1, "t1"); //!!!!
}
```

**添加过滤器链**

```java
/**
 * select *from t1 where age <= 18 and name = we
 */
@Test
public void singleColumnvalueFilter() throws Exception {
   //1.1、过滤器链(将多个过滤器拼接在一起) and
   FilterList filterList = new FilterList(FilterList.Operator.MUST_PASS_ALL);

   //1.2、创建单列值过滤器
   SingleColumnValueFilter filter1 =
         new SingleColumnValueFilter(Bytes.toBytes("info"), Bytes.toBytes("age"),
               CompareFilter.CompareOp.LESS_OR_EQUAL, Bytes.toBytes("18"));
   SingleColumnValueFilter filter2 =
         new SingleColumnValueFilter(Bytes.toBytes("info"), Bytes.toBytes("name"),
               CompareFilter.CompareOp.LESS_OR_EQUAL, Bytes.toBytes("we"));
   /**
    * 设置单值过滤，如果没有这个属性列，就不计算它
    * 假如某个列键中的列族中只有name属性，没有age属性
    * 设置下面的属性后，该列键就不参加计算，此时不会输出，
    * 否则会输出
    */
   filter1.setFilterIfMissing(true);
   //1.3
   filter2.setFilterIfMissing(true);

   //1.4添加过滤器链
   filterList.addFilter(filter1);
   filterList.addFilter(filter2);

   //2、扫描整个表
   Scan scan = new Scan();
   //3、设置过滤器
   scan.setFilter(filterList);
   //4、获取表
   Table table = HBaseUtil.getTable("t1");
   //5、打印
   HBaseUtil.showScan(table, scan);
   //6、释放资源
   HBaseUtil.close(table);
}
```

## 5.2、正则比较器

```java
//  select *from t1 where name like %z%
@Test
public void regexStringComparator() throws Exception {

   //0、正则比较器  表达式：\\W*(大写)，一个或多个
   RegexStringComparator comparator = new RegexStringComparator("\\W*z\\W*");

   //1、单列值过滤器 将正则比较器加入到里面
   //CompareFilter.CompareOp.EQUAL 变成EQUAL
   SingleColumnValueFilter filter1 =
         new SingleColumnValueFilter(Bytes.toBytes("info"), Bytes.toBytes("name"),
               CompareFilter.CompareOp.EQUAL, comparator);

   filter1.setFilterIfMissing(true);
   //2、扫描整个表
   Scan scan = new Scan();
   //3、设置过滤器
   scan.setFilter(filter1);
   //4、获取表
   Table table = HBaseUtil.getTable("t1");
   //5、打印
   HBaseUtil.showScan(table, scan);
   //6、释放资源
   HBaseUtil.close(table);
}
```

## 5.3、SubstringComparator

```java
//select *from t1 where name like %z%
//substringComparator和上面的正则表达式实现同样的功能
@Test
public void SubStringComparator() throws Exception {

   SubstringComparator substringComparator = new SubstringComparator("z");
   SingleColumnValueFilter filter1 =
         new SingleColumnValueFilter(Bytes.toBytes("info"), Bytes.toBytes("name"),
               CompareFilter.CompareOp.EQUAL, substringComparator);

   filter1.setFilterIfMissing(true);
   HBaseUtil.showFilter(filter1,"t1");
}
```

## 5.4、BinaryComparator

```java
//select *from t1 where age = 18
@Test
public void binaryComparator() throws Exception {
   //二进制比较器,加引号的"18"，才是一个正常的字节。能够正常输出
   BinaryComparator binaryComparator = new BinaryComparator(Bytes.toBytes("18"));
   SingleColumnValueFilter filter1 =
         new SingleColumnValueFilter(Bytes.toBytes("info"), Bytes.toBytes("age"),
               CompareFilter.CompareOp.EQUAL, binaryComparator);

   filter1.setFilterIfMissing(true);
   HBaseUtil.showFilter(filter1, "t1");
}
```

## 5.5、BinaryPrefixComparator

```java
//select *from t1 where name like "z%"
@Test
public void binaryPrefixComparator() throws Exception {
   //二进制前置比较器
   BinaryPrefixComparator prefixComparator = new BinaryPrefixComparator(Bytes.toBytes("z"));
   SingleColumnValueFilter filter1 =
         new SingleColumnValueFilter(Bytes.toBytes("info"), Bytes.toBytes("name"),
               CompareFilter.CompareOp.EQUAL, prefixComparator);

   filter1.setFilterIfMissing(true);
   HBaseUtil.showFilter(filter1, "t1");
}
```

## 5.6、FamilyFilter

```java
@Test
public void familyFilter() throws Exception {
   //列族比较器，将列族中头为i的取出来
   BinaryPrefixComparator prefixComparator = new BinaryPrefixComparator(Bytes.toBytes("i"));
   FamilyFilter filter1 = new FamilyFilter(CompareFilter.CompareOp.EQUAL, prefixComparator);
   HBaseUtil.showFilter(filter1, "t1");
}
```

## 5.7、列名-QualifierFilter

```java
@Test
public void familyFilter() throws Exception {
   //列名过滤器,将列名以a开头的查出来
   BinaryPrefixComparator prefixComparator = new BinaryPrefixComparator(Bytes.toBytes("a"));
   QualifierFilter filter1 = new QualifierFilter(CompareFilter.CompareOp.EQUAL, prefixComparator);
   HBaseUtil.showFilter(filter1, "t1");
}
```

## 5.8、列名前缀-ColumnPrefixFilter

```java
@Test
	public void columnPrefixFilter() throws Exception {
		//列名前缀过滤器,将列名以a开头的查出来
		ColumnPrefixFilter filter1 = new ColumnPrefixFilter(Bytes.toBytes("a"));
		HBaseUtil.showFilter(filter1, "t1");
	}

	@Test
	public void multiplecolumnPrefixFilter() throws Exception {
		//列多前缀匹配过滤器,将列名以a和n开头的查出来
		byte [][] prefixes = {Bytes.toBytes("a"),Bytes.toBytes("n")};
		MultipleColumnPrefixFilter prefixFilter = new MultipleColumnPrefixFilter(prefixes);
		HBaseUtil.showFilter(prefixFilter, "t1");
	}
```

## 5.9、ColumnRangeFilter

```java
//将age --> name 列数据全部查出来
@Test
public void columnRangeFilter() throws Exception {
   //列范围过滤器 false:表示小于；true：表示大于等于
   ColumnRangeFilter filter = new ColumnRangeFilter(Bytes.toBytes("age"), true, Bytes.toBytes("name"), false);
   HBaseUtil.showFilter(filter, "t1");
}
```

## 5.10、RowFilter

```java
//查询001行键的数据
@Test
public void rowFilter() throws Exception {
   BinaryComparator comparator = new BinaryComparator(Bytes.toBytes("001"));
   //行键比较器
   RowFilter filter = new RowFilter(CompareFilter.CompareOp.EQUAL,comparator );
   HBaseUtil.showFilter(filter, "t1");
}
```

## 5.11、FirstKeyOnlyFilter

```java
@Test
public void firstKeyOnlyFilter() throws Exception {
    //打印第一行key的过滤器（只讲age那个行键打印出来）
    FirstKeyOnlyFilter filter1 = new FirstKeyOnlyFilter();
    HBaseUtil.showFilter(filter1, "t1");
}
```

# 6 布隆过滤器

<img src="HBase学习.assets\image-20200809220925253.png" alt="image-20200809220925253" style="zoom:80%;" />

1、应用场景

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200809220425346.png" alt="image-20200809220425346" style="zoom:80%;" />

2、HBase中布隆过滤器的应用

<img src="HBase学习.assets\image-20200809221132798.png" alt="image-20200809221132798" style="zoom:80%;" />

# 7、协处理器

## 1、介绍

1、概述

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200810104910812.png" alt="image-20200810104910812" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200810105128335.png" alt="image-20200810105128335" style="zoom:80%;" />

2、创建协处理类

```java
public class HBaseProcessor extends BaseRegionObserver {
   /**
    * 1、该方法会在put命令执行之前被调用
    * 2、Put put：就是你的put命令，这个命令里面封装了你执行时候的数据
    * 3、put 'guanzhu','canglaoshi','cf:name','xiaoxi'
    */
   @Override
   public void prePut(ObserverContext<RegionCoprocessorEnvironment> e, Put put, WALEdit edit, Durability durability) throws IOException {
      //1、获取行键
      byte[] rowKey = put.getRow();
      //2、获取name属性对应的多个版本的值
      List<Cell> cells = put.get(Bytes.toBytes("cf"), Bytes.toBytes("name"));
      //3、获取最新版本
      Cell cell = cells.get(0);
      //4、获取到值
      byte[] value = CellUtil.cloneValue(cell);
      //5、创建新的Put对象
      Put new_Put = new Put(value);
      new_Put.addColumn(CellUtil.cloneFamily(cell), Bytes.toBytes("star"), rowKey);
      //6、提交
      Table table = HBaseUtil.getTable("fans");
      table.put(new_Put);
      //7、释放资源
      HBaseUtil.close(table);
   }
}
```

3、测试

（1）打包上传到服务器

（2）将服务器的包上传到HDFS

```ini
 bin/hadoop fs -put ../gaunzhu.jar /hbase_demo
```

（3）执行命名导入协处理器

```ini
#1、停用表
disable 'guanzhu'

#2、HBaseProcessor需要加上全类名
#guanzhu.jar 对应/链接 （2）中上传到HDFS的jar
alter 'guanzhu',METHOD => 'table_att','coprocessor' => 'hdfs://hadoop101:9000/hbase_demo/guanzhu.jar|com.xiaolun.HBaseProcessor|1001'

#3、启用表
enable 'guanzhu'
```

（4）输入下面指令

```ini
put 'guanzhu','canglaoshi','cf:name','xiaoxi'
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200810115915727.png" alt="image-20200810115915727" style="zoom:80%;" />

网上的协处理卸载，可以参考，未测试：

```ini
disable 'mytable'
alter 'mytable',METHOD=>'table_att_unset',NAME=>'coprocessor$1'
enable 'mytable'
```

# 8、HBase优化

1、简单介绍

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200810120121442.png" alt="image-20200810120121442" style="zoom:80%;" />