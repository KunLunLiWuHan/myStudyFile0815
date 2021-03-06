# 1 介绍

​	Kafka 是一个分布式的基于发布/订阅模式的消息队列（Message Queue），主要应用于 大数据实时处理领域。

**应用场景**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200714215702216.png" alt="image-20200714215702216" style="zoom:80%;" />

**使用消息队列的好处**

1. 解耦。允许你独立的扩展或修改两边的处理过程，只要确保它们遵守同样的接口约束。 
2. 可恢复性。系统的一部分组件失效时，不会影响到整个系统。消息队列降低了进程间的耦合度，所以即使一个处理消息的进程挂掉，加入队列中的消息仍然可以在系统恢复后被处理。 
3. 缓冲。有助于控制和优化数据流经过系统的速度，解决生产消息和消费消息的处理速度不一致 的情况。

4. 灵活性 & 峰值处理能力。 在访问量剧增的情况下，应用仍然需要继续发挥作用，但是这样的突发流量并不常见。 如果为以能处理这类峰值访问为标准来投入资源随时待命无疑是巨大的浪费。使用消息队列能够使关键组件顶住突发的访问压力，而不会因为突发的超负荷的请求而完全崩溃。

5. 异步通信。 很多时候，用户不想也不需要立即处理消息。消息队列提供了异步处理机制，允许用户把一个消息放入队列，但并不立即处理它。想向队列中放入多少消息就放多少，然后在需要的时候再去处理它们。

## 1.1 消息队列的两种模式

### 1、点对点模式

​		即一对一，消费者主动拉取数据，消息收到后消息清除。

​		消息生产者生产消息发送到Queue中，然后消息消费者从Queue中取出并且消费消息。 消息被消费以后，queue 中不再有存储，所以消息消费者不可能消费到已经被消费的消息。 Queue 支持存在多个消费者，但是对一个消息而言，只会有一个消费者可以消费。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200714220209216.png" alt="image-20200714220209216" style="zoom:80%;" />

### 2、发布/订阅模式

即一对多，消费者消费数据之后不会清除消息。

消息生产者（发布）将消息发布到 `topic` 中，同时有多个消息消费者（订阅）消费该消息。和点对点方式不同，发布到 topic 的消息会被所有订阅者消费。

其中，有两种模式，一种是消费者拉取模式，比较耗费时间，消费者一直维护一个常轮询，去到Topic中询问或者获取消息；一种是Topic推送模式，由于速率不匹配，可能会造成消费者崩溃或者资源的浪费。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200714215702216.png" alt="image-20200714220321149" style="zoom:80%;" />

## 1.2 Kafka基础架构

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200714220404927.png" alt="image-20200714220404927" style="zoom:80%;" />

1. Producer ：消息生产者，就是向 kafka broker 发消息的客户端(发布消息到 kafka 集群的终端或服务)。
2. Consumer ：消息消费者，向 kafka broker 取消息的客户端( kafka 集群中消费消息的终端或服务)。
3. Consumer Group （CG）：消费者组，由多个 consumer 组成。消费者组内每个消费者负责消费不同分区的数据，**一个分区只能由一个组内消费者消费**；消费者组之间互不影响。所有的消费者都属于某个消费者组，即消费者组是逻辑上的一个订阅者。
4. Broker ：一台 kafka 服务器就是一个 broker。一个集群由多个 broker 组成。一个 broker 可以容纳多个 topic。
5. Topic ：可以理解为一个队列，生产者和消费者面向的都是一个 topic（主题：对生产者的数据进行一个分类）。
6. Partition：为了实现扩展性，一个非常大的 topic 可以分布到多个 broker（即服务器）上， 一个 topic 可以分为多个 partition，每个 partition 是一个有序的队列。
7. Replica：副本，为保证集群中的某个节点发生故障时，该节点上的 partition 数据不丢失，且 kafka 仍然能够继续工作，kafka 提供了副本机制，一个 topic 的每个分区都有若干个副本， 一个 leader 和若干个 follower。
8. leader：每个分区多个副本的“主”，生产者发送数据的对象，以及消费者消费数据的对象都是 leader。
9. follower：每个分区多个副本中的“从”，实时从 leader 中同步数据，保持和 leader 数据的同步。leader 发生故障时，某个 follower 会成为新的 leader。
10. controller：kafka 集群中的其中一个服务器，用来进行 leader election 以及 各种 failover。Kafka集群中的其中一个Broker会被选举为Controller，主要负责Partition管理和副本状态管理，也会执行类似于重分配Partition之类的管理任务。如果当前的Controller失败，会从其他正常的Broker中重新选举Controller。

## 1.3 安装

网址

```http
http://kafka.apache.org/downloads
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200715170805450.png" alt="image-20200715170805450" style="zoom: 50%;" />



1. 解压缩，并将/home/zookeeper/software目录下的文件重命名。

```ini
 mv kafka_2.11-0.11.0.0/ kafka
```

2. 在/home/zookeeper/software/kafka目录下创建logs文件夹

```ini
 mkdir logs
```

3. 在/home/zookeeper/software/kafka/config中修改配置文件

```ini
 vi server.properties
```

```ini
#broker 的全局唯一编号，不能重复
broker.id=0（1，2）

#删除 topic 功能，将其使能
delete.topic.enable=true

#kafka 运行日志存放的路径
#log.dirs=/home/zookeeper/software/kafka/logs
#我们将日志和数据相分离,设置成这样即可。（有问题）
log.dirs=/home/zookeeper/software/kafka/data

#配置连接 Zookeeper 集群地址
zookeeper.connect=hadoop101:2181,hadoop102:2181,hadoop103:2181
```

4. 在root权限下，修改环境变量。

```ini
 vim /etc/profile
 
#KAFKA_HOME
export KAFKA_HOME=/home/zookeeper/software/kafka
export PATH=$PATH:$KAFKA_HOME/bin

#使配置文件生效
source /etc/profile
```

5. 分别在 hadoop102和 hadoop103 上修改配置文件server.properties。

6. 启动集群

(6.1) 依次在  hadoop101，hadoop102、hadoop103 节点上启动 kafka。注意，**我们要事先启动zookeeper**。

```ini
# 1./home/zookeeper/software/kafka目录下启动
# 2.添加这个属性（-daemon）后，控制台不输出信息。
bin/kafka-server-start.sh -daemon config/server.properties

#关闭集群（/home/zookeeper/software/kafka）
bin/kafka-server-stop.sh stop
```

(6.2) 输入下面指令后，将会有zookeeper和kafka两个进程。

```ini
jps
```

(6.3) 创建 topic

```ini
#在第一次启动kafka集群的时候，我们使用下面命令创建一个主题
bin/kafka-topics.sh --zookeeper hadoop101:2181  --create --replication-factor 3 --partitions 2 --topic first
```

```ini
#选项说明
#定义主题名
--topic 名字  

#定义副本数
--replication-factor 副本数

#定义分区数
--partitions 分区数
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200716205910918.png" alt="image-20200716205910918" style="zoom:80%;" />

​	(6.4) 查看主题

```ini
#）查看当前hadoop101服务器中的所有 topic
bin/kafka-topics.sh --zookeeper hadoop101:2181 --list
```

如果下面有输出，就说明已经创建成功了。

### 命令行操作

1. 删除topic

```ini
bin/kafka-topics.sh --zookeeper hadoop101:2181 --delete --topic first(主题名)
```

需要 server.properties 中设置 delete.topic.enable=true 否则只是标记删除。

2. 查看分区详情

```ini
bin/kafka-topics.sh --zookeeper hadoop101:2181 --describe --topic first(主题名)
```

3. 修改分区数

```ini
bin/kafka-topics.sh --zookeeper hadoop101:2181 --alter --topic first --partitions 3
```

4. 生产者发送消息

```ini
# 9092是kafka的默认端口；first是主题
bin/kafka-console-producer.sh --broker-list hadoop101:9092 --topic first
```

5. 消费者接收消息

```ini
#from-beginning标识从头开始接收消息；去掉的话，标识即可接收消息。
bin/kafka-console-consumer.sh  --bootstrap-server hadoop101:9092 --from-beginning --topic first

#老版本的bootstrap-server为zookeeper，此时我们可以通过zkCli.sh登录zookeeper，使用ls /consumers 来查看消费者情况。
bin/kafka-console-consumer.sh  --zookeeper hadoop102:2181 --topic first
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200716212222501.png" alt="image-20200716212222501" style="zoom:67%;" />

6.查看kafka的消费组的group.id

kafka/config目录下的consumer.properties中可以看到：

![image-20201117110019348](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201117110019348.png)

7、查看主题的消费情况

```shell
# 命令
root@xiaolunserver:/mykafka/kafka# bin/kafka-consumer-offset-checker.sh --zookeeper localhost:2181 --group group_id --topic topic_name

# 举例
bin/kafka-consumer-offset-checker.sh --zookeeper xiaolunserver:2181 --group test-consumer-group --topic test02
```

其中，group_id通过consumer.properties来查看得到：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201117113250160.png" alt="image-20201117113250160" style="zoom:80%;" />

第二种方式：使用下面的方式来进行查看主题的消费情况

```shell
bin/kafka-consumer-groups.sh --bootstrap-server 192.168.1.65:9092 --describe --group group01
```

# 2 Kafka 架构深入

## 2.1 工作流程

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200717082616899.png" alt="image-20200717082616899" style="zoom: 80%;" />

topic 是逻辑上的概念，而 partition 是物理上的概念，每个 partition 对应于一个 log 文 件，该 log 文件中存储的就是 producer 生产的数据。			

Producer 生产的数据会被不断追加到该 log 文件末端，且每条数据都有自己的 offset。消费者组中的每个消费者，都会实时记录自己消费到了哪个 offset，以便出错恢复时，从上次的位置继续消费。

### 2.1.1 文件存储机制

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200717084640708.png" alt="image-20200717084640708" style="zoom:80%;" />

由于生产者生产的消息会不断追加到 log 文件末尾，为防止 log 文件过大导致数据定位 效率低下，Kafka 采取了分片和索引机制，将每个 partition 分为多个 segment。

每个 segment 对应两个文件—`.index`文件和`.log`文件。这些文件位于一个文件夹下，该文件夹的命名 规则为：topic 名称+分区序号。例如，first 这个 topic 有三个分区，则其对应的文件夹为 first-0,first-1,first-2。

index 和 log 文件以当前 segment 的第一条消息的 offset 命名。下图为 index 文件和 log 文件的结构示意图。

​	<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200717084823433.png" alt="image-20200717084823433" style="zoom:80%;" />

​		`.index`文件存储大量的索引信息，`.log`文件存储大量的数据，索引文件中的元数据指向对应数据文件中 message 的物理偏移地址，系统通过偏移地址能够很快的将数据查出来。

## 2.2 Kafka生产者

### 2.2.1 分区策略

**分区的原因**

1. 方便在集群中扩展，每个 Partition 可以通过调整以适应它所在的机器，而一个 topic 又可以有多个 Partition 组成，因此整个集群就可以适应任意大小的数据了； 

2. 可以提高并发，因为可以以 Partition 为单位读写了。

**分区的原则**

​		我们需要将 producer 发送的数据封装成一个 **ProducerRecord** 对象。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200717085814380.png" alt="image-20200717085814380" style="zoom:80%;" />

发送数据分为三种情况：

1. 指明 partition 的情况下，直接将指明的值直接作为 partiton 值。
2. 没有指明 partition 值但有 key 的情况下，将 key 的 hash 值与 topic 的 partition 数进行取余得到 partition 值； 
3. 既没有 partition 值又没有 key 值的情况下，第一次调用时随机生成一个整数（后 面每次调用在这个整数上自增），将这个值与 topic 可用的 partition 总数取余得到 partition 值，也就是常说的 round-robin 算法。

### 2.2.2 数据可靠性保证

为保证 producer 发送的数据，能可靠的发送到指定的 topic，topic 的每个 partition 收到 producer 发送的数据后，都需要向 producer 发送 ack（acknowledgement 确认收到），如果 producer 收到 ack，就会进行下一轮的发送，否则重新发送数据。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200717090642397.png" alt="image-20200717090642397" style="zoom:80%;" />

**副本数据同步策略**

| 方案                             | 优点                                                       | 缺点                                                        |
| :------------------------------- | :--------------------------------------------------------- | :---------------------------------------------------------- |
| 半数以上完成同步，topic就发送ack | 延迟低                                                     | 选举新的 leader 时，容忍 n 台 节点的故障，需要 2n+1 个副 本 |
| 全部完成同步，topic就发送ack     | 选举新的 leader 时，容忍 n 台 节点的故障，需要 n+1 个副 本 | 延迟高                                                      |

Kafka 选择了第二种方案，原因如下： 

1. 同样为了容忍 n 台节点的故障，第一种方案（假设集群中有2n+1台机器，选举leader的时候至少需要半数以上即n+1台机器投票，那么能容忍的故障，最多就是n台机器发生故障）需要 2n+1 个副本，而第二种方案（假设集群中有n+1台机器，选举leader的时候只要有一个副本就可以了）只需要 n+1 个副本，而 Kafka 的每个分区都有大量的数据，第一种方案会造成大量数据的冗余。 

2. 虽然第二种方案的网络延迟会比较高，但网络延迟对 Kafka 的影响较小。

#### 1、ISR

​		采用第二种方案之后，设想以下情景：leader 收到数据，所有 follower 都开始同步数据， 但有一个 follower，因为某种故障，迟迟不能与 leader 进行同步，那 leader 就要一直等下去， 直到它完成同步，才能发送 ack。这个问题需要解决。

​		Leader 维护了一个动态的 同步副本in-sync replica set (ISR)，意为和 leader 保持同步的 follower 集 合。当 ISR 中的 follower 完成数据的同步之后，leader 就会给 follower 发送 ack。如果 follower 长时间 未 向 leader 同 步 数 据 ， 则 该 follower 将 被 踢 出 ISR ， 该 时 间 阈 值 由`replica.lag.time.max.ms` 参数设定。Leader 发生故障之后，就会从 ISR 中选举新的 leader。

#### 2、ack 应答机制

​		主要解决生产数据的不丢失的问题。

​		对于某些不太重要的数据，对数据的可靠性要求不是很高，能够容忍数据的少量丢失， 所以没必要等 ISR 中的 follower 全部接收成功。 

​		所以 Kafka 为用户提供了三种可靠性级别，用户根据对可靠性和延迟的要求进行权衡， 选择以下的acks 参数的配置：

​		0：producer 不等待 broker 的 ack（一直发送数据），这一操作提供了一个最低的延迟，broker 一接收到还没有写入磁盘就已经返回，当 broker 故障时有可能丢失数据。

​		1：producer 等待 broker 的 ack，partition 的 leader 落盘成功后返回 ack，如果在 follower 同步成功之前 leader 故障，那么将会丢失数据。

​		-1（all）：producer 等待 broker 的 ack，partition 的 leader 和 follower （ISR中的）全部落盘成功后才 返回 ack。但是如果在 follower 同步完成后，broker 发送 ack 之前，leader 发生故障，那么会 造成数据重复。此时对于ack=-1这种情况下，也会丢失数据，情况为ISR中无foller，且learder挂掉。

数据重复的原因：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200717094229774.png" alt="image-20200717094229774" style="zoom:80%;" />

#### 3、故障处理细节

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200717095531884.png" alt="image-20200717095531884" style="zoom:80%;" />

`HW`主要解决的是消费者消费数据的一致性。

LEO：指的是每个副本最大的 offset。

HW：**指的是消费者能见到的最大的 offset**，ISR 队列中最小的 LEO。

HW和LEO存在于`.log`文件中。

1. **follower 故障** 

follower 发生故障后会被临时踢出 ISR，待该 follower 恢复后，follower 会读取本地磁盘记录的上次的 HW，并将 log 文件高于 HW 的部分截取掉，从 HW 开始向 leader 进行同步。 等该 follower 的 LEO 大于等于该 Partition 的 HW，即 follower 追上 leader 之后，就可以重新加入 ISR 了。

2. **leader 故障**

leader 发生故障之后，会从 ISR 中选出一个新的 leader，之后，为保证多个副本之间的数据一致性，其余的 follower 会先将各自的 log 文件高于 HW 的部分截掉，然后从新的 leader 同步数据。

**注意**：这只能保证副本之间的数据一致性，并不能保证数据不丢失或者不重复。

#### 4、Exactly Once 语义

将服务器的 ACK 级别设置为-1，可以保证 Producer 到 Server 之间不会丢失数据，即 **At Least Once** 语义。相对的，将服务器 ACK 级别设置为 0，可以保证生产者每条消息只会被 发送一次，即 **At Most Once** 语义。 

At Least Once 可以保证数据不丢失，但是不能保证数据不重复；相对的，At Least Once 可以保证数据不重复，但是不能保证数据不丢失。但是，对于一些非常重要的信息，比如说交易数据，下游数据消费者要求数据既不重复也不丢失，即 **Exactly Once** 语义。

在 0.11 版 本以前的 Kafka，对此是无能为力的，只能保证数据不丢失，再在下游消费者对数据做全局 去重。对于多个下游应用的情况，每个都需要单独做全局去重，这就对性能造成了很大影响。 

0.11 版本的 Kafka，引入了一项重大特性：**幂等性**。所谓的幂等性就是指 Producer 不论 向 Server 发送多少次重复数据，Server 端都只会持久化一条。幂等性结合 At Least Once 语 义，就构成了 Kafka 的 Exactly Once 语义。即： 

```ini
At Least Once + 幂等性 = Exactly Once 
```

要启用幂等性，只需要将 Producer 的参数中 **enable.idompotence** 设置为 **true** 即可(此时ack参数等于-1)。Kafka 的幂等性实现其实就是将原来下游需要做的**去重**放在了数据上游。

开启幂等性的 Producer 在 初始化的时候会被分配一个 PID（produce ID），发往同一 Partition 的消息会附带 Sequence Number。而 Broker 端会对做<PID,Partition ,SeqNumber>缓存，当具有相同主键的消息提交时，Broker 只 会持久化一条。 

但是 PID 重启就会变化，同时不同的 Partition 也具有不同主键，所以幂等性无法保证**跨分区跨会话**的 Exactly Once。

## 2.3 Kafka 消费者

### 2.3.1 消费方式

consumer 采用 pull（拉）模式从 broker 中读取数据。		  

push（推）模式很难适应消费速率不同的消费者，因为消息发送速率是由 broker决定的。 它的目标是尽可能以最快速度传递消息，但是这样很容易造成 consumer 来不及处理消息，典型的表现就是拒绝服务以及网络拥塞。而 pull 模式则可以根据 consumer 的消费能力以适当的速率消费消息。

pull 模式不足之处是，如果 kafka 没有数据，消费者可能会陷入循环中，一直返回空数据。针对这一点，Kafka 的消费者在消费数据时会传入一个时长参数 `timeout`，如果当前没有 数据可供消费，consumer 会等待一段时间之后再返回，这段时长即为 timeout。

### 2.3.2 分区分配策略

一个 consumer group 中有多个 consumer，一个 topic 有多个 partition，所以必然会涉及 到 partition 的分配问题，即确定那个 partition 由哪个 consumer 来消费。 

Kafka 有两种分配策略，一是 RoundRobin，一是 Range。

#### 1、RoundRobin方式

情况1：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200717102917405.png" alt="image-20200717102917405" style="zoom: 67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200717103102250.png" alt="image-20200717103102250" style="zoom:67%;" />

情况2：有可能会出现多个主题的情况：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200717103303974.png" alt="image-20200717103303974" style="zoom: 50%;" />

#### 2、Range方式 

Kafka中默认采取这种方式，根据主题topic来发送，优先看是谁订阅了主题，与消费者的组关系不大。

情况1：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200717103610979.png" alt="image-20200717103610979" style="zoom: 67%;" />

情况2：有可能会出现多个主题的情况：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200717103918706.png" alt="image-20200717103918706" style="zoom: 50%;" />

​		当消费者重新加进来之后，分配策略会重新分配。

情况3：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200717153652569.png" alt="image-20200717153652569" style="zoom: 50%;" />

A，B是一个消费者组1，C单独是要给消费者组2，主题T1发给A，B，C,假如采用轮询方式的话，组1会将T1和T2看成一个整体，然后将数据轮询发给组1，此时A会收到T2的数据，不符合；需要采用Range方式发送数据。

此时T2根据主题topic来发送，优先看是谁订阅了主题，然后再看组，与消费者的组的关系不大，对于T1会采用T1（0，1）给A，T2（2）给B，同时T1全部分区的数据都要给C。

### 2.3.3 offset 的维护

​		在上面的分配策略确定之后，就该进行下面的消费过程了。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200717111612567.png" alt="image-20200717111612567" style="zoom:80%;" />

由于 consumer 在消费过程中可能会出现断电宕机等故障，consumer 恢复后，需要从故障前的位置的继续消费，所以 consumer 需要实时记录自己消费到了哪个 offset，以便故障恢复后继续消费。

Kafka 0.9 版本之前，consumer 默认将 offset 保存在 Zookeeper 中，从 0.9 版本开始， consumer 默认将 offset 保存在 Kafka 一个内置的 topic 中，该 topic 为__consumer_offsets。

**查看**

使用zookeeper的方式接收消息，然后使用zkCli.sh登录zookeeper进行查看。

```ini
bin/kafka-console-consumer.sh  --zookeeper hadoop102:2181 --topic second
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200717144729873.png" alt="image-20200717144729873" style="zoom:67%;" />



<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200717145051919.png" alt="image-20200717145051919" style="zoom:80%;" />

其中，**/brokers/ids**在Kafka集群中，每个broker都有一个唯一的id值用来区分彼此。Kafka在启动时会在zookeeper中/brokers/ids路径下创建一个与当前broker的id为名称的虚节点，Kafka的健康状态检查就依赖于此节点。进入创建一个集群。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200717145240090.png" alt="image-20200717145240090" style="zoom:80%;" />

也就是使用一个组保存offsets，使用group（组）+topic（主题） + partition(分区)来保存一个数据内容。

### 2.3.4 消费者组案例

测试同一个消费者组中的消费者，同一时刻只能有一个消费者消费。

（1）在 hadoop102、hadoop103 上修改/kafka/config/consumer.properties 配置 文件中的 group.id 属性为任意组名。	

```ini
#指令
vi consumer.properties

#修改组名（原本:test-consumer-group），保证hadoop102、hadoop103在一个组，不然是任意的。
group.id=xiaolun
```

（2）在 hadoop102、hadoop103 上分别启动消费者

```ini
#注意：指定对应的消费者配置文件
bin/kafka-console-consumer.sh  --bootstrap-server hadoop102:9092 --topic second --consumer.config config/consumer.properties
```

（3）在 hadoop101 上启动生产者

```ini
bin/kafka-console-producer.sh --broker-list hadoop101:9092 --topic second
```

（4）查看 hadoop102 和 hadoop103 的接收者。

可以发现：同一时刻只有一个消费者接收到消息（轮询），但是不是同一个组的话，可以同时和这个组接收消息。

## 2.4 Kafka 高效读写数据

1. **顺序写磁盘**

Kafka 的 producer 生产数据，要写入到 log 文件中，写的过程是一直追加到文件末端， 为顺序写。官网有数据表明，同样的磁盘，顺序写能到 600M/s，而随机写只有 100K/s。这 与磁盘的机械机构有关，顺序写之所以快，是因为其省去了大量磁头寻址的时间。

2. **零复制技术**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200717153005259.png" alt="image-20200717153005259" style="zoom:80%;" />

## 2.5 Zookeeper 在 Kafka 中的作用

Kafka 集群中有一个 broker 会被选举为 Controller，负责管理集群 broker 的上下线，所 有 topic 的分区副本分配和 leader 选举等工作。 Controller 的管理工作都是依赖于 Zookeeper 的。

## 2.6 事务

Kafka 从 0.11 版本开始引入了事务支持。事务可以保证 Kafka 在 Exactly Once 语义的基础上，生产和消费可以跨分区和会话，要么全部成功，要么全部失败。

### 1、Producer 事务

为了实现跨分区跨会话的事务，需要引入一个全局唯一的 `Transaction ID`（客户端给的），并将 Producer 获得的PID 和Transaction ID 绑定。这样当Producer 重启后就可以通过正在进行的 Transaction ID 获得原来的 PID。 

为了管理 Transaction，Kafka 引入了一个新的组件 Transaction Coordinator（事务协调器）。Producer 就是通过和 Transaction Coordinator 交互获得 Transaction ID 对应的任务状态。Transaction Coordinator 还负责将事务所有写入 Kafka 的一个内部 Topic，这样即使整个服务重启，由于事务状态得到保存，进行中的事务状态可以得到恢复，从而继续进行。

### 2、Consumer 事务

上述事务机制主要是从 Producer 方面考虑，对于 Consumer 而言，事务的保证就会相对较弱(主要解决Exactly Once问题，这方面内容很少提)，尤其时无法保证 Commit 的信息被精确消费。这是由于 Consumer 可以通过 offset访 问任意信息，而且不同的 Segment File 生命周期不同，同一事务的消息可能会出现重启后被删除的情况。

# 3 Kafka API

## 3.1Producer API

### 3.1.1 消息发送流程

Kafka 的 Producer 发送消息采用的是**异步发送**的方式。在消息发送的过程中，涉及到了 两个线程—`main` 线程和 `Sender` 线程，以及一个线程共享变量—RecordAccumulator。 main 线程将消息发送给 RecordAccumulator，Sender 线程不断从 RecordAccumulator 中拉取消息发送到 Kafka broker。

​	KafkaProducer 发送消息流程如下：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200717155817626.png" alt="image-20200717155817626" style="zoom:80%;" />

​	**相关参数**： 

1. batch.size：只有数据积累到 batch.size 之后，sender 才会发送数据。通过这个参数来设置批量提交的数据大小，默认是16k,当积压的消息达到这个值的时候就会统一发送（发往**同一分区**的消息）。
2. linger.ms：如果数据迟迟未达到 batch.size，sender 等待 linger.time 之后就会发送数据。

注意：当这两个参数同时设置的时候，只要两个条件中满足一个就会发送。

## 3.2 生产者

**1、需要用到的类**：

+ KafkaProducer：需要创建一个生产者对象，用来发送数据。

+ ProducerConfig：获取所需的一系列配置参数 。

+ ProducerRecord：每条数据都要封装成一个 ProducerRecord 对象。

**2、不带回调函数的 API**

1. **pom文件**

```xml
 <!--01、kafka客户端依赖，和集群保持一致-->
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka-clients</artifactId>
    <version>0.11.0.0</version>
</dependency>
```

2. 测试

```java
public class MyProducer {
    public static void main(String[] args) {
        //创建kafka生产者信息
        Properties props = new Properties();

        /**
         *  1.指定连接的kafka 集群，broker-list
         *  2.我们需要记住ProducerConfig，也等同下面的配置
         */
//        props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "hadoop101:9092");
        props.put("bootstrap.servers", "hadoop101:9092");

        //2.应答级别
        props.put("acks", "all");

        //3.重试次数
        props.put("retries", 3);

        //4.批次大小（一次发送多少的数据）,单位字节（现为16K）
        props.put("batch.size", 16384);

        //5.等待时间，现为1ms，和批次大小相配合来发送数据
        props.put("linger.ms", 1);

        //6.RecordAccumulator 缓冲区大小，现为32M
        props.put("buffer.memory", 33554432);

        //7.key,value序列化类
        props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");

        //8.创建生产者对象
        KafkaProducer<String, String> producer = new KafkaProducer<String, String>(props);

        //9.发送数据
        for (int i = 0; i < 10; i++) {
            producer.send(new ProducerRecord<String, String>("second","xiaolun-->" + i));
        }
        //10.关闭资源
        producer.close();
    }
```

我们需要在centos中启动kafka集群，并创建一个消费者，当上面程序运行的时候，在消费者端接收数据为：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200717171106332.png" alt="image-20200717171106332" style="zoom:80%;" />

回调函数会在 producer 收到 ack 时调用，为异步调用，该方法有两个参数，分别是 RecordMetadata 和 Exception，如果 Exception 为 null，说明消息发送成功，如果 Exception 不为 null，说明消息发送失败。 

注意：消息发送失败会自动重试，不需要我们在回调函数中手动重试。

3、**带回调函数的 API**

```java
 for (int i = 0; i < 10; i++) {
            producer.send(new ProducerRecord<String, String>("second", "xiaolun:second-->" + i), new Callback() {
                public void onCompletion(RecordMetadata recordMetadata, Exception e) {
                    //回调函数，该方法会在 Producer 收到 ack 时调用，为异步调用
                    if (e == null) {
                        System.out.println("success:offset---->" + recordMetadata.offset());
                        System.out.println("success:partition" + recordMetadata.partition());
                    } else {
                        e.printStackTrace();
                    }
                }
            });
        }
```

​		控制台输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200717172511887.png" alt="image-20200717172511887" style="zoom:67%;" />

4、**同步发送 API**

同步发送的意思就是，一条消息发送之后，会阻塞当前线程，直至返回 ack（不是上面的acks参数，它的含义是告诉main线程，send线程已经做完了，你可以苏醒了）。 

由于 send 方法返回的是一个 Future 对象，根据 Futrue 对象的特点，我们也可以实现同步发送的效果（将main线程阻塞即可），只需在调用 Future 对象（和线程相关的类）的 get 发送即可。使用的比较少。

```java
// 添加get方法 
for (int i = 0; i < 10; i++) {
            producer.send(new ProducerRecord<String, String>("second","xiaolun-->" + i)).get();
}
```

## 3.3 消费者

consumer 消费数据时的可靠性是很容易保证的，因为数据在 Kafka 中是持久化的，故不用担心数据丢失问题。 

由于 consumer 在消费过程中可能会出现断电宕机等故障，consumer 恢复后，需要从故障前的位置的继续消费，所以 consumer 需要实时记录自己消费到了哪个 offset，以便故障恢复后继续消费。 

因此， offset 的维护是 Consumer 消费数据是必须考虑的问题。

**需要用到的类**： 

1. KafkaConsumer：需要创建一个消费者对象，用来消费数据。
2. ConsumerConfig：获取所需的一系列配置参数 。
3. ConsuemrRecord：每条数据都要封装成一个 ConsumerRecord 对象。

为了使我们能够专注于自己的业务逻辑，Kafka 提供了自动提交 offset 的功能。 自动提交 offset 的相关参数：

+ enable.auto.commit：是否开启自动提交 offset 功能。
+ auto.commit.interval.ms：自动提交 offset 的时间间隔。

### 1、自动提交offset

```java
public class MyConsumer {
    public static void main(String[] args) {
        //创建kafka消费者信息
        Properties props = new Properties();

        //1.连接的集群
        props.put("bootstrap.servers", "hadoop101:9092");

        //2.开启自动提交 offset 功能
        props.put("enable.auto.commit", "true");

        //3.自动提交 offset 的时间间隔,现为1秒
        props.put("auto.commit.interval.ms", "1000");

        //4.key,value的反序列化
        props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");

        /**
         * 5.创建消费者组
         *  消费者组，只要group.id相同，就属于同一个消费者组
         *  唯一的指明了consumer的group的名字，group名一样的进程属于同一个consumer group。
         */
        props.put(ConsumerConfig.GROUP_ID_CONFIG, "bigdata");

        //6.创建消费者
        KafkaConsumer<String, String> consumer = new KafkaConsumer<String, String>(props);

        //7.订阅主题
        consumer.subscribe(Arrays.asList("second"));

        while (true){
            //8.获取数据
            ConsumerRecords<String, String> consumerRecords = consumer.poll(100);
            //9.解析并打印consumerRecords
            for (ConsumerRecord<String, String> consumerRecord : consumerRecords) {
                System.out.println("consumerRecord.key()----->"+consumerRecord.key());
                System.out.println("consumerRecord.value()---->"+consumerRecord.value());
            }
        }
    }
}
```

当生产者编写的`MyProducer`类运行的时（即输入数据的时候），该

`MyConsumer`能够接收到来自生产者的数据。

消费者Consumer导入的依赖很生产者Producer一样。

对于参数auto.offset.reset的理解：主要是针对当Kafka中没有初始偏移量（消费者组第一次消费，或者新换一个新组名（此时会初始偏移量会失效））或服务器上不再存在当前偏移量时（消费者的offset，在7天后删除的操作）的操作。

```java
props.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG,"earliest ");
```

earliest :当各分区下有已提交的offset时，从提交的offset开始消费；无提交的offset时，从头开始消费。
​		latest :当各分区下有已提交的offset时，从提交的offset开始消费；无提交的offset时，消费新产生的该分区下的数据。

```
props.put("enable.auto.commit", "false");
```

当我们关闭自动提交时，消费者启动的时候只会在存放到磁盘的文件中读取一次offset，然后的话，消费者在内存中维护一个offset(因为每次访问文件中的offset速度比较慢)。

假设生产者每次发送10条数据，消费者每次消费10条数据，现在offset为90，下一次操作会消费91-100之间的数据，只是消费完成后，并没有把offset等于100这个值写入外部文件offset中，消费者会在一定的时间快速地拉取数据，但是控制台显示的界面在拉取数据后显示一直为空白，假如拉取的是外部文件中的offset（offset没有提交，此时offset仍然为90），那么控制台会一直显示91-100之间的数据。这说明，消费者获取的offset为内存中的offset。

总之，当我们关闭自动提交，外部文件中的offset数据一直为一个固定值，没有增加，每次消费者启动时都是从（offset+1）开始拿数据。

### 2、手动提交 offset

虽然自动提交 offset 十分简介便利，但由于其是基于时间提交的，开发人员难以把握 offset 提交的时机(生产者提交100条数据，消费者没有处理完，挂掉了，下次从101处开始读取，导致数据丢失)。因此 Kafka 还提供了手动提交 offset 的 API。 		

手动提交 offset 的方法有两种：分别是 commitSync（同步提交）和 commitAsync（异步提交）。

两者的相同点是，都会将**本次 poll 的一批数据最高的偏移量提交**；不同点是， commitSync 阻塞当前线程，一直到提交成功，并且会自动失败重试（由不可控因素导致， 也会出现提交失败）；而 commitAsync 则没有失败重试机制，故有可能提交失败。

#### 2.1、同步提交

**编码步骤**：

​	1. 关闭自动提交

```java
props.put("enable.auto.commit", "false");
```

2. 同步提交

```java
consumer.commitSync();
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200717223717180.png" alt="image-20200717223717180" style="zoom:80%;" />

当前线程会阻塞直到 offset 提交成功，然后才会从生产者中拉取新的数据。

#### 2.2、异步提交 offset

虽然同步提交 offset 更可靠一些，但是由于其会阻塞当前线程，直到提交成功。因此吞 吐量会收到很大的影响。因此更多的情况下，会选用异步提交 offset 的方式。

**编码步骤**：

1. 关闭自动提交

```java
props.put("enable.auto.commit", "false");
```

2. 执行异步提交操作

```java
while (true){
            ConsumerRecords<String, String> consumerRecords = consumer.poll(10);
            for (ConsumerRecord<String, String> consumerRecord : consumerRecords) {
                System.out.println("consumerRecord.key()----->"+consumerRecord.key());
            }

            //异步提交
            consumer.commitAsync(new OffsetCommitCallback() {
                public void onComplete(Map<TopicPartition, OffsetAndMetadata> offsets, Exception exception) {
                    //打印反馈的结果
                    if (exception != null) {
                        System.err.println("Commit failed for" + offsets);
                    }
                }
            });
        }
```

#### 2.3 、数据漏消费和重复消费分析

无论是同步提交还是异步提交 offset，都有可能会造成数据的漏消费或者重复消费。先提交 offset 后消费，有可能造成数据的漏消费；而先消费后提交 offset，有可能会造成数据的重复消费。

#### 2.4 、自定义存储 offset

​		Kafka 0.9 版本之前，offset 存储在 zookeeper，0.9 版本及之后，默认将 offset 存储在 Kafka 的一个内置的 topic 中。除此之外，Kafka 还可以选择自定义存储 offset。

​		offset 的维护是相当繁琐的，因为需要考虑到消费者的 Rebalance。

​		当有新的消费者加入消费者组、已有的消费者退出消费者组或者所订阅的主题的分区发生变化，就会触发到分区的重新分配，重新分配的过程叫做 **Rebalance**。

​		消费者发生 Rebalance 之后，每个消费者消费的分区就会发生变化。因此消费者要首先取到自己被重新分配到的分区，并且定位到每个分区最近提交的 offset 位置继续消费。 要实现自定义存储 offset，需要借助 **ConsumerRebalanceListener**，来监听消费者的分区发生变化。

​		以下为示例代码，其中提交和获取 offset 的方法，需要根据所选的 offset 存储系统自行实现。

​		我看来有点困难。

### 3、自定义拦截器

#### 1、拦截器原理

​		Producer 拦截器(interceptor)是在 Kafka 0.10 版本被引入的，主要用于实现 clients 端的定 制化控制逻辑。

​		对于 producer 而言，interceptor 使得用户在消息发送前以及 producer 回调逻辑前有机会 对消息做一些定制化需求，比如修改消息等。同时，producer 允许用户指定多个 interceptor 按序作用于同一条消息从而形成一个拦截链(interceptor chain)。

​		Intercetpor 的实现接口是 `org.apache.kafka.clients.producer.ProducerInterceptor`，其定义的方法包括：

（1）configure(configs)：获取配置信息和初始化数据时调用。

（2）onSend(ProducerRecord)：该方法封装进 KafkaProducer.send 方法中，即它运行在用户主线程中。Producer 确保在消息被序列化以及计算分区前调用该方法。用户可以在该方法中对消息做任何操作，但最好 保证不要修改消息所属的 topic 和分区，否则会影响目标分区的计算。

（3）onAcknowledgement(RecordMetadata, Exception)：该方法会在消息从 RecordAccumulator 成功发送到 Kafka Broker 之后，或者在发送过程 中失败时调用。并且通常都是在 producer 回调逻辑触发之前。onAcknowledgement 运行在 producer 的 IO 线程中，因此不要在该方法中放入很重的逻辑，否则会拖慢 producer 的消息 发送效率。

（4）close：关闭 interceptor，主要用于执行一些资源清理工作。

​		如前所述，interceptor 可能被运行在多个线程中，因此在具体实现时用户需要自行确保 线程安全。另外倘若指定了多个 interceptor，则 producer 将按照指定顺序调用它们，并仅仅 是捕获每个 interceptor 可能抛出的异常记录到错误日志中而非在向上传递。这在使用过程中 要特别留意。

## 2、案例

**需求**： 

​		实现一个简单的双 interceptor 组成的拦截链。第一个 interceptor 会在消息发送前将时间 戳信息加到消息 value 的最前部；第二个 interceptor 会在消息发送后更新成功发送消息数或 失败发送消息数。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200718083054227.png" alt="image-20200718083054227" style="zoom:80%;" />

1. **增加时间戳拦截器**

```java
//时间戳拦截器
public class TimeInterceptor implements ProducerInterceptor<String,String> {
    public ProducerRecord<String, String> onSend(ProducerRecord<String, String> record) {
        // 创建一个新的 record，把时间戳写入消息体的最前部
        return new ProducerRecord(record.topic(), record.partition(), record.timestamp(),
                record.key(), System.currentTimeMillis() + "," + record.value().toString());
    }

    public void onAcknowledgement(RecordMetadata metadata, Exception exception) {
    }

    public void close() {
    }

    public void configure(Map<String, ?> configs) {
    }
}
```

2. 统计发送消息成功和发送失败消息数，并在 producer 关闭时打印这两个计数器

```java
public class CountInterceptor implements ProducerInterceptor<String, String> {
    private int errorCounter = 0;
    private int successCounter = 0;

    public ProducerRecord<String, String> onSend(ProducerRecord<String, String> record) {
        return record;
    }

    public void onAcknowledgement(RecordMetadata metadata, Exception exception) {
        // 统计成功和失败的次数
        if (exception == null) {
            successCounter++;
        } else {
            errorCounter++;
        }
    }

    public void close() {
        // 保存结果
        System.out.println("Successful sent: " + successCounter);
        System.out.println("Failed sent: " + errorCounter);
    }

    public void configure(Map<String, ?> configs) {
    }
}
```

3. 测试

```java
public class InterceptorProducerMain {
    public static void main(String[] args) {
        // 1.设置配置信息
        Properties props = new Properties();
        props.put("bootstrap.servers", "hadoop101:9092");
        props.put("acks", "all");
        props.put("retries", 3);
        props.put("batch.size", 16384);
        props.put("linger.ms", 1);
        props.put("buffer.memory", 33554432);
        props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");

        // 2.构建拦截链(里面放置全类名)
        List<String> interceptors = new ArrayList<String>();
        interceptors.add("com.xiaolun.interceptor.TimeInterceptor");
        interceptors.add("com.xiaolun.interceptor.CountInterceptor");

        props.put(ProducerConfig.INTERCEPTOR_CLASSES_CONFIG, interceptors);
        String topic = "second";
        Producer<String, String> producer = new KafkaProducer<String, String>(props);
        // 3.发送消息
        for (int i = 0; i < 10; i++) {
            ProducerRecord<String, String> record = new ProducerRecord<String, String>(topic, "message" + i);
            producer.send(record);
        }

        // 4.一定要关闭 producer，这样才会调用 interceptor 的 close 方法
        producer.close();
    }
}
```

​		在centos中开启kafka集群的消费者，然后启动测试类。集群获取的值为：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200718090654937.png" alt="image-20200718090654937" style="zoom:80%;" />

​		控制台输出：

```ini
Successful sent: 10
Failed sent: 0
```

