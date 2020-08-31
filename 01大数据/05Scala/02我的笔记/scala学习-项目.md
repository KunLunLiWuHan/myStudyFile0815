# 1 CRM项目

具体参考韩顺平_Scala语言核心编程。

1、项目开发流程

![image-20200803111333080](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831133904.png)

2、需求分析

​		模拟实现基于文本界面的《客户信息管理软件》，该软件scala能够实现对客户对象的插入、修改和删除,显示，查询（用ArrayBuffer或者ListBuffer实现），并能够打印客户明细表。

# 2 AKKA

## 1、介绍

（1）Akka是JAVA虚拟机JVM平台上构建高并发、分布式和容错应用的工具包和运行时，你可以理解成Akka是编写并发程序的框架。

（2）Akka用Scala语言写成，同时提供了Scala和Java的开发接口。

（3）Akka主要解决的问题是：可以轻松的写出高效稳定的并发程序，程序员不再过多的考虑线程、锁和资源竞争等细节。

1、Akka中的Actor模型

Actor 模型用于解决下面的问题：

（1）处理并发问题关键是要保证共享数据的一致性和正确性，因为程序是多线程时，多个线程对同一个数据进行修改，若不加同步条件，势必会造成数据污染。但是当我们对关键代码加入同步条件synchronized 后，实际上大并发就会阻塞在这段代码，对程序效率有很大影响。

（2）若是用单线程处理，不会有数据一致性的问题，但是系统的性能又不能保证。

（3）Actor 模型的出现解决了这个问题，简化并发编程，提升程序性能。 你可以这里理解：Actor 模型是一种处理并发问题的解决方案，很牛!

## 2、Actor

### 1、Actor模型

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831133905.png" alt="image-20200804153829472"  />

1、Actor模型及其说明

（1）Akka 处理并发的方法基于 Actor 模型。

（2）在基于 Actor 的系统里，所有的事物都是 Actor，就好像在面向对象设计里面所有的事物都是对象一样。

（3）Actor 模型是作为一个并发模型设计和架构的。Actor 与 Actor 之间只能通过消息通信，如图的信封。

（4）Actor 与 Actor 之间只能用消息进行通信，当一个 Actor 给另外一个 Actor发消息，消息是有顺序的(消息队列)，只需要将消息投寄的相应的邮箱即可。

（5）怎么处理消息是由接收消息的Actor决定的，发送消息Actor可以等待回复，也可以异步处理（类似ajax）。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831133906.png" alt="image-20200804155235911" style="zoom:67%;" />

（6）ActorSystem 的职责是负责创建并管理其创建的 Actor， ActorSystem 是单例的(可以ActorSystem是一个工厂，专门创建Actor)，一个 JVM 进程中有一个即可，而 Acotr 是可以有多个的。

（7）Actor模型是对并发模型进行了更高的抽象。

（8）Actor模型是异步、非阻塞、高性能的事件驱动编程模型。

（9）Actor模型是轻量级事件处理（1GB 内存可容纳百万级别个 Actor），因此处理大并发性能高。

### 2、Actor模型工作机制

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831133907.png" alt="image-20200804155630562" style="zoom:80%;" />

（1）A Actor 如果想给自己发消息，就通过A ActorRef。

（2）A Actor 想给B Actor 发消息，就需要有(**持有**)B ActorRef , 通过B ActorRef 发消息。

```ini
B Actor 中
receive 方法 {
 //1.消息接收和处理
 //2. 通过sender() 方法可以得到发送消息的Actor的ActorRef, 通过这个ActorRef  ，B Actor 也可以回复消息
}
```

1、说明：

（1）ActorySystem创建Actor。

（2）ActorRef:可以理解成是Actor的**代理或者引用**。消息是通过ActorRef来发送,而不能通过Actor 发送消息，通过哪个ActorRef 发消息，就表示把该消息发给哪个Actor。

（3）消息发送到Dispatcher Message (消息分发器)，它得到消息后，会将消息进行分发到对应的MailBox。(注: Dispatcher Message 可以理解成是一个线程池, MailBox 可以理解成是消息队列，可以缓冲多个消息，遵守FIFO)

（4）Actor 可以通过 receive方法来获取消息，然后进行处理。

2、Actor间传递消息机制

（1）每一个消息就是一个Message对象。Message 继承了Runable， 因为Message就是线程类。

（2）从Actor模型工作机制看上去很麻烦，但是程序员编程时只需要编写Actor就可以了，其它的交给Actor模型完成即可。

（3）A Actor要给B Actor 发送消息，那么A Actor 要先拿到(也称为持有) B Actor 的 代理对象ActorRef 才能发送消息 

## 3、快速入门

1、需求

​		编写一个Actor, 名字叫SayHelloActor，然后使用SayHelloActor 可以给自己发送消息。

2、Actor通讯机制

![image-20200804165940878](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831133908.png)

3、创建项目

（1）导入依赖

```xml
<!-- 定义一下常量 -->
<properties>
    <encoding>UTF-8</encoding>
    <scala.version>2.11.8</scala.version>
    <scala.compat.version>2.11</scala.compat.version>
    <akka.version>2.4.17</akka.version>
</properties>

<dependencies>
    <!-- 添加scala的依赖 -->
    <dependency>
        <groupId>org.scala-lang</groupId>
        <artifactId>scala-library</artifactId>
        <version>${scala.version}</version>
    </dependency>

    <!-- 添加akka的actor依赖 -->
    <dependency>
        <groupId>com.typesafe.akka</groupId>
        <artifactId>akka-actor_${scala.compat.version}</artifactId>
        <version>${akka.version}</version>
    </dependency>

    <!-- 多进程之间的Actor通信 -->
    <dependency>
        <groupId>com.typesafe.akka</groupId>
        <artifactId>akka-remote_${scala.compat.version}</artifactId>
        <version>${akka.version}</version>
    </dependency>
</dependencies>

<!-- 指定插件-->
<build>
    <!-- 指定源码包和测试包的位置 -->
    <sourceDirectory>src/main/scala</sourceDirectory>
    <testSourceDirectory>src/test/scala</testSourceDirectory>
    <plugins>
        <!-- 指定编译scala的插件 -->
        <plugin>
            <groupId>net.alchim31.maven</groupId>
            <artifactId>scala-maven-plugin</artifactId>
            <version>3.2.2</version>
            <executions>
                <execution>
                    <goals>
                        <goal>compile</goal>
                        <goal>testCompile</goal>
                    </goals>
                    <configuration>
                        <args>
                            <arg>-dependencyfile</arg>
                            <arg>${project.build.directory}/.scala_dependencies</arg>
                        </args>
                    </configuration>
                </execution>
            </executions>
        </plugin>

        <!-- maven打包的插件 -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>2.4.3</version>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                    <configuration>
                        <filters>
                            <filter>
                                <artifact>*:*</artifact>
                                <excludes>
                                    <exclude>META-INF/*.SF</exclude>
                                    <exclude>META-INF/*.DSA</exclude>
                                    <exclude>META-INF/*.RSA</exclude>
                                </excludes>
                            </filter>
                        </filters>
                        <transformers>
                            <transformer implementation="org.apache.maven.plugins.shade.resource.AppendingTransformer">
                                <resource>reference.conf</resource>
                            </transformer>
                            <!-- 指定main方法 -->
                            <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                <mainClass>xxx</mainClass>
                            </transformer>
                        </transformers>
                    </configuration>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

（2）测试

```scala
import akka.actor.{Actor, ActorRef, ActorSystem, Props}

class SayHelloActor extends Actor {
  //说明
  //1. receive方法，会被该Actor的MailBox(实现了Runnable接口)调用
  //2. 当该Actor的MailBox 接收到消息,就会调用 receive
  //3. type Receive = PartialFunction[Any, Unit] （偏函数）
  override def receive: Receive = {
    case "hello" => println("收到hello, 回应hello too:)")
    case "ok" => println("收到ok, 回应ok too:)")
    case "exit" => {
      println("接收到exit指令，退出系统")
      context.stop(self) //停止actorref(self:本身)
      context.system.terminate() //退出actorsystem
    }
    case _ => println("匹配不到")
  }
}

object SayHelloActorDemo {

  //1. 先创建一个ActorSystem, 专门用于创建Actor
  private val actoryFactory = ActorSystem("actoryFactory")
  //2. 创建一个Actor的同时，返回Actor的ActorRef
  //   说明
  //(1) Props[SayHelloActor] 创建了一个 SayHelloActor实例，使用反射
  //(2) "sayHelloActor" 给actor取名
  //(3) sayHelloActorRef: ActorRef 就是 Props[SayHelloActor] 对应的ActorRef
  //(4) 创建的SayHelloActor 实例被ActorSystme接管
  private val sayHelloActorRef: ActorRef = actoryFactory.actorOf(Props[SayHelloActor], "sayHelloActor")

  def main(args: Array[String]): Unit = {

    /**
     * 1、给SayHelloActor 发消息(邮箱)
     * 2、sayHelloActorRef表示发给谁
     */
    sayHelloActorRef ! "hello"
    sayHelloActorRef ! "ok"
    sayHelloActorRef ! "ok~"
    //研究异步如何退出ActorSystem
    sayHelloActorRef ! "exit"
  }
}
```

控制台输出:

```ini
收到hello, 回应hello too:)
收到ok, 回应ok too:)
匹配不到
接收到exit指令，退出系统
```

代码运行讲解：

![image-20200804170206386](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831133909.png)

小结

当程序执行 *aActorRef* = *actorFactory*.actorOf(*Props*[AActor], "aActor") ，会完成如下任务：

（1）actorFactory 是 *ActorSystem*(ActorFactory) 这样创建的。

（2）这里的 *Props*[AActor] 会使用反射机制，创建一个AActor 对象，如果是*actorFactory*.actorOf(*Props*(new AActor(bActorRef)), "aActorRef") 形式，就是使用new 的方式创建一个AActor对象, 注意Props() 是小括号。

（3）会创建一个AActor 对象的代理对象 aActorRef , 使用aActorRef 才能发送消息。

（4）会在底层创建 Dispather Message ，是一个线程池，用于分发消息， 消息是发送到对应的Actor的 MailBox。

（5）会在底层创建AActor 的MailBox 对象，该对象是一个队列，可接收Dispatcher Message 发送的消息。

（6）MailBox 实现了Runnable 接口，是一个线程，一直运行并调用Actor的receive 方法，因此当Dispather 发送消息到MailBox时，Actor 在receive 方法就可以得到信息。

（7）aActorRef !  "hello", 表示把hello消息发送到A Actor 的mailbox （通过Dispatcher Message 转发）。

## 4、Actor间通讯

### 1、需求

​		编写2个 Actor , 分别是  AActor 和 BActor，AActor和BActor之间可以相互发送消息。

### 2、Actor通讯机制

![image-20200804170807533](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831133910.png)

### 3、创建项目

（1）创建AActor

```scala
import akka.actor.{Actor, ActorRef}

class AActor(actorRef: ActorRef) extends Actor {

  //拿到引用
  val bActorRef: ActorRef = actorRef

  override def receive: Receive = {
    case "start" => {
      println("AActor 出招了 , start ok")
      self ! "我打" //发给自己
    }
    case "我打" => {
      //给BActor 发出消息
      //这里需要持有BActor的引用(BActorRef)
      println("AActor(黄飞鸿) 厉害 看我佛山无影脚")
      Thread.sleep(1000)
      bActorRef ! "我打" //给BActor 发出消息
    }
  }
}
```

（2）创建BActor

```scala
import akka.actor.Actor

class BActor extends Actor{
  override def receive:Receive = {
    case "我打" => {
      println("BActor(乔峰) 挺猛 看我降龙十八掌")
      Thread.sleep(1000)
      //通过sender() 可以获取到发送消息的actor的ref
      sender() ! "我打"
    }
  }
}
```

（3）创建调用对象

```scala
//调用
object ActorGame extends App {
  //创建ActorSystem
  val actorfactory = ActorSystem("actorfactory")
  /**
   * 1、先创建BActor引用/代理，再创建AActor的引用
   * 因为A需要BActorRef的一个引用的。
   * 2、创建AActor的引用需要根据AActor的构造器来创建。
   */
  val bActorRef: ActorRef = actorfactory.actorOf(Props[BActor], "bActor")
  val aActorRef: ActorRef = actorfactory.actorOf(Props(new AActor(bActorRef)), "aActor")

  //aActor出招，触发动作
  aActorRef ! "start"
}
```

控制台输出：

```ini
AActor 出招了 , start ok
AActor(黄飞鸿) 厉害 看我佛山无影脚
BActor(乔峰) 挺猛 看我降龙十八掌
AActor(黄飞鸿) 厉害 看我佛山无影脚
BActor(乔峰) 挺猛 看我降龙十八掌
...
```

小结

（1）两个Actor通讯机制和Actor 自身发消息机制基本一样。

（2）如果A Actor 在需要给B Actor 发消息，则需要持有B Actor 的 ActorRef，可以通过创建时，传入 B Actor的 代理对象(ActorRef)。

（3）当B Actor 在receive 方法中接收到消息，需要回复时，可以通过sender() 获取到发送Actor的 代理对象。

Actor 的receive 方法被调用的理解：

（1）每个Actor对应MailBox。

（2）MailBox 实现了Runnable 接口，处于运行的状态。

（3）当有消息到达MailBox,就会去调用Actor的receive方法，将消息推送给receive 。

## 5、AKKA网络编程

​		Akka支持面向大并发后端服务程序，网络通信这块是服务端程序重要的一部分。

网络编程有两种:

（1）TCP socket编程，是网络编程的主流。之所以叫Tcp socket编程，是因为底层是基于Tcp/ip协议的. 比如: QQ聊天 。

（2）b/s结构的http编程，我们使用浏览器去访问服务器时，使用的就是http协议，而http底层依旧是用tcp socket实现的。 比如: 京东商城 。

### 1、基础知识

1、OSI与Tcp/ip参考模型

模型 (推荐tcp/ip协议3卷)。

![image-20200804173447467](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831133911.png)

2、ip地址

​		每个internet上的主机和路由器都有一个ip地址，它包括网络号和主机号，ip地址有ipv4(32位)或者ipv6(128位)，可以通过ipconfig 来查看。

3、端口

​		我们这里所指的端口不是指物理意义上的端口，而是特指TCP/IP协议中的端口，是逻辑意义上的端口。

​		如果把IP地址比作一间房子，端口就是出入这间房子的门。真正的房子只有几个门，但是一个IP地址的端口 可以有65535（即：256×256-1）个之多！端口是通过端口号来标记的。(端口号 0：Reserved)

（1）端口分类

（a）0号是保留端口.

（b）1-1024是固定端口

 又叫有名端口,即被某些程序固定使用,一般程序员不使用.

+ 22: SSH远程登录协议 
+ 23: telnet使用 
+ 21: ftp使用

+  25: smtp服务使用  
+  80: iis使用 
+ 7: echo服务

（c）1025-65535是动态端口

 这些端口，程序员可以使用。

（2）注意点

（a）在计算机(尤其是做服务器)要尽可能的少开端口。

（b）一个端口只能被一个程序监听。

（c）如果使用 `netstat –an` 可以查看本机有哪些端口在监听。

（d）可以使用 `netstat –anb` 来查看监听端口的`pid`,在结合任务管理器关闭不安全的端口。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831133912.png" alt="image-20200804174551798" style="zoom:80%;" />

4、socket编程中客户端和服务器的网络分布

![image-20200804174610083](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831133913.png)

### 2、小黄鸡客服

#### 1、需求

（1）服务端进行监听(9999)。

（2）客户端可以通过键盘输入，发送咨询问题给小黄鸡客服(服务端)。

（3）小黄鸡(服务端) 回答客户的问题。

#### 2、网络拓扑图

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831133914.png" alt="image-20200804175107340" style="zoom:80%;" />

#### 3、程序架构图

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831133915.png" alt="image-20200804180058898" style="zoom:80%;" />

#### 4、创建项目

1、服务器完成监听

```scala
class YellowChickenServer extends Actor {
  override def receive: Receive = {
    case "start" => println("start 小黄鸡客服开始工作了....")
    //如果接收到ClientMessage
    case ClientMessage(mes) => {
      /**
       * 1、使用match --case中的模糊匹配
       * 也可以使用模糊匹配。
       */
      mes match {
        case "大数据学费" => sender() ! ServerMessage("35000RMB")
        case "学校地址" => sender() ! ServerMessage("北京昌平xx路xx大楼")
        case "学习什么技术" => sender() ! ServerMessage("大数据 前端 python")
        case _ => sender() ! ServerMessage("你说的啥子~")
      }
    }
  }
}

//主程序-入口
object YellowChickenServer extends App {

  val host = "127.0.0.1" //服务端ip地址
  val port = 9999
  //创建config对象,指定协议类型，监听的ip和端口
  val config = ConfigFactory.parseString(
    s"""
       |akka.actor.provider="akka.remote.RemoteActorRefProvider"
       |akka.remote.netty.tcp.hostname=$host
       |akka.remote.netty.tcp.port=$port
        """.stripMargin)

  /**
   * 1、创建ActorSystem
   * 2、url (统一资源定位)，actor是一个资源
   * 3、Server是actor的名字
   * 4、YellowChickenServer是serverActorSystem的名字
   * 5、config需要将ip和host带进去（单机版不需要指定）
   */
  val serverActorSystem = ActorSystem("Server", config)
  //创建YellowChickenServer 的actor和返回actorRef
  val yellowChickenServerRef: ActorRef = serverActorSystem.actorOf(Props[YellowChickenServer], "YellowChickenServer")

  //给自己先发一个消息，让自己启动
  yellowChickenServerRef ! "start"
}
```

2、发送和接收协议

```scala
/**
 * 1、使用样例类来构建协议，样例类默认实现了序列化
 * 2、客户端发给服务器协议(序列化的对象)
 * 3、构建样例类中的参数(mes: String)，默认会成为该样例类
 * 的一个私有书属性
 */
case class ClientMessage(mes: String)

//服务端发给客户端的协议(样例类对象)
case class ServerMessage(mes: String)
```

3、客户端

```scala
class CustomerActor(serverHost: String, serverPort: Int) extends Actor {
  //定义一个YellowChickenServerRef
  var serverActorRef: ActorSelection = _

  /**
   * 1、在Actor中有一个方法PreStart方法，他会在actor运行前执行
   * 2、在akka的开发中，通常将初始化的工作，放在preStart方法
   * 3、Server@${serverHost}中的Server对应YellowChickenServer的 actor名字
   * 4、/user/YellowChickenServer 添加路径
   */
  override def preStart(): Unit = {
    println("preStart() 执行")

    /**
     * 1、找到YellowChickenServer服务器
     * 2、serverActorRef=ActorSelection[Anchor(akka.tcp://Server@127.0.0.1:9999/), Path(/user/YellowChickenServer)]
     */
    serverActorRef = context.actorSelection(s"akka.tcp://Server@${serverHost}:${serverPort}/user/YellowChickenServer")
    println("serverActorRef=" + serverActorRef)
  }

  override def receive: Receive = {
    case "start" => println("start,客户端运行，可以咨询问题")
    case mes: String => {
      /**
       * 1、真正的转发给小黄鸡客服。
       * 2、使用ClientMessage case class apply
       * 因为样例类默认会实现apply方法。
       */
      serverActorRef ! ClientMessage(mes)
    }
    //如果接收到服务器的回复
    case ServerMessage(mes) => {
      println(s"收到小黄鸡客服(Server): $mes")
    }
  }
}

//主程序-入口
object CustomerActor extends App {

  val (clientHost, clientPort, serverHost, serverPort) = ("127.0.0.1", 9990, "127.0.0.1", 9999)
  val config = ConfigFactory.parseString(
    s"""
       |akka.actor.provider="akka.remote.RemoteActorRefProvider"
       |akka.remote.netty.tcp.hostname=$clientHost
       |akka.remote.netty.tcp.port=$clientPort
        """.stripMargin)

  //创建ActorSystem
  val clientActorSystem = ActorSystem("client", config)

  //创建CustomerActor的实例和引用
  val customerActorRef: ActorRef = clientActorSystem.actorOf(Props(new CustomerActor(serverHost, serverPort)),
    "CustomerActor")

  //启动customerRef/也可以理解启动Actor
  customerActorRef ! "start"

  //客户端可以发送消息给服务器
  while (true) {
    println("请输入要咨询的问题")
    val mes = StdIn.readLine()
    customerActorRef ! mes
  }
}
```

控制台输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831133916.png" alt="image-20200804212706109" style="zoom:80%;" />

## 6、Spark Master Worker进行通讯

### 1、项目意义

（1）深入理解Spark的Master和Worker的通讯机制。

（2）加深对主从服务心跳检测机制(HeartBeat)的理解，方便以后spark源码二次开发。

### 2、需求

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831133917.png" alt="image-20200804213014159" style="zoom:67%;" />

（1）worker注册到Master, Master完成注册，并回复worker注册成功。

（2）worker定时发送心跳，并在Master接收到。

（3）Master接收到worker心跳后，要更新该worker的最近一次发送心跳的时间。

（4）给Master启动定时任务，定时检测注册的worker有哪些没有更新心跳,并将其从hashmap中删除。

（5）master worker 进行分布式部署(Linux系统)->如何给maven项目打包->上传linux。

### 3、程序框架图

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831133918.png" alt="image-20200804213859360" style="zoom:80%;" />

### 4、创建项目

项目结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831133919.png" alt="image-20200804224627119" style="zoom:80%;" />

1、SparkMaster

```scala
class SparkMaster extends Actor {
  //定义个hm,管理workers（包含worker中的信息）
  val workers = mutable.Map[String, WorkerInfo]()

  override def receive: Receive = {
    case "start" => {
      println("master服务器启动了...")
      //这里开始检测定时任务
      self ! StartTimeOutWorker
    }
    case RegisterWorkerInfo(id, cpu, ram) => {
      //接收到worker注册信息
      if (!workers.contains(id)) { //查看id是否重复
        //创建WorkerInfo 对象
        val workerInfo = new WorkerInfo(id, cpu, ram)
        //加入到workers 的hasmap
        workers += ((id, workerInfo))
        println("服务器的workers=" + workers)
        //回复一个消息，说注册成功
        sender() ! RegisteredWorkerInfo
      }
    }
    case HeartBeat(id) => {
      //1.从workers取出WorkerInfo
      val workerInfo = workers(id)
      //对上一次的心跳时间进行一次更新（对应的worker）
      workerInfo.lastHeartBeat = System.currentTimeMillis()
      println("master更新了 " + id + " 心跳时间...")
    }
    case StartTimeOutWorker => {
      println("开始了定时检测worker心跳的任务")
      import context.dispatcher
      //说明
      //1. 0 millis 不延时，立即执行定时器
      //2. 9000 millis 表示每隔9秒执行一次
      //3. self:表示发给自己
      //4. RemoveTimeOutWorker 发送的内容
      context.system.scheduler.schedule(0 millis, 9000 millis, self, RemoveTimeOutWorker)
    }

    /**
     * 1、对RemoveTimeOutWorker消息处理
     * 2、这里需求检测哪些worker心跳超时（now - lastHeartBeat > 6000），并从map中删除
     * 考虑到了网络延时，保证超时时间大于6秒后删除
     */
    case RemoveTimeOutWorker => {
      //首先将所有的 workers 的 所有WorkerInfo
      val workerInfos = workers.values
      val nowTime = System.currentTimeMillis()

      /**
       * 1、把超时的所有workerInfo,删除即可
       * 2、 workerInfos.filter(workerInfo)的workerInfo是workerInfos遍历后的元素
       *  workerInfos.filter(workerInfo => (nowTime - workerInfo.lastHeartBeat) > 6000)
       *  表示要删除的集合
       * foreach(workerInfo)中的workerInfo表示集合传进来的那个元素
       */
      workerInfos.filter(workerInfo => (nowTime - workerInfo.lastHeartBeat) > 6000)
        .foreach(workerInfo => workers.remove(workerInfo.id))
      println("当前有 " + workers.size + " 个worker存活的")
    }
  }
}

object SparkMaster {
  def main(args: Array[String]): Unit = {

    //这里我们分析出有3个host,port,sparkMasterActor
    if (args.length != 3) {
      println("请输入参数 host port sparkMasterActor名字")
      sys.exit() //退出系统
    }

    val host = args(0)
    val port = args(1)
    val name = args(2)

    //先创建ActorSystem
    val config = ConfigFactory.parseString(
      s"""
         |akka.actor.provider="akka.remote.RemoteActorRefProvider"
         |akka.remote.netty.tcp.hostname=${host}
         |akka.remote.netty.tcp.port=${port}
            """.stripMargin)
    val sparkMasterSystem = ActorSystem("SparkMaster", config)
    //创建SparkMaster -actor
    val sparkMasterRef = sparkMasterSystem.actorOf(Props[SparkMaster], s"${name}")
    //启动SparkMaster
    sparkMasterRef ! "start"
  }
}
```

2、SparkWorker

```scala
class SparkWorker(masterHost: String, masterPort: Int, masterName: String) extends Actor {
  //masterProxy是Master的代理/引用ref
  var masterPorxy: ActorSelection = _
  val id = java.util.UUID.randomUUID().toString

  override def preStart(): Unit = {
    println("preStart()调用")
    //初始化masterPorxy
    masterPorxy = context.actorSelection(s"akka.tcp://SparkMaster@${masterHost}:${masterPort}/user/${masterName}")
    println("masterProxy=" + masterPorxy)
  }

  override def receive: Receive = {
    case "start" => {
      println("worker启动了")
      //发出一个注册消息
      masterPorxy ! RegisterWorkerInfo(id, 16, 16 * 1024)
    }
    case RegisteredWorkerInfo => {
      println("workerid= " + id + " 注册成功~")
      //当注册成功后，就定义一个定时器,每隔一定时间，发送SendHeartBeat给自己
      import context.dispatcher
      //说明
      //1. 0 millis 不延时，立即执行定时器
      //2. 3000 millis 表示每隔3秒执行一次
      //3. self:表示发给自己
      //4. SendHeartBeat 发送的内容
      context.system.scheduler.schedule(0 millis, 3000 millis, self, SendHeartBeat)
    }
    case SendHeartBeat => {
      println("worker = " + id + "给master发送心跳")
      masterPorxy ! HeartBeat(id)
    }
  }
}

object SparkWorker {
  def main(args: Array[String]): Unit = {

    if (args.length != 6) {
      println("请输入参数 workerHost workerPort workerName masterHost masterPort masterName")
      sys.exit()
    }

    val workerHost = args(0)
    val workerPort = args(1)
    val workerName = args(2)
    val masterHost = args(3)
    val masterPort = args(4)
    val masterName = args(5)
    val config = ConfigFactory.parseString(
      s"""
         |akka.actor.provider="akka.remote.RemoteActorRefProvider"
         |akka.remote.netty.tcp.hostname=${workerHost}
         |akka.remote.netty.tcp.port=${workerPort}
            """.stripMargin)

    //创建ActorSystem
    val sparkWorkerSystem = ActorSystem("SparkWorker", config)

    //创建SparkWorker 的引用/代理
    val sparkWorkerRef = sparkWorkerSystem.actorOf(Props(new SparkWorker(masterHost, masterPort.toInt, masterName)), s"${workerName}")

    //启动actor
    sparkWorkerRef ! "start"
  }
}
```

3、协议

```scala
/**
 * worker注册信息
 * @param id
 * @param cpu 使用几个cpu核
 * @param ram 多少内存
 */
case class RegisterWorkerInfo(id: String, cpu: Int, ram: Int)

/**
 * 1、这个是WorkerInfo, 这个信息将来是保存到master的 hm(该hashmap是用于管理worker)
 * 2、将来这个WorkerInfo会扩展（比如增加worker上一次的心跳时间）
 * 3、和 RegisterWorkerInfo 信息一致，只是没有添加case,并不是协议
 * 4、lastHeartBeat 上一次的心跳时间
 */
class WorkerInfo(val id: String, val cpu: Int, val ram: Int) {
  var lastHeartBeat : Long = System.currentTimeMillis()
}

// 当worker注册成功，服务器返回一个RegisteredWorkerInfo 对象
case object RegisteredWorkerInfo

//worker每隔一定时间由定时器发给自己的一个消息
case object SendHeartBeat
//worker每隔一定时间由定时器触发，而向master发送的协议消息
//协议要带上自己的id号
case class HeartBeat(id: String)

//master给自己发送一个触发检查超时worker的信息
case object StartTimeOutWorker
// master给自己发消息，检测worker,对于心跳超时的.
case object RemoveTimeOutWorker
```

4、启动参数运行时执行

Master和Worker的启动参数在运行时指定，而不是固定写在程序里的。

（1）找到对应的Master的配置。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831133920.png" alt="image-20200804224819220" style="zoom:80%;" />

![image-20200804224839239](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831133921.png)



（2）填入启动参数后保存

![image-20200804225010841](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831133922.png)

```ini
#Master的启动参数
127.0.0.1
10005
SparkMaster01

#Worker的启动参数
127.0.0.1
10001
SparkWork01
127.0.0.1
10005
SparkMaster01
```

5、控制台输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831133923.png" alt="image-20200804225230835" style="zoom:80%;" />

![image-20200804225254295](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831133924.png)

# 3 WordCount讲解

```scala
object WordCountsDemo01 {
  def main(args: Array[String]): Unit = {
    /**
     * 需求
     *  val lines = List("hello xiaolun tom","xiaolun hello hello")
     *  使用映射集合，统计各个单词出现的次数，并按照出现次数排序（从小到大排）
     */
    val lines = List("hello xiaolun tom","xiaolun hello hello")
    /**
     * 步骤：先分步，再组合
     * 1、步骤1：
     * res1执行过后的结果：
     * List(hello, xiaolun, tom, xiaolun, hello, hello)
     */
//    val res1 = lines.flatMap((s:String) => s.split(""))
    val res1 = lines.flatMap(_.split(" "))
    println("res1 = " + res1)

    /**
     * 步骤2：做成一个对偶list,才能够分组并统计
     */
    println("------------------------------------------")
//    res1.map((s:String) => (s,1))
    var res2 = res1.map((_,1))
    //List((hello,1), (xiaolun,1), (tom,1), (xiaolun,1), (hello,1), (hello,1))
    println(res2)

    /**
     * 步骤3：分组，把不同的单词，归属到不同的组
     * 对遍历出来的对偶元素，按照其第一个元素进行分组
     */
    println("------------------------------------------")
//    var res3 = res2.groupBy((x:(String,Int)) => x._1)
    var res3 = res2.groupBy(_._1)
    //Map(tom -> List((tom,1)), xiaolun -> List((xiaolun,1), (xiaolun,1)), hello -> List((hello,1), (hello,1), (hello,1)))
    println("res3 =" +res3)

    /**
     * 步骤4：对上面的元组进行统计大小
     */
    println("------------------------------------------")
//    var res4 = res3.map((x:(String,List[(String,Int)])) => (x._1,x._2.size))
    var res4 = res3.map(x => (x._1,x._2.size))
    //res4 =Map(tom -> 1, xiaolun -> 2, hello -> 3)
    println("res4 =" +res4)

    /**
     * 步骤5：排序
     * ((x: (String, Int)) => x._2)按照第二个元素排序
     */
    println("------------------------------------------")
//    val res5 = res4.toList.sortBy((x: (String, Int)) => x._2)
    val res5 = res4.toList.sortBy(_.x._2) //从小到大
//    val res5 = res4.toList.sortBy(_.x._2).reverse //从大到小
    //res5 =List((tom,1), (xiaolun,2), (hello,3)) -》ok
    println("res5 =" +res5)
    
  }
}
```