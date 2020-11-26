# Windows下安装Redis

1. 从云盘上将Redis压缩包下载下来，然后将其解压到自己电脑上的环境目录（D:\03Enviroment\06Redis\Redis-x64-3.2.100）就可以了。Redis十分的小，约为5M。
2. 开启Redis,双击运行服务即可。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131932.png" alt="image-20200620190518635" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131933.png" alt="image-20200620190539587" style="zoom: 67%;" />

3. 使用Redis客户端连接Redis。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131934.png" alt="image-20200620190934328" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131935.png" alt="image-20200620190917954" style="zoom:80%;" />

windows下开发确实简单，但是Redis推荐我们使用Linux去开发。

# 1、NoSQL入门概述

NoSQL(NoSQL = Not Only SQL )，意即“不仅仅是SQL”，泛指非关系型的数据库。随着互联网web2.0网站的兴起，传统的关系数据库在应付web2.0网站，特别是超大规模和高并发的SNS类型的web2.0纯动态网站已经显得力不从心，暴露了很多难以克服的问题，而非关系型的数据库则由于其本身的特点得到了非常迅速的发展。

NoSQL数据库的产生就是为了解决大规模数据集合多重数据种类带来的挑战，尤其是大数据应用难题，包括超大规模数据的存储。

（例如谷歌或Facebook每天为他们的用户收集万亿比特的数据）。**这些类型的数据存储不需要固定的模式，无需多余操作就可以横向扩展。**

## 1、特性

1. 易扩展

NoSQL数据库种类繁多，但是一个共同的特点都是去掉关系数据库的关系型特性。

2. 大数据量高性能

NoSQL数据库都具有非常高的读写性能，尤其在大数据量下，同样表现优秀。

3. 多种多样的数据类型

NoSQL无需事先为要存储的数据建立字段，随时可以存储自定义的数据格式。

## 2、RDBMS vs NoSQL

```ini
RDBMS
- 高度组织化结构化数据
- 结构化查询语言（SQL）
- 数据和关系都存储在单独的表中。
- 数据操纵语言，数据定义语言
- 严格的一致性
- 基础事务
 
NoSQL
- 代表着不仅仅是SQL
- 没有声明性查询语言
- 没有预定义的模式
- 键 - 值对存储，列存储，文档存储，图形数据库
- 最终一致性，而非ACID属性
- 非结构化和不可预知的数据
- CAP定理
- 高性能，高可用性和可伸缩性
```

操作：KV+Cache+Persistence。

## 3、NoSQL数据库的四大分类和对比

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131936.png" alt="image-20200718101654040" style="zoom:80%;" />

## 4、分布式数据库CAP原理

CAP理论的核心是：一个分布式系统不可能同时很好的满足一致性，可用性和分区容错性这三个需求，最多只能同时较好的满足两个。

因此，根据 CAP 原理将 NoSQL 数据库分成了满足 CA 原则、满足 CP 原则和满足 AP 原则三大类，但分区容忍性是我们必须需要实现的，所以我们只能在一致性和可用性之间进行权衡，没有NoSQL系统能同时保证这三点。

```ini
C:Consistency（强一致性）。
A:Availability（可用性）。
P:Partition tolerance（分区容错性）。

CA - 单点集群，满足一致性，可用性的系统，通常在可扩展性上不太强大。
CP - 满足一致性，分区容忍性的系统，通常性能不是特别高。
AP - 满足可用性，分区容忍性的系统，通常可能对一致性要求低一些。

CA 传统Oracle数据库
AP 大多数网站架构的选择
CP Redis、Mongodb （ 单机Redis 属于 cp 模型。 Redis-cluster 属于 ap 模型）
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131937.png" alt="image-20200718110155890" style="zoom:67%;" />

**BASE**

BASE就是为了解决关系数据库强一致性引起的问题而引起的可用性降低而提出的解决方案。

BASE其实是下面三个术语的缩写：

```ini
基本可用（Basically Available）
软状态（Soft state）
最终一致（Eventually consistent）
```

它的思想是通过让系统放松对某一时刻数据一致性的要求来换取系统整体伸缩性和性能上改观。

## 5、分布式和集群简介

分布式系统（distributed system）由多台计算机和通信的软件组件通过计算机网络连接（本地网络或广域网）组成。分布式系统是建立在网络之上的软件系统。正是因为软件的特性，所以分布式系统具有高度的内聚性和透明性。

因此，网络和分布式系统之间的区别更多的在于高层软件（特别是操作系统），而不是硬件。分布式系统可以应用在在不同的平台上如：Pc、工作站、局域网和广域网上等。可以总结如下：

分布式：不同的多台服务器上面部署不同的服务模块（工程），他们之间通过Rpc（Remote Procedure Call远程过程调用）/Rmi（Remote Method Invoke 远程方法调用）之间通信和调用，对外提供服务和组内协作。（一个任务需要多个节点共同完成，这个任务的执行方式就是分布式的）

集群：不同的多台服务器（节点）上面部署相同的服务模块，通过分布式调度软件进行统一的调度，对外提供服务和访问，来完成一个任务（存储或者计算等）。

负载均衡：一个集群中，各个节点承担的压力（存储）相当。负载均衡一定要和每一个节点的硬件配置相关。

# 2、Redis入门介绍

## 2.1入门概述

​		Redis:REmote DIctionary Server(远程字典服务器)，是完全开源免费的，用C语言编写的，遵守BSD协议，是一个高性能的(key/value)分布式内存数据库，基于内存运行并支持持久化的NoSQL数据库，是当前最热门的NoSql数据库之一,也被人们称为数据结构服务器。

Redis 与其他 key - value 缓存产品有以下三个特点：

1. Redis支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用。
2. Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。
3. Redis支持数据的备份，即master-slave模式的数据备份。

我们可以使用Redis执行下面的功能：

1. 内存存储和持久化：redis支持异步将内存中的数据写到硬盘上，同时不影响继续服务。
2. 取最新N个数据的操作，如：可以将最新的10条评论的ID放在Redis的List集合里面。
3. 模拟类似于HttpSession这种需要设定过期时间的功能。
4. 发布、订阅消息系统。
5. 定时器、计数器。

## 2.2 安装和配置 

**1、下载地址**

外网下载：

```http
Http://redis.io/
```

各个版本的下载地址：

```http
http://download.redis.io/releases/
```

中文官方网站：

```http
Http://www.redis.cn/
```

**2、安装**

环境：Centos6.8。

1. 解压

以root用户（192.168.10.129）登录，下载获得redis-3.0.4.tar.gz后，将它放入我们的Linux目录/opt并解压。

2. 执行make/make install命令

首先进入到redis-3.0.4目录下：

分别执行`make,make install`命令（目录：[zookeeper@hadoop101 redis-3.0.4]$ ）。

安装后之后，我们可以在下面的目录中看到redis选项。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131938.png" alt="image-20200718151547920" style="zoom:80%;" />

在ubuntu18.04上按照上面步骤进行安装，在执行`make`会遇到下面的问题：

（1）缺少文件

![image-20201108174657621](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108174657621.png)

```shell
sudo apt-get install libc6-dev
```

（2）libc不是默认的分配器

![image-20201108174812984](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108174812984.png)

```shell
sudo make MALLOC=libc
```

3. 查看安装目录

   默认为`usr/local/bin`：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131939.png" alt="image-20200718151936733" style="zoom:80%;" />

配置文件说明：

```ini
Redis-benchmark:性能测试工具，可以在自己本子运行，看看自己本子性能如何
Redis-check-aof：修复有问题的AOF文件，rdb和aof后面讲
Redis-check-dump：修复有问题的dump.rdb文件
Redis-cli：客户端，操作入口
Redis-sentinel：redis集群使用
Redis-server：Redis服务器启动命令
```

4. 启动

   （1）修改redis.conf文件将里面的`daemonize no` 改成 yes，让服务在后台启动。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131940.png" alt="image-20200718152312774" style="zoom:80%;" />

​		（2）为了避免配置出错，将默认的`/opt/redis-3.0.4/redis.conf`拷贝到自己定义好的一个路径下，比如/myRedis中。

​		（3）启动Redis时，运行自定义的/myRedis中的配置文件。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131941.png" alt="image-20200718152914239" style="zoom:80%;" />

5. 连通测试

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131942.png" alt="image-20200718153020322" style="zoom:80%;" />

6. 关闭

   （1）单实例关闭

```ini
redis-cli shutdown
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131943.png" alt="image-20200718153507733" style="zoom:80%;" />

​		可以看出，redis的进程已经关闭：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131944.png" alt="image-20200718153526483" style="zoom:80%;" />

​		（2）多实例关闭

```ini
#指定端口关闭
redis-cli -p 6379 shutdown
```

## 2.3 杂项讲解

1. 单进程

单进程模型来处理客户端的请求。对读写等事件的响应是通过对epoll函数的包装来做到的。Redis的实际处理速度完全依靠主进程的执行效率。

Epoll是Linux内核为处理大批量文件描述符而作了改进的epoll，是Linux下多路复用IO接口select/poll的增强版本，它能显著提高程序在大量并发连接中只有少量活跃的情况下的系统CPU利用率。

2. 默认16个数据库，类似数组下表从零开始，初始默认使用零号库。统一密码管理，16个库都是同样密码，要么都OK要么一个也连接不上。同时，Redis索引都是从零开始。

3. 其他

```ini
# 命令切换数据库
Select 库名（0-15）

# 查看当前数据库的key的数量
Dbsize;

# 清空当前库
Flushdb：
# 通杀全部库
Flushall；
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131945.png" alt="image-20200718161824894" style="zoom:80%;" />

# 3、Redis数据类型

## 3.1 Redis的五大数据类型

### 1、String

​		string（字符串）是redis最基本的类型，你可以理解成与Memcached一模一样的类型，一个key对应一个value，一个redis中字符串value最多可以是512M。

​		string类型是二进制安全的。意思是redis的string可以包含任何数据。比如jpg图片或者序列化的对象 。

### 2、Hash

​		Redis hash （哈希，类似java里的Map<String,Object>）是一个键值对集合。
​		Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象。

### 3、List

Redis 的List(列表)是简单的字符串列表，按照插入顺序排序。你可以添加一个元素导列表的头部（左边）或者尾部（右边）。它的底层实际是个链表。

### 4、Set

Redis的Set（集合）是string类型的无序集合。它是通过HashTable实现实现的。

### 5、zset

Redis zset (sorted set：有序集合)和 set 一样也是string类型元素的集合,且不允许重复的成员。不同的是每个元素都会关联一个double类型的分数。
​		Redis正是通过分数来为集合中的成员进行从小到大的排序。zset的成员是唯一的,但分数(score)却可以重复。

## 3.2 数据操作

### 1、key关键字

1. 常用命令

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131946.png" alt="image-20200718163321025" style="zoom:80%;" />

2. 案例

（1）查看当前库中存在的key

```ini
keys *
```

（2） 判断某个key是否存在

```ini
#存在返回1，反之返回0。
exists key名
```

（3） 将当前库中的key移除了db库，当前库中的key名没有了

```ini
move key名 db（库0-15）
```

（4）为给定的key设置过期时间

```ini
expire key名  时间（秒钟）
```

（5） 查看当前的key还有多少秒过期

```ini
#-1表示永不过期，-2表示已过期
ttl key名
```

（6）查看你的key是什么类型

```ini
type key名 
```

### 2、String命令

1. 常用命令

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131947.png" alt="image-20200718164916165" style="zoom:80%;" />

2. 案例

   单值单value。

（1）set/get/del/append/strlen

```ini
#追加数据
append key名 数据
```

（2）Incr/decr/incrby/decrby,

```ini
#value中一定要是数字才能进行加减
# value进行加1操作
incr key名

#value+加数进行增加
INCRBY key名  加数
```

（3）getrange/setrange

```ini
#getrange:获取指定区间范围内的值，类似between......and的关系,其中从零到负一表示全部
getrange key名 范围1 范围2

#setrange:设置指定区间范围内的值
setrange key名 范围 具体值
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131948.png" alt="image-20200718165914876" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131949.png" alt="image-20200718165921935" style="zoom:80%;" />

（4） setex/setnx

```ini
#setex(set with expire):设置带过期时间的key，动态设置
setex key名 存活时间（秒数）value值

#setnx(set if not exist):只有在 key 不存在时设置 key 的值
setnx key名 value值
```

（5） mset/mget/msetnx

```ini
#mset(more):同时设置一个或多个 key-value 对
#mget:获取所有(一个或多个)给定 key 的值
#msetnx:同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在。
mset/msetnx key1名 value1 key2名 value2 ...
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131950.png" alt="image-20200718170840403" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131951.png" alt="image-20200718170852116" style="zoom:67%;" />

（6）getset

```ini
#getset:将给定 key 的值设为 value ，并返回 key 的旧值(old value)。简单一句话，先get然后立即set
getset key名 value
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131952.png" alt="image-20200718171025003" style="zoom:67%;" />

### 3、List命令

1. 常用命令

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131953.png" alt="image-20200718171140861" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131954.png" alt="image-20200718171155420" style="zoom:67%;" />

2. 案例

   单值多value

（1）lpush/rpush/lrange

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131955.png" alt="image-20200718171838090" style="zoom:67%;" />

（2）lpop/rpop

```ini
#出栈
lpop list名
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131956.png" alt="image-20200718172255816" style="zoom:80%;" />

（3）lindex，按照索引下标获得元素(从上到下)

```ini
#通过索引获取列表中的元素 
lindex list名  下标索引
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131957.png" alt="image-20200718172627700" style="zoom:50%;" />

（4）llen

```ini
#获取list的列表长度
llen list名
```

（5） lrem key

```ini
# lrem（remove）;删除list名中的N个value
lrem list名 N（counts） value 
```

（6）ltrim

```ini
#截取指定索引区间的元素，并把剩余的值返回赋值。
ltrim list名 起始索引 结束索引
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131958.png" alt="image-20200718173139455" style="zoom:67%;" />

（7） rpoplpush 源列表 目的列表

```ini
#将list1中的栈底数据元素出栈，放到list2的栈顶。
rpoplpush list1源列表 list2目的列表
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131959.png" alt="image-20200718173713052" style="zoom:80%;" />

（8） lset key index value

```ini
#将list名中的索引值（可能+1）换成value。
lset list名 索引index value
```

（9） linsert key  before/after 值1 值2

```ini
# 在list名的value1值的前/后插入value2值，并赋值返回。
linsert list名 before/after value1 value12
```

总结：

1. list是一个字符串链表，left、right都可以插入添加。
2. 如果值全移除，对应的键也就消失了。即空值没有存在的意义。
3. 链表的操作无论是头和尾效率都极高，但假如是对中间元素进行操作，效率就很惨淡了。

### 4、Set命令

1. 常用命令

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132000.png" alt="image-20200718174416828" style="zoom:80%;" />

2. 案例

   单值多value。

（1）sadd/smembers/sismember

```ini
#添加元素到集合，且集合中的元素无序的并且唯一
SADD set集合 value1 ..
```

（2）scard

```ini
#获取集合里面的元素个数
scard set集合
```

（3）srem 

```ini
#删除集合中元素
srem set集合key value
```

（4）srandmember 

```ini
#随机抛出set集合中的N个数
srandmember set集合 N
```

（5）spop

```ini
#随机出栈
spop set集合
```

（6）smove 

```ini
#将set1中的value放到set2中（迁移）
smove set1集合 set2集合 value(set1中的某个值) 
```

（7）diff

差集：在第一个set里面而不在后面任何一个set里面的项

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132001.png" alt="image-20200718180514598" style="zoom:80%;" />

同时，我们也理解了交集（sinter）和并集（sunion）的含义。

### 5、Hash

1. 常用命令

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132002.png" alt="image-20200718180757799" style="zoom:80%;" />

2. 案例

   KV模式不变，但V是一个键值对。

（1） **hset/hget/hmset/hmget/hgetall/hdel**

```ini
#为hash设置和获取值
hset hash集合 key1 value1
hget hash集合 key1
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132003.png" alt="image-20200718181141533" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132004.png" alt="image-20200718181341382" style="zoom:80%;" />

（2）hlen

```ini
#hash集合中key的多少
hlen hash集合
```

（3）hexists

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132005.png" alt="image-20200718181532920" style="zoom:80%;" />

（4）**hkeys/hvals**

```ini
#展示集合中所有的key值
hkeys hash集合
```

（5）hincrby/hincrbyfloat

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132006.png" alt="image-20200718181837667" style="zoom:80%;" />

（6）hsetnx

对不存在的key赋值，存在了无效。

### 6、Zset

1. 常用

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132007.png" alt="image-20200718182033032" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132008.png" alt="image-20200718182046996" style="zoom:80%;" />

2. 案例

​		在set基础上，加一个score值。之前set是（set集合 v1 v2），现在zset是（set集合 score1 v1 score2 v2）（score1 v1是键值对）。 

（1） zadd/zrange

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132009.png" alt="image-20200718182912298" style="zoom: 67%;" />

（2）zrangebyscore 

```ini
#查询从score1开始score2结束的value值，“（score1”表示不包含score1
#从显示的value中从N1开始截取N2个，类似分页。
zrangebyscore zset集合 score1 score2 limit N1 N2
```

（3）zrem 

```ini
#删除元素
zrem zset集合 value
```

（4）zcard/zcount /zrank /zscore 

```ini
#获取集合中的元素个数，key-value为一组
zcard zset集合

#获取分数区间score1和score2元素个数
zcount zset集合 score1 score2

#获取value在zset中的下标位置
zrank zset集合 value

#按照分数获得对应的key值
zscore zset score
```

（5）zrevrank

```ini
#将score递减排序后获得下标值
zrevrank zset集合 values
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132010.png" alt="image-20200718184209412" style="zoom:80%;" />

（6）zrevrange

```ini
#返回该集合中指定区间的成员，分数从高到低
zrevrange zset集合 start stop(暂时不知道start啥意思)
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132011.png" alt="image-20200718184329902" style="zoom:80%;" />

（7） zrevrangebyscore 

```ini
#分数反着排序
zrevrangebyscore zset集合 score1 score2
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132012.png" alt="image-20200718184900658" style="zoom:80%;" />

# 4、解析配置文件

## 1、Units单位

1. 配置大小单位,开头定义了一些基本的度量单位，只支持bytes，不支持bit。

2. 对大小写不敏感。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132013.png" alt="image-20200718203218556" style="zoom:80%;" />

## 2、INCLUDES包含

可以通过includes包含，redis.conf可以作为总闸，包含其他。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132014.png" alt="image-20200718203334982" style="zoom:80%;" />

# 5、持久化

## 5.1、RDB

### 1、介绍

RDB（Redis DataBase）在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是行话讲的**Snapshot快照**，它恢复时是将快照文件直接读到内存里。

Redis会单独创建（fork）（拷贝）一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。
​		整个过程中，主进程是不进行任何IO操作的，这就确保了极高的性能如果需要进行大规模数据的恢复，且对于**数据恢复的完整性**不是非常敏感，那RDB方式要比AOF方式更加的高效。

RDB的缺点是最后一次持久化后的数据可能丢失。

### 2、Fork的作用

Fork的作用是复制一个与当前进程一样的进程。新进程的所有数据（变量、环境变量、程序计数器等）数值都和原进程一致，但是是一个全新的进程，并作为原进程的子进程。

Rdb 保存的是**dump.rdb**文件。

### 3、配置位置

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132015.png" alt="image-20200718211115400" style="zoom:80%;" />

```ini
1.DB是整个内存的压缩过的Snapshot，RDB的数据结构，可以配置复合的快照触发条件。

2.默认
是15分钟（900秒）内改了1次
或5分钟内（300秒）改了10次
或1分钟（60秒）内改了1万次。
如果我们触发上面的任何一个条件，redis就将内存中的数据同步到磁盘中。

3.如果想禁用RDB持久化的策略，只是用Redis的缓存功能，只要不设置任何save指令，或者给save传入一个空字符串参数也可以。（上图的save ""打开注释）

4.在控制端使用save指令可以当前时刻迅速备份。
```

1.Stop-writes-on-bgsave-error

​	表示后台保存数据出错，前台写数据是否停止。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132016.png" alt="image-20200718213218865" style="zoom:80%;" />

​	如果配置成no，表示你不在乎数据不一致或者有其他的手段发现和控制

2.rdbcompression

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132017.png" alt="image-20200718213443982" style="zoom:80%;" />

​		rdbcompression：对于存储到磁盘中的快照，可以设置是否进行压缩存储。如果是的话，redis会采用LZF算法进行压缩。如果你不想消耗CPU来进行压缩的话，可以设置为关闭此功能。

3. rdbchecksum

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132018.png" alt="image-20200718213548028" style="zoom:80%;" />

​		rdbchecksum：在存储快照后，还可以让redis使用CRC64算法来进行数据校验，但是这样做会增加大约10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能。

### 4、触发RDB快照

1. **配置文件中默认的快照配置**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132019.png" alt="image-20200718213757193" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132020.png" alt="image-20200718213806047" style="zoom:67%;" />

注意：我们需要将备份拷贝到另一台机器上，避免意外故障出现。

```ini
cp dump.rdb dump_new.rdb
```

2. **命令save/bgsave**

save：save时只管保存，其它不管，全部阻塞。

bgsave：Redis会在后台异步进行快照操作，快照同时还可以响应客户端请求。可以通过lastsave命令获取最后一次成功执行快照的时间。

注意：执行flushall命令，也会产生dump.rdb文件，但里面是空的，无意义。

3. **恢复数据**

将备份文件 (dump.rdb) 移动到 redis 安装目录并启动服务即可。使用下面指令获取目录：

```ini
CONFIG GET dir
```

4. **优点和缺点**

优点：适合大规模的数据恢复，对数据完整性和一致性要求不高。

缺点：在一定间隔时间做一次备份，所以如果redis意外down掉的话，就
会丢失最后一次快照后的所有修改；Fork的时候，内存中的数据被克隆了一份，大致2倍的膨胀性需要考虑。

5. **停止RDB保存**

动态停止所有RDB保存规则的方法：

```ini
redis-cli config set save ""
```

## 5.2 、AOF

### 1、介绍

​		AOF（Append Only File）以日志的形式来记录每个**写操作**，将Redis执行过的所有写指令记录下来(读操作不记录)，只许追加文件但不可以改写文件，redis启动之初会读取该文件重新构建数据，换言之，redis重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作。

​		Aof保存的是**appendonly.aof**文件。

### 2、配置位置

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132021.png" alt="image-20200718222136682" style="zoom:80%;" />

（1）Appendfsync

![image-20200718223041668](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132022.png)

```ini
Always:同步持久化,每次发生数据变更会被立即记录到磁盘,性能较差但数据完整性比较好。
Everysec:出厂默认推荐，异步操作，每秒记录,如果一秒内宕机，有数据丢失。
No:write后不会有fsync调用（从不同步），由操作系统自动调度刷磁盘，性能是最好的。
```

（2）No-appendfsync-on-rewrite

重写时是否可以运用Appendfsync，用默认no即可，保证数据安全性。

如果该参数设置为no，是最安全的方式，不会丢失数据，但是要忍受阻塞的问题。如果设置为yes，这就相当于将appendfsync设置为no，这说明并没有执行磁盘操作，只是写入了缓冲区，因此这样并不会造成阻塞（因为没有竞争磁盘），但是如果这个时候redis挂掉，就会丢失数据。

（3）Auto-aof-rewrite-min-size/Auto-aof-rewrite-percentage

设置重写的基准值。

auto-aof-rewrite-percentage：默认值为100。aof自动重写配置，当目前aof文件大小超过上一次重写的aof文件大小的百分之多少进行重写，即当aof文件增长到一定大小的时候，Redis能够调用bgrewriteaof对日志文件进行重写。当前AOF文件大小是上次日志重写得到AOF文件大小的二倍（设置为100）时，自动启动新的日志重写过程。

​		auto-aof-rewrite-min-size：64mb。设置允许重写的最小aof文件大小，避免了达到约定百分比但尺寸仍然很小的情况还要重写。

### 3、AOF启动/修复/恢复

正常恢复：

（1）修改默认的appendonly no，改为yes。

（2）将有数据的aof文件复制一份保存到对应目录。

（3）重启redis然后重新加载即可。

异常恢复：

（1）修改默认的appendonly no，改为yes。

（2）备份被写坏的AOF文件

（3）恢复

```ini
Redis-check-aof --fix appendonly.aof(文件)
```

![image-20200718222552096](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132023.png)

（4）恢复：重启redis然后重新加载。

### 4、Rewrite

Rewrite是AOF采用文件追加方式，文件会越来越大为避免出现此种情况，新增了重写机制,当AOF文件的大小超过所设定的阈值时，Redis就会启动AOF文件的内容压缩，只保留可以恢复数据的最小指令集.可以使用命令**bgrewriteaof**。

Rewrite的原理：

AOF文件持续增长而过大时，会fork出一条新进程来将文件重写(也是先写临时文件最后再rename)，遍历新进程的内存中数据，每条记录有一条的Set语句。重写aof文件的操作，并没有读取旧的aof文件，而是将整个内存中的数据库内容用命令的方式重写了一个新的aof文件，这点和快照有点类似。

**触发机制**：

Redis会记录上次重写时的AOF大小，默认配置是当AOF文件大小是上次rewrite后大小的一倍且文件大于64M时触发。参考`Auto-aof-rewrite-min-size/Auto-aof-rewrite-percentage`参数。

### 5、优缺点

对于相同数据集的数据而言，aof文件要远大于rdb文件，恢复速度慢于rdb；Aof运行效率要慢于rdb，每秒同步策略效率较好，不同步效率和rdb相同。

### 6、选择持久化策略

1. 只做缓存

如果你只希望你的数据在服务器运行的时候存在,你也可以不使用任何持久化方式。

2. 同时开启两种持久化方式

（1）在这种情况下，当redis重启的时候会优先载入AOF文件来恢复原始的数据，因为在通常情况下AOF文件保存的数据集要比RDB文件保存的数据集要完整。

（2）RDB的数据不实时，同时使用两者时服务器重启也只会找AOF文件。那要不要只使用AOF呢？作者建议不要，因为RDB更适合用于备份数据库(AOF在不断变化不好备份)，快速重启，而且不会有AOF可能潜在的bug，留着作为一个万一的手段。

3. 性能建议

（1）因为RDB文件只用作后备用途，建议只在Slave上持久化RDB文件，而且只要15分钟备份一次就够了，只保留save 900 1这条规则。

（2）如果Enalbe AOF，好处是在最恶劣情况下也只会丢失不超过两秒数据，启动脚本较简单只load自己的AOF文件就可以了。代价一是带来了持续的IO，二是AOF rewrite的最后将rewrite过程中产生的新数据写到新文件造成的阻塞几乎是不可避免的。只要硬盘许可，应该尽量减少AOF rewrite的频率，AOF重写的基础大小默认值64M太小了，可以设到5G以上。默认超过原大小100%大小时重写可以改到适当的数值。

​		如果不Enable AOF ，仅靠Master-Slave Replication 实现高可用性也可以。能省掉一大笔IO也减少了rewrite时带来的系统波动。代价是如果Master/Slave同时倒掉，会丢失十几分钟的数据，启动脚本也要比较两个Master/Slave中的RDB文件，载入较新的那个。新浪微博就选用了这种架构。

# 6、事务

## 6.1 介绍

可以一次执行多个命令，本质是一组命令的集合。一个事务中的
所有命令都会序列化，**按顺序地串行化执行而不会被其它命令插入，不许加塞。**

因此，我们可以在一个队列中，一次性、顺序性、排他性的执行一系列命令。

**常用指令**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132024.png" alt="image-20200719080554644" style="zoom:80%;" />

## 6.2 操作

1. 正常执行

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132025.png" alt="image-20200719081016450" style="zoom:80%;" />

2. 放弃执行

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132026.png" alt="image-20200719081230462" style="zoom:80%;" />

3. 全体连坐

在mutil后面的语句中, 语句出错可能有2种情况:

第一种：语法有问题,此时exec报错, 所有语句得不到执行:

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132027.png" alt="image-20200719081457409" style="zoom:80%;" />

4. 冤头债主

第二种：语法本身没错,但适用对象有问题，Exec之后会执行正确的语句,并跳过有问题的语句。

在操作的事务中没有异常错误，在运行的过程中报错对应的情况。即**Redis部分支持事务**。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132028.png" alt="image-20200719081612433" style="zoom:80%;" />

mysql的rollback与redis的discard的区别：

mysql回滚为sql全部成功才执行,一条sql失败则全部失败,执行rollback后所有语句造成的影响消失

redis的discard只是结束本次事务,正确命令造成的影响仍然还在。

### 6.2.1 watch监控

1、悲观锁/乐观锁/CAS(Check And Set)

悲观锁(Pessimistic Lock)，顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会block直到它拿到锁。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁。

乐观锁(Optimistic Lock),，顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在**更新**的时候会判断一下在此期间别人有没有去更新这个数据，可以使用**版本号**等机制。乐观锁适用于多读的应用类型，这样可以提高吞吐量。

乐观锁策略：提交版本必须大于记录当前版本才能执行更新。

Redis的事务中启用的是乐观锁,只负责监测key没有被改动.如果没变正常执行,如果有变事务取消。

WATCH是一个乐观锁,它可以在exec命令执行之前,监视任意数量的数据库键,并在exec命令执行时,检查被监视的键是否至少有一个已经被修改过了,如果是服务器则拒绝执行事务,并向客户端返回代表事务执行失败的空回复。

2、案例

（1）初始化信用卡可用余额和欠额

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132029.png" alt="image-20200719083912768" style="zoom: 67%;" />

（2）无加塞篡改，先监控再开启**multi**，保证两笔金额变动在同一个事务内。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132030.png" alt="image-20200719084028728" style="zoom:67%;" />

（3）有加塞篡改，监控了key，如果key被修改了，后面的事务的执行会失效。我们需要在缓存中重新获取数据进行加载。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132031.png" alt="image-20200719084243923" style="zoom:80%;" />

（4）unwatch 命令用于取消 WATCH 命令对所有 key 的监视。

一旦执行了exec指令，其之前所加的监控锁都会被取消掉了。

3、总结

（1）Watch指令，类似乐观锁，事务提交时，如果Key的值已被别的客户端改变，比如某个list已被别的客户端push/pop过了，整个事务队列都不会被执行。

（2）通过WATCH命令在事务执行之前监控了多个Keys，倘若在WATCH之后有任何Key的值发生了变化，EXEC命令执行的事务都将被放弃，同时返回Nullmulti-bulk应答以通知调用者事务执行失败。

### 6.3.2 操作三阶段

1、开启：以MULTI开始一个事务。

2、入队：将多个命令入队到事务中，接到这些命令并不会立即执行，而是放到等待执行的事务队列里面。

3、执行：由EXEC命令触发事务。

### 6.3.3 特性

1、单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。

2、没有隔离级别的概念：队列中的命令没有提交之前都不会实际的被执行，因为事务提交前任何指令都不会被实际执行，也就不存在”事务内的查询要看到事务里的更新，在事务外查询不能看到”这个让人万分头痛的问题。

3、不保证原子性：redis同一个事务中如果有一条命令执行失败，其后的命令仍然会被执行，没有回滚。

# 7、发布订阅

1、介绍

进程间的一种消息通信模式：发送者(pub)发送消息，订阅者(sub)接收消息。

其中，订阅/发布消息图如下：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132032.png" alt="image-20200719085210344" style="zoom:67%;" />

2、常用命令

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132033.png" alt="image-20200719085423524"  />

3. 案例

​		先订阅后发布后才能收到消息。

```ini
#可以一次性订阅多个频道：c1,c2,c3
SUBSCRIBE c1 c2 c3
#消息发布:向c2发布消息，c2可以收到
PUBLISH c2 消息（hello）

#3 订阅多个new通配的频道(通配符*) 
PSUBSCRIBE new*
#4 收取消息:上面的订阅可以收到 
PUBLISH new1(2..) 消息（hello）
```

# 8、主从复制

​		主从复制（Master/Slave）：主机数据更新后根据配置和策略，自动同步到备机的master/slaver机制，Master以写为主，Slave以读为主。

​		作用：读写分离和容灾恢复。

## 8.1 操作

1. 配从(库)不配主(库)。

2. 从库配置

```ini
slaveof 主库IP 主库端口
```

每次从机与master断开之后，都需要重新连接，除非你配置进redis.conf文件。

3. 修改配置文件细节操作

（1）拷贝多个redis.conf文件

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132034.png" alt="image-20200719100607163" style="zoom:80%;" />

（2）修改配置文件中参数

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132035.png" alt="image-20200719100651533" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132036.png" alt="image-20200719100711554" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132037.png" alt="image-20200719100728924" style="zoom:80%;" />

### 1、一主二仆

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132038.png" alt="image-20200719104639519" style="zoom:67%;" />

1. 配置

（1）启动三台Redis

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132039.png" alt="image-20200719100951695" style="zoom:80%;" />

后台线程：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132040.png" alt="image-20200719101014066" style="zoom:80%;" />

（2）使用slaveof命令将6380，6381端口的Redis变成仆人。

可以使用下面的命令查看从机配置状态：

```ini
info replication
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132041.png" alt="image-20200719101145215" style="zoom:80%;" />

2. 主从遇到的问题

（1）切入点问题

​		slave1、slave2是从头开始复制，而不是从切入点开始复制。比如从k4进来，那之前的123也可以复制。

（2）从机不可以写，set失效。

（3）主机shutdown后情况

​		从机是原地待命模式，不会上位，Master挂掉后，Master关系依然存在，等待主机回来后，主机新增记录，从机还能顺利复制（即数据恢复）。

（4）从机shutdown后情况

​		它不能跟上大部队，角色此时为master，需要使用`slaveof`重新配置。

### 2、薪火相传

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132042.png" alt="image-20200719104739199" style="zoom:67%;" />

​		上一个Slave可以是下一个slave的Master，Slave同样可以接收其它slaves的连接和同步请求，那么该slave作为了链条中下一个的master,
可以有效减轻master的写压力。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132043.png" alt="image-20200719105247593" style="zoom:80%;" />

命令：

```ini
# slave2以slave1作为master
#如果slave中途变更转向:会清除之前的数据，重新建立拷贝最新的数据。即slave2会重新拷贝新数据。
Slaveof 127.0.0.1(新主库IP) 6380(新主库端口)
```

　**优点：**从机可以接收其他从机的连接和同步请求，那么该从机作为链条中下一个的主机, 可以有效减轻主机的写压力,去中心化降低风险。

​	**缺点：**一旦某个从机宕机，后面的从机都无法备份。

### 3、反客为主

​		当Master挂掉后，Slave可键入命令 `slaveof no one` 使当前redis停止与其他Master redis数据同步，转成Master。

命令：

```ini
SLAVEOF no one
```

（1）情况1：在薪火相传的情况下，slave6381成为slave6380的slave。

​		使用上面的命令后，二者可以同步数据。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132044.png" alt="image-20200719110150411" style="zoom:80%;" />

（2）情况2：在一仆二主的情况下，slave6380和slave6381都是slave。

​		使用上面的命令后，slave6381不能同步数据。需要使用下面的指令设定

slave6381为它的master。

```ini
Slaveof 127.0.0.1 6380
```

注意：

​		当之前的master重新复活后（角色仍为master），不能在原来的体系中继续成为master，它现在在另一个体系中，和slave6380、slave6381组成的体系毫无关系。

## 8.2 复制原理

Slave启动成功连接到master后会发送一个sync命令，Master接到命令启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令，在后台进程执行完毕之后，master将传送整个数据文件到slave,以完成一次完全同步。

有两种复制方式：

全量复制：而slave服务在接收到数据库文件数据后，将其存盘并加载到内存中。

增量复制：Master继续将新的所有收集到的修改命令依次传给slave,完成同步。

但是只要是重新连接master,一次完全同步（全量复制)将被自动执行。

复制延时：

由于所有的写操作都是先在Master上操作，然后同步更新到Slave上，所以从Master同步到Slave机器有一定的延迟，当系统很繁忙的时候，延迟问题会更加严重，Slave机器数量的增加也会使这个问题更加严重。

## 8.3 哨兵模式

​		哨兵模式(sentinel)反客为主的自动版，能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库。当原来的master重新启动后，会成为slave6380、slave6381组成的体系中的一个slave。

**Redis Sentinel 的功能**

+ 监控：Sentinel 节点会定期检测 Redis 数据节点、其余 Sentinel 节点是否可达。
+ 通知：Sentinel 节点会将故障转移的结果通知给应用方。
+ 主节点故障转移：实现从节点晋升为主节点并维护后续正确的主从关系。
+ 配置提供者：在 Redis Sentinel 结构中，客户端在初始化的时候连接的是 Sentinel 节点集合，从中获取主节点信息。

同时Redis Sentinel 包含了若个 Sentinel 节点，这样做也带来了两个好处：

+ 对于节点的故障判断是由多个 Sentinel 节点共同完成，这样可以有效地防止误判。

+ Sentinel 节点集合是由若干个 Sentinel 节点组成的，这样即使个别 Sentinel 节点不可用，整个 Sentinel 节点集合依然是健壮的。

​		**Sentinel 节点本身就是独立的 Redis 节点，只不过它们有一些特殊，它们不存储数据，只支持部分命令**

### 8.3.1 操作

1. 自定义的/myredis目录下新建**sentinel.conf**文件，名字绝不能错。
2. 配置哨兵,填写内容。

```ini
sentinel monitor <master-name> <ip> <port> <count>

#举例
#上面最后一个数字1，表示主机挂掉后salve投票看让谁接替成为主机，得票数多少后成为主机
sentinel monitor 被监控数据库名字(自己起名字) 127.0.0.1 6379 1
```

配置说明：		

监控的主节点的名字、IP 和端口，最后一个count的意思是有几台 Sentinel 发现有问题，就会发生故障转移，例如 配置为2，代表至少有2个 Sentinel 节点认为主节点不可达，那么这个不可达的判定才是客观的。

count设置的越小，那么达到下线的条件越宽松，反之越严格。**一般建议将其设置为 Sentinel 节点的一半加1**。
<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132045.png" alt="image-20200719111612095" style="zoom: 50%;" />

**/myRedis/sentinel.conf**配置文件图示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132046.png" alt="image-20200719113042854" style="zoom:80%;" />

3. 启动哨兵

```ini
#/usr/local/bin路径下
Redis-sentinel /myredis/sentinel.conf 
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132047.png" alt="image-20200719113417136" style="zoom:80%;" />

4. 投票新选

​		在原来的master6379挂掉之后，slave6380和slave6381需要重新进行投票选举。在启动哨兵的窗口我们可以看到下面的界面：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132048.png" alt="image-20200719113545299" style="zoom:80%;" />

​	如果之前的master重启回来，不会和master有冲突，会自动成为现在体系中的一个slave。

# 9、Java客户端Jedis

1. 测试连通性

```java
public class TestPing {
    public static void main(String[] args) {
        Jedis jedis = new Jedis("127.0.0.1", 6379);
        System.out.println("-------连接结果-----"+jedis.ping());
    }
}
```

返回结果：pong，即连接正常。

2. 主从复制

```java
public class TestMS {
    public static void main(String[] args) {
        //主机
        Jedis jedis_M = new Jedis("127.0.0.1",6379);
        //从机
        Jedis jedis_S = new Jedis("127.0.0.1",6380);

        //配置6380端口为从机
        jedis_S.slaveof("127.0.0.1",6379);

        jedis_M.set("test1","1122");

        String result = jedis_S.get("test1");
        //可能读得的数据为null，主要是程序操作太快，Redis还没操作完。
        //此时，我们应该让其多操作几次。
        System.out.println(result);
    }
}
```

​		依次启动6379,6380，在其独立运行一段时间后，然后再启动上面的程序。

## 9.1 JedisPool

获取`Jedis`实例需要从`JedisPool`中获取，用完`Jedis`实例需要返还给`JedisPool`。如果`Jedis`在使用过程中出错，则也需要还给`JedisPool`。

1、工具类

```java
public class JedisPoolUtil {
    private static volatile JedisPool jedisPool = null;

    private JedisPoolUtil(){}

    public static JedisPool getJedisPoolInstance() {
        if(null == jedisPool) {
            synchronized (JedisPoolUtil.class) {
                if(null == jedisPool) {
                    //JedisPool的配置
                    JedisPoolConfig poolConfig = new JedisPoolConfig();
                    //控制一个pool可分配多少个jedis实例
                    poolConfig.setMaxActive(1000);
                    //最多有多少个jedis空闲
                    poolConfig.setMaxIdle(32);
                    poolConfig.setMaxWait(100*1000);
                    //获得一个jedis实例的时候检查连接可用性
                    poolConfig.setTestOnBorrow(true);

                    jedisPool = new JedisPool(poolConfig,"127.0.0.1",6379);
                }
            }
        }
        return jedisPool;
    }

    //把Jedis的连接资源放到池子中。
    public static void release(JedisPool jedisPool, Jedis jedis) {
        if(null != jedis) {
            jedisPool.returnResourceObject(jedis);
        }
    }
}
```

将连接池配置成单例模式，全局唯一。

2、测试

```java
public class TestPool {
    public static void main(String[] args) {
        JedisPool jedisPool = JedisPoolUtil.getJedisPoolInstance();

        Jedis jedis = null;
        try {
            jedis = jedisPool.getResource();
            jedis.set("aa","bb");
        } catch (Exception e) {
            e.printStackTrace();
        }finally{
            JedisPoolUtil.release(jedisPool, jedis);
        }
    }
}
```

运行结束后，我们可以再管理员通过下面指令获得设置的值“bb”，表明测试成功。

```ini
get aa
```
