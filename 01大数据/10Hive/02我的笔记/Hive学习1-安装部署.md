## 1、介绍

1、概述

官网：

```http
http://hive.apache.org/
```

文档查看网址：

```http
https://cwiki.apache.org/confluence/display/Hive/GettingStarted
```

jar包下载地址：

```http
http://archive.apache.org/dist/hive/
```

Hive：由 Facebook 开源用于解决海量结构化日志的数据统计。Hive 是基于 Hadoop 的一个数据仓库工具，可以将结构化的数据文件映射为一张表，并 提供类 SQL 查询功能。

本质是：将 HQL 转化成 MapReduce 程序

![image-20200907083212691](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200907083212691.png)

（1）Hive 处理的数据存储在 HDFS 。

（2）Hive 分析数据底层的默认实现是 MapReduce。 

（3）执行程序运行在 Yarn 上。

2、优缺点

优点：

（1）操作接口采用类 SQL 语法，提供快速开发的能力（简单、容易上手）。 

（2） 避免了去写 MapReduce，减少开发人员的学习成本。

（3）Hive 的执行延迟比较高，因此 Hive 常用于数据分析，对实时性要求不高的场合。

 （4）Hive 优势在于处理大数据，对于处理小数据没有优势，因为 Hive 的执行延迟比较 高。 

（5）Hive 支持用户自定义函数，用户可以根据自己的需求来实现自己的函数。

缺点：

Hive 的 HQL 表达能力有限，主要表现在：

（1）迭代式算法无法表达 。

（2）数据挖掘方面不擅长。

Hive 的效率比较低，表现在：

（1）Hive 自动生成的 MapReduce 作业，通常情况下不够智能化。

（2）Hive 调优比较困难，粒度较粗。

3、架构

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200907083625228.png" alt="image-20200907083625228" style="zoom:67%;" />

（1）用户接口：Client 

CLI（hive shell）、JDBC/ODBC(java 访问 hive)、WEBUI（浏览器访问 hive） 

（2）元数据：Metastore 

元数据包括：表名、表所属的数据库（默认是 default）、表的拥有者、列/分区字段、表的类型（是否是外部表）、表的数据所在目录等；默认存储在自带的 derby 数据库中，推荐使用 MySQL 存储 Metastore。

（3）Hadoop

使用 HDFS 进行存储，使用 MapReduce 进行计算。

（4）驱动器：Driver

（a）解析器（SQL Parser）：将 SQL 字符串转换成抽象语法树 AST，这一步一般都用第三方工具库完成，比如 antlr；对 AST 进行语法分析，比如表是否存在、字段是否存在、SQL 语义是否有误。 

（b）编译器（Physical Plan）：将 AST 编译生成逻辑执行计划。 

（c）优化器（Query Optimizer）：对逻辑执行计划进行优化。 

（d）执行器（Execution）：把逻辑执行计划转换成可以运行的物理计划。对于 Hive 来 说，就是 MR/Spark。

（5）运行机制

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200907084003981.png" alt="image-20200907084003981" style="zoom:80%;" />

Hive 通过给用户提供的一系列交互接口，接收到用户的指令(SQL)，使用自己的 Driver， 结合元数据(MetaStore)，将这些指令翻译成 MapReduce，提交到 Hadoop 中执行，最后，将执行返回的结果输出到用户交互接口。

4、 Hive 和数据库比较

由于 Hive 采用了类似 SQL 的查询语言 HQL(Hive Query Language)，因此很容易 将 Hive 理解为数据库。

其实从结构上来看，Hive 和数据库除了拥有类似的查询语言，再无 类似之处。本节将从多个方面来阐述 Hive 和数据库的差异。数据库可以用在 Online 的应用 中，但是 Hive 是为数据仓库而设计的，清楚这一点，有助于从应用角度理解 Hive 的特性。

5、查询语言

由于 SQL 被广泛的应用在数据仓库中，因此，专门针对 Hive 的特性设计了类 SQL 的 查询语言 HQL。熟悉 SQL 开发的开发者可以很方便的使用 Hive 进行开发。

6、数据存储位置

Hive 是建立在 Hadoop 之上的，所有 Hive 的数据都是存储在 HDFS 中的。而数据库则可以将数据保存在块设备或者本地文件系统中。

7、数据更新

由于 Hive 是针对数据仓库应用设计的，而数据仓库的内容是读多写少的。因此，Hive 中不建议对数据的改写，所有的数据都是在加载的时候确定好的。而数据库中的数据通常是需要经常进行修改的 ， 因此 可 以 使 用 INSERT INTO … VALUES 添 加 数 据 ， 使 用 UPDATE … SET 修改数据。

8、索引

Hive 在加载数据的过程中不会对数据进行任何处理，甚至不会对数据进行扫描，因此，也没有对数据中的某些 Key 建立索引。

Hive 要访问数据中满足条件的特定值时，需要暴力扫描整个数据，因此访问延迟较高。由于 MapReduce 的引入， Hive 可以并行访问数据，因此即使没有索引，对于大数据量的访问，Hive 仍然可以体现出优势。数据库中，通常会针对一个或者几个列建立索引，因此对于少量的特定条件的数据的访问，数据库可以有很高的效率，较低的延迟。由于数据的访问延迟较高，决定了 Hive 不适合在线数据查询。

9、执行

Hive 中大多数查询的执行是通过 Hadoop 提供的 MapReduce 来实现的。而数据库通常有自己的执行引擎。

10、执行延迟

Hive 在查询数据的时候，由于没有索引，需要扫描整个表，因此延迟较高。另外一个 导致 Hive 执行延迟高的因素是 MapReduce 框架。由于 MapReduce 本身具有较高的延迟， 因此在利用 MapReduce 执行 Hive 查询时，也会有较高的延迟。

相对的，数据库的执行延迟较低。当然，这个低是有条件的，即数据规模较小，当数据规模大到超过数据库的处理能力的时候，Hive 的并行计算显然能体现出优势。

11、可扩展性

由于 Hive 是建立在 Hadoop 之上的，因此 Hive 的可扩展性是和 Hadoop 的可扩展性是 一致的（世界上最大的 Hadoop 集群在 Yahoo!，2009 年的规模在 4000 台节点左右）。

而数据库由于 ACID 语义的严格限制，扩展行非常有限。目前最先进的并行数据库 Oracle 在理 论上的扩展能力也只有 100 台左右。

## 2、安装和部署

1、网址

（1）Hive官网地址

```http
http://hive.apache.org/
```

（2）下载网址

```http
http://archive.apache.org/dist/hive/
```

2、安装

（1）配置Hive的配置文件hive-env.sh

```ini
# 配置 HADOOP_HOME 路径
export HADOOP_HOME=/home/zookeeper/software/hadoop-2.7.2

# 配置 HIVE_CONF_DIR 路径
export HIVE_CONF_DIR=/myhive/hive/conf
```

（2）启动hdfs和yarn

```ini
sbin/start-dfs.sh
sbin/start-yarn.sh
```

（3）启动hive

```shell
 bin/hive
```

使用user时可能会遇到下面的报错：

```
Database Class Loader started - derby.database.classpath=''
```

主要是metastore数据库read only，没有写权限，因此，修改hive安装目录的所属用户和组为zookeeper:zookeeper即可。

```shell
[zookeeper@hadoop101 hive]$ sudo chown -R zookeeper:zookeeper /myhive/hive/
```

3、hive基本操作

```sql
-- 查看数据库和表
hive> show databases;
hive> show tables;
-- 创建表
hive> create table student(id int, name string);
-- 查看表的结构
hive> desc student;
-- 退出 hive
hive> quit;
```

4、简单案例

（1）需求

将本地/opt/module/data/student.txt 这个目录下的数据导入到 hive 的 student(id int, name string)表中。

（2）操作

```sql
-- 创建student表，并声明文件分隔符'\t'
create table stu(id int, name string) ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t';

-- 创建文件 student.txt，以table键分隔开
1       z3
2       li4


-- 加载/home/zookeeper/software/student.txt文件到 student 数据库表中。如果是hdfs中加载文件，将下面的local去掉。
load data local inpath '/home/zookeeper/software/student.txt' into table stu;

-- 查询结果 
select * from stu;
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200909101456889.png" alt="image-20200909101456889" style="zoom:67%;" />

## 3、 Hive 元数据配置到 MySql

1、驱动拷贝

将mysql-connector-java-5.1.27驱动拷贝到hive的配置目录下：/myhive/hive/lib。

2、配置 Metastore 到 MySql

在/myhive/hive/conf目录下创建一个文件hive-site.xml，并在文件中添加下面的配置：

```xml
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
<property>
	<name>javax.jdo.option.ConnectionURL</name>
	<value>jdbc:mysql://hadoop101:3306/metastore?createDatabaseIfNotExist=true</value>
	<description>JDBC connect string for a JDBC metastore</description>
</property>
<property>
  <name>javax.jdo.option.ConnectionDriverName</name>
  <value>com.mysql.jdbc.Driver</value>
  <description>Driver class name for a JDBC metastore</description>
</property>
<property>
 <name>javax.jdo.option.ConnectionUserName</name>
 <value>root</value>
 <description>username to use against metastore database</description>
</property>
<property>
 <name>javax.jdo.option.ConnectionPassword</name>
 <value>123</value>
 <description>password to use against metastore database</description>
</property>
</configuration>
```

配置完成后，如果启动hive异常，可以冲洗启动虚拟机。

3、重启hive后，再重启mysql查看数据库

```mysql
bin/hive

mysql -u root -p 123
 
show databases;
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200909111430302.png" alt="image-20200909111430302" style="zoom: 80%;" />

可以看到新建了metastore数据库。

4、在hive中新建数据库和表

```mysql
create database db03;
create table stu(id int,name String);
```

在navicate中可以看到：

![image-20200909111841521](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200909111841521.png)

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200909111929229.png" alt="image-20200909111929229" style="zoom:67%;" />

5、Hive 常用交互命令

（1）“-e”不进入 hive 的交互窗口执行 sql 语句

```mysql
 bin/hive -e "select id from stu;"
```

（2）“-f”执行脚本中 sql 语句

```shell
# 创建一个sql脚本文件
touch hivef.sql
 
# 并在文件中写入下面的sql语句
select * from stu;

# 执行文件中的sql语句，也可以将结果写入到相应的文件中
bin/hive -f /home/zookeeper/sofeware/hivef.sql

bin/hive -f /home/zookeeper/sofeware/hivef.sql >
/home/zookeeper/sofeware/hivef.sql/hive_result.txt
```

6、Hive 常见属性配置

（1）查询后信息显示配置

在 hive-site.xml 文件中添加如下配置信息，就可以实现显示当前数据库，以及查询表的头信息配置。

```xml
<property>
    <name>hive.cli.print.header</name>
    <value>true</value>
</property>
<property>
    <name>hive.cli.print.current.db</name>
    <value>true</value>
</property>
```

（2） Hive 运行日志信息配置

默认情况下，日志log存放在在/tmp/zookeeper/hive.log中。我们可以使用下面的配置修改log日志的存放路径：

修改/hive/conf/hive-log4j.properties.template 文件名称为hive-log4j.properties

```shell
mv hive-log4j.properties.template hive-log4j.properties
```

在 hive-log4j.properties 文件中修改 log 存放位置：

```ini
hive.log.dir=/home/zookeeper/sofeware/hive/logs
```

（3）参数配置方式

（a）配置文件方式

默认配置文件：hive-default.xml ，用户自定义配置文件：hive-site.xml。

（b）命令行参数方式

启动 Hive 时，可以在命令行添加-hiveconf param=value 来设定参数。

```shell
bin/hive -hiveconf mapred.reduce.tasks=10;
```

这仅对本次 hive 启动有效。

（c）参数声明方式

可以在 HQL 中使用 SET 关键字设定参数。

```shell
 set mapred.reduce.tasks=100;
```

上述三种设定方式的优先级依次递增。即配置文件<命令行参数<参数声明。

## 4、Hive数据类型

### 1、基本数据类型

1、基本数据类型

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200909151903359.png" alt="image-20200909151903359" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200909151936137.png" alt="image-20200909151936137" style="zoom:67%;" />

对于 Hive 的 String 类型相当于数据库的 varchar 类型，该类型是一个可变的字符串，不 过它不能声明其中最多能存储多少个字符，理论上它可以存储 2GB 的字符数。

2、集合数据类型

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200909152010578.png" alt="image-20200909152010578" style="zoom:80%;" />

Hive 有三种复杂数据类型 ARRAY、MAP 和 STRUCT。ARRAY 和 MAP 与 Java 中的 Array 和 Map 类似，而 STRUCT 与 C 语言中的 Struct 类似，它封装了一个命名字段集合， 复杂数据类型允许任意层次的嵌套。

3、案例

（1）需求

假设某表有如下一行，我们用 JSON 格式来表示其数据结构。在 Hive 下访问的格式为：

```json
{
 "name": "songsong",
 "friends": ["bingbing" , "lili"] , //列表 Array,
 "children": {            //键值 Map,
 "xiao song": 18 ,
 "xiaoxiao song": 19
 }
 "address": {             //结构 Struct,
 "street": "hui long guan" ,
 "city": "beijing"
 }
}
```

基于上述数据结构，我们在 Hive 里创建对应的表，并导入数据。

（2）创建本地测试文件test.txt

```
xiaolun,zhihao_xiaohongzi,xiao song:18_xiaoxiao song:19,henan_zhoukou
```

MAP，STRUCT 和 ARRAY 里的元素间关系都可以用同字符“_”表示。

（3）建表

```sql
create table test(
name string,
friends array<string>,
children map<string, int>,
address struct<street:string, city:string>
)
row format delimited
fields terminated by ','
collection items terminated by '_'
map keys terminated by ':'
lines terminated by '\n';
```

字段解释：

```sql
row format delimited fields terminated by ',' -- 列分隔符
collection items terminated by '_' --MAP STRUCT 和 ARRAY 的分隔符(数据分割符号)
map keys terminated by ':' -- MAP 中的 key 与 value 的分隔符
lines terminated by '\n'; -- 行分隔符(默认，可不加)
```

（4）导入文本数据到测试表中

```sql
load data local inpath
'/home/zookeeper/sofeware/test.txt' into table test;
```

（5）访问三种集合中的数据，以下分别是 ARRAY，MAP，STRUCT 的访问方式

```sql
 select friends[0],children['xiao song'],address.city from test where name="xiaolun";
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200909153456376.png" alt="image-20200909153456376" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200909153525896.png" alt="image-20200909153525896" style="zoom:80%;" />

### 2、类型转换

Hive 的原子数据类型是可以进行隐式转换的，类似于 Java 的类型转换，例如某表达式 使用 INT 类型，TINYINT 会自动转换为 INT 类型，但是 Hive 不会进行反向转化，例如， 某表达式使用 TINYINT 类型，INT 不会自动转换为 TINYINT 类型，它会返回错误，除非使 用 CAST 操作。

1、隐式类型转换规则如下

（1）任何整数类型都可以隐式地转换为一个范围更广的类型，如 TINYINT 可以转换 成 INT，INT 可以转换成 BIGINT。 

（2）所有整数类型、FLOAT 和 STRING 类型都可以隐式地转换成 DOUBLE。 

（3）TINYINT、SMALLINT、INT 都可以转换为 FLOAT。 

（4）BOOLEAN 类型不可以转换为任何其它的类型。

2、可以使用 CAST 操作显示进行数据类型转换

例如 CAST('1' AS INT)将把字符串'1' 转换成整数 1；如果强制类型转换失败，如执行 CAST('X' AS INT)，表达式返回空值 NULL。

## 5、DDL-data definition language

### 1、数据库的增删改查

1、创建数据库

数据库在 HDFS 上的默认存储路径是/user/hive/warehouse/*.db。

```sql
create database db_hive if not exists db_hive;

-- 指定数据库在 HDFS 上存放的位置
create database db_hive2 location '/db';
```

2、查询数据库

```sql
-- 过滤显示查询的数据库（模糊查询）
show databases like 'db_hive*';

-- 查看数据库详情，extended表示显示数据库详细信息
desc database db_hive;
desc database extended db_hive;
```

3、修改数据库

用户可以使用 ALTER DATABASE 命令为某个数据库的 DBPROPERTIES 设置键-值对 属性值，来描述这个数据库的属性信息。数据库的其他元数据信息都是不可更改的，包括数据库名和数据库所在的目录位置。

```sql
-- 增加时间戳信息
alter database hive set
dbproperties('createtime'='20170830');
```

4、删除数据库

```sql
 -- 删除空数据库
 drop database if exists db_hive;
 
 -- 删除非空数据库
 drop database db_hive cascade;
```

### 2、创建表

1、建表语法

```sql
CREATE [EXTERNAL] TABLE [IF NOT EXISTS] table_name
[(col_name data_type [COMMENT col_comment], ...)]
[COMMENT table_comment]
[PARTITIONED BY (col_name data_type [COMMENT col_comment], ...)]
[CLUSTERED BY (col_name, col_name, ...)
[SORTED BY (col_name [ASC|DESC], ...)] INTO num_buckets BUCKETS]
[ROW FORMAT row_format]
[STORED AS file_format]
[LOCATION hdfs_path]
```

字段解释：

（1）CREATE TABLE 创建一个指定名字的表。如果相同名字的表已经存在，则抛出 异常；用户可以用 IF NOT EXISTS 选项来忽略这个异常。 

（2）EXTERNAL 关键字可以让用户创建一个外部表，在建表的同时指定一个指向实际 数据的路径（LOCATION），Hive 创建内部表时，会将数据移动到数据仓库指向的路 径；若创建外部表，仅记录数据所在的路径，不对数据的位置做任何改变。在删除表的 时候，内部表的元数据和数据会被一起删除，而外部表只删除元数据，不删除数据。 

（3）COMMENT：为表和列添加注释。 

（4）PARTITIONED BY 创建分区表 。

（5）CLUSTERED BY 创建分桶表 。

（6）SORTED BY 不常用。

（7）ROW FORMAT

```sql
DELIMITED [FIELDS TERMINATED BY char] [COLLECTION ITEMS
TERMINATED BY char]
 [MAP KEYS TERMINATED BY char] [LINES TERMINATED BY char]
 | SERDE serde_name [WITH SERDEPROPERTIES (property_name=property_value,
property_name=property_value, ...)]
```

用户在建表的时候可以自定义 SerDe 或者使用自带的 SerDe。如果没有指定 ROW FORMAT 或者 ROW FORMAT DELIMITED，将会使用自带的 SerDe。在建表的时候，用户还需要为表指定列，用户在指定表的列的同时也会指定自定义的 SerDe，Hive 通过 SerDe 确定表的具体的列的数据。 

SerDe 是 Serialize/Deserilize 的简称，目的是用于序列化和反序列化。

（8）STORED AS 指定存储文件类型

常用的存储文件类型：SEQUENCEFILE（二进制序列文件）、TEXTFILE（文本）、 RCFILE（列式存储格式文件） 如果文件数据是纯文本，可以使用 STORED AS TEXTFILE。如果数据需要压缩， 使用 STORED AS SEQUENCEFILE。

（9）LOCATION ：指定表在 HDFS 上的存储位置。

（10）LIKE 允许用户复制现有的表结构，但是不复制数据。

### 3、内部表

1、理论

默认创建的表都是所谓的管理表，有时也被称为内部表。因为这种表，Hive 会（或多 或少地）控制着数据的生命周期。Hive 默认情况下会将这些表的数据存储在由配置项 hive.metastore.warehouse.dir(例如，/user/hive/warehouse)所定义的目录的子目录下。

当我们删除一个管理表时，Hive 也会删除这个表中数据。管理表不适合和其他工具共享数据。

2、操作

（1）普通创建表

```sql
create table if not exists student(
id int, name string
)row format delimited fields terminated by '\t'
stored as textfile
location '/user/hive/warehouse/student';
```

使用下面的命令来查询表的类型：

```sql
 desc formatted student;
```

（2）删除

```sql
 drop table student;
```

当我们删除一个管理表时，Hive /mysql中删除这个表中数据。

### 4、外部表

1、介绍

因为表是外部表，所以 Hive 并非认为其完全拥有这份数据。删除该表并不会删除掉这份数据，不过描述表的元数据信息会被删除掉。

2、使用场景

每天将收集到的网站日志定期流入 HDFS 文本文件。在外部表（原始日志表）的基础 上做大量的统计分析，用到的中间表、结果表使用内部表存储，数据通过 SELECT+INSERT 进入内部表。

```sql
create external table if not exists student2(
id int,
name string
)row format delimited fields terminated by '\t';
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200914103937310.png" alt="image-20200914103937310" style="zoom:67%;" />

3、内部表和外部表的互换

```sql
alter table student2 set tblproperties('EXTERNAL'='TRUE');
```

其中：('EXTERNAL'='TRUE')和('EXTERNAL'='FALSE')为固定写法，区分大小写。

### 5、分区表

分区表实际上就是对应一个 HDFS 文件系统上的独立的文件夹，该文件夹下是该分区 所有的数据文件。Hive 中的分区就是分目录，把一个大的数据集根据业务需要分割成小的 数据集。在查询时通过 WHERE 子句中的表达式选择查询所需要的指定的分区，这样的查询效率会提高很多。

1、基本操作

（1）创建分区表语法

```sql
create table partition01(
id int, name string
)partitioned by (month string)
row format delimited fields terminated by '\t';
```

（2）加载数据到分区表中

```sql
load data local inpath '/home/zookeeper/software/student.txt' into table db03.partition01 partition(month='202010');
```

（3）查询分区表中的数据

```sql
select * from partition01 where month='202010';
```

（4）增加分区

```sql
alter table partition01 add partition(month='202009') ;

-- 同时增加多个分区，分区之间使用空格分开
alter table partition01 add
partition(month='202009') partition(month='202008');
```

（5）删除分区

```sql
alter table partition01 drop partition(month='202008');

-- 删除多个分区，分区之间使用逗号隔开
alter table partition01 drop partition
(month='202008'), partition (month='202009');
```

（6）查看分区表有多少个分区

```sql
 show partitions partition01;
```

2、二级分区表

（1）创建二级分区表

```sql
create table partition02(
 id int, name string
 )partitioned by (month string, day string)
row format delimited fields terminated by '\t';
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200914132918506.png" alt="image-20200914132918506" style="zoom:80%;" />

（2）加载数据 

```sql
load data local inpath
'/home/zookeeper/software/student.txt' into table
db03.partition02 partition(month='202010', day='11');
```

（3）查询分区数据

```sql
select * from dept_partition2 where month='202010' and day='11';
```

3、分区表和数据产生关联

（1）创建hadoop文件夹

```sql
hive > dfs -mkdir -p
/user/hive/warehouse/db03.db/partition03/month=202010/day=12;
```

（2）上传数据

```sql
hive > dfs -put /home/zookeeper/software/student.txt
/user/hive/warehouse/db03.db/partition03/month=202010/day=12;
```

（3）创建二级分区表partition03

（4）添加分区

```sql
alter table partition03 add partition(month='202010',day='12');
```

这样的话，hadoop中的数据就和MySQL中的数据相关联，通过下面的语句就可以查询到数据了

```sql
select * from partition03 where
month='202010' and day='12';
```

### 6、修改表

1、重命名表

```sql
-- 语法
ALTER TABLE table_name RENAME TO new_table_name;

-- 举例
alter table test01 rename to test02;
```

2、表中增加|替换列

```sql
-- 语法
ALTER TABLE table_name ADD|REPLACE COLUMNS (col_name data_type [COMMENT col_comment], ...) 

-- 举例
alter table test01 add columns(address string);
```

其中ADD表示新增一个列的字段，而REPLACE表示替换表中所有的字段。

3、表中修改（更新）列

```sql
-- 语法
ALTER TABLE table_name CHANGE [COLUMN] col_old_name col_new_name column_type [COMMENT col_comment] [FIRST|AFTER column_name]

-- 举例
 alter table test01 change column
id did int;
```

## 6、DML-Data Manipulation Language

### 1、数据导入

1、向表中装载数据（Load）

```sql
hive> load data [local] inpath '/opt/module/datas/student.txt' [overwrite] | into table student
[partition (partcol1=val1,…)];

（1）load data:表示加载数据
（2）local:表示从本地加载数据到 hive 表；否则从 HDFS 加载数据到 hive 表
（3）inpath:表示加载数据的路径
（4）overwrite:表示覆盖表中已有数据，否则表示追加
（5）into table:表示加载到哪张表
（6）student:表示具体的表
（7）partition:表示上传到指定分区
```

2、 通过查询语句向表中插入数据（Insert）

```sql
-- 创建一张分区表
create table student(id int, name string)
partitioned by (month string) row format delimited fields
terminated by '\t';

-- 将查询到的test02表中的数据插入到test01中
hive > insert overwrite table test01
partition(month='202010')
select id, name from test02 where month='202010';
```

3、查询语句中创建表并加载数据（As Select）

```sql
-- 创建和test02相同的表test01
create table if not exists test01
as select id, name from test02;
```

4、创建表时通过 Location 指定加载数据路径

```sql
 create table if not exists test02(
 id int, name string
 )row format delimited fields terminated by '\t'
 location '/user/hive/warehouse/student5';
```

### 2、数据导出

1、Insert导出

将查询的结果格式化地导出到本地，当没有local关键字时表示将查询地结果导出到HDFS中。

```sql
hive > insert overwrite local directory '/home/zookeeper/software/student2' 
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
select * from student2;
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200914142721042.png" alt="image-20200914142721042" style="zoom:67%;" />

2、Hadoop命令导出到本地

```shell
hive > dfs -get /user/hive/warehouse/student/month=201709/000000_0
/home/zookeeper/software/student2
```

3、Hive shell命令导出

```sql
[xx@hadoop102 hive]$ bin/hive -e 
'select * from db03.student;' >
/opt/module/datas/export/student.txt;
```

4、Export 导出到 HDFS 中

```sql
hive > export table db03.student to
'/user/hive/warehouse/export/student';
```

5、Sqoop导出

### 3、清除表中的数据

```sql
-- Truncate 只能删除管理表，不能删除外部表中数据
hive > truncate table student;
```

## 7、查询

### 1、介绍

1、基本语法

```sql
[WITH CommonTableExpression (, CommonTableExpression)*] (Note:Only available starting with Hive 0.13.0)
SELECT [ALL | DISTINCT] select_expr, select_expr, ...
  FROM table_reference
  [WHERE where_condition]
  [GROUP BY col_list]
  [ORDER BY col_list]
  [CLUSTER BY col_list
   | [DISTRIBUTE BY col_list] [SORT BY col_list]
  ]
  [LIMIT number]
```

参考文档：

```http
https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Select
```

注意点：

（1）SQL 语言大小写不敏感。 

（2）SQL 可以写在一行或者多行 。

（3）关键字不能被缩写也不能分行 。

（4）各子句一般要分行写。 

（5）使用缩进提高语句的可读性。

2、列别名

```sql
-- 在列名和别名之间添加关键字AS
hive > select ename AS name from emp;
```

3、算术运算符

![image-20200914143947595](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200914143947595.png)

~A 表示A按位取反。

案例：查询出所有员工的薪水后加 1 显示

```sql
hive > select sal +1 from emp;
```

4、常用函数

```sql
1．求总行数（count）
hive > select count(*) cnt from emp;

2．求工资的最大值（max）
hive > select max(sal) max_sal from emp;

3．求工资的最小值（min）
hive > select min(sal) min_sal from emp;

4．求工资的总和（sum）
hive > select sum(sal) sum_sal from emp;

5．求工资的平均值（avg）
hive > select avg(sal) avg_sal from emp;
```

5、Limit语句

```sql
-- LIMIT 子句用于限制返回的行数为5行
hive > select * from emp limit 5;
```

### 2、where语句

1、介绍

（1）使用 WHERE 子句，将不满足条件的行过滤掉 。

（2）WHERE 子句紧随 FROM 子句。

2、 比较运算符（Between/In/ Is Null）

下面表中描述了谓词操作符，这些操作符同样可以用于 JOIN…ON 和 HAVING 语句中

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200914144526943.png" alt="image-20200914144526943" style="zoom:80%;" />

![image-20200914144540075](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200914144540075.png)

案例

（1）查询工资在 500 到 1000 的员工信息

```sql
hive > select * from emp where sal between 500 and 1000;
```

（2）查询 comm 为空的所有员工信息

```sql
hive > select * from emp where comm is null;
```

（3）查询工资是 1500 或 5000 的员工信息

```sql
hive > select * from emp where sal IN (1500, 5000);
```

3、 Like 和 RLike

（1）使用 LIKE 运算选择类似的值。

（2）选择条件可以包含字符或数字: 

% 代表零个或多个字符(任意个字符)， _ 代表一个字符。 

（3）RLIKE 子句是 Hive 中这个功能的一个扩展，其可以通过 Java 的正则表达式这个更强大的语言来指定匹配条件。

案例：

```sql
-- 查找以 2 开头薪水的员工信息
hive > select * from emp where sal LIKE '2%';

-- 查找薪水中含有 2 的员工信息
hive > select * from emp where sal RLIKE '[2]';
```

4、逻辑运算符（And/Or/Not）

![image-20200914145222857](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200914145222857.png)

### 3、分组

1、 Group By 语句

GROUP BY 语句通常会和聚合函数一起使用，按照一个或者多个列队结果进行分组， 然后对每个组执行聚合操作。

```sql
-- 计算 emp 表每个部门的平均工资
hive > select t.deptno, avg(t.sal) avg_sal from emp t
group by t.deptno;
```

### 4、Join语句

1、等值 Join

Hive 支持通常的 SQL JOIN 语句，但是只支持等值连接，不支持非等值连接。

2、笛卡尔积

（1）产生条件

省略连接条件 ，连接条件无效 ，所有表中的所有行互相连接。

（2）案例

```sql
hive >  select empno, dname from emp, dept;
```

3、连接谓词中不支持 or

```sql
-- 会报错
select * from emp e join dept d on
e.deptno=d.deptno or e.ename=d.dname;
```

### 5、排序

1、介绍

使用order by子句排序

```sql
ASC（ascend）: 升序（默认）
DESC（descend）: 降序
```

案例1：多个列排序-按照部门和工资升序排序

```sql
hive > select ename, deptno, sal from emp order by deptno,sal ;
```

2、Sort by排序

Sort By：每个 Reducer 内部进行排序，对全局结果集来说不是排序。

```sql
-- 设置 reduce 个数
hive > set mapreduce.job.reduces=3;
-- 查看设置 reduce 个数
hive > set mapreduce.job.reduces;
-- 根据部门编号降序查看员工信息
hive > select * from emp sort by empno desc;
-- 将查询结果导入到文件中（按照部门编号降序排序）
hive > insert overwrite local directory
'/opt/module/datas/sortby-result'
select * from emp sort by deptno desc;
```

但是，我们推荐使用下面的方式（加上分区排序）进行排序。

3、分区排序（Distribute By）

Distribute By：类似 MR 中 partition，进行分区，结合 sort by 使用。 

注意，Hive 要求 DISTRIBUTE BY 语句要写在 SORT BY 语句之前。 

对于 distribute by 进行测试，一定要分配多 reduce 进行处理，否则无法看到 distribute by 的效果。

案例：先按照部门编号分区，再按照员工编号降序排序。

```sql
hive > set mapreduce.job.reduces=3;

hive > insert overwrite local directory
'/opt/module/datas/distribute-result' select * from emp
distribute by deptno sort by empno desc;
```

4、 Cluster By

当 distribute by 和 sorts by 字段相同时，可以使用 cluster by 方式。 

cluster by 除了具有 distribute by 的功能外还兼具 sort by 的功能。但是排序只能是升序排 序，不能指定排序规则为 ASC 或者 DESC。

```sql
hive > select * from emp cluster by deptno;
hive > select * from emp distribute by deptno sort by deptno;
```

### 6、分桶及抽样查询

1、介绍

分区针对的是数据的存储路径；分桶针对的是数据文件。

分区提供一个隔离数据和优化查询的便利方式。不过，并非所有的数据集都可形成合理的分区，特别是之前所提到过的要确定合适的划分大小这个疑虑。

 分桶是将数据集分解成更容易管理的若干部分的另一个技术。

2、创建分桶表，数据通过子查询的方式导入

（1）创建一个普通的stu表

```sql
create table stu(id int, name string)
row format delimited fields terminated by '\t';
```

（2）向stu 表中导入数据

```sql
load data local inpath '/opt/module/datas/student.txt' into table stu;
```

（3）清空stu_buck表中数据

```sql
-- stu_buck表结构和stu同
truncate table stu_buck;
select * from stu_buck;
```

（4）通过子查询的方式，导入数据到分桶表

```sql
insert into table stu_buck
select id, name from stu;
```

（5）设置分桶表属性

```sql
hive > set hive.enforce.bucketing=true;
hive > set mapreduce.job.reduces=-1;
hive > insert into table stu_buck
select id, name from stu;
```

可以在前端界面中看到，stu_buck已经分成了4个文件。

3、分桶抽样查询

对于非常大的数据集，有时用户需要使用的是一个具有代表性的查询结果而不是全部结果。Hive 可以通过对表进行抽样来满足这个需求。

查询表 stu_buck 中的数据：

```sql
-- ：tablesample 是抽样语句，语法：TABLESAMPLE(BUCKET x OUT OF y) 。
hive > select * from stu_buck tablesample(bucket 1 out of 4 on id);
```

x 表示从哪个 bucket 开始抽取，y决定了抽多少个bucket。

y 必须是 table 总 bucket 数的倍数或者因子。hive 根据 y 的大小，决定抽样的比例。

例 如，table 总共分了 4 份，当 y=2 时，抽取(4/2=)2 个 bucket 的数据，当 y=8 时，抽取(4/8=)1/2 个 bucket 的数据。

如果需要取多个分区，以后的分区号为当前分区号加上y。例如，table 总 bucket 数为 4，tablesample(bucket 1 out of 2)，表示总共抽取（4/2=）2 个 bucket 的数据，抽取第 1(x)个和第 3(x+y)个 bucket 的数据（后一个是x + 2y）。

其中，x 的值必须小于等于 y 的值，否则会报出下面的错误：

```
FAILED: SemanticException [Error 10061]: Numerator should not be bigger than denominator in sample clause for table stu_buck
```

### 7、其他常用函数

1、空字段赋值

（1）基本语法

```sql
-- 如果员工的 comm 为 NULL，则用-1 代替
hive > select nvl(comm,-1) from emp;
```

NVL：给值为 NULL 的数据赋值，它的格式是 NVL( string1, replace_with)。它的功能是如果 string1 为 NULL，则 NVL 函数返回 replace_with 的值。

2、时间类

（1）date_format:格式化时间

```sql
 select date_format('2019-06-29','yyyy-MM-dd');
```

（2）date_add:时间跟天数相加

```sql
-- 天数为-5表示时间相减
select date_add('2019-06-29',5);
```

3、case when

（1）需求

求出下面数据中不同部门的男女各多少人，结果如下：

```
A 2 1
B 1 2
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200914184810562.png" alt="image-20200914184810562" style="zoom:80%;" />

（2）将下面的数据添加到emp_sex.txt文件中

```
悟空	A	男
大海	A	男
宋宋	B	男
凤姐	A	女
婷姐	B	女
婷婷	B	女
```

（3）创建hive表并导入数据

```sql
create table emp_sex(
name string,
dept_id string,
sex string)
row format delimited fields terminated by "\t";


load data local inpath '/home/zookeeper/software/emp_sex.txt' into table emp_sex;
```

（4）按照要求查询数据

```sql
select
 dept_id,
 sum(case sex when '男' then 1 else 0 end) male_count,
 sum(case sex when '女' then 1 else 0 end) female_count
from
 emp_sex
group by
 dept_id;
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200914185646820.png" alt="image-20200914185646820" style="zoom:80%;" />

4、行转列

（1）介绍

CONCAT(string A/col, string B/col…)：返回输入字符串连接后的结果，支持任意个输入 字符串。

CONCAT_WS(separator, str1, str2,...)：它是一个特殊形式的 CONCAT()。第一个参数剩表示参数间的分隔符。分隔符可以是与剩余参数一样的字符串。如果分隔符是 NULL， 返回值也将为 NULL。这个函数会跳过分隔符参数后的任何 NULL 和空字符串。分隔符将被加到被连接的字符串之间。

COLLECT_SET(col)：函数只接收基本数据类型，它的主要作用是将某字段的值进行去重汇总，产生 array 类型字段。

（2）需求

把星座和血型一样的人归类到一起。数据如下：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200914190046779.png" alt="image-20200914190046779" style="zoom:80%;" />

结果如下：

```
射手座,A	大海|凤姐
白羊座,A	孙悟空|猪八戒
白羊座,B	宋宋
```

（3）创建本地文件 constellation.txt

```
孙悟空	白羊座	A
大海	射手座	A
宋宋	白羊座	B
猪八戒	白羊座	A
凤姐	射手座	A
```

（4）创建hive表并导入数据

```sql
create table person_info(
name string,
constellation string,
blood_type string)
row format delimited fields terminated by "\t";

load data local inpath "/home/zookeeper/software/constellation.txt" into table person_info;
```

（5）按需求查询数据

```sql
select name,concat(constellation, ",", blood_type) 
 from person_info;
 -- 首先得到的结果如下：
射手座,A	大海
射手座,A 凤姐
白羊座,A	孙悟空
白羊座,A 猪八戒
白羊座,B	宋宋

-- 将得到的结果进行嵌套，得到图示的结果
select
 t1.base,concat_ws('|', collect_set(t1.name)) name
from
 (select name,concat(constellation, ",", blood_type) base
 from person_info) t1
group by
 t1.base;
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200914190730860.png" alt="image-20200914190730860" style="zoom:80%;" />

5、列转行

（1）介绍

EXPLODE(col)：将 hive 一列中复杂的 array 或者 map 结构拆分成多行。

```sql
LATERAL VIEW ：LATERAL VIEW udtf(expression) tableAlias AS columnAlias 
```

解释：用于和 split, explode 等 UDTF 一起使用，它能够将一列数据拆成多行数据，在此基础上可以对拆分后的数据进行聚合。

（2）将电影分类中的数组数据展开，输入如下：

![image-20200914191559824](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200914191559824.png)

结果如下：

```
《疑犯追踪》	悬疑
《疑犯追踪》	动作
《疑犯追踪》	科幻
《疑犯追踪》	剧情
《Lie to me》	悬疑
《Lie to me》	警匪
《Lie to me》	动作
《Lie to me》	心理
《Lie to me》	剧情
《战狼 2》	战争
《战狼 2》	动作
《战狼 2》	灾难
```

（3）创建本地movie.txt，导入下面的数据

```
《疑犯追踪》	悬疑,动作,科幻,剧情
《Lie to me》	悬疑,警匪,动作,心理,剧情
《战狼 2》	战争,动作,灾难
```

（4）创建 hive 表并导入数据

```sql
create table movie_info(
 movie string,
 category array<string>)
row format delimited fields terminated by "\t"
collection items terminated by ",";


load data local inpath "/home/zookeeper/software/movie.txt" into table
movie_info;
```

（5）按照需求查询数据

```sql
select
 movie,
 category_name
from
 movie_info lateral view explode(category) table_tmp as
category_name;
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200914192214041.png" alt="image-20200914192214041" style="zoom:67%;" />

6、窗口函数

（1）介绍

```
OVER()：指定分析函数工作的数据窗口大小，这个数据窗口大小可能会随着行的变化而变化；
CURRENT ROW：当前行；
n PRECEDING：往前 n 行数据；
n FOLLOWING：往后 n 行数据；
UNBOUNDED：起点，UNBOUNDED PRECEDING 表示从前面的起点，UNBOUNDED FOLLOWING 表示到后面的终点；
LAG(col,n)：往前第 n 行数据；
LEAD(col,n)：往后第 n 行数据；
NTILE(n)：把有序分区中的行分发到指定数据的组中，各个组有编号，编号从 1 开始，
对于每一行，NTILE 返回此行所属的组的编号。注意：n 必须为 int 类型。
```

（2）创建本地 business.txt，并导入下面的数据

```
jack,2017-01-01,10
tony,2017-01-02,15
jack,2017-02-03,23
tony,2017-01-04,29
jack,2017-01-05,46
jack,2017-04-06,42
tony,2017-01-07,50
jack,2017-01-08,55
mart,2017-04-08,62
mart,2017-04-09,68
neil,2017-05-10,12
mart,2017-04-11,75
neil,2017-06-12,80
mart,2017-04-13,94
```

（3）创建 hive 表并导入数据

```sql
create table business(
name string,
orderdate string,
cost int
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',';

load data local inpath "/home/zookeeper/software/business.txt" into table
business;
```

（4）需求1-查询在 2017 年 4 月份购买过的顾客及总人数

```sql
-- over ()写在聚合函数后面，为每一条数据开窗
select name,count(*) over ()
from business
where substring(orderdate,1,7) = '2017-04'
group by name;

-- 去掉 over ()，输出结果如下：
jack 1
mark 4
-- 去掉 group by name，输出结果如下：
jack 5
mark 5
mark 5
mark 5
mark 5
```

![image-20200914195654282](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200914195654282.png)

over ()表示开窗，开窗的作用范围，仅仅是给count(*)使用，当执行到mart时开一个窗，输出结果为2（此时就是mart和jack两个数据）。

（5）需求2-查询顾客的购买明细及购买总额，并将 cost 按照日期进行累加。

```sql
-- 对每一个数据都开了一个独立的窗口
select orderdate,cost,sum(cost) over(order by orderdate)
from business;
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200914202235733.png" alt="image-20200914202235733" style="zoom:80%;" />

（6）需求3-将每个人的购买明细累加

```sql
select name,orderdate,cost,sum(cost) over(distribute by name sort by orderdate) from business;
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200914203007580.png" alt="image-20200914203007580" style="zoom:80%;" />

7、Rank排名函数

（1）介绍

```
RANK() 排序相同时会重复，总数不会变
DENSE_RANK() 排序相同时会重复，总数会减少
ROW_NUMBER() 会根据顺序计算
```

举例如下：

| 成绩 | RANK()排序 | DENSE_RANK()排序 | ROW_NUMBER()排序 |
| ---- | ---------- | ---------------- | ---------------- |
| 84   | 1          | 1                | 1                |
| 84   | 1          | 1                | 2                |
| 78   | 3          | 2                | 3                |
| 76   | 4          | 3                | 4                |

（2）需求

计算英语学科成绩排名。

（a）将下面的数据插入到grade.txt中。

```
孙悟空	英语	68
大海	英语	84
宋宋	英语	84
婷婷	英语	78

select name,
subject,
score,
rank() over(partition by subject order by score desc) rp,
dense_rank() over(partition by subject order by score desc) drp,
row_number() over(partition by subject order by score desc) rmp
from score;
```

（b）创建 hive 表并导入数据

```sql
create table score(
name string,
subject string,
score int)
row format delimited fields terminated by "\t";

hive> load data local inpath '/home/zookeeper/software/grade.txt' into table score;
```

（c）按需求查询数据

```sql
-- 按照sroce排序进行累计，order by是个默认的开窗函数，按照subject分区。
select name,
subject,
score,
rank() over(partition by subject order by score desc) rp,
dense_rank() over(partition by subject order by score desc) drp,
row_number() over(partition by subject order by score desc) rmp
from score;
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200914204421891.png" alt="image-20200914204421891" style="zoom:80%;" />

