# 1 MySQL锁机制

## 1、介绍

（1）锁的定义

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125224.png" alt="image-20200829225052018" style="zoom:80%;" />

（2）举例

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125225.png" alt="image-20200829225123898" style="zoom:80%;" />

（3）锁的分类

（a）按照对数据操作的类型（读/写）分：

读锁（共享锁）：针对同一份数据，每个读操作可以同时进行而不会相互影响。

写锁（排它锁）：当前写操作没有完成前，它会阻断其他写锁或者读锁。

（b）从对数据操作的粒度分：分为表锁和行锁。

## 2、表锁-偏读

### 1、特点

偏向于MyISAM存储引擎，开销小，加锁快，无死锁，锁定力度大，发生锁冲突的概率最高，并发度较低。

### 2、案例

1、建表SQL

```mysql
-- 引擎使用MyISAM
create table mylock(
id int not null primary key auto_increment,
name varchar(20)
)engine=MyISAM;

-- 插入数据
insert into mylock(name) value ('a');
insert into mylock(name) value ('b');
insert into mylock(name) value ('c');
insert into mylock(name) value ('d');
insert into mylock(name) value ('e');
```

2、加读锁

（1）手动增加表锁

```mysql
lock table 表名 read(write), 表名字2 read(write),其它;
--给mylock表加读锁（共享锁），给book表加写锁（排它锁）
lock table mylock read,book write;


-- 查看表锁的情况，In_Use=0表示无锁
show open tables;

-- 释放锁
unlock tables;
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125226.png" alt="image-20200830143326887" style="zoom:80%;" />

（2）开启两个session，在session1中对mylock表加读锁，在session2中对mylock进行一些操作。

（a）在session1中可以进行mylock进行下面的查询操作：

```mysql
select * from mylock;
```

但是不能进行下面的写操作，没有加锁的book表执行查询操作也不行：

```mysql
update mylock set name = 'a2' where id = 1;
select * from book;
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125227.png" alt="image-20200830144535736" style="zoom:80%;" />

（2）在session2中可以对mylock进行查询操作，也可以对book表进行查询，写操作，但是不能对mylock表进行写操作，此时会发生阻塞：

```mysql
update mylock set name = 'a2' where id = 1;
```

当session1对mylock表执行解锁操作后，session2中的阻塞会执行。

3、加写锁

在session1中添加写锁：

```mysql
lock table mylock write;
```

对于session1:

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125228.png" alt="image-20200830145309904" style="zoom:67%;" />

对于session2，可以读其他表，但是对mylock表读/写的权力都被剥夺，此时会处于阻塞状态，同样在session1解锁时阻塞会执行：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125229.png" alt="image-20200830145353131" style="zoom:67%;" />

4、总结

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125230.png" alt="image-20200830145743251" style="zoom:80%;" />

结合上表,所以对MyISAM表进行操作,会有以下情况: 

（1）对MyISAM表的读操作(加读锁) ,不会阻塞其他进程对同一表的读请求,但会阻塞对同一表的写请求。只有当读锁释放后,才会执行其它进程的写操作。

（2）对MyISAM表的写操作(加写锁) ,会阻塞其他进程对同一表的读和写操作,只有当写锁释放后,才会执行其它进程的读写操操作。

简而言之,就是读锁会阻塞写,但是不会堵塞读。而写锁则会把读和写都堵塞。

5、表锁分析

可以使用下面的SQL语句查看：

```mysql
 show status like 'table%';
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125231.png" alt="image-20200830150552433" style="zoom:67%;" />

下面两个状态变量记录MySQL内部表级锁定的情况，现说明如下：

Table_locks_immediate：表示表级锁锁定的次数，表示可以立即获取锁的查询次数，没立即获取锁值加1。

Table_locks_waited：出现表级锁锁定而等待的次数（即不能立即获取表的次数，没等待一次加1），该值高说明存在着严重的表级锁争用情况。

此外，MyISAM的读写锁调度是写优先，MyISAM不适合做写为主表的引擎。因为写锁后，其它线程不能做任何操作，大量的更 新会使查询很难得到锁，从而造成永远阻塞。

## 3、行锁-偏写

### 1、介绍

1、特点

偏向InnoDB存储引擎，开销大，加锁慢，会出现死锁，锁定粒度最小，发生锁冲突的概率最低，并发度也最高。

InnoDB与MyISAM的最大不同的两点：

+ 一是支持事务（transaction）
+ 二是采用了行级锁

2、事务辑器ACID属性

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125232.png" alt="image-20200830151538787" style="zoom:80%;" />

3、并发事务处理带来的问题

（1）更新丢失（Lost Update）

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125233.png" alt="image-20200830151946464" style="zoom:80%;" />

（2）脏读（Dirty Reads）

事务A读取到了事务B已修改但尚未提交的数据。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125234.png" alt="image-20200830152106743" style="zoom:80%;" />

（3）不可重复读（Non-Repeatable Reads）

事务A读取到了事务B已经提交的数据。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125235.png" alt="image-20200830152308587" style="zoom:80%;" />

（4）幻读（Phantom Reads）

事务A读取到了事务B提交的新增数据。

![image-20200830152421148](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125236.png)

脏读是事务B里面修改了数据，幻读是事务B里面新增了数据。

4、事务隔离级别

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125237.png" alt="image-20200830152623521" style="zoom:80%;" />

通过下面的指令查看当前数据库的事务隔离级别：

```mysql
show variables like 'tx_isolation';
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125238.png" alt="image-20200830153335575" style="zoom:80%;" />

### 2、案例

1、建表SQL

```mysql
create table test_innodb_lock (
a int(11),
b varchar(16)
)engine=innodb;

-- 插入数据，a少个2,为后来讨论间隙锁危害埋下铺垫
insert into test_innodb_lock values(1,'b1');
insert into test_innodb_lock values(3,'3');
insert into test_innodb_lock values(4,'4000');
insert into test_innodb_lock values(5,'5000');
insert into test_innodb_lock values(6,'6000');
insert into test_innodb_lock values(7,'7000');
insert into test_innodb_lock values(8,'8000');
insert into test_innodb_lock values(9,'9000');

-- 创建两个单值索引
create index test_innodb_a on test_innodb_lock(a);
create index test_innodb_b on test_innodb_lock(b);
```

2、行锁分析

（1）开启两个session，在session1中对mylock表加读锁，在session2中对mylock进行一些操作。

首先将两个session中的自动提交给关闭：

```mysql
set autocommit=0;
```

（2）在session1使用下面的语句更新一行记录，但是不执行自动提交：

```mysql
UPDATE test_innodb_lock SET b='4001' WHERE a=4;
-- 不执行提交操作
-- commit;
```

此时通过执行下面的查询语句，可以在session1中查到更新后的数据，但是在session2中看不到更新的数据：

```mysql
select * from test_innodb_lock;
```

主要原因是，在session1中没有提交数据。当session1提交数据后，能够在session2中执行上面的语句看到更新的变换。

（3）首先在session1执行下面的语句更新一行记录，但是不执行自动提交：

```mysql
-- session1中执行
UPDATE test_innodb_lock SET b='4002' WHERE a=4;

-- 之后在session2中执行
UPDATE test_innodb_lock SET b='4003' WHERE a=4;
```

然后再session2中更新一行记录，此时session2中的更新语句会被阻塞，当session1中执行提交后，阻塞会执行，并执行更新操作。

### 3、索引失效-行锁变表锁

在session1中执行下面的更新操作，但是不执行自动提交：

```mysql
--  故意将b='4006'单引号去掉
UPDATE test_innodb_lock SET a=41 WHERE b=4006;
```

此时，在session2中执行下面的语句，会发生阻塞：

```mysql
update test_innodb_lock set b='9001'  where a=9;
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125239.png" alt="image-20200830160422964" style="zoom:80%;" />

### 4、间隙锁危害

1、介绍

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125240.png" alt="image-20200830160606089" style="zoom:80%;" />

2、操作

在session1中执行下面的更新操作，但是不执行自动提交：

```mysql
-- 注意在建表插入数据过程中，没有a=2,有个间隙
update test_innodb_lock set b='0830' where a>1 and a<6;
```

此时，在session2中执行下面的语句，会发生阻塞：

```mysql
 insert into test_innodb_lock values(2,'2000');
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125241.png" alt="image-20200830161104641" style="zoom:67%;" />

### 5、锁定一行步骤

1、开始执行

```mysql
begin;
```

2、开启锁定

```mysql
-- select xxx.. for update 锁定某一行后，其他的操作会被阻塞，直到锁定行的会话提交commit
select * from test_innodb_lock where a=8 for update;
```

3、提交，释放锁资源

```mysql
commit;
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125242.png" alt="image-20200830161625540" style="zoom:80%;" />

### 6、总结

1、分析

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125243.png" alt="image-20200830161930705" style="zoom:80%;" />

2、查看行锁状态

执行下面的指令：

```mysql
 show status like 'innodb_row_lock%';
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125244.png" alt="image-20200830162104959" style="zoom:80%;" />

状态变量分析如下：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125245.png" alt="image-20200830162137342" style="zoom:80%;" />

3、优化建议

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125246.png" alt="image-20200830162157005" style="zoom:80%;" />

## 4、页锁

![image-20200830162219862](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125247.png)

了解一下即可。

# 2 主从复制

参考博客：

```http
https://www.cnblogs.com/xuekun/p/9517161.html
```

1、复制的基本原理

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125248.png" alt="image-20200830171852511" style="zoom:80%;" />

复制的最大问题是：延时。

2、复制的基本原则

（1）每个slave只有一个master。

（2）每个master只能有一个唯一的服务器ID。

（3）每个master可以有多个salve。

3、主机master的配置

注意：

主从机的mysql版本一致，且服务都在后台运行。主从配置都是在[mysqld]节点下配置的，都是小写。

（1）修改主服务器唯一Id（必须）

```ini
server.id=1
```

（2）启用二进制日志（必须）

```ini
log-bin=mysql-bin
```

（3）mysql的安装根目录

```ini
basedir=/usr/local/mysql
```

（4）mysql数据文件目录

```ini
datadir=/usr/local/mysql/data
```

3、从机salve配置

（1）修改从服务器Id

```ini
server.id=2
```

（2）启用二进制日志

```ini
log-bin=mysql-bin
```

4、因为修改过配置文件，在这里主机/从机需要重新启动后台mysql服务。

5、主机/从机都要关闭防火墙

6、在主机上建立账户并授权

（1）授权

```mysql
-- 授权复制给从机，允许从机以什么用户名和123这个密码登录到主机进行复制操作
grant replication slave on *.* to '用户名'@'从机数据库ip' identified by '密码';

-- 设置
grant replication slave on *.* to 'root'@'192.168.10.102' identified by '123';

flush privileges;
```

（2）查询master的状态

```mysql
show master status;
```

（3）记录下File和Position的值

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125249.png" alt="image-20200830173132974" style="zoom:67%;" />

执行完此步骤后，不要再操作主服务器MySQL，防止主服务器状态值变换。

7、在从机上配置需要复制的主机

（1）配置需要复制主机

```mysql
CHANGE MASTER TO MASTER_HOST='192.168.10.101',
MASTER_USER='root',MASTER_PASSWORD='123',
MASTER_LOG_FILE='mysql-bin.000006',MASTER_LOG_POS=602; 
```

（2）启动从服务器复制功能

```mysql
start slave;

-- 查看启动状态
show slave status\G
```

如果状态变量Slave_IO_Running和Slave_SQL_Running都为Yes，说明主从配置成功。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125250.png" alt="image-20200830173906713" style="zoom:67%;" />

注：

（1）在该配置文件中对应上从主服务器上查询到的File和Position的值。

（2）有可能这一步会出现执行不了的情况，可能是之前做过同步引起的，我们需要执行下面的操作来停止salve：

```mysql
stop slave;
```

（3）在重新配置过程中，需要再次执行show master status;命令，获取新的刻度后（可能会变），然后在配置复制主机的文件中对应。

8、测试

（1）在主服务器中建立一个新的数据库，创建表然后执行插入操作

```mysql
create database mydbTest;
use mydbTest;
create table dog(id int not null,name varchar(10));

insert into dog values(1,'z3');
insert into dog values(1,'li3');
...
```

（2）在从服务器中执行查询操作，查看自己是否备份在主服务器中新创建的数据库。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125251.png" alt="image-20200830174259396" style="zoom:80%;" />

9、停止从服务复制功能

```mysql
-- 在从服务器上执行
stop slave;
```

