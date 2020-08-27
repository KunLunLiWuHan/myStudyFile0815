#   1 介绍

​		Zookeeper是一个开源的分布式的，为分布式应用提供协调服务的Apache项目。

​		Zookeeper从设计模式角度来理解：是一个基于观察者模式设计的分布式服务管理框架，它负责存储和管理数据，然后接受观察者的注册，一旦这些数据的状态发生变化，Zookeeper就对它上面已经注册的观察者做出相应的反应（通知）。

结构图：观察者可以理解为客户端。

<img src="Zookeeper学习1-单机.assets\image-20200711105939626.png" alt="image-20200711105939626" style="zoom:80%;" />

## 1.1 特点

<img src="Zookeeper学习1-单机.assets\image-20200711110715061.png" alt="image-20200711110715061" style="zoom:80%;" />

+ Zookeeper：只有一个领导者（Leader）,多个跟随者（Follower）组成的集群。
+ 集群中只要有半数以上的节点能够存活，Zookeeper集群就能够正常工作。
+ 全局数据一致：每个Server保存一份相同的数据副本，Client无论连接到那个Server，数据都是一致的。
+ 更新请求执行顺序，来自同一个Client的更新请求按照发送顺序依次执行。
+ 数据更新原子性：一次数据更新要么成功，要么失败。
+ 实时性：在一定的时间内，Client能够读到最新的数据。

## 1.2 数据结构

<img src="Zookeeper学习1-单机.assets\image-20200711111332088.png" alt="image-20200711111332088" style="zoom:80%;" />

​		Zookeeper数据模型的结构与Unix文件系统很类似，整体上可以看成是一棵树，每个节点称作一个ZNode（（即zookeeper node），一个znode可以有多个子节点。每个ZNode默认能够存储1MB的数据，每个ZNode都可以通过其路径唯一标识，比如/ns1/itcast/mysql/schema1/table1，此处ns-1、itcast、mysql、schema1、table1分别是 根节点、2级节点、3级节点以及4级节点；其中ns-1是itcast的父节点，itcast是ns-1的子 节点，itcast是mysql的父节点，mysql是itcast的子节点，以此类推。

​		znode，兼具文件和目录两种特点。既像文件一样维护着数据、元信息、ACL、时间戳等数据结构，又像目录一样可以作为路径标识的一部分。

​		从三个维度来描述zNode：

+ 节点的数据：即znode data(节点path, 节点data)的关系就像是java map中(key, value)的关系。
+ 节点的子节点children。
+ 节点的状态stat：用来描述当前节点的创建、修改记录，包括cZxid、ctime等。

 **节点状态stat的属性**

​		在zookeeper安装成功之后，我们可以在在zookeeper shell中使用get命令查看指定路径节点的data、stat信息：

```ini
[zk: localhost:2181(CONNECTED) 7] get /ns-1/tenant

cZxid = 0x6a0000000a
ctime = Wed Mar 27 09:56:44 CST 2019
mZxid = 0x6a0000000a
mtime = Wed Mar 27 09:56:44 CST 2019
pZxid = 0x6a0000000e
cversion = 2
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 0
numChildren = 2
```

属性说明：

+  cZxid：数据节点创建时的事务 ID 
+ ctime：数据节点创建时的时间 
+ mZxid：数据节点最后一次更新时的事务 ID 
+ mtime：数据节点最后一次更新时的时间 
+ pZxid：数据节点的子节点最后一次被修改时的事务 ID 
+ cversion：子节点的更改次数 
+ **dataVersion**：节点数据的更改次数 
+ aclVersion：节点的 ACL 的更改次数 
+ ephemeralOwner：如果节点是临时节点，则表示创建该节点的会话的 SessionID；如果节点是持久节点，则该属性值为 0 
+ dataLength：数据内容的长度 
+ numChildren：数据节点当前的子节点个数

​		对于节点各个属性，其中一个重要的概念是 Zxid(ZooKeeper Transaction Id)，ZooKeeper 节点的每一次更改都具有唯一的 Zxid，如果 Zxid1 小于 Zxid2，则 Zxid1 的更改发生在 Zxid2 更改之前。

节点类型:

​		zookeeper中的节点有两种，分别为临时节点和永久节点。节点的类型在创建时即 被确定，并且不能改变。 

+ 临时节点：该节点的生命周期依赖于创建它们的会话。一旦会话(Session)结束，临 时节点将被自动删除，当然可以也可以手动删除。虽然每个临时的Znode都会绑定到 一个客户端会话，但他们对所有的客户端还是可见的。另外，ZooKeeper的临时节点不允许拥有子节点。 
+ 持久化节点：该节点的生命周期不依赖于会话，并且只有在客户端显示执行删除操作 的时候，他们才能被删除

## 1.3 应用场景

​		提供的服务包括：统一命名服务、统一配置管理、统一集群管理、服务器节点动态上下线、软负载均衡等。

### 1、统一命名服务

​		在分布式环境下，经常需要对应用/服务进行统一命名，便于识别。例如：IP不容易记住，而域名容易记住。

<img src="Zookeeper学习1-单机.assets\image-20200711111939429.png" alt="image-20200711111939429" style="zoom:80%;" />

### 2、统一配置管理

1. 在分布式环境中，配置文件同步十分常见。

+ 一般要求一个集群中，所有节点的配置信息一致，比如Kafka集群。
+ 对配置文件修改后，希望能够快速同步到每个节点上。

2. 配置管理可交给Zookeeper实现。

​		可将配置信息写入Zookeeper上的一个ZNode，各个客服端服务器监听这个ZNode，一旦ZNode中的数据被修改，Zookeeper将通知到各个客户端配置。

<img src="Zookeeper学习1-单机.assets\image-20200711112530522.png" alt="image-20200711112530522" style="zoom:80%;" />

### 3、统一集群管理

1. 分布式环境下，实时掌握各个节点的状态是必要的。可根据节点实时状态做出一些调整。
2. Zookeeper可实现实时监控节点状态变化。

​		可将节点信息写入Zookeeper上的一个ZNode，监听这个ZNode可以实时获取它的实时状态变化。

<img src="Zookeeper学习1-单机.assets\image-20200711112934810.png" alt="image-20200711112934810" style="zoom:80%;" />

### 4、软负载均衡

​		在Zookeeper中记录每台服务器的访问数，让访问数最少的服务器去处理最新的客户端请求。

<img src="Zookeeper学习1-单机.assets\image-20200711113215723.png" alt="image-20200711113215723" style="zoom:80%;" />

# 2 安装和下载

## 2.1 JDK安装

下载地址：https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html

<img src="Zookeeper学习1-单机.assets\image-20200711115625280.png" alt="image-20200711115625280" style="zoom:80%;" />

## 2.2 下载地址

官网：https://zookeeper.apache.org/。

下载网址：https://zookeeper.apache.org/releases.html。

<img src="Zookeeper学习1-单机.assets\image-20200711113803762.png" alt="image-20200711113803762" style="zoom:80%;" />

### 2.2.1 单机安装

当前测试系统环境centos6.8。

zookeeper:zookeeper-3.4.10.tar.gz

1. 在centos中使用root用户创建zookeeper用户，用户名：zookeeper 密 码:zookeeper

```ini
useradd zookeeper
passwd zookeeper
```

2. zookeeper底层依赖于jdk，退出当前的root用户，使用zookeeper**用户登录**，然后再根目录下先进行jdk的安装。然后检测jdk安装。

```ini
// 敲如下命令,系统如图反馈说明安装成功
java -version
```

<img src="Zookeeper学习1-单机.assets\image-20200712081945854.png" alt="image-20200712081945854" style="zoom:80%;" />

3. zookeeper使用zookeeper-3.4.10.tar.gz，上传并解压

```ini
// 解压zookeeper
tar -xzvf zookeeper-3.4.10.tar.gz
```

4. 为zookeeper准备配置文件

```ini
// 进入conf目录
cd /tmp/zookeeper/zookeeper-3.4.10/conf

// 复制配置文件
cp zoo_sample.cfg zoo.cfg

// zookeeper根目录下新建data目录
mkdir data

// 进入conf目录下，编辑zoo.cfg
vim zoo.cfg

//此路径用于存储zookeeper中数据的内存快照、及事物日志文件
dataDir=/tmp/zookeeper/zookeeper-3.4.10/data
```

5. 启动zookeeper

```ini
// 进入zookeeper的bin目录
cd /tmp/zookeeper/zookeeper-3.4.10/bin
// 启动zookeeper
./zkServer.sh start

//其他指令
//启动：./zkServer.sh start
//停止：./zkServer.sh stop
//查看状态：./zkServer.sh status
```

注：配置环境变量启动起来更快捷

```ini
export ZOOKEEPER_HOME=/home/zookeeper01/zookeeper-3.4.10
export PATH=$PATH:$ZOOKEEPER_HOME/bin

source /etc/profile  --是环境变量生效

#执行下面的的指令，当返回这样的话的时候，说明环境变量已经配好了
[root@hadoop02 ~]# echo $ZOOKEEPER_HOME
/home/zookeeper01/zookeeper-3.4.10  
```

### 2.2.2 zookeeper常用shell命令

#### 1、新增节点

```ini
create [-s] [-e] path data #其中-s 为有序节点，-e 临时节点
```

开启zookeeper，使用下面命令登录（链接）本机zookeeper服务器。

```ini
./zkCli.sh 
```

<img src="Zookeeper学习1-单机.assets\image-20200712093026749.png" alt="image-20200712093026749" style="zoom:80%;" />

1. 创建持久化节点并写入数据：

```ini
create /hadoop "123456" --创建路径为hadoop的节点
```

<img src="Zookeeper学习1-单机.assets\image-20200712093341885.png" alt="image-20200712093341885" style="zoom:80%;" />

2. 创建持久化有序节点，此时创建的节点名为指定节点名 + 自增序号

```ini
create -s /b "bbb"
```

<img src="Zookeeper学习1-单机.assets\image-20200712093640116.png" alt="image-20200712093640116" style="zoom:80%;" />

3. 创建临时节点，临时节点会在会话过期后被删除：

```ini
create -e /tmp "tmp"
```

<img src="Zookeeper学习1-单机.assets\image-20200712093850414.png" alt="image-20200712093850414" style="zoom:80%;" />

​		使用命令`quit`退出之后，重新登录后，临时节点不存在，取不到数据。

4. 创建临时有序节点，临时节点会在会话过期后被删除：

<img src="Zookeeper学习1-单机.assets\image-20200712094042819.png" alt="image-20200712094042819" style="zoom:80%;" />

#### 2、更改节点

更新节点的命令是 `set` ，可以直接进行修改，如下：

```ini
set /hadoop "345" --更改节点的数据

# 也可以基于版本号进行更改，此时类似于乐观锁机制，当你传入的数据版本号(dataVersion) 和当前节点的数据版本号不符合时，zookeeper 会拒绝本次修改。
set /hadoop "3456" 1 
```

<img src="Zookeeper学习1-单机.assets\image-20200712094612993.png" alt="image-20200712094612993" style="zoom:80%;" />

#### 3、删除节点

​		基本语法如下：

```ini
delete path [version]
```

​		和更新节点数据一样，也可以传入版本号，当你传入的数据版本号 (dataVersion) 和当前节点的数据版本号不符合时，zookeeper 不会执行删除操作。

```ini
delete /hadoop 1
```

<img src="Zookeeper学习1-单机.assets\image-20200712094839667.png" alt="image-20200712094839667" style="zoom:80%;" />

​		要想删除某个节点及其所有后代节点，可以使用递归删除，命令为 

```ini
rmr path  --r:recursion(递归)
```

<img src="Zookeeper学习1-单机.assets\image-20200712095553783.png" alt="image-20200712095553783" style="zoom:80%;" />

<img src="Zookeeper学习1-单机.assets\image-20200712095638890.png" alt="image-20200712095638890" style="zoom:80%;" />

#### 4、查看节点

```ini
get path	
```

#### 5、查看节点状态

​		可以使用 stat 命令查看节点状态，它的返回值和 get 命令类似，但不会返回节点数据。

<img src="Zookeeper学习1-单机.assets\image-20200712100825223.png" alt="image-20200712100825223" style="zoom:80%;" />

#### 6、查看节点列表

​		查看节点列表有 ls path 和 ls2 path 两个命令，后者是前者的增强，不仅可以查看指定路径下的所有节点，还可以查看当前节点的信息。

<img src="Zookeeper学习1-单机.assets\image-20200712101042158.png" alt="image-20200712101042158" style="zoom:80%;" />

<img src="Zookeeper学习1-单机.assets\image-20200712101107993.png" alt="image-20200712101107993" style="zoom:80%;" />

#### 7、监听器

```ini
get path [watch]

stat path [watch]

ls\ls2 path [watch]
```

​		使用 get path [watch] 注册的监听器能够在**节点内容**发生改变的时候，向客户端发出通知。需要注意的是 zookeeper 的触发器是一次性的 (One-time trigger)，即触发一次后就会立即失效。

<img src="Zookeeper学习1-单机.assets\image-20200712102029163.png" alt="image-20200712102029163" style="zoom:80%;" />

​		使用 stat path [watch] 注册的监听器能够在**节点状态**发生改变的时候，向客 户端发出通知。

​		使用 ls path [watch] 或 ls2 path [watch] 注册的监听器能够监听**该节点下所有子节点**（不是当前节点）的增加和删除操作。

### 2.2.3zookeeper的acl权限控制

#### 1、介绍

​		zookeeper 类似文件系统，client 可以创建节点、更新节点、删除节点，zookeeper的`access control list` 访问控制列表实现节点的控制。

​		acl 权限控制，使用scheme：id：permission 来标识，主要涵盖 3 个方面：

+ 权限模式（scheme）：授权的策略。

+ 授权对象（id）：授权的对象。

+ 权限（permission）：授予的权限。

其特性如下：

+ zooKeeper的权限控制是基于每个znode节点的，需要对每个节点设置权限。
+ 每个znode支持设置多种权限控制方案和多个权限。
+ 子节点不会继承父节点的权限，客户端无权访问某节点，但可能可以访问它的子节点。

##### 1.1、权限模式

采用何种方式授权。

|  方案  |                          描述                           |
| :----: | :-----------------------------------------------------: |
| world  | 只有一个用户：anyone，代表登录zookeeper所有人。（默认） |
|   ip   |                 对客户端使用ip地址认证                  |
|  auth  |         使用已添加认证的用户认证（指定的用户）          |
| digest |               使用“用户名：密码”方式认证                |

##### 1.2、授予权限

​		create、delete、read、writer、admin也就是 增、删、改、查、管理权限， 这5种权限简写为cdrwa，注意:这5种权限中，delete是指对**子节点**的删除权限，其它4种权限指对自身节点的操作权限。

|  权限  | ACL简写 |             描述             |
| :----: | :-----: | :--------------------------: |
| create |    c    |          创建子节点          |
| delete |    d    |          删除子节点          |
|  read  |    r    | 读取节点数据及显示子节点列表 |
| write  |    w    |         设置节点数据         |
| adimin |    a    |   设置节点访问控制列表权限   |

##### 1.3 、授权相关指令

|  命令   | 使用方式 |     描述     |
| :-----: | :------: | :----------: |
| getAcl  |  getAcl  | 读取Acl权限  |
| setAcl  |  setAcl  | 设置Acl权限  |
| addauth | addauth  | 添加认证权限 |

##### 1.4、案例

```ini
// 将节点权限设置为Ip:192.168.60.130的客户端可以对节点进行增、删、改、查、管理权限
setAcl /test2 ip:192.168.60.130:crwda 
```

1. world授权模式

   命令：

```ini
//对于当前登录到zookeeper上该节点的的所有用户，给与的权限列表
setAcl <path> world:anyone:<acl>
```

<img src="Zookeeper学习1-单机.assets\image-20200712144051751.png" alt="image-20200712144051751" style="zoom:80%;" />

2. ip授权模式

命令：

```ini
setAcl <path> ip:<ip>:<acl>
```

注意：远程登录zookeeper命令:

```ini
./zkCli.sh -server ip
```

启动zookeeper遇到的问题：

​		主要原因是权限不够，我们使用下面的命令，为所有人增加读写执行权限。

<img src="Zookeeper学习1-单机.assets\image-20200712163500261.png" alt="image-20200712163500261" style="zoom:80%;" />

连接同一个zookeeper:

<img src="Zookeeper学习1-单机.assets\image-20200712213352397.png" alt="image-20200712213352397" style="zoom:80%;" />

为两台服务器进行授权：

<img src="Zookeeper学习1-单机.assets\image-20200712213737747.png" alt="image-20200712213737747" style="zoom:80%;" />

通过上面我们可以发现：同一个节点可以同时使用多种模式授权，授权格式如上。

3. Auth授权模式

```ini
#添加认证用户，下次重新登录时，需要加上认证用户，进行操作。
addauth digest <user>:<password> 

#添加此权限的用户可以针对节点进行操作
setAcl <path> auth:<user>:<acl>
```

<img src="Zookeeper学习1-单机.assets\image-20200712215455661.png" alt="image-20200712215455661" style="zoom:80%;" />

4. Digest授权模式

​		和auth授权模式有两点不同，1是不需要事先进行授权用户的添加，2是在授权过程中需要提供加密后的密码。

```ini
setAcl <path> digest:<user>:<password>:<acl>
```

​		这里的密码是经过SHA1及BASE64处理的密文，在SHELL中可以通过以下命令计算：

```ini
echo -n <user>:<password> | openssl dgst -binary -sha1 | openssl base64
```

<img src="Zookeeper学习1-单机.assets\image-20200712221332856.png" alt="image-20200712221332856" style="zoom:80%;" />

<img src="Zookeeper学习1-单机.assets\image-20200712221538899.png" alt="image-20200712221538899" style="zoom:80%;" />

##### 1.5 acl 超级管理员

​		zookeeper的权限管理模式有一种叫做super，该模式提供一个超管可以方便的访问任何权限的节点。

假设这个超管是：super:admin，需要先为超管生成密码的密文

```ini
echo -n super:admin | openssl dgst -binary -sha1 | openssl base64
```

那么打开zookeeper目录下的`/bin/zkServer.sh`服务器脚本文件，找到如下一行：

```ini
nohup $JAVA "-Dzookeeper.log.dir=${ZOO_LOG_DIR}" "-
Dzookeeper.root.logger=${ZOO_LOG4J_PROP}"
```

这就是脚本中启动zookeeper的命令，默认只有以上两个配置项，我们需要加一个 超管的配置项：

```ini
"-
Dzookeeper.DigestAuthenticationProvider.superDigest=super:xQJmxLMiHGwaqBvst5y6rkB6HQs="
```

完整命令如下：

```ini
   nohup "$JAVA" "-Dzookeeper.log.dir=${ZOO_LOG_DIR}" "-Dzookeeper.root.logger=${ZOO_LOG4J_PROP}""-
Dzookeeper.DigestAuthenticationProvider.superDigest=super:xQJmxLMiHGwaqBvst5y6rkB6HQs=" \
    -cp "$CLASSPATH" $JVMFLAGS $ZOOMAIN "$ZOOCFG" > "$_ZOO_DAEMON_OUT" 2>&1 < /dev/null &
```

<img src="Zookeeper学习1-单机.assets\image-20200713000810794.png" alt="image-20200713000810794" style="zoom:80%;" />

​		复制完成之后，不知道为啥启动后会爆出这样的错误，我们需要重新复制即可设置成功，复制时不要调格式：

<img src="Zookeeper学习1-单机.assets\image-20200712232651768.png" alt="image-20200712232651768" style="zoom:67%;" />

​		**注**：设置完成之后，需要重新启动，超级管理员功能才能够生效。

​	非root用户启动不了zookeeper的解决方法：

1. 添加完成之后，我们使用zookeeper用户去启动zookeeper应用，遇到下面的问题（此时防火墙已经关闭）：

<img src="Zookeeper学习1-单机.assets\image-20200712225141371.png" alt="image-20200712225141371" style="zoom:80%;" />

有可能2181端口被占用，我们需要将进程全部杀死：

<img src="Zookeeper学习1-单机.assets\image-20200712225247872.png" alt="image-20200712225247872" style="zoom:80%;" />

2. 如果还是启动不了，进行下面的方法：到zookeeper-3.4.10的data目录下，version-2文件夹和zookeeper_server.pid两个文件删除。

   <img src="Zookeeper学习1-单机.assets\image-20200712234520231.png" alt="image-20200712234520231" style="zoom:80%;" />

然后就可以正常启动了。

测试案例：

<img src="Zookeeper学习1-单机.assets\image-20200713000655268.png" alt="image-20200713000655268" style="zoom:80%;" />

<img src="Zookeeper学习1-单机.assets\image-20200713000605123.png" alt="image-20200713000605123" style="zoom:80%;" />

# 3 JavaAPI

​		znode是zooKeeper集合的核心组件，zookeeper API提供了一小组方法使用 zookeeper集合来操纵znode的所有细节。

​		客户端应该遵循以步骤，与zookeeper服务器进行清晰和干净的交互。

+ 连接到zookeeper服务器。zookeeper服务器为客户端分配会话ID。 
+ 定期向服务器发送心跳。否则，zookeeper服务器将过期会话ID，客户端需要重新连接。 
+ 只要会话ID处于活动状态，就可以获取/设置znode。
+  所有任务完成后，断开与zookeeper服务器的连接。如果客户端长时间不活动，则 zookeeper服务器将自动断开客户端。

## 3.1 连接到ZooKeeper

```java
ZooKeeper(String connectionString, int sessionTimeout, Watcher watcher)
```

+ connectionString - zookeeper主机 
+ sessionTimeout - 会话超时（以毫秒为单位)
+  watcher - 实现“监视器”对象。
+ zookeeper集合通过监视器对象返回连接状态。

项目结构：

<img src="Zookeeper学习1-单机.assets\image-20200713090115739.png" alt="image-20200713090115739" style="zoom:50%;" />

1. 事先需要将jar包导进去：

<img src="Zookeeper学习1-单机.assets\image-20200713090206329.png" alt="image-20200713090206329" style="zoom:50%;" />

```java
public class ZkConnection {
    public static void main(String[] args) throws Exception {

        //计数器对象（线程同步类）
        CountDownLatch countDownLatch = new CountDownLatch(1);
        /**
         * 1.服务器iP和端口；客户端和服务端之间的会话时间，以毫秒为单位；监视对象
         * 2.
         */
        ZooKeeper zooKeeper = new ZooKeeper("192.168.10.129:2181", 5000, new Watcher() {
            @Override
            public void process(WatchedEvent event) {
                if(event.getState() == Event.KeeperState.SyncConnected){
                    System.out.println("连接创建成功---------");
                    countDownLatch.countDown(); //启动主线程，不让其进行阻塞
                }
            }
        });
        //主线程阻塞等待连接对象创建成功
        countDownLatch.await();
        //会话编号
        System.out.println("会话编号："+zooKeeper.getSessionId());
        zooKeeper.close();
    }
}
```

控制台输出：

<img src="Zookeeper学习1-单机.assets\image-20200713090002774.png" alt="image-20200713090002774" style="zoom:80%;" />

## 3.2 新增节点

```java
// 同步方式
create(String path, byte[] data, List<ACL> acl, CreateMode createMode)
    
// 异步方式
create(String path, byte[] data, List<ACL> acl, CreateMode createMode，
AsyncCallback.StringCallback callBack,Object ctx)
```

+ path - znode路径。例如，/node1 /node1/node11
+  data - 要存储在指定znode路径中的数据 
+ acl - 要创建的节点的访问控制列表。zookeeper API提供了一个静态接口 ZooDefs.Ids 来获取一些基本的acl列表。例如，`ZooDefs.Ids.OPEN_ACL_UNSAFE` 返回打开znode的acl列表。 
+ createMode - 节点的类型,这是一个枚举。 
+ callBack-异步回调接口 
+ ctx-传递上下文参数

```java
public class ZKCreate {
    String IP = "192.168.10.129:2181";
    ZooKeeper zooKeeper;

    @Before
    public void before() throws Exception {
        System.out.println("before");

        CountDownLatch countDownLatch = new CountDownLatch(1);
        zooKeeper = new ZooKeeper(IP, 5000, new Watcher() {
            @Override
            public void process(WatchedEvent event) {
                if (event.getState() == Event.KeeperState.SyncConnected) {
                    System.out.println("连接创建成功!");
                    countDownLatch.countDown();
                }
            }
        });
        countDownLatch.await();
    }

    @After
    public void after() throws Exception {
        System.out.println("after");
        zooKeeper.close();
    }

    @Test
    public void create1() throws Exception {
        System.out.println("create1");

        /**
         * arg1:节点的路径
         * arg2:节点的数据
         * arg3:权限列表 world:anyone:cdrwa
         * arg4:节点类型 持久化节点
         */
        zooKeeper.create("/create/node1", "node1".getBytes(),
                ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
    }

    @Test
    public void create2() throws Exception {
        // Ids.READ_ACL_UNSAFE world:anyone:r
        zooKeeper.create("/create/node2", "node2".getBytes(),
                ZooDefs.Ids.READ_ACL_UNSAFE, CreateMode.PERSISTENT);
    }

    @Test //自定义权限
    public void create3() throws Exception {
        // world授权模式
        // 权限列表
        List<ACL> acls = new ArrayList<ACL>();
        // 授权模式和授权对象
        Id id = new Id("world", "anyone");
        // 权限设置
        acls.add(new ACL(ZooDefs.Perms.READ, id));
        acls.add(new ACL(ZooDefs.Perms.WRITE, id));
        zooKeeper.create("/create/node3", "node3".getBytes(), acls,
                CreateMode.PERSISTENT);
    }

    @Test
    public void create4() throws Exception {
        // ip授权模式
        // 权限列表
        List<ACL> acls = new ArrayList<ACL>();
        // 授权模式和授权对象
        Id id = new Id("ip", "192.168.10.129");
        // 权限设置（所有权限）
        acls.add(new ACL(ZooDefs.Perms.ALL, id));
        zooKeeper.create("/create/node4-1", "node4-1".getBytes(), acls,
                CreateMode.PERSISTENT);
    }

    @Test
    public void create5() throws Exception {
        // auth授权模式（用户名和密码）
        // 添加授权用户
        zooKeeper.addAuthInfo("digest", "itcast:123456".getBytes());
        zooKeeper.create("/create/node5", "node5".getBytes(),
                ZooDefs.Ids.CREATOR_ALL_ACL, CreateMode.PERSISTENT);
    }

    @Test
    public void create6() throws Exception {
        // auth授权模式
        // 添加授权用户
        zooKeeper.addAuthInfo("digest", "itcast:123456".getBytes());
        // 权限列表
        List<ACL> acls = new ArrayList<ACL>();
        // 授权模式和授权对象
        Id id = new Id("auth", "itcast");
        // 权限设置
        acls.add(new ACL(ZooDefs.Perms.READ, id));
        zooKeeper.create("/create/node6", "node6".getBytes(), acls,
                CreateMode.PERSISTENT);
    }
    @Test
    public void create7() throws Exception {
        // digest授权模式
        // 权限列表
        List<ACL> acls = new ArrayList<ACL>();
        // 授权模式和授权对象
        Id id = new Id("digest", "itheima:qlzQzCLKhBROghkooLvb+Mlwv4A=");
        // 权限设置
        acls.add(new ACL(ZooDefs.Perms.ALL, id));
        zooKeeper.create("/create/node7", "node7".getBytes(), acls,
                CreateMode.PERSISTENT);
    }

    @Test
    public void create8() throws Exception {
        // 持久化有序节点
        // Ids.OPEN_ACL_UNSAFE world:anyone:cdrwa
        String result = zooKeeper.create("/create/node8",
                "node8".getBytes(), ZooDefs.Ids.OPEN_ACL_UNSAFE,
                CreateMode.PERSISTENT_SEQUENTIAL);
        ///create/node80000000007
        System.out.println(result);
    }

    @Test
    public void create11() throws Exception {
        // 异步方式创建节点
        zooKeeper.create("/create/node11", "node11".getBytes(),
                ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT, new
                        AsyncCallback.StringCallback() {
                            @Override
                            public void processResult(int rc, String path, Object ctx, String name) {
                                // 0 代表创建成功
                                System.out.println(rc);
                                // 节点的路径 /create/node11
                                System.out.println(path);
                                // 节点的名字 /create/node11
                                System.out.println(name);
                                // 上下文参数
                                System.out.println(ctx);
                            }
                        }, "I am context"); //这里的数据会传到参数中
        Thread.sleep(10000);
        System.out.println("结束");
    }
}
```

+ 我们可以在Xshell中进行查看。临时节点查看时已经消失。

## 3.3 更新节点

```java
// 同步方式
setData(String path, byte[] data, int version)
    
// 异步方式
setData(String path, byte[] data, int version，AsyncCallback.StatCallback
callBack， Object ctx)
```

+ path- znode路径
+ data - 要存储在指定znode路径中的数据。 
+ version- znode的当前版本。每当数据更改时，ZooKeeper会更新znode的版本号。 
+ callBack-异步回调接口 ctx-传递上下文参数

```java
@Test
    public void set1() throws Exception {
        // arg1:节点的路径
        // arg2:节点修改的数据
        // arg3:版本号 -1代表版本号不作为修改条件，版本号不匹配时会报错
        Stat stat=zookeeper.setData("/set/node1","node11".getBytes(),2);
        // 节点的版本号
        System.out.println(stat.getVersion());
        // 节点的创建时间
        System.out.println(stat.getCtime());
    }

    @Test
    public void set2() throws Exception {
        // 异步方式修改节点
        zookeeper.setData("/set/node2", "node21".getBytes(), -1, new
                AsyncCallback.StatCallback() {
                    
                    @Override//服务器端回显事件
                    public void processResult(int rc, String path, Object ctx, Stat stat) {
                        // 0 代表修改成功
                        System.out.println(rc);
                        // 修改节点的路径
                        System.out.println(path);
                        // 上下文的参数对象
                        System.out.println(ctx);
                        // 属性信息
                        System.out.println(stat.getVersion());
                    }
                },"I am Context");
        Thread.sleep(50000);
        System.out.println("结束");
    }
```

## 3.4 删除节点

```java
// 同步方式
delete(String path, int version)

// 异步方式
delete(String path, int version, AsyncCallback.VoidCallback callBack,
Object ctx)
```

+ path - znode路径。 
+ version - znode的当前版本 
+ callBack-异步回调接口 
+ ctx-传递上下文参数

```java
 @Test
    public void delete1() throws Exception {
        // arg1:删除节点的节点路径
        // arg2:数据版本信息 -1代表删除节点时不考虑版本信息
        zooKeeper.delete("/delete/node1",-1);
    }

    @Test
    public void delete2() throws Exception {
        // 异步使用方式
        zooKeeper.delete("/delete/node2", -1, new
                AsyncCallback.VoidCallback() {
                    @Override
                    public void processResult(int rc, String path, Object ctx) {
                        // 0代表删除成功
                        System.out.println(rc);
                        // 节点的路径
                        System.out.println(path);
                        // 上下文参数对象
                        System.out.println(ctx);
                    }
                },"I am Context");
        Thread.sleep(10000);
        System.out.println("结束");
    }
```

## 3.5 查看节点

```java
// 同步方式
getData(String path, boolean b, Stat stat)

// 异步方式
getData(String path, boolean b，AsyncCallback.DataCallback callBack，
Object ctx)
```

+ path - znode路径。 
+ b- 是否使用连接对象中注册的监视器。 
+ stat - 返回znode的元数据。 
+ callBack-异步回调接口 。
+ ctx-传递上下文参数。

```java
 @Test
    public void get1() throws Exception {
        // arg1:节点的路径
        // arg3:读取节点属性的对象
        Stat stat=new Stat();
        byte [] bys=zooKeeper.getData("/get/node1",false,stat);
        // 打印数据
        System.out.println(new String(bys));
        // 版本信息
        System.out.println(stat.getVersion());
    }

    @Test
    public void get2() throws Exception {
        //异步方式
        zooKeeper.getData("/get/node1", false, new
                AsyncCallback.DataCallback() {
                    @Override
                    public void processResult(int rc, String path, Object ctx,
                                              byte[] data, Stat stat) {
                        // 0代表读取成功
                        System.out.println(rc);
                        // 节点的路径
                        System.out.println(path);
                        // 上下文参数对象
                        System.out.println(ctx);
                        // 数据
                        System.out.println(new String(data));
                        // 属性对象
                        System.out.println(stat.getVersion());
                    }
                },"I am Context");
        Thread.sleep(10000);
        System.out.println("结束");
    }
```

## 3.6 查看子节点

```java
// 同步方式
getChildren(String path, boolean b)

// 异步方式
getChildren(String path, boolean b,AsyncCallback.ChildrenCallback
callBack,Object ctx
```

+ path - Znode路径。 
+ b- 是否使用连接对象中注册的监视器。 
+ callBack - 异步回调接口。 
+ ctx-传递上下文参数

```java
 @Test
    public void get1() throws Exception {
        // arg1:节点的路径
        List<String> list = zooKeeper.getChildren("/get", false);
        for (String str : list) {
            System.out.println(str);
        }
    }

    @Test    // 异步用法
    public void get2() throws Exception {
        zooKeeper.getChildren("/get", false, new
                AsyncCallback.ChildrenCallback() {
                    @Override
                    public void processResult(int rc, String path, Object ctx, List<String> children) {
                        // 0代表读取成功
                        System.out.println(rc);
                        // 节点的路径
                        System.out.println(path);
                        // 上下文参数对象
                        System.out.println(ctx);
                        // 子节点信息
                        for (String str : children) {
                            System.out.println(str);
                        }
                    }
                },"I am Context");
        Thread.sleep(10000);
        System.out.println("结束");
    }
```

## 3.7 检查节点是否存在

```java
// 同步方法
exists(String path, boolean b)

// 异步方法
exists(String path, boolean b，AsyncCallback.StatCallback callBack,Object
ctx)
```

+ path- znode路径。 
+ b- 是否使用连接对象中注册的监视器。 
+ callBack - 异步回调接口。 
+ ctx-传递上下文参数

```java
 @Test
    public void exists1() throws Exception {
        // arg1:节点的路径
        //stat为null时不存在
        Stat stat = zooKeeper.exists("/exists1", false);
        System.out.println(stat.getVersion());
    }

    @Test
    public void exists2() throws Exception {
        zooKeeper.exists("/exists1", false, new
                AsyncCallback.StatCallback() {
                    @Override
                    public void processResult(int rc, String path, Object ctx,
                                              Stat stat) {
                        // 0 判断成功
                        System.out.println(rc);
                        // 路径
                        System.out.println(path);
                        // 上下文参数
                        System.out.println(ctx);
                        // null 节点不存在
                        System.out.println(stat.getVersion());
                    }
                }, "I am Context");
        Thread.sleep(5000);
        System.out.println("结束");
    }
```

# 4 zookeeper事件监听机制

## 4.1 watcher介绍

zookeeper提供了数据的发布/订阅功能，多个订阅者可同时监听某一特定主题对象，当该主题对象的自身状态发生变化时(例如节点内容改变、节点下的子节点列表改变 等)，会实时、主动通知所有订阅者。 

zookeeper采用了`Watcher`机制实现数据的发布/订阅功能。该机制在被订阅对象发生变化时会异步通知客户端，因此客户端不必在`Watcher`注册后轮询阻塞，从而减轻 客户端压力。 

`watcher`机制实际上与观察者模式类似，也可看作是一种观察者模式在分布式场 景下的实现方式。

### 4.1.1 watcher架构

Watcher实现由三个部分组成：

+ Zookeeper服务端 
+ Zookeeper客户端 
+ 客户端的ZKWatchManager对象

客户端首先将`Watcher`注册到服务端，同时将`Watcher`对象保存到客户端的`Watch`管理器中。当ZooKeeper服务端监听的数据状态发生变化时，服务端会主动通知客户端， 接着客户端的`Watch`管理器会触发相关`Watcher`来回调相应处理逻辑，从而完成整体的数据发布/订阅流程。

<img src="Zookeeper学习1-单机.assets\image-20200713105946718.png" alt="image-20200713105946718" style="zoom:80%;" />

### 4.1.2 watcher特性

|       特性        |                             描述                             |
| :---------------: | :----------------------------------------------------------: |
|      一次性       | watcher是一次性的，一旦被触发就会移除，再次使用时需要重新注册 |
| 客户 端顺 序回 调 | watcher回调是顺序串行化执行的，只有回调后客户端才能看到最新的数 据状态。一个watcher回调逻辑不应该太多，以免影响别的watcher执行 |
|      轻量 级      | WatchEvent是最小的通信单元，结构上只包含通知状态、事件类型和节点 路径，并不会告诉数据节点变化前后的具体内容 |
|      时效 性      | watcher只有在当前session彻底失效时才会无效，若在session有效期内 快速重连成功，则watcher依然存在，仍可接收到通知 |

### 4.1.3 watcher接口设计

Watcher是一个接口，任何实现了Watcher接口的类就是一个新的Watcher。 Watcher内部包含了两个枚举类：KeeperState、EventType

<img src="Zookeeper学习1-单机.assets\image-20200713110253908.png" alt="image-20200713110253908" style="zoom:80%;" />

+ **Watcher通知状态(KeeperState)**

KeeperState是客户端与服务端**连接状态**发生变化时对应的通知类型。路径为 org.apache.zookeeper.Watcher.Event.KeeperState，是一个枚举类，其枚举属性 如下：

|   枚举属性    |           描述           |
| :-----------: | :----------------------: |
| SyncConnected | 客户端与服务器正常连接时 |
| Disconnected  | 客户端与服务器断开连接时 |
|    Expired    |    会话session失效时     |
|  AuthFailed   |      身份认证失败时      |

+ **Watcher事件类型(EventType)**

EventType是**数据节点(znode)**发生变化时对应的通知类型。当EventType发生变化时，KeeperState永远处于SyncConnected通知状态下；当KeeperState发生变化时， EventType永远为None。其路径为org.apache.zookeeper.Watcher.Event.EventType， 是一个枚举类，枚举属性如下：

|      枚举类型       |                            描述                            |
| :-----------------: | :--------------------------------------------------------: |
|        None         |                             无                             |
|     NodeCreated     |               Watcher监听的数据节点被创建时                |
|     NodeDeleted     |               Watcher监听的数据节点被删除时                |
|   NodeDataChanged   | Watcher监听的数据节点内容发生变更时(无论内容数据 是否变化) |
| NodeChildrenChanged |        Watcher监听的数据节点的子节点列表发生变更时         |

注：客户端接收到的相关事件通知中只包含状态及类型等信息，不包括节点变化前后的具体内容，变化前的数据需业务自身存储，变化后的数据需调用get等方法重新获取。

**process**方法是`Watcher`接口中的一个回调方法，当ZooKeeper向客户端发送一个Watcher事件通知时，客户端就会对相应的process方法进行回调，从而实现对事件的处理，只要程序在运行，process就会一直监听来自zookeeper端的状态变化。

### 4.1.4 捕获相应的事件

上面讲到zookeeper客户端连接的状态和zookeeper对znode节点监听的事件类型，下面我们来讲解如何建立zookeeper的watcher监听。在zookeeper中采用 `zk.getChildren(path, watch)、zk.exists(path, watch)、zk.getData(path, watcher, stat`) 这样的方式为某个znode注册监听。

下表以node-x节点为例，说明调用的注册方法和可监听事件间的关系：

|             注册方式             | Created | ChildrenChanged | Changed | Deleted |
| :------------------------------: | :-----: | :-------------: | :-----: | :-----: |
|   zk.exists(“/nodex”,watcher)    | 可监控  |                 | 可监控  | 可监控  |
|   zk.getData(“/nodex”,watcher)   |         |                 | 可监控  | 可监控  |
| zk.getChildren(“/nodex”,watcher) |         |     可监控      |         | 可监控  |

## 4.2 注册watcher的方法

### 1、 客户端与服务器的连接状态

```ini
KeeperState 通知状态
SyncConnected:客户端与服务器正常连接时
Disconnected:客户端与服务器断开连接时
Expired:会话session失效时
AuthFailed:身份认证失败时

事件类型为:None
```

```java
public class ZKConnectionWatcher implements Watcher {
    // 计数器对象
    static CountDownLatch countDownLatch = new CountDownLatch(1);
    // 连接对象
    static ZooKeeper zooKeeper;

    public static void main(String[] args) {
        try {
            /**
             *  arg2：设置超时时间，50s内网路能够畅通的话，还可以重新连接上。否则，会将该会话剔除。
             *  arg3:为监听器对象
             */
            zooKeeper = new ZooKeeper("192.168.10.129:2181", 5000, new ZKConnectionWatcher());
            // 阻塞线程等待连接的创建
            countDownLatch.await();
            // 会话id
            System.out.println(zooKeeper.getSessionId());
            // 添加授权用户auth方式。
            zooKeeper.addAuthInfo("digest","xiaolun:123456".getBytes());
            byte [] bs=zooKeeper.getData("/node2",false,null);
            System.out.println(new String(bs));
            Thread.sleep(50000);
            zooKeeper.close();
            System.out.println("结束");
        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }

    @Override
    public void process(WatchedEvent event) {
        try {
            // 事件类型
            if (event.getType() == Event.EventType.None) {
                if (event.getState() == Event.KeeperState.SyncConnected) {
                    System.out.println("连接创建成功!");
                    countDownLatch.countDown();  //通知线程可以继续运行
                } else if (event.getState() == Event.KeeperState.Disconnected) {
                    System.out.println("断开连接！");
                } else if (event.getState() == Event.KeeperState.Expired) {
                    System.out.println("会话超时!");
                    zooKeeper = new ZooKeeper("192.168.10.129:2181", 5000, new ZKConnectionWatcher());
                } else if (event.getState() == Event.KeeperState.AuthFailed) {
                    System.out.println("认证失败！");
                }
            }
        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }
}
```

+ 在虚拟机上需要添加`/node2`节点,并使用auth授权方式，这样上述代码才能够操作。

### 2、检查节点是否存在

```java
// 使用连接对象的监视器
exists(String path, boolean b)
    
// 自定义监视器
exists(String path, Watcher w)
// NodeCreated:节点创建
// NodeDeleted:节点删除
// NodeDataChanged:节点内容发生变化
```

+ path- znode路径。 
+ b- 是否使用连接对象中注册的监视器。 
+ w-监视器对象。

```java
public class ZKWatcherExists {
    String IP = "192.168.10.129:2181";
    ZooKeeper zooKeeper = null;

    @Before
    public void before() throws IOException, InterruptedException {
        CountDownLatch countDownLatch = new CountDownLatch(1);
        // 连接zookeeper客户端
        zooKeeper = new ZooKeeper(IP, 6000, new Watcher() {
            @Override
            public void process(WatchedEvent event) {
                System.out.println("连接对象的参数!");
                // 连接成功
                if (event.getState() == Event.KeeperState.SyncConnected) {
                    System.out.println("--------启动阻塞的线程---------");
                    countDownLatch.countDown();
                }
                //获取发生时间的节点路径和类型
                System.out.println("path=" + event.getPath());
                System.out.println("eventType=" + event.getType());
            }
        });
        countDownLatch.await();
    }

    @After
    public void after() throws InterruptedException {
        zooKeeper.close();
    }

    @Test
    public void watcherExists1() throws Exception {
        // arg1:节点的路径
        // arg2:使用连接对象中的watcher，要进行监听
        zooKeeper.exists("/watcher1", true);
        Thread.sleep(50000);
        System.out.println("结束");
    }

    @Test
    public void watcherExists2() throws Exception {
        // arg1:节点的路径
        // arg2:使用自定义watcher对象进行监听。
        zooKeeper.exists("/watcher1", new Watcher() {
            @Override
            public void process(WatchedEvent event) {
                System.out.println("自定义watcher");
                System.out.println("path=" + event.getPath());
                System.out.println("eventType=" + event.getType());
            }
        });
        Thread.sleep(50000);
        System.out.println("结束");
    }

    @Test // watcher一次性
    public void watcherExists3() throws Exception {
        //创建一个watcher对象
        Watcher watcher = new Watcher() {
            @Override
            public void process(WatchedEvent event) {
                try {
                    System.out.println("自定义watcher");
                    System.out.println("path=" + event.getPath());
                    System.out.println("eventType=" + event.getType());

                    //可以实现多次监听。注释掉后，只能实现一次监听事件
                    zooKeeper.exists("/watcher1", this);
                } catch (Exception ex) {
                    ex.printStackTrace();
                }
            }
        };
        zooKeeper.exists("/watcher1", watcher);
        Thread.sleep(80000);
        System.out.println("结束");
    }

    @Test   //在客户端可以注册多个监听器对象
    public void watcherExists4() throws Exception {
        zooKeeper.exists("/watcher1", new Watcher() {
            @Override
            public void process(WatchedEvent event) {
                System.out.println("1");
                System.out.println("path=" + event.getPath());
                System.out.println("eventType=" + event.getType());
            }
        });

        zooKeeper.exists("/watcher1", new Watcher() {
            @Override
            public void process(WatchedEvent event) {
                System.out.println("2");
                System.out.println("path=" + event.getPath());
                System.out.println("eventType=" + event.getType());
            }
        });
        Thread.sleep(80000);
        System.out.println("结束");
    }
}
```

### 3、查看节点

```java
// 使用连接对象的监视器
getData(String path, boolean b, Stat stat)

// 自定义监视器
getData(String path, Watcher w, Stat stat)
    
// NodeDeleted:节点删除
// NodeDataChanged:节点内容发生变化
```

+ path- znode路径。 
+ b- 是否使用连接对象中注册的监视器。 
+ w-监视器对象。 
+ stat- 返回znode的元数据。

```java
@Test
public void watcherGetData1() throws Exception {
    // arg1:节点的路径
    // arg2:使用连接对象中的watcher
    zooKeeper.getData("/watcher2", true, null);
    Thread.sleep(50000);
    System.out.println("结束");
}

@Test //自定义watcher2
public void watcherGetData2() throws Exception {
    // arg1:节点的路径
    // arg2:自定义watcher对象
    zooKeeper.getData("/watcher2", new Watcher() {
        @Override
        public void process(WatchedEvent event) {
            System.out.println("自定义watcher");
            System.out.println("path=" + event.getPath());
            System.out.println("eventType=" + event.getType());
        }
    }, null);
    Thread.sleep(50000);
    System.out.println("结束");
}

@Test// 一次性
public void watcherGetData3() throws Exception {
    Watcher watcher = new Watcher() {
        @Override
        public void process(WatchedEvent event) {
            try {
                System.out.println("自定义watcher");
                System.out.println("path=" + event.getPath());
                System.out.println("eventType=" + event.getType());
                if (event.getType() == Event.EventType.NodeDataChanged) {
                    zooKeeper.getData("/watcher2", this, null);
                } //被删除的话，就不注册了。只监听数据改变的情况。
            } catch (Exception ex) {
                ex.printStackTrace();
            }
        }
    };
    zooKeeper.getData("/watcher2", watcher, null);
    Thread.sleep(50000);
    System.out.println("结束");
}

@Test
public void watcherGetData4() throws Exception {
    // 注册多个监听器对象
    zooKeeper.getData("/watcher2", new Watcher() {
        @Override
        public void process(WatchedEvent event) {
            try {
                System.out.println("1");
                System.out.println("path=" + event.getPath());
                System.out.println("eventType=" + event.getType());
                if (event.getType() == Event.EventType.NodeDataChanged) {
                    zooKeeper.getData("/watcher2", this, null);
                }
            } catch (Exception ex) {
                ex.printStackTrace();
            }
        }
    }, null);
    zooKeeper.getData("/watcher2", new Watcher() {
        @Override
        public void process(WatchedEvent event) {
            try {
                System.out.println("2");
                System.out.println("path=" + event.getPath());
                System.out.println("eventType=" + event.getType());
                if (event.getType() == Event.EventType.NodeDataChanged) {
                    zooKeeper.getData("/watcher2", this, null);
                }
            } catch (Exception ex) {
                ex.printStackTrace();
            }
        }
    }, null);
    Thread.sleep(50000);
    System.out.println("结束");
}
```

### 4、查看子节点

```java
// 使用连接对象的监视器
getChildren(String path, boolean b)

// 自定义监视器
getChildren(String path, Watcher w)
// NodeChildrenChanged:子节点发生变化
// NodeDeleted:节点删除
```

+ path- znode路径。 
+ b- 是否使用连接对象中注册的监视器。 
+ w-监视器对象。

```java
@Test
public void watcherGetChild1() throws Exception {
    // arg1:节点的路径
    // arg2:使用连接对象中的watcher
    zooKeeper.getChildren("/watcher3", true);
    Thread.sleep(50000);
    System.out.println("结束");
}

@Test
public void watcherGetChild2() throws Exception {
    // arg1:节点的路径
    // arg2:自定义watcher
    zooKeeper.getChildren("/watcher3", new Watcher() {
        @Override
        public void process(WatchedEvent event) {
            System.out.println("自定义watcher");
            System.out.println("path=" + event.getPath());
            System.out.println("eventType=" + event.getType());
        }
    });
    Thread.sleep(50000);
    System.out.println("结束");
}

@Test// 一次性
public void watcherGetChild3() throws Exception {
    Watcher watcher = new Watcher() {
        @Override
        public void process(WatchedEvent event) {
            try {
                System.out.println("自定义watcher");
                System.out.println("path=" + event.getPath());
                System.out.println("eventType=" + event.getType());
                if (event.getType() ==
                        Event.EventType.NodeChildrenChanged) {
                    zooKeeper.getChildren("/watcher3", this);
                }
            } catch (Exception ex) {
                ex.printStackTrace();
            }
        }
    };
    zooKeeper.getChildren("/watcher3", watcher);
    Thread.sleep(50000);
    System.out.println("结束");
}

@Test // 多个监视器对象
public void watcherGetChild4() throws Exception {
    zooKeeper.getChildren("/watcher3", new Watcher() {
        @Override
        public void process(WatchedEvent event) {
            try {
                System.out.println("1");
                System.out.println("path=" + event.getPath());
                System.out.println("eventType=" + event.getType());
                if (event.getType() ==
                        Event.EventType.NodeChildrenChanged) {
                    zooKeeper.getChildren("/watcher3", this);
                }
            } catch (Exception ex) {
                ex.printStackTrace();
            }
        }
    });
    zooKeeper.getChildren("/watcher3", new Watcher() {
        @Override
        public void process(WatchedEvent event) {
            try {
                System.out.println("2");
                System.out.println("path=" + event.getPath());
                System.out.println("eventType=" + event.getType());
                if (event.getType() ==
                        Event.EventType.NodeChildrenChanged) {
                    zooKeeper.getChildren("/watcher3", this);
                }
            } catch (Exception ex) {
                ex.printStackTrace();
            }
        }
    });
    Thread.sleep(50000);
    System.out.println("结束");
}
```

### 5、配置中心案例

工作中有这样的一个场景: 数据库用户名和密码信息放在一个配置文件中，应用 读取该配置文件，配置文件信息放入缓存。 

若数据库的用户名和密码改变时候，还需要重新加载缓存，比较麻烦，通过 ZooKeeper可以轻松完成，当数据库发生变化时自动完成缓存同步。 

设计思路： 

 	1. 连接zookeeper服务器 
 	2. 读取zookeeper中的配置信息，注册watcher监听器，存入本地变量 
 	3. 当zookeeper中的配置信息发生变化时，通过watcher的回调方法捕获数据变化事件 
 	4. 重新获取配置信息



```java
public class MyConfigCenter implements Watcher {
    // zk的连接串
    String IP = "192.168.10.129:2181";
    // 计数器对象
    CountDownLatch countDownLatch = new CountDownLatch(1);
    // 连接对象
    static ZooKeeper zooKeeper;

    // 用于本地化存储配置信息(记得要添加相应的get/set方法)
    private String url;
    private String username;
    private String password;

    @Override
    public void process(WatchedEvent event) {
        try {
            // 捕获事件状态
            if (event.getType() == Event.EventType.None) {
                if (event.getState() == Event.KeeperState.SyncConnected) {
                    System.out.println("连接成功");
                    countDownLatch.countDown();
                } else if (event.getState() == Event.KeeperState.Disconnected) {
                    System.out.println("连接断开!");
                } else if (event.getState() == Event.KeeperState.Expired) {
                    System.out.println("连接超时!");
                    // 超时后服务器端已经将连接释放，需要重新连接服务器端
                    zooKeeper = new ZooKeeper("192.168.10.129:2181", 6000, new ZKConnectionWatcher());
                } else if (event.getState() == Event.KeeperState.AuthFailed) {
                    System.out.println("验证失败!");
                }
                // 当配置信息发生变化时
            } else if (event.getType() == Event.EventType.NodeDataChanged) {
                initValue();
            }
        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }

    // 构造方法
    public MyConfigCenter() {
        initValue();
    }

    // 连接zookeeper服务器，读取配置信息
    public void initValue() {
        try {
            // 创建连接对象
            zooKeeper = new ZooKeeper(IP, 5000, this);
            // 阻塞线程，等待连接的创建成功
            countDownLatch.await();
            // 读取配置信息
            this.url = new String(zooKeeper.getData("/config/url", true, null));
            this.username = new String(zooKeeper.getData("/config/username", true, null));
            this.password = new String(zooKeeper.getData("/config/password", true, null));
        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }

    public static void main(String[] args) {
        try {
            MyConfigCenter myConfigCenter = new MyConfigCenter();
            for (int i = 1; i <= 30; i++) {
                System.out.println("次数：i = "+ i);
                Thread.sleep(5000);
                System.out.println("url:" + myConfigCenter.getUrl());
                System.out.println("username:" + myConfigCenter.getUsername());
                System.out.println("password:" + myConfigCenter.getPassword());
                System.out.println("########################################");
            }
        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }
}
```

### 6、生成分布式唯一ID

在过去的单库单表型系统中，通常可以使用数据库字段自带的`auto_increment` 属性来自动为每条记录生成一个唯一的ID。

但是分库分表后，就无法在依靠数据库的 `auto_increment`属性来唯一标识一条记录了。此时我们就可以用zookeeper在分布式环境下生成全局唯一ID。

设计思路： 

1. 连接zookeeper服务器 

2. 指定路径生成临时有序节点 

3. 取序列号及为分布式环境下的唯一ID

```java
public class GloballyUniqueId implements Watcher {
    // zk的连接串
    String IP = "192.168.10.129:2181";
    // 计数器对象
    CountDownLatch countDownLatch = new CountDownLatch(1);
    // 用户生成序号的节点
    String defaultPath = "/uniqueId";
    // 连接对象
    ZooKeeper zooKeeper;

    @Override
    public void process(WatchedEvent event) {
        try {
            // 捕获事件状态
            if (event.getType() == Watcher.Event.EventType.None) {
                if (event.getState() == Watcher.Event.KeeperState.SyncConnected) {
                    System.out.println("连接成功");
                    countDownLatch.countDown();
                } else if (event.getState() == Watcher.Event.KeeperState.Disconnected) {
                    System.out.println("连接断开!");
                } else if (event.getState() == Watcher.Event.KeeperState.Expired) {
                    System.out.println("连接超时!");
                    // 超时后服务器端已经将连接释放，需要重新连接服务器端
                    zooKeeper = new ZooKeeper(IP, 6000, new ZKConnectionWatcher());
                } else if (event.getState() == Watcher.Event.KeeperState.AuthFailed) {
                    System.out.println("验证失败!");
                }
            }
        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }

    // 构造方法
    public GloballyUniqueId() {
        try {
            //打开连接
            zooKeeper = new ZooKeeper(IP, 5000, this);
            // 阻塞线程，等待连接的创建成功
            countDownLatch.await();
        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }

    // 生成id的方法
    public String getUniqueId() {
        String path = "";
        try {
            //创建临时有序节点
            path = zooKeeper.create(defaultPath, new byte[0], ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.EPHEMERAL_SEQUENTIAL);
        } catch (Exception ex) {
            ex.printStackTrace();
        }
        // 节点名称 /uniqueId0000000001 -> 从第九个字符开始获取0000000001作为唯一ID
        return path.substring(9);
    }

    public static void main(String[] args) {
        GloballyUniqueId globallyUniqueId = new GloballyUniqueId();
        for (int i = 1; i <= 5; i++) {
            String id = globallyUniqueId.getUniqueId();
            //输出：0000000011、0000000012 ... 0000000015
            System.out.println(id);
        }
    }
}
```

# 5 分布式锁

​		分布式锁有多种实现方式，比如通过数据库、redis都可实现。作为分布式协同 工具ZooKeeper，当然也有着标准的实现方式。下面介绍在zookeeper中如何实现排他锁。

设计思路： 

1. 每个客户端往/Locks下创建临时有序节点/Locks/Lock_,创建成功后/locks下面会有每一个客户端对应的节点，如：/Locks/Lock_00000001 。

2. 客户端取得/Locks下子节点，并进行排序，判断排在最前面的是否为自己，如果自己的锁节点在第一位，代表获取锁成功。

3. 如果自己的锁节点不在第一位，则监听自己前一位的锁节点。例如，自己锁节点 Lock_000000002,那么就监听 Lock_000000001节点。

4. 当前一位锁节点（Lock 000000001）的对应的客户端执行完成后，释放了锁，将会触发监听客户端（Lock 000000002）的逻辑。

5. 监听客户端重新执行第2步逻辑，判断自己是否获得了锁。

**项目结构**

<img src="Zookeeper学习1-单机.assets\image-20200713182046629.png" alt="image-20200713182046629" style="zoom:80%;" />



1. **创建和释放分布锁逻辑**

```java
public class MyLock {
    private static final String LOCK_ROOT_PATH = "/Locks";
    private static final String LOCK_NODE_NAME = "Lock_";
    // zk的连接串
    String IP = "192.168.10.129:2181";
    // 计数器对象
    CountDownLatch countDownLatch = new CountDownLatch(1);
    //ZooKeeper配置信息
    ZooKeeper zooKeeper;

    //测试临时有序节点
    public static void main(String[] args) {
        try {
            MyLock myLock = new MyLock();
            myLock.createLock();
        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }

    //监视器对象，监视上一个节点是否被删除
    Watcher watcher = new Watcher() {
        @Override
        public void process(WatchedEvent event) {
            if (event.getType() == Event.EventType.NodeDeleted) {
                synchronized (this) {
                    notifyAll(); //唤醒线程
                }
            }
        }
    };
    private String lockPath;

    // 打开zookeeper连接
    public MyLock() {
        try {
            zooKeeper = new ZooKeeper(IP, 5000, new Watcher() {
                @Override
                public void process(WatchedEvent event) {
                    if (event.getType() == Event.EventType.None) {
                        if (event.getState() == Event.KeeperState.SyncConnected) {
                            System.out.println("连接成功!");
                            countDownLatch.countDown();
                        }
                    }
                }
            });
            countDownLatch.await();
        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }

    //获取锁（重要）
    public void acquireLock() throws Exception {
        //创建锁节点
        createLock();
        //尝试获取锁
        attemptLock();
    }

    //创建锁节点
    private void createLock() throws Exception {
        //判断Locks是否存在，不存在创建持续节点
        Stat stat = zooKeeper.exists(LOCK_ROOT_PATH, false);
        if (stat == null) {
            zooKeeper.create(LOCK_ROOT_PATH, new byte[0], ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
        }
        // 创建临时有序节点
        lockPath = zooKeeper.create(LOCK_ROOT_PATH + "/" +
                        LOCK_NODE_NAME, new byte[0], ZooDefs.Ids.OPEN_ACL_UNSAFE,
                CreateMode.EPHEMERAL_SEQUENTIAL);
        System.out.println("节点创建成功:" + lockPath);
    }

    //尝试获取锁
    private void attemptLock() throws Exception {
        // 获取Locks节点下的所有子节点
        List<String> list = zooKeeper.getChildren(LOCK_ROOT_PATH, false);
        // 对子节点进行排序,比如：0021,0020 --> 0020,0021
        Collections.sort(list);

        //
        /** 1./Locks/Lock_000000001  -> Lock_000000001(截取后半部分)
         *  2.然后和Lock_0000000021比较，并将索引输出
         */
        int index = list.indexOf(lockPath.substring(LOCK_ROOT_PATH.length() + 1));
        if (index == 0) { //为0时，说明前面的客户端已经完成。
            System.out.println("获取锁成功!");
            return;
        } else {
            // 获取上一个节点的路径
            String path = list.get(index - 1);
            Stat stat = zooKeeper.exists(LOCK_ROOT_PATH + "/" + path, watcher);  //监视上一个（/Locks/Lock_000000xx）状态
            if (stat == null) {
                attemptLock();
            } else {//让当前客户端的线程处于阻塞等待状态
                synchronized (watcher) {
                    watcher.wait();
                }
                attemptLock();
            }
        }
    }

    //释放锁(重要)
    public void releaseLock() throws Exception {
        /**
         * 1.删除临时有序节点
         * 2.lockPath----> /Locks/Lock_0000000021
         */
        zooKeeper.delete(this.lockPath,-1);
        zooKeeper.close();
        System.out.println("锁已经释放:"+this.lockPath);
    }
}
```

2. **模拟开启多线程，进行调用**

```java
public class TicketSeller {

    private void sell() {
        System.out.println("售票开始");
        // 线程随机休眠数毫秒，模拟现实中的费时操作
        int sleepMillis = 5000;
        try {
            //代表复杂逻辑执行了一段时间
            Thread.sleep(sleepMillis);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("售票结束");
    }

    //使用分布式锁，让下面代码同步执行
    public void sellTicketWithLock() throws Exception {
        MyLock lock = new MyLock();
        // 获取锁
        lock.acquireLock();
        sell();  //买卖票
        //释放锁
        lock.releaseLock();
    }

    public static void main(String[] args) throws Exception {
        TicketSeller ticketSeller = new TicketSeller();
        for (int i = 0; i < 10; i++) {
            ticketSeller.sellTicketWithLock();
        }
    }
}
```

+ 控制台测试输出

<img src="Zookeeper学习1-单机.assets\image-20200713182429012.png" alt="image-20200713182429012" style="zoom:80%;" />