# 1 zookeeper集群搭建

单机环境下，jdk、zookeeper 安装完毕，基于一台虚拟机，进行zookeeper伪集群搭建，zookeeper集群中包含3个节点，节点对外提供服务端口号分别为2181、 2182、2183。

1. 基于zookeeper-3.4.10复制三份zookeeper安装好的服务器文件，目录名称分别为 zookeeper2181、zookeeper2182、zookeeper2183。

   注意：我们使用的zookeeper01这个用户登录，上面三个文件都放到/home/zookeeper01/下：

```ini
cp ‐r zookeeper‐3.4.10 zookeeper2181（2，3）
```

2. 修改（zookeeper2181）服务器对应配置文件。

（2.1）首先我们需在路径/home/zookeeper/software/zookeeper-3.4.10/conf下，使用下面的命令拷贝一下配置文件:

```ini
cp zoo_sample.cfg zoo.cfg
```

（2.2）对配置文件zoo.cfg进行更改：

+ 伪集群配置信息

```ini
#服务器对应端口号，三个不同的端口
clientPort=2181（2，3）

#数据快照文件所在路径
dataDir=/home/zookeeper01/zookeeper2181（2，3）/zoo_data
dataLogDir=/home/zookeeper01/zookeeper2181（2，3）/zoo_log

#伪集群配置信息
#服务器名称和地址：集群信息（服务器编号，服务器地址，LF通信端口，选举端口）
#server.A=YYY:C:D
    #A：是一个数字，表示服务器的编号
    #YYY：表示服务器的ip地址
    #C：LF通信端口，表示该服务器与集群中的leader交换信息的端口
    #D：Leader选举端口,表示选举新leader时服务器间相互通信的端口（当leader挂掉时，其余服务器会相互通信，选举出新的leader） 
    
#一般来说，集群中每一个服务器的A/B端口都是一样的。但是，当说采用的是伪集群时，IP地址都一样，只能对A端口和B端口不一样。
server.1=192.168.10.129:2287:3387
server.2=192.168.10.129:2288:3388
server.3=192.168.10.129:2289:3389
```

+ 集群配置信息

```ini
#集群配置信息，未加dataDir，dataLogDir而报错
#所使用的端口号都是2181,未做其他更改。
dataDir=/home/zookeeper/software/zookeeper-3.4.10/zoo_data
dataLogDir=/home/zookeeper/software/zookeeper-3.4.10/zoo_log

#伪集群配置端口信息
server.1=hadoop101:2888:3888
server.2=hadoop102:2888:3888
server.3=hadoop103:2888:3888
```

（2.3）我们需要在配置的基础上添加配置文件，比如：在集群下，添加配置文件为：zoo_data（/home/zookeeper/software/zookeeper-3.4.10/zoo_data）、zoo_log。

3. 在 上一步 dataDir 指定的目录下，创建 myid 文件，然后在该文件添加上一步 server 配置的对应 A 数字。

```ini
#zookeeper2181(2，3)对应的数字为1(2，3)
#/home/zookeeper01/zookeeper2181/data目录下执行命令
echo "1（2，3）" > myid
echo "1" > myid
```

4. zookeeper2182、zookeeper2183参照步骤2/3进行相应配置。
5. 分别启动三台服务器，检验集群状态。

```ini
#配置完成后，启动的时候要将zoo_data中的文件xxx.pid等文件删除，只留下myid文件。
./zkServer.sh start
```

​		**错误1**：未知的名称或服务。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130857.png" alt="image-20200715154607277" style="zoom:67%;" />

在root权限下，使用下面指令，添加ip映射关系：

```ini
vim /etc/hosts
```

```ini
192.168.10.101 hadoop101
192.168.10.102 hadoop102
192.168.10.103 hadoop103
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130858.png" alt="image-20200715155123053" style="zoom:67%;" />

**错误2**：地址已在使用。

我们需要检查端口2181的使用状态，将占用该端口的进程杀死。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130859.png" alt="image-20200715155526764" style="zoom:67%;" />

```ini
#检查端口状态
netstat -alnp | grep 2181
```

6. 客户端登录命令

+ 伪集群方式

```ini
#因为没有加-server,指令不识别，会连接不上服务器。
./zkCli.sh ‐server 192.168.10.129:2181
./zkCli.sh ‐server 192.168.10.129:2182
./zkCli.sh ‐server 192.168.10.129:2183
```

+ 集群方式

```ini
./zkCli.sh ‐server hadoop101:2181
./zkCli.sh ‐server hadoop102:2181
./zkCli.sh ‐server hadoop103:2181
```

登录后，可以在三个服务器端创建/获取节点进行测试。

在IDEA中，使用下面集群的方式进行登录测试：

```java
String IP = "hadoop101:2181,hadoop102:2181,hadoop103:2181";
```

## 1.1 一致性协议:zab协议

zab协议 的全称是 Zookeeper Atomic Broadcast （zookeeper原子广播）。zookeeper 是通过 zab协议来保证分布式事务的最终一致性。

基于zab协议，zookeeper集群中的角色主要有以下三类，如下表所示：

|             角色             |                             描述                             |
| :--------------------------: | :----------------------------------------------------------: |
|       领导者（leader）       |        领导者负责进行投票的发起和决议，更新系统状态。        |
| 跟随者（Follower）（学习者） | Follower用于接收客户请求并向客户端返回结果，在选主的时候进行投票。 |
| 观察者（Observer）（学习者） | Observer可以接收客户端的连接，将写请求转发给Leader节点，但Observer不参加投票过程，只同步Leader的状态。Observer只是为了扩展系统，提高读取速度。 |
|       客户端（client）       |                          请求发起方                          |

​		zab广播模式工作原理，通过类似两阶段提交协议的方式解决数据一致性：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130900.png" alt="image-20200714101154594" style="zoom:67%;" />

1. leader从客户端收到一个写请求 
2. leader生成一个新的事务并为这个事务生成一个唯一的ZXID 
3. leader将这个事务提议(propose)发送给所有的follows节点  
4. follower节点将收到的事务请求加入到历史队列(history queue)中,并发送ack给 leader 
5. 当leader收到大多数follower（半数以上节点）的ack消息，leader会发送commit请求 
6. 当follower收到commit请求时，从历史队列中将事务请求commit

## 1.2 zookeeper的leader选举

### 1.2.1 服务器状态

|   状态    |                             描述                             |
| :-------: | :----------------------------------------------------------: |
|  looking  | 寻找leader状态。当服务器处于该状态时，它会认为当前集群中没有 leader，因此需要进入leader选举状态。 |
|  leading  |           领导者状态。表明当前服务器角色是leader。           |
| following |          跟随者状态。表明当前服务器角色是follower。          |
| observing |          观察者状态。表明当前服务器角色是observer。          |

### 1.2.2 服务器启动时期的leader选举

​		在`zookeeper`集群初始化阶段，当有一台服务器`server1`(2181)启动时，其单独无法进行和完成 leader选举，当第二台服务器`server2`(2182)启动时，此时两台机器可以相互通信，每台机器都试图找到leader，于是进入leader选举过程。选举过程如下:

1. 每个server发出一个投票。由于是初始情况，server1和server2都会将自己作为 leader服务器来进行投票，每次投票会包含所推举的服务器的myid和zxid，使用 `(myid, zxid)`来表示，此时server1的投票为(1, 0)，server2的投票为(2, 0)，然后各自将这个投票发给集群中其他机器。 
2. 集群中的每台服务器接收来自集群中各个服务器的投票。 
3. 处理投票。针对每一个投票，服务器都需要将别人的投票和自己的投票进行pk，pk 规则如下 
   + 优先检查zxid。zxid比较大的服务器优先作为leader。 
   + 如果zxid相同，那么就比较myid。myid较大的服务器作为leader服务器。 对于Server1而言，它的投票是(1, 0)，接收Server2的投票为(2, 0)，首先会比较 两者的zxid，均为0，再比较myid，此时server2的myid最大，于是更新自己的投票 为(2, 0)，然后重新投票，对于server2而言，其无须更新自己的投票，只是再次向集群中所有机器发出上一次投票信息即可。 

4. 统计投票。每次投票后，服务器都会统计投票信息，判断是否已经有过半机器接受到相同的投票信息，对于server1、server2而言，都统计出集群中已经有两台机器接受 了(2, 0)的投票信息，此时便认为已经选出了leader 。

5. 改变服务器状态。一旦确定了leader，每个服务器就会更新自己的状态，如果是 follower，那么就变更为following，如果是leader，就变更为leading。

### 1.2.3 服务器运行时期的Leader选举

​		在zookeeper运行期间，leader与非leader服务器各司其职，即便当有非leader 服务器宕机或新加入，此时也不会影响leader，但是一旦leader服务器挂了，那么整个集 群将暂停对外服务，进入新一轮leader选举，其过程和启动时期的Leader选举过程基本 一致。 

​		假设正在运行的有`server1（2181）、server2（2182）、server3（2183）`三台服务器，当前leader是 server2，若某一时刻leader挂了，此时便开始Leader选举。选举过程如下:

1. 变更状态。leader挂后，余下的服务器都会将自己的服务器状态变更为looking，然 后开始进入leader选举过程。 
2. 每个server会发出一个投票。在运行期间，每个服务器上的zxid可能不同，此时假定 server1的zxid为122，server3的zxid为122，在第一轮投票中，server1和server3 都会投自己，产生投票(1, 122)，(3, 122)，然后各自将投票发送给集群中所有机器。 
3. 接收来自各个服务器的投票。与启动时过程相同 
4. 处理投票。与启动时过程相同，此时，server3将会成为leader。
5. 统计投票。与启动时过程相同。 
6. 改变服务器的状态。与启动时过程相同。

## 1.2 observer角色及其配置

**observer角色特点**

​		不参与集群的leader选举，同时也不参与集群中写数据时的ack反馈，为了使用observer角色，在任何想变成observer角色的配置文件（2183：zoo.cfg文件）中加入如下配置：

```ini
peerType=observer
```

​		并在所有server的配置文件中，配置成observer模式的server的那行配置追 加:observer，例如：

```ini
server.3=192.168.60.130:2289:3389:observer
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130901.png" alt="image-20200714104645760" style="zoom: 67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130902.png" alt="image-20200714104832305" style="zoom:67%;" />

## 1.3 .zookeeperAPI连接集群

```java
ZooKeeper(String connectionString, int sessionTimeout, Watcher watcher)
```

+ connectionString - zooKeeper集合主机。 
+ sessionTimeout - 会话超时（以毫秒为单位）。 
+ watcher - 实现“监视器”界面的对象。ZooKeeper集合通过监视器对象返回连接状态。

```java
public class ZookeeperConnection {
    public static void main(String[] args) {
        try {
            // 计数器对象
            CountDownLatch countDownLatch = new CountDownLatch(1);
            // arg1:服务器的ip和端口(是集群的ip地址，逗号隔开)
            // arg2:客户端与服务器之间的会话超时时间 以毫秒为单位的
            // arg3:监视器对象
            ZooKeeper zooKeeper = new
                    ZooKeeper("192.168.10.129:2181,192.168.10.129:2182,192.168.10.129:2183", 5000, new Watcher() {
                @Override
                public void process(WatchedEvent event) {
                    if (event.getState() == Event.KeeperState.SyncConnected) {
                        System.out.println("连接创建成功!");
                        countDownLatch.countDown();
                    }
                }
            });
            // 主线程阻塞等待连接对象的创建成功
            countDownLatch.await();
            // 会话编号
            System.out.println(zooKeeper.getSessionId());
            zooKeeper.close();
        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }
}
```

# 2 curator

## 2.1 介绍

​		curator是Netflix公司开源的一个zookeeper客户端，后捐献给apache， curator框架在zookeeper原生API接口上进行了包装，解决了很多zooKeeper客户端非常 底层的细节开发。提供zooKeeper各种应用场景(比如：分布式锁服务、集群领导选举、 共享计数器、缓存机制、分布式队列等)的抽象封装，实现了Fluent风格的API接口，是最 好用，最流行的zookeeper的客户端。

​		原生zookeeperAPI的不足：

+ 连接对象异步创建，需要开发人员自行编码等待 

+ 连接没有自动重连超时机制 

+ watcher一次注册生效一次 

+ 不支持递归创建树形节点 

  curator特点： 

+ 解决session会话超时重连 

+ watcher反复注册 

+ 简化开发api 

+ 遵循Fluent风格的API 

+ 提供了分布式锁服务、共享计数器、缓存机制等机制

**Maven依赖**

```
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.7</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-framework</artifactId>
    <version>2.6.0</version>
    <type>jar</type>
    <exclusions>
        <exclusion>
        <groupId>org.apache.zookeeper</groupId>
        <artifactId>zookeeper</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.apache.zookeeper</groupId>
    <artifactId>zookeeper</artifactId>
    <version>3.4.10</version>
    <type>jar</type>
</dependency>
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-recipes</artifactId>
    <version>2.6.0</version>
    <type>jar</type>
</dependency>
```

### 1、连接对象的创建

```java
public class CuratorConnection {

    public static void main(String[] args) {
        // 创建连接对象
        CuratorFramework client = CuratorFrameworkFactory.builder()
                // IP地址端口号
                .connectString("192.168.10.129:2181,192.168.10.129:2182,192.168.10.129:2183")
                // 会话超时时间
                .sessionTimeoutMs(5000)
                // 重连机制
                .retryPolicy(retryPolicy)
                // 命名空间(父节点)
                .namespace("create")
                // 构建连接对象
                .build();
        client.start();
        System.out.println(client.isStarted());  //为true时，说明已经创建。
        // 关闭连接
        client.close();
    }
}
```

session重连策略：

```ini
#策略1:3秒后重连一次，只重连1次
RetryPolicy retryPolicy = new RetryOneTime(3000);

#策略2:每3秒重连一次，重连3次
#arg1:重连次数；arg2:重连间隔
RetryPolicy retryPolicy = new RetryNTimes(3,3000);
        
#策略3:每3秒重连一次，总等待时间超过10秒后停止重连
RetryPolicy retryPolicy=new RetryUntilElapsed(10000,3000);
 
#策略4:公式：baseSleepTimeMs * Math.max(1, random.nextInt(1 << (retryCount+ 1)))
        //随着重连次数的增减，重连间隔也会增加
RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000, 3);
```

### 2、新增节点

```java
public class CuratorCreate {

    String IP = "192.168.10.129:2181,192.168.10.129:2182,192.168.10.129:2183";
    CuratorFramework client;

    @Before
    public void before() {
        RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000, 3);
        client = CuratorFrameworkFactory.builder()
                .connectString(IP)
                .sessionTimeoutMs(5000)
                .retryPolicy(retryPolicy)
                .namespace("create")
                .build();
        client.start();
    }

    @After
    public void after() {
        client.close();
    }

    @Test
    public void create1() throws Exception {
        // 新增节点
        client.create()
                // 节点的类型
                .withMode(CreateMode.PERSISTENT)
                // 节点的权限列表 world:anyone:cdrwa
                .withACL(ZooDefs.Ids.OPEN_ACL_UNSAFE)
                // arg1:节点的路径
                // arg2:节点的数据
                .forPath("/node1", "node1".getBytes());
        System.out.println("结束");
    }

    @Test    // 自定义权限列表
    public void create2() throws Exception {
        // 权限列表
        List<ACL> list = new ArrayList<ACL>();
        // 授权模式和授权对象
        Id id = new Id("ip", "192.168.10.129");
        list.add(new ACL(ZooDefs.Perms.ALL, id));
        client.create().withMode(CreateMode.PERSISTENT).withACL(list).forPath("/node2", " node2".getBytes());
        System.out.println("结束");
    }

    @Test   // 递归创建节点树
    public void create3() throws Exception {
        client.create()
                // 递归节点的创建
                .creatingParentsIfNeeded()
                .withMode(CreateMode.PERSISTENT)
                .withACL(ZooDefs.Ids.OPEN_ACL_UNSAFE)
                .forPath("/node3/node31", "node31".getBytes());
        System.out.println("结束");
    }

    @Test// 异步方式创建节点
    public void create4() throws Exception {
        client.create()
                .creatingParentsIfNeeded()
                .withMode(CreateMode.PERSISTENT)
                .withACL(ZooDefs.Ids.OPEN_ACL_UNSAFE)
                // 异步回调接口
                .inBackground(new BackgroundCallback() {
                    public void processResult(CuratorFramework curatorFramework, CuratorEvent curatorEvent) throws Exception {
                        // 节点的路径
                        System.out.println(curatorEvent.getPath());
                        // 时间类型
                        System.out.println(curatorEvent.getType());
                    }
                })
                .forPath("/node4", "node4".getBytes());
        Thread.sleep(5000);
        System.out.println("结束");
    }
}
```

### 3、更新节点

```java
@Test
public void set1() throws Exception {
    // 更新节点
    client.setData()
            // arg1:节点的路径
            // arg2:节点的数据
            .forPath("/node1", "node11".getBytes());
    System.out.println("结束");
}

@Test
public void set2() throws Exception {
    client.setData()
            // 指定版本号
            .withVersion(2)
            .forPath("/node1", "node1111".getBytes());
    System.out.println("结束");
}

@Test
public void set3() throws Exception {
    // 异步方式修改节点数据
    client.setData()
            .withVersion(-1).inBackground(new BackgroundCallback() {
        public void processResult(CuratorFramework curatorFramework, CuratorEvent curatorEvent) throws Exception {
            // 节点的路径
            System.out.println(curatorEvent.getPath());
            // 事件的类型
            System.out.println(curatorEvent.getType());
        }
    }).forPath("/node1", "node1".getBytes());
    Thread.sleep(5000);
    System.out.println("结束");
}
```

### 4、删除节点

```java
@Test
public void delete1() throws Exception {
    // 删除节点
    client.delete()
            // 节点的路径
            .forPath("/node1");
    System.out.println("结束");
}

@Test
public void delete2() throws Exception {
    client.delete()
            // 版本号
            .withVersion(0)
            .forPath("/node1");
    System.out.println("结束");
}

@Test
public void delete3() throws Exception {
    //删除包含字节点的节点
    client.delete()
            .deletingChildrenIfNeeded()
            .withVersion(-1)
            .forPath("/node1");
    System.out.println("结束");
}

@Test
public void delete4() throws Exception {
    // 异步方式删除节点
    client.delete()
            .deletingChildrenIfNeeded()
            .withVersion(-1)
            .inBackground(new BackgroundCallback() {
                public void processResult(CuratorFramework curatorFramework, CuratorEvent curatorEvent) throws Exception {
                    // 节点路径
                    System.out.println(curatorEvent.getPath());
                    // 事件类型
                    System.out.println(curatorEvent.getType());
                }
            })
            .forPath("/node1");
    Thread.sleep(5000);
    System.out.println("结束");
}
```

### 5、查看节点

```java
@Test
public void get1() throws Exception {
    // 读取节点数据
    byte[] bys = client.getData()
            // 节点的路径
            .forPath("/node1");
    System.out.println(new String(bys));
}

@Test
public void get2() throws Exception {
    // 读取数据时读取节点的属性
    Stat stat = new Stat();
    byte[] bys = client.getData()
            // 读取属性
            .storingStatIn(stat)
            .forPath("/node1");
    System.out.println(new String(bys));
    System.out.println(stat.getVersion());
}

@Test
public void get3() throws Exception {
    // 异步方式读取节点的数据
    client.getData()
            .inBackground(new BackgroundCallback() {
                public void processResult(CuratorFramework curatorFramework, CuratorEvent curatorEvent) throws Exception {
                    // 节点的路径
                    System.out.println(curatorEvent.getPath());
                    // 事件类型
                    System.out.println(curatorEvent.getType());
                    // 数据
                    System.out.println(new String(curatorEvent.getData()));
                }
            })
            .forPath("/node1");
    Thread.sleep(5000);
    System.out.println("结束");
}
```

### 6、查看子节点

```java
@Test
public void getChild1() throws Exception {
        // 读取子节点数据
    List<String> list = client.getChildren()
            // 节点路径
            .forPath("/get");
    for (String str : list) {
        System.out.println(str);
    }
}

@Test
public void getChild2() throws Exception {
    // 异步方式读取子节点数据
    client.getChildren()
            .inBackground(new BackgroundCallback() {
                public void processResult(CuratorFramework curatorFramework, CuratorEvent curatorEvent) throws Exception {
                    // 节点路径
                    System.out.println(curatorEvent.getPath());
                    // 事件类型
                    System.out.println(curatorEvent.getType());
                    // 读取子节点数据
                    List<String> list = curatorEvent.getChildren();
                    for (String str : list) {
                        System.out.println(str);
                    }
                }
            })
            .forPath("/get");
    Thread.sleep(5000);
    System.out.println("结束");
}
```

### 7、检查节点是否存在

```java
@Test
public void exists1() throws Exception {
        // 判断节点是否存在
    Stat stat = client.checkExists()
            // 节点路径
            .forPath("/node2");
    System.out.println(stat.getVersion());
}

@Test
public void exists2() throws Exception {
    // 异步方式判断节点是否存在
    client.checkExists()
            .inBackground(new BackgroundCallback() {
                public void processResult(CuratorFramework curatorFramework, CuratorEvent curatorEvent) throws Exception {
                    // 节点路径
                    System.out.println(curatorEvent.getPath());
                    // 事件类型
                    System.out.println(curatorEvent.getType());
                    System.out.println(curatorEvent.getStat().getVersion());
                }
            })
            .forPath("/node2");
    Thread.sleep(5000);
    System.out.println("结束");
}
```

### 8、watcherAPI

​		curator提供了两种Watcher(Cache)来监听结点的变化。

+ NodeCache : 只是监听某一个特定的节点，监听节点的新增和修改 。
+ PathChildrenCache : 监控一个ZNode的子节点. 当一个子节点增加， 更新，删除 时， Path Cache会改变它的状态， 会包含最新的子节点， 子节点的数据和状态。

```java
@Test// 监视某个节点的数据变化
public void watcher1() throws Exception {
    // arg1:连接对象
    // arg2:监视的节点路径
    final NodeCache nodeCache = new NodeCache(client, "/watcher1");
    // 启动监视器对象
    nodeCache.start();
    //注册监听器
    nodeCache.getListenable().addListener(new NodeCacheListener() {
        // 节点变化时回调的方法（不是一次性监听，处于一直监听的状态）
        public void nodeChanged() throws Exception {
            System.out.println(nodeCache.getCurrentData().getPath());
            System.out.println(new String(nodeCache.getCurrentData().getData()));
        }
    });
    Thread.sleep(100000);
    System.out.println("结束");
    //关闭监视器对象
    nodeCache.close();
}

@Test // 监视子节点的变化
public void watcher2() throws Exception {
    // arg1:连接对象
    // arg2:监视的节点路径
    // arg3:事件中是否可以获取节点的数据
    PathChildrenCache pathChildrenCache = new PathChildrenCache(client, "/watcher1", true);
    // 启动监听
    pathChildrenCache.start();
    pathChildrenCache.getListenable().addListener(new PathChildrenCacheListener() {
        // 当子节点方法变化时回调的方法
        public void childEvent(CuratorFramework curatorFramework, PathChildrenCacheEvent pathChildrenCacheEvent) throws Exception {
            // 节点的事件类型
            System.out.println(pathChildrenCacheEvent.getType());
            // 节点的路径
            System.out.println(pathChildrenCacheEvent.getData().getPath());
            // 节点数据
            System.out.println(new String(pathChildrenCacheEvent.getData().getData()));
        }
    });
    Thread.sleep(100000);
    System.out.println("结束");
    // 关闭监听
    pathChildrenCache.close();
}
```

### 9、事务

```java
@Test
public void tra1() throws Exception {
    //开启事务
    client.inTransaction().
            //创建两个节点
            create().forPath("node1", "node1".getBytes()).
            and().
            create().forPath("node2", "node2".getBytes()).
            and().
            //提交事务
            commit();
}
```

### 10、分布式锁

+ InterProcessMutex：分布式可重入排它
+ InterProcessReadWriteLock：分布式读写锁

```java
  @Test   // 排他锁
    public void lock1() throws Exception {

        /**
         * 排他锁需要实现的接口
         * arg1:连接对象
         * arg2:节点路径
         * 当两个排他锁对象指向同一个路径的话，两个对象对应同一把锁。
         */
        InterProcessLock interProcessLock = new InterProcessMutex(client, "/lock1");
        System.out.println("等待获取锁对象!");
        // 获取锁
        interProcessLock.acquire();
        for (int i = 1; i <= 10; i++) {
            Thread.sleep(3000);
            System.out.println(i);
        }
        // 释放锁
        interProcessLock.release();
        System.out.println("等待释放锁!");
    }

    @Test    // 读写锁
    public void lock2() throws Exception {
        InterProcessReadWriteLock interProcessReadWriteLock = new InterProcessReadWriteLock(client, "/lock1");
        // 获取读锁对象（读锁），可以共享同一个资源的
        InterProcessLock interProcessLock = interProcessReadWriteLock.readLock();
        System.out.println("等待获取锁对象!");
        // 获取锁
        interProcessLock.acquire();
        for (int i = 1; i <= 10; i++) {
            Thread.sleep(3000);
            System.out.println(i);
        }
        // 释放锁
        interProcessLock.release();
        System.out.println("等待释放锁!");
    }

    @Test
    public void lock3() throws Exception {
        // 读写锁
        InterProcessReadWriteLock interProcessReadWriteLock = new InterProcessReadWriteLock(client, "/lock1");
        // 获取写锁对象（写锁）
        InterProcessLock interProcessLock = interProcessReadWriteLock.writeLock();
        System.out.println("等待获取锁对象!");
        // 获取锁
        interProcessLock.acquire();
        for (int i = 1; i <= 10; i++) {
            Thread.sleep(3000);
            System.out.println(i);
        }
        // 释放锁
        interProcessLock.release();
        System.out.println("等待释放锁!");
    }
```

+ 需要开启两个进程来对节点进行资源的操作，当先启动读操作，后启动写操作时，写操作需要等待等操作执行完才能够进行写操作的执行。

## 2.2 zookeeper四字监控命令

zooKeeper支持某些特定的四字命令与其的交互。它们大多是查询命令，用来 获取 zooKeeper服务的当前状态及相关信息。

用户在客户端可以通过 `telnet` （没有安装，暂时无效）或 `nc` 向 `zooKeeper`提交相应的命令。 zooKeeper常用四字命令见下表所示：

| 命令 |                             描述                             |
| :--: | :----------------------------------------------------------: |
| conf | 输出相关服务配置的详细信息。比如端口、zk数据及日志配置路径、最大 连接数，session超时时间、serverId等 |
| cons | 列出所有连接到这台服务器的客户端连接/会话的详细信息。包括“接受/发 送”的包数量、session id 、操作延迟、最后的操作执行等信息 |
| crst |          重置当前这台服务器所有连接/会话的统计信息           |
| dump |                 列出未经处理的会话和临时节点                 |
| envi | 测试服务是否处于正确运行状态。如果正常返回"imok"，否则返回空 |
| stat | 输出服务器的详细信息：接收/发送包数量、连接数、模式 （leader/follower）、节点总数、延迟。 所有客户端的列表 |
| srst |                        重置server状态                        |
| wchs | 列出服务器watches的简洁信息：连接总数、watching节点总数和 watches总数 |
| wchc | 通过session分组，列出watch的所有节点，它的输出是一个与 watch 相关 的会话的节点列表 |
| mntr | 列出集群的健康状态。包括“接受/发送”的包数量、操作延迟、当前服务模 式（leader/follower）、节点总数、watch总数、临时节点总数 |

```
wget http://vault.centos.org/6.6/os/x86_64/Packages/nc-1.84-22.el6.x86_64.rpm
```

nc命令工具安装:

```ini
#root用户安装
#下载安装包
wget http://vault.centos.org/6.6/os/x86_64/Packages/nc-1.84-22.el6.x86_64.rpm

#rpm安装
rpm -iUv nc-1.84-22.el6.x86_64.rpm
```

​		在集群开启的情况下，使用命令：echo mntr | nc 192.168.10.129 2181来测试nc指令是否正常。、

### 1、conf命令

conf：输出相关服务配置的详细信息。

```ini
#shell终端输入：
echo cons| nc localhost 2181
```

|       属性        |                             含义                             |
| :---------------: | :----------------------------------------------------------: |
|    clientPort     |                         客户端端口号                         |
|      dataDir      |   数据快照文件目录 默认情况下100000次事务操作生成一次 快照   |
|    dataLogDir     |         事物日志文件目录，生产环境中放在独立的磁盘上         |
|     tickTime      | 服务器之间或客户端与服务器之间维持心跳的时间间隔(以 毫秒为单位) |
|  maxClientCnxns   |                          最大连接数                          |
| minSessionTimeout |         最小session超时 minSessionTimeout=tickTime*2         |
| maxSessionTimeout |        最大session超时 maxSessionTimeout=tickTime*20         |
|     serverId      |                          服务器编号                          |
|     initLimit     | 集群中的follower服务器(F)与leader服务器(L)之间初始连接时能容忍的最多心跳数 |
|     syncLimit     | 集群中的follower服务器(F)与leader服务器(L)之间 请求和应答之间能容忍的最多心跳数 |
|    electionAlg    | 0:基于UDP的LeaderElection 1:基于UDP的 FastLeaderElection 2:基于UDP和认证的 FastLeaderElection 3:基于TCP的FastLeaderElection 在 3.4.10版本中，默认值为3另外三种算法已经被弃用，并且 有计划在之后的版本中将它们彻底删除而不再支持 |
|   electionPort    |                           选举端口                           |
|    quorumPort     |                         数据通信端口                         |
|     peerType      |                    是否为观察者 1为观察者                    |

### 2、cons命令

cons:列出所有连接到这台服务器的客户端连接/会话的详细信息。

```ini
#shell终端输入：
echo cons| nc localhost 2181
```



|   属性   |                         属性                         |
| :------: | :--------------------------------------------------: |
|    ip    |                        ip地址                        |
|   port   |                        端口号                        |
|  queued  |         等待被处理的请求数，请求缓存在队列中         |
| received |                      收到的包数                      |
|   sent   |                      发送的包数                      |
|   sid    |                        会话id                        |
|   lop    | 最后的操作 GETD-读取数据 DELE-删除数据 CREA-创建数据 |
|   est    |                      连接时间戳                      |
|    to    |                       超时时间                       |
|  lcxid   |                   当前会话的操作id                   |
|  lzxid   |                      最大事务id                      |
|  lresp   |                    最后响应时间戳                    |
|   llat   |                    最后/最新 延时                    |
|  minlat  |                       最小延时                       |
|  maxlat  |                       最大延时                       |
|  avglat  |                        avglat                        |

### 3、crst命令

crst:重置当前这台服务器所有连接/会话的统计信息。 

```ini
#shell终端输入：
echo crst| nc localhost 2181
```

### 4、dump命令

dump:列出未经处理的会话和临时节点。

```ini
#shell终端输入：
echo dump| nc localhost 2181
```

|    属性    |                         含义                          |
| :--------: | :---------------------------------------------------: |
| session id | znode path(1对多 , 处于队列中排队的session和临时节点) |

### 5、 envi命令

envi:输出关于服务器的环境配置信息 

```ini
#shell终端输入：
echo envi| nc localhost 2181
```

|       属性        |                    含义                     |
| :---------------: | :-----------------------------------------: |
| zookeeper.version |                    版本                     |
|     host.name     |                  host信息                   |
|   java.version    |                  java版本                   |
|    java.vendor    |                   供应商                    |
|     java.home     |              运行环境所在目录               |
|  java.class.path  |                  classpath                  |
| java.library.path | 第三方库指定非java类包的位置（如：dll，so） |
|  java.io.tmpdir   |             默认的临时文件路径              |
|   java.compiler   |              JIT 编译器的名称               |
|      os.name      |                    Linux                    |
|      os.arch      |                    amd64                    |
|    os.version     |            3.10.0-514.el7.x86_64            |
|     user.name     |                  zookeeper                  |
|     user.home     |               /home/zookeeper               |
|     user.dir      |      /home/zookeeper/zookeeper2181/bin      |

### 6、ruok命令 

ruok:测试服务是否处于正确运行状态 。

```ini
#shell终端输入：
echo ruok| nc localhost 2181
```

### 7、 stat命令

stat:输出服务器的详细信息与srvr相似，但是多了每个连接的会话信息。

```ini
#shell终端输入：
echo stat| nc localhost 2181
```

