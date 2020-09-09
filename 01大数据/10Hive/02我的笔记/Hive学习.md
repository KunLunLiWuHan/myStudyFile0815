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













