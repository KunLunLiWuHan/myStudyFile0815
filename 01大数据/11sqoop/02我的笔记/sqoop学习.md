## 1、介绍

1、概述

Sqoop 是一款开源的工具，主要用于在 Hadoop(Hive)与传统的数据库(mysql、postgresql...) 间进行数据的传递，可以将一个关系型数据库（例如 ： MySQL ,Oracle ,Postgres 等）中的 数据导进到 Hadoop 的 HDFS 中，也可以将 HDFS 的数据导进到关系型数据库中。 Sqoop 项目开始于 2009 年，最早是作为 Hadoop 的一个第三方模块存在，后来为了让使 用者能够快速部署，也为了让开发人员能够更快速的迭代开发，Sqoop 独立成为一个 Apache 项目。

2、原理

将导入或导出命令翻译成 mapreduce 程序来实现。 在翻译出的 mapreduce 中主要是对 inputformat 和 outputformat 进行定制。

## 2、安装和部署

安装 Sqoop 的前提是已经具备 Java 和 Hadoop 的环境。

下载网址：

```http
http://218.197.116.221/archive.apache.org/dist/sqoop/1.4.6/sqoop-1.4.6.bin__hadoop-2.0.4-alpha.tar.gz
```

（1）将sqoop解压包放到/mysqoop/目录下

（2）修改sqoop的配置文件conf/sqoop-env-template.sh

```shell
# 在末尾添加下面的内容
export HADOOP_COMMON_HOME=/home/zookeeper/software/hadoop-2.7.2
export HADOOP_MAPRED_HOME=/home/zookeeper/software/hadoop-2.7.2
export HIVE_HOME=/myhive/hive
export ZOOKEEPER_HOME=/home/zookeeper/software/zookeeper-3.4.10
export ZOOCFGDIR=/home/zookeeper/software/zookeeper-3.4.10
export HBASE_HOME=/home/zookeeper/software/hbase-1.3.1
```

（3）拷贝JDBC驱动到sqoop的lib目录下

```shell
cp mysql-connector-java-5.1.35.jar  /mysqoop/sqoop/lib/
```

（4）检测配置情况

```shell
[zookeeper@hadoop101 sqoop]$ bin/sqoop help
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200915175011677.png" alt="image-20200915175011677" style="zoom: 50%;" />

（5）测试sqoop能否连接数据库

```sql
bin/sqoop list-databases --connect jdbc:mysql://hadoop101:3306/ --username root --password 123
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200915175134011.png" alt="image-20200915175134011" style="zoom: 67%;" />

## 3、导入数据

在 Sqoop 中，“导入”概念指：从非大数据集群（RDBMS）向大数据集群（HDFS，HIVE， HBASE）中传输数据，叫做：导入，即使用 import 关键字。

### 1、RDBMS 到 HDFS

（1）确定mysql服务正常启动

（2）在 Mysql 中新建一张表staff并插入一些数据

```sql
-- 创建新库company
create database company;

-- 创建新表
create table staff(id int(4) primary key not null
auto_increment, name varchar(255), sex varchar(255));

-- 插入2条数据
insert into company.staff(name, sex) values('aa', 'boy');
insert into company.staff(name, sex) values('bb','girl');
```

（3）全部导入数据

```sql
bin/sqoop import \
--connect jdbc:mysql://192.168.10.101:3306/company \
--username root \
--password 123 \
--table staff \
--target-dir /user/company \
--delete-target-dir \
--num-mappers 1 \
--fields-terminated-by "\t"
```

有时候会报出下面的错误：

```
Access denied for user 'root'@'192.168.10.102' to database 'company'
```

但是，我们可以通过下面的网址下载文件后，文件中包含mysql中的数据：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200915195024529.png" alt="image-20200915195024529" style="zoom: 50%;" />

```
--target-dir 指代hadoop上的目录。
--delete-target-dir 表示如果指定目录（--target-dir）存在，则先删除掉。
```

（4）查询导入

```sql
 bin/sqoop import \
--connect jdbc:mysql://192.168.10.101:3306/company \
--username root \
--password 123 \
--target-dir /user/company \
--delete-target-dir \
--num-mappers 1 \
--fields-terminated-by "\t" \
--query 'select name,sex from staff where id <=1 and $CONDITIONS;'
```

其中，$CONDITIONS需要添加上；如果 query 后使用的是双引号，则$CONDITIONS 前必须加转移符，防止 shell 识别为自己的 变量。

（5）导入指定列

```sql
 bin/sqoop import \
--connect jdbc:mysql://192.168.10.101:3306/company \
--username root \
--password 123 \
--target-dir /user/company \
--delete-target-dir \
--num-mappers 1 \
--fields-terminated-by "\t" \
--columns id \
--table staff
```

其中，columns 中如果涉及到多列，用逗号分隔，分隔时不要添加空格。

（6）使用 sqoop 关键字筛选查询导入数据

```sql
 bin/sqoop import \
--connect jdbc:mysql://192.168.10.101:3306/company \
--username root \
--password 123 \
--target-dir /user/company \
--delete-target-dir \
--num-mappers 1 \
--fields-terminated-by "\t" \
--table staff \
--where "id=1"
```

### 2、RDBMS 到 Hive

```sql
 bin/sqoop import \
--connect jdbc:mysql://192.168.10.101:3306/company \
--username root \
--password 123 \
--table staff \
--num-mappers 1 \
--hive-import \
--fields-terminated-by "\t" \
--hive-overwrite \
--hive-table staff_hive
```

将mysql表中的数据库company的表staff中的数据导入到hive中staff_hive中，默认情况下，导入到hive中的数据会放到default表中。

该过程分为两步，第一步将数据导入到 HDFS，第二步将导入到 HDFS 的数据迁移到 Hive 仓库，这个过程中会创建临时表。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200915200315476.png" alt="image-20200915200315476" style="zoom:67%;" />

### 3、RDBMS 到 Hbase

sqoop1.4.6 只支持 HBase1.0.1 之前的版本的自动创建 HBase 表的功能，因此，在将mysql数据导入到HBase之前，我们需要手动创建一个HBase表。

（1）创建HBase表

```sql
hbase> create 'hbase_company,'info'
```

（2）执行下面的代码，转移数据

```sql
bin/sqoop import \
--connect jdbc:mysql://192.168.10.101:3306/company \
--username root \
--password 123 \
--table company \
--columns "id,name,sex" \
--column-family "info" \
--hbase-create-table \
--hbase-row-key "id" \
--hbase-table "hbase_company" \
--num-mappers 1 \
--split-by id
```

（3）查询HBase中数据的导入情况

```sql
hbase> scan ‘hbase_company’
```

## 4、导出数据

在 Sqoop 中，“导出”概念指：从大数据集群（HDFS，HIVE，HBASE）向非大数据集群 （RDBMS）中传输数据，叫做：导出，即使用 export 关键字。

1、HIVE/HDFS 到 RDBMS

在Mysql 中，如果相应的表不存在，mysql不会自动创建。

```sql
$ bin/sqoop export \
--connect jdbc:mysql://192.168.10.101:3306/company \
--username root \
--password 123 \
--table staff \
--num-mappers 1 \
--export-dir /user/hive/warehouse/staff_hive \
--input-fields-terminated-by "\t"
```

执行上面的代码后，可以将hadoop中/user/hive/warehouse/staff_hive目录下的数据导入到mysql中。

2、脚本打包

使用 opt 格式的文件打包 sqoop 命令，然后执行

（1）创建一个.opt文件job_HDFS2RDBMS.opt

（2）编写sqoop脚本

```sql
export
--connect 
jdbc:mysql://192.168.10.101:3306/company
--username 
root
--password 
123
--table 
staff
--num-mappers 
1
--export-dir 
/user/hive/warehouse/staff_hive
--input-fields-terminated-by 
"\t"
```

上面的脚本分行写吧，这样才会执行。

（3）执行该脚本

```sql
 bin/sqoop --options-file /home/zookeeper/software/job_HDFS2RDBMS.opt
```

