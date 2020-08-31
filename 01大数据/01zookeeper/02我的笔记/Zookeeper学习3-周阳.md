## 1、介绍

1、概述

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130913.png" alt="image-20200827093322824" style="zoom:67%;" />

总计：zookeeper=文件系统+通知机制

2、操作

（1）统一命名服务（Name Service如Dobbu服务注册中心）

（2）配置管理（Configuration Management）

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130914.png" alt="image-20200827103239863" style="zoom:80%;" />

（3）集群管理（Group Membershio，如Hadoop分布式集群管理）

（4）Java操作API。

## 2、zk-setup

1、安装步骤

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130915.png" alt="image-20200827110930471" style="zoom:80%;" />

2、zoo.cfg解读

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130916.png" alt="image-20200827111051795" style="zoom:67%;" />

文件显示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130917.png" alt="image-20200827111212820" style="zoom:80%;" />

（1）tickTime

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130918.png" alt="image-20200827111128004" style="zoom:80%;" />

（2）initLimit

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130919.png" alt="image-20200827111237388" style="zoom:80%;" />

（3）synclimit

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130920.png" alt="image-20200827111345844" style="zoom:80%;" />

（4）dataDir-数据目录

（5）clientPort-默认端口

3、开启服务+客户端链接

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130921.png" alt="image-20200827111645735" style="zoom:80%;" />

​		当启动zookeeper服务器之后，可以通过下面的命令来测试该服务器是否成功启动：

```shell
echo ruok | nc 127.0.0.1 2181
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130922.png" alt="image-20200827111828394" style="zoom:80%;" />

## 3、数据节点znode节点深入讲解

1、介绍

（1）zookeeper维护一个类似文件系统的数据结构。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130923.png" alt="image-20200827113820261" style="zoom: 67%;" />

（2）初始zonde节点

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130924.png" alt="image-20200827113935697" style="zoom:80%;" />

（3）概述

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130925.png" alt="image-20200827114056477" style="zoom:67%;" />

zookeeper的Stat结构体：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130926.png" alt="image-20200827114213254" style="zoom:80%;" />

czxid介绍：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130927.png" alt="image-20200827114236319" style="zoom:80%;" />

（4）总结

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130928.png" alt="image-20200827114321228" style="zoom:67%;" />

2、znode中的存在类型

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130929.png" alt="image-20200827114557760" style="zoom:80%;" />

临时/持久图：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130930.png" alt="image-20200827114715372" style="zoom:80%;" />

介绍：

​		znode是由客户端创建的，它和创建它的客户端的内在联系，决定了它的存在性。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130931.png" alt="image-20200827114739225" style="zoom:80%;" />

## 4、客户端操作

1、zkCli常用命令操作

（1）总结：和Redis的KV键值对类似，只不过key变成了一个路径节点值，value就是data。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130932.png" alt="image-20200827165803565" style="zoom:80%;" />

（2）常用命令：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130933.png" alt="image-20200827165858815" style="zoom:67%;" />

2、四字命令

（1）介绍

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130934.png" alt="image-20200827170043557" style="zoom:80%;" />

（2）常用

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130935.png" alt="image-20200827170121118" style="zoom:67%;" />

## 5、通知机制

1、session

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130936.png" alt="image-20200827204029244" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130937.png" alt="image-20200827204104750" style="zoom:80%;" />

2、watch

（1）通知机制

客户端注册监听它关心的目录节点，当目录节点发生变化（数据改变、被删除、子目录节点增加或删除）时，zookeeper会通知客户端。

（2）观察者的功能

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130938.png" alt="image-20200827204628733" style="zoom:67%;" />

总结：异步+回调+触发机制。

（3）watch事件的理解

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130939.png" alt="image-20200827204758515" style="zoom:80%;" />

步骤：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130940.png" alt="image-20200827204833034" style="zoom:80%;" />

（a）一次触发

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130941.png" alt="image-20200827204917978" style="zoom:80%;" />

（b）发往客户端

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130942.png" alt="image-20200827205123802" style="zoom:80%;" />

（c）为数据设置watch

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130943.png" alt="image-20200827205331750" style="zoom:80%;" />

（d）变化备注

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130944.png" alt="image-20200827205418925" style="zoom:80%;" />

## 6、zkCluster

1、介绍

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130945.png" alt="image-20200827205729483" style="zoom:80%;" />

2、伪分布式单机配置

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130946.png" alt="image-20200827210452070" style="zoom:67%;" />