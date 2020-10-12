# 1、Spark核心组件

1、Driver

Spark驱动器节点，用于执行Spark任务中的main方法，负责实际代码的执行工作。Driver在Spark作业执行时主要负责：

将用户程序转化为任务（job）；

在Executor之间调度任务（task）；

跟踪Executor的执行情况；

通过UI展示查询运行情况。

2、Executor

Spark Executor节点是一个JVM进程，负责在 Spark 作业中运行具体任务，任务彼此之间相互独立。Spark 应用启动时，Executor节点被同时启动，并且始终伴随着整个 Spark 应用的生命周期而存在。如果有Executor节点发生了故障或崩溃，Spark 应用也可以继续执行，会将出错节点上的任务调度到其他Executor节点上继续运行。

Executor有两个核心功能：

（1）负责运行组成Spark应用的任务，并将结果返回给Driver进程。

（2）它们通过自身的块管理器（Block Manager）为用户程序中要求缓存的 RDD 提供内存式存储。RDD 是直接缓存在Executor进程内的，因此任务可以在运行时充分利用缓存数据加速运算。

3、通用运行流程概述

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201012145444243.png" alt="image-20201012145444243" style="zoom:67%;" />

不论Spark以何种模式进行部署，任务提交后，都会先启动Driver进程，随后Driver进程向集群管理器注册应用程序，之后集群管理器根据此任务的配置文件分配Executor并启动，当Driver所需的资源全部满足后，Driver开始执行main函数，Spark查询为懒执行，当执行到action算子时开始反向推算，根据宽依赖进行stage的划分，随后每一个stage对应一个taskset，taskset中有多个task，根据本地化原则，task会被分发到指定的Executor去执行，在任务执行的过程中，Executor也会不断与Driver进行通信，报告任务运行情况。

4、Standalone Client模式

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201012145929492.png" alt="image-20201012145929492" style="zoom:67%;" />

在Standalone Client模式下，Driver在任务提交的本地机器上运行，Driver启动后向Master注册应用程序，Master根据submit脚本的资源需求找到内部资源至少可以启动一个Executor的所有Worker，然后在这些Worker之间分配Executor，Worker上的Executor启动后会向Driver反向注册，所有的Executor注册完成后，Driver开始执行main函数，之后执行到Action算子时，开始划分stage，每个stage生成对应的taskSet，之后将task分发到各个Executor上执行。

5、Standalone Cluster模式

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201012150112336.png" alt="image-20201012150112336" style="zoom:50%;" />

在Standalone Cluster模式下，任务提交后，Master会找到一个Worker启动Driver进程， Driver启动后向Master注册应用程序，Master根据submit脚本的资源需求找到内部资源至少可以启动一个Executor的所有Worker，然后在这些Worker之间分配Executor，Worker上的Executor启动后会向Driver反向注册，所有的Executor注册完成后，Driver开始执行main函数，之后执行到Action算子时，开始划分stage，每个stage生成对应的taskSet，之后将task分发到各个Executor上执行。

注意，Standalone的两种模式下（client/Cluster），Master在接到Driver注册Spark应用程序的请求后，会获取其所管理的剩余资源能够启动一个Executor的所有Worker，然后在这些Worker之间分发Executor，此时的分发只考虑Worker上的资源是否足够使用，直到当前应用程序所需的所有Executor都分配完毕，Executor反向注册完毕后，Driver开始执行main程序。

6、YARN Cluster模式

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201012150347126.png" alt="image-20201012150347126" style="zoom:80%;" />

在YARN Cluster模式下，任务提交后会和ResourceManager通讯申请启动ApplicationMaster，随后ResourceManager分配container，在合适的NodeManager上启动ApplicationMaster，此时的ApplicationMaster就是Driver。

Driver启动后向ResourceManager申请Executor内存，ResourceManager接到ApplicationMaster的资源申请后会分配container，然后在合适的NodeManager上启动Executor进程，Executor进程启动后会向Driver反向注册，Executor全部注册完成后Driver开始执行main函数，之后执行到Action算子时，触发一个job，并根据宽依赖开始划分stage，每个stage生成对应的taskSet，之后将task分发到各个Executor上执行。

# 2、Spark通讯架构

1、架构

（1）RPC架构

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201012150945402.png" alt="image-20201012150945402" style="zoom:67%;" />

（2）Spark通信架构

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201012150854312.png" alt="image-20201012150854312" style="zoom:67%;" />

2、分析

（1）RpcEndpoint：RPC端点，Spark针对每个节点（Client/Master/Worker）都称之为一个Rpc端点，且都实现RpcEndpoint接口，内部根据不同端点的需求，设计不同的消息和不同的业务处理，如果需要发送（询问）则调用Dispatcher。

（2）RpcEnv：RPC上下文环境，每个RPC端点运行时依赖的上下文环境称为RpcEnv。

（3）Dispatcher：消息分发器，针对于RPC端点需要发送消息或者从远程RPC接收到的消息，分发至对应的指令收件箱/发件箱。如果指令接收方是自己则存入收件箱，如果指令接收方不是自己，则放入发件箱。

（4）Inbox：指令消息收件箱，一个本地RpcEndpoint对应一个收件箱，Dispatcher在每次向Inbox存入消息时，都将对应EndpointData加入内部ReceiverQueue中，另外Dispatcher创建时会启动一个单独线程进行轮询ReceiverQueue，进行收件箱消息消费。

（5）RpcEndpointRef：RpcEndpointRef是对远程RpcEndpoint的一个引用。当我们需要向一个具体的RpcEndpoint发送消息时，一般我们需要获取到该RpcEndpoint的引用，然后通过该应用发送消息。

（6）OutBox：指令消息发件箱，对于当前RpcEndpoint来说，一个目标RpcEndpoint对应一个发件箱，如果向多个目标RpcEndpoint发送信息，则有多个OutBox。当消息放入Outbox后，紧接着通过TransportClient将消息发送出去。消息放入发件箱以及发送过程是在同一个线程中进行；

（7）RpcAddress：表示远程的RpcEndpointRef的地址，Host + Port。

（8）TransportClient：Netty通信客户端，一个OutBox对应一个TransportClient，TransportClient不断轮询OutBox，根据OutBox消息的receiver信息，请求对应的远程TransportServer。

（9）TransportServer：Netty通信服务端，一个RpcEndpoint对应一个TransportServer，接受远程消息后调用Dispatcher分发消息至对应收发件箱。













































