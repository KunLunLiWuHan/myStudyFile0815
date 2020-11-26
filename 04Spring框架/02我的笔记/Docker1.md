# 1、介绍

1、概述

（1）docker出现的原因

环境配置如此麻烦，换一台机器，就要重来一次，费力费时。解决上面的问题的方法就是：软件可以带环境安装。即安装的时候，把原始环境一模一样地复制过来。开发人员利用 Docker 可以消除协作编码时“在我的机器上可正常工作”的问题。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108091350141.png" alt="image-20201108091350141" style="zoom:80%;" />

传统上认为，软件编码开发/测试结束后，所产出的成果即是程序或是能够编译执行的二进制字节码等(java为例)。而为了让这些程序可以顺利执行，开发团队也得准备完整的部署文件，让维运团队得以部署应用程式，开发需要清楚的告诉运维部署团队，用的全部配置文件+所有软件环境。不过，即便如此，仍然常常发生部署失败的状况。Docker镜像的设计，使得Docker得以打破过去「程序即应用」的观念。透过镜像(images)将作业系统核心除外，运作应用程式所需要的系统环境，由下而上打包，达到应用程式跨平台间的无缝接轨运作。

（2）docker理念

Docker是基于Go语言实现的云开源项目。

Docker的主要目标是“Build，Ship and Run Any App,Anywhere”，也就是通过对应用组件的封装、分发、部署、运行等生命周期的管理，使用户的APP（可以是一个WEB应用或数据库应用等等）及其运行环境能够做到“一次封装，到处运行”。

![image-20201108091548372](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108091548372.png)

Linux 容器技术的出现就解决了这样一个问题，而 Docker 就是在它的基础上发展过来的。将应用运行在 Docker 容器上面，而 Docker 容器在任何操作系统上都是一致的，这就实现了跨平台、跨服务器。只需要一次配置好环境，换到别的机子上就可以一键部署好，大大简化了操作。

即docker技术是一种为解决运行环境和配置问题而产生的软件容器，它可以做持续集成并有助于整体发布的容器虚拟化技术。

（3）传统的虚拟化技术

虚拟机（virtual machine）就是带环境安装的一种解决方案。

它可以在一种操作系统里面运行另一种操作系统，比如在Windows 系统里面运行Linux 系统。应用程序对此毫无感知，因为虚拟机看上去跟真实系统一模一样，而对于底层系统来说，虚拟机就是一个普通文件，不需要了就删掉，对其他部分毫无影响。这类虚拟机完美的运行了另一套系统，能够使应用程序，操作系统和硬件三者之间的逻辑不变。 

![image-20201108091947235](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108091947235.png)

虚拟机的缺点具有资源占用多，冗余步骤多，启动慢等缺点。

（4）容器虚拟化技术

由于前面虚拟机存在这些缺点，Linux 发展出了另一种虚拟化技术：Linux 容器（Linux Containers，缩写为 LXC）。

Linux 容器不是模拟一个完整的操作系统，而是对进程进行隔离。有了容器，就可以将软件运行所需的所有资源打包到一个隔离的容器中。容器与虚拟机不同，不需要捆绑一整套操作系统，只需要软件工作所需的库资源和设置。系统因此而变得高效轻量并保证部署在任何环境中的软件都能始终如一地运行。

![image-20201108092140519](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108092140519.png)

比较了 Docker 和传统虚拟化方式的不同之处：

+ 传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整操作系统，在该系统上再运行所需应用进程。

+ 而容器内的应用进程直接运行于宿主的内核，容器内没有自己的内核，而且也没有进行硬件虚拟。因此容器要比传统虚拟机更为轻便。

+ 每个容器之间互相隔离，每个容器有自己的文件系统 ，容器之间进程不会相互影响，能区分计算资源。

（5）Docker比VM块的原因

+ docker有着比虚拟机更少的抽象层。由亍docker不需要Hypervisor实现硬件资源虚拟化,运行在docker容器上的程序直接使用的都是实际物理机的硬件资源。因此在CPU、内存利用率上docker将会在效率上有明显优势。
+ docker利用的是宿主机的内核,而不需要Guest OS。因此,当新建一个容器时,docker不需要和虚拟机一样重新加载一个操作系统内核。仍而避免引寻、加载操作系统内核返个比较费时费资源的过程,当新建一个虚拟机时,虚拟机软件需要加载Guest OS,返个新建过程是分钟级别的。而docker由于直接利用宿主机的操作系统,则省略了返个过程,因此新建一个docker容器只需要几秒钟。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108110143951.png" alt="image-20201108110143951" style="zoom: 80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108110156694.png" alt="image-20201108110156694" style="zoom:80%;" />

2、作用

（1）开发/运维（DevOps）-一次构建，处处运行

+ 更快捷的应用交付和部署

传统的应用开发完成后，需要提供一堆安装程序和配置说明文档，安装部署后需根据配置文档进行繁杂的配置才能正常运行。Docker化之后只需要交付少量容器镜像文件，在正式生产环境加载镜像并运行即可，应用安装配置在镜像里已经内置好，大大节省部署配置和测试验证时间。

+ 更便捷的升级和扩缩容

随着微服务架构和Docker的发展，大量的应用会通过微服务方式架构，应用的开发构建将变成搭乐高积木一样，每个Docker容器将变成一块“积木”，应用的升级将变得非常容易。当现有的容器不足以支撑业务处理时，可通过镜像运行新的容器进行快速扩容，使应用系统的扩容从原先的天级变成分钟级甚至秒级。

+ 更简单的系统运维

应用容器化运行后，生产环境运行的应用可与开发、测试环境的应用高度一致，容器会将应用程序相关的环境和状态完全封装起来，不会因为底层基础架构和操作系统的不一致性给应用带来影响，产生新的BUG。当出现程序异常时，也可以通过测试环境的相同容器进行快速定位和修复。

+ 更高效的计算资源利用

Docker是内核级虚拟化，其不像传统的虚拟化技术一样需要额外的Hypervisor支持，所以在一台物理机上可以运行很多个容器实例，可大大提升物理服务器的CPU和内存的利用率。

3、Docker的基本组成

Docker的架构图如下：
<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108095910160.png" alt="image-20201108095910160" style="zoom:80%;" />

（1）镜像（image）

Docker 镜像（Image）就是一个**只读**的模板。镜像可以用来创建 Docker 容器，一个镜像可以创建很多容器。

容器和镜像的关系类似于面向对象编程的对象和类。

（2）容器（container）

Docker 利用容器独立运行的一个或一组应用。容器是用镜像创建的运行实例。它可以被启动、开始、停止、删除。每个容器都是相互隔离的、保证安全的平台。

**可以把容器看做是一个简易版的 Linux 环境**（包括root用户权限、进程空间、用户空间和网络空间等）和运行在其中的应用程序。

容器的定义和镜像几乎一模一样，也是一堆层的统一视角，唯一区别在于容器的最上面那一层是可读可写的。

（3）仓库（repository）

仓库是集中存放镜像文件的场所。仓库(Repository)和仓库注册服务器（Registry）是有区别的。仓库注册服务器上往往存放着多个仓库，每个仓库中又包含了多个镜像，每个镜像有不同的标签（tag）。

仓库分为公开仓库（Public）和私有仓库（Private）两种形式。

最大的公开仓库是 Docker Hub，里面存放了数量庞大的镜像供用户下载。国内的公开仓库包括阿里云 、网易云等。

（4）总结

Docker 本身是一个容器运行载体或称之为管理引擎。**我们把应用程序和配置依赖打包好形成一个可交付的运行环境，这个打包好的运行环境就似乎 image镜像文件。**只有通过这个镜像文件才能生成 Docker 容器。image 文件可以看作是容器的模板。Docker 根据 image 文件生成容器的实例。同一个 image 文件，可以生成多个同时运行的容器实例。

+ image 文件生成的容器实例，本身也是一个文件，称为镜像文件。
+ 一个容器运行一种服务，当我们需要的时候，就可以通过docker客户端创建一个对应的运行实例，也就是我们的容器。
+ 至于仓储，就是放了一堆镜像的地方，我们可以把镜像发布到仓储中，需要的时候从仓储中拉下来就可以了。

4、官网地址

```http
#docker官网
https://www.docker.com/

#docker hub官网
https://hub.docker.com/
```

# 2、安装

## 1、前提条件

1、概述

（1）Docker支持以下的CentOS版本

CentOS 7 (64-bit)，系统内核版本为 3.10 以上

CentOS 6.5 (64-bit) 或更高的版本，要求系统为64位、系统内核版本为 2.6.32-431 或者更高版本。

（2）查看虚拟机的内核信息

```shell
#uname命令用于打印当前系统相关信息（内核版本号、硬件架构、主机名称和操作系统类型等）。
[root@hadoop101 zookeeper]# uname -r
2.6.32-642.el6.x86_64

#查看已安装的CentOS版本信息（CentOS6.8有，CentOS7无该命令）
[root@hadoop101 zookeeper]# cat /etc/redhat-release 
CentOS release 6.8 (Final)
```

## 2、安装和部署

这里针对的是Centos6.8的系统进行配置。

1、安装EPEL仓库

```shell
yum install -y epel-release
```

Docker使用EPEL发布，RHEL系的OS首先要确保已经持有EPEL仓库，否则先检查OS的版本，然后安装相应的EPEL包。

2、安装docker

```shell
yum install -y docker-io
```

会有下面的报错信息，导致安装失败。

```
No package docker-io available.
```

我们需要使用下面的命令直接进行安装docker-io

```shell
yum install https://get.docker.com/rpm/1.7.1/centos-6/RPMS/x86_64/docker-engine-1.7.1-1.el6.x86_64.rpm
```

3、测试

（1）查看安装后的配置文件

```shell
cat /etc/sysconfig/docker
```

（2）启动docker

```shell
service docker start
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108101932649.png" alt="image-20201108101932649" style="zoom:80%;" />

（3）版本查看

```shell
docker version
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108101910022.png" alt="image-20201108101910022" style="zoom:80%;" />

## 3、入门测试-HelloWord

1、阿里云镜像加速

（1）官网地址

```http
https://dev.aliyun.com/search.html
```

注册一个属于自己的阿里云账号。

（2）获取加速器地址连接

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108104829416.png" alt="image-20201108104829416" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108104805974.png" alt="image-20201108104805974" style="zoom:67%;" />

（3）配置本机的Docker运行镜像加速器

鉴于国内网络问题，后续拉取 Docker 镜像十分缓慢，我们可以配置加速器来解决，使用的是阿里云的本人自己账号的镜像地址。

在FinalShell中进入下面的文件中,然后将自己账户的阿里云加速地址配置进文件中。

```shell
vim /etc/sysconfig/docker

# 配置自己的阿里云加速地址
other_args="--registry-mirror=https://eeqh66oo.mirror.aliyuncs.com"
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108105246211.png" alt="image-20201108105246211" style="zoom:80%;" />

（4）重新启动docker后台服务，使配置生效。

```shell
[root@hadoop101 zookeeper]# service docker restart
```

（5）测试

检查加速器是否生效。

```shell
[root@hadoop101 zookeeper]# ps -ef|grep docker
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108105513271.png" alt="image-20201108105513271" style="zoom:80%;" />

（6）测试运行hello-world

```shell
[root@hadoop101 zookeeper]# docker run hello-world
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108105713224.png" alt="image-20201108105713224" style="zoom: 67%;" />

由于本地没有hello-world这个镜像，所以会下载一个hello-world的镜像，并在容器中运行。当输出“Share image...”这段提示之后，hello world就会停止运行，容器自动终止。

（7）run命令的作用

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108105928316.png" alt="image-20201108105928316" style="zoom:80%;" />

（8）Docker的工作机制

Docker是一个Client-Server结构的系统，Docker守护进程运行在主机上， 然后通过Socket连接从客户端访问，守护进程从客户端接受命令并管理运行在主机上的容器。 容器，是一个运行时环境，就是我们前面说到的集装箱。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108110035383.png" alt="image-20201108110035383" style="zoom:67%;" />

# 3、docker常用命令

## 1、帮助命令

```shell
docker version
docker info
docker --help
```

## 2、镜像命令

1、列出本地镜像

```shell
docker images

#参数
-a:列出本地所有的镜像（含中间映像层）
-q:只显示镜像ID
--digests:显示镜像的摘要信息
--no-trunc:显示完整的镜像信息
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108144159570.png" alt="image-20201108144159570" style="zoom:80%;" />

```
REPOSITORY：表示镜像的仓库源
TAG：镜像的标签
IMAGE ID：镜像ID
CREATED：镜像创建时间
SIZE：镜像大小
```

同一仓库源可以有多个 TAG，代表这个仓库源的不同个版本，我们使用 REPOSITORY:TAG 来定义不同的镜像。

如果你不指定一个镜像的版本标签，例如你只使用 ubuntu，docker 将默认使用 ubuntu:latest 镜像。

2、查询镜像

```shell
docker search xx(镜像名字)

#参数
--no-trunc:显示完整的镜像信息
-s:列出收藏数不小于指定值的的镜像
--automated:只列出automated build类型的镜像
```

3、下载镜像

```shell
# 默认下载最新的镜像(lastest)
docker pull xx(镜像名字)[:tag]
```

4、删除镜像

```shell
#-f:force：强制删除
docker rmi -f xx(镜像名字/ID)
#删除多个镜像文件
docker rmi -f 镜像1:tag 镜像2:tag
#删除全部镜像
docker rmi -f ${docker images -qa}
```

## 3、容器命令

有镜像才能够创建容器，下面我们以下载一个Centos镜像为例。

1、下载镜像

```shell
docker pull centos
```

2、新建并启动容器

```shell
#命令的默认 COMMAND 为 /bin/bash，因此用户的输入是基于 bash shell 执行的
docker run [options] xx(镜像，比如centos:6.8） [command]

#参数说明
--name="容器新名字": 为容器指定一个名称；
-d: 后台运行容器，并返回容器ID，也即启动守护式容器；
-i：以交互模式运行容器，通常与 -t 同时使用；
-t：为容器重新分配一个伪输入终端，通常与 -i 同时使用；（-i 选项指示 docker 要在容器上打开一个标准的输入接口，-t 指示 docker 要创建一个伪 tty 终端，连接容器的标准输入接口，之后用户就可以通过终端进行输入）
-P: 随机端口映射；
-p: 指定端口映射，有以下四种格式
      ip:hostPort:containerPort
      ip::containerPort
      hostPort:containerPort
      containerPort
-v:宿主机的/test目录挂载到容器的/soft目录，可通过以下方式指定:
docker run -it -v /test(宿主机绝对路径目录):/soft（容器内目录） centos（镜像名） /bin/bash
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108150833416.png" alt="image-20201108150833416" style="zoom:80%;" />

使用-d命令启动守护式容器，当我们使用`docker ps -a` 指令进行查看, 发现容器已经退出，这是因为Docker容器后台运行后，无事可做，会自动自杀，因此就必须有一个前台进程。最佳的解决方案是,将你要运行的程序以前台进程的形式运行。

容器运行的命令如果不是那些一直挂起的命令（比如运行top，tail），就是会自动退出的。

3、查看后台运行的容器

```shell
docker ps [options]
 
#参数说明
-a :列出当前所有正在运行的容器+历史上运行过的
-l :显示最近创建的容器。
-n：显示最近n个创建的容器。
-q :静默模式，只显示容器编号。
--no-trunc :不截断输出。
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108151011307.png" alt="image-20201108151011307" style="zoom:80%;" />

4、退出容器

```shell
# 容器停止后退出
exit

# 容器不停止后退出
ctrl+P+Q
```

对于上面的第二种情况，我们有下面两种方式来进入正在运行的容器，进而可以进行命令行交互：

```shell
#其中bashshell（/bin/bash），在容器外打开新的终端，可以启动新的进程
docker exec -it 容器ID bashshell
#直接进入容器内启动命令终端，不会启动新的进程
docker attach 容器ID
```

5、启动和停止容器

```shell
#启动容器
docker start 容器ID/容器名
#重启容器
docker restart 容器ID/容器名
#停止容器
docker stop 容器ID/容器名
#强制停止容器
docker kill 容器ID/容器名
```

6、删除已经停止的容器

```shell
docker rm 容器ID
# 一次性删除多个容器
docker rm -f $(docker ps -qa)
```

7、查看容器日志

```shell
#以下面的方式在后台启动centos
#每两秒钟输出内容到日志文件中
docker run -d centos:6.8 /bin/sh -c "while true;do echo hello zzyy;sleep 2;done"

# 查看日志
docker logs [options] 容器ID
# 参数
-t：是加入时间戳，显示时间信息
-f:跟随最新的日志打印
--tail 数字：显示最后多少条
```

8、查看系统内运行进程

```shell
docker top 容器ID
```

9、查看系统内容运行细节

```shell
docker inspect 容器ID
```

10、将容器中的文件拷贝到宿主机上

```shell
docker cp 容器id:容器中路径 目的主机路径 
```

![image-20201108154227892](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108154227892.png)

总结：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108154403648.png" alt="image-20201108154403648" style="zoom:80%;" />

```
attach    Attach to a running container                 # 当前 shell 下 attach 连接指定运行镜像

build     Build an image from a Dockerfile              # 通过 Dockerfile 定制镜像

commit    Create a new image from a container changes   # 提交当前容器为新的镜像

cp        Copy files/folders from the containers filesystem to the host path   #从容器中拷贝指定文件或者目录到宿主机中

create    Create a new container                        # 创建一个新的容器，同 run，但不启动容器

diff      Inspect changes on a container's filesystem   # 查看 docker 容器变化

events    Get real time events from the server          # 从 docker 服务获取容器实时事件

exec      Run a command in an existing container        # 在已存在的容器上运行命令

export    Stream the contents of a container as a tar archive   # 导出容器的内容流作为一个 tar 归档文件[对应 import ]

history   Show the history of an image                  # 展示一个镜像形成历史

images    List images                                   # 列出系统当前镜像

import    Create a new filesystem image from the contents of a tarball # 从tar包中的内容创建一个新的文件系统映像[对应export]

info      Display system-wide information               # 显示系统相关信息

inspect   Return low-level information on a container   # 查看容器详细信息

kill      Kill a running container                      # kill 指定 docker 容器

load      Load an image from a tar archive              # 从一个 tar 包中加载一个镜像[对应 save]

login     Register or Login to the docker registry server    # 注册或者登陆一个 docker 源服务器

logout    Log out from a Docker registry server          # 从当前 Docker registry 退出

logs      Fetch the logs of a container                 # 输出当前容器日志信息

port      Lookup the public-facing port which is NAT-ed to PRIVATE_PORT    # 查看映射端口对应的容器内部源端口

pause     Pause all processes within a container        # 暂停容器

ps        List containers                               # 列出容器列表

pull      Pull an image or a repository from the docker registry server   # 从docker镜像源服务器拉取指定镜像或者库镜像

push      Push an image or a repository to the docker registry server    # 推送指定镜像或者库镜像至docker源服务器

restart   Restart a running container                   # 重启运行的容器

rm        Remove one or more containers                 # 移除一个或者多个容器

rmi       Remove one or more images             # 移除一个或多个镜像[无容器使用该镜像才可删除，否则需删除相关容器才可继续或 -f 强制删除]

run       Run a command in a new container              # 创建一个新的容器并运行一个命令

save      Save an image to a tar archive                # 保存一个镜像为一个 tar 包[对应 load]

search    Search for an image on the Docker Hub         # 在 docker hub 中搜索镜像

start     Start a stopped containers                    # 启动容器

stop      Stop a running containers                     # 停止容器

tag       Tag an image into a repository                # 给源中镜像打标签

top       Lookup the running processes of a container   # 查看容器中运行的进程信息

unpause   Unpause a paused container                    # 取消暂停容器

version   Show the docker version information           # 查看 docker 版本号

wait      Block until a container stops, then print its exit code   # 截取容器停止时的退出状态值
```

# 4、docker镜像

## 1、介绍

1、概述

镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码、运行时、库、环境变量和配置文件。

2、UnionFS（联合文件系统）

Union文件系统（UnionFS）是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下。

Union 文件系统是 Docker 镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。

特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录。

3、docker镜像加载原理

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108161517425.png" alt="image-20201108161517425" style="zoom:67%;" />

bootfs(boot file system)主要包含bootloader和kernel, bootloader主要是引导加载kernel, Linux刚启动时会加载bootfs文件系统，在Docker镜像的最底层是bootfs。这一层与我们典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成之后整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。

rootfs (root file system) ，在bootfs之上。包含的就是典型 Linux 系统中的 /dev, /proc, /bin, /etc 等标准目录和文件。rootfs就是各种不同的操作系统发行版，比如Ubuntu，Centos等等。

平时我们安装进虚拟机的CentOS都是好几个G，docker这里才200M左右：

对于一个精简的OS，rootfs可以很小，只需要包括最基本的命令、工具和程序库就可以了，因为底层直接用Host的kernel，自己只需要提供 rootfs 就行了。由此可见对于不同的linux发行版, bootfs基本是一致的, rootfs会有差别, 因此不同的发行版可以公用bootfs。

4、分层的镜像

最大的一个好处就是 - 共享资源。

比如：有多个镜像都从相同的 base 镜像构建而来，那么宿主机只需在磁盘上保存一份base镜像，

同时内存中也只需加载一份 base 镜像，就可以为所有容器服务了。而且镜像的每一层都可以被共享。

5、镜像的特点

Docker镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部，该层叫做“容器层”，“容器层”之下叫做“镜像层”。

## 2、镜像commit操作

1、介绍

```
docker commit 提交容器副本使之成为一个新的镜像。具体使用为：
docker commit -m="提交的描述信息" -a="作者" 容器ID 要创建的目标容器名:[tag]
```

2、操作

（1）拉取tomcat镜像并在本地运行

```shell
#-p 主机端口:docker容器（tomcat）端口，此时docker的8888对外暴露，并映射到其容器中的8080端口。
#-P 表示随机分配端口，docker run -it P tomcat
docker run -it -p 8888:8080 tomcat
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108172004654.png" alt="image-20201108172004654" style="zoom:80%;" />

外部访问通过8888端口就可以访问到docker内部的tomcat(8888)端口。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108172221927.png" alt="image-20201108172221927" style="zoom:80%;" />

上图为虚拟机上火狐浏览器，在本地浏览器上也可以访问到该端口。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108172307514.png" alt="image-20201108172307514" style="zoom:80%;" />

遇到的问题：

我们发现在docker容器中的tomcat/webapps中没有文档，我们需要进行下面的操作，将文档复制到webapps中：

```shell
root@7d6bd28c133f:/usr/local/tomcat# rm -rf webapps
root@7d6bd28c133f:/usr/local/tomcat# mv webapps.dist/ webapps
```

（2）故意删除上一步镜像生产tomcat容器的文档

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108172647191.png" alt="image-20201108172647191" style="zoom:80%;" />

（3）以当前没有docs文件的tomcat为模板，将其提交作为一个新镜像atguigu/comcat02

![image-20201108172834394](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108172834394.png)

（4）启动新镜像和原来的镜像对比

将正在运行的容器删除，然后再重新启动容器。

```shell
docker rm -f ${docker ps -qa}
```

我们删除容器后，在docker中会存在两个镜像：tomcat（阿里云上面拉取下面的），atguigu/tomcat02（我们新提交的），将其作为类生成对象后运行。

原来的tomcat，有docs：

![image-20201108172944829](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108172944829.png)

启动的atguigu/comcat02，没有docs:

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201108173011898.png" alt="image-20201108173011898" style="zoom:80%;" />

# 5、容器数据卷

## 1、介绍

1、概述

Docker的数据卷类似于我们的Redis里面的rdb和aof文件。

将运行的环境打包形成容器运行 ，但是我们对数据的要求希望是持久化的，同时也希望容器之间可以共享数据。

Docker容器产生的数据，如果不通过docker commit生成新的镜像，使得数据做为镜像的一部分保存下来，那么当容器删除后，数据自然也就没有了。因此，为了能保存数据在docker中我们使用卷。

2、作用

卷就是目录或文件，存在于一个或多个容器中，由docker挂载到容器，但不属于联合文件系统，因此能够绕过Union File System提供一些用于持续存储或共享数据的特性：

 卷的设计目的就是数据的持久化，完全独立于容器的生存周期，因此Docker不会在容器删除时删除其挂载的数据卷。

特点如下：

（1）数据卷可在容器之间共享或重用数据。

（2）卷中的更改可以直接生效。

（3）数据卷中的更改不会包含在镜像的更新中。

（4）数据卷的生命周期一直持续到没有容器使用它为止。

综上所述，容器数据卷的目的就是容器的持久化，容器间集成+共享数据 。

## 2、容器内添加

1、直接命令添加

（1）将宿主机目录挂载到容器目录上，实现二者之间的数据共享

```shell
docker run -it -v /mydataVolume（宿主机绝对路径目录）:/dataVolumeContainer（容器内目录） centos
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201109145331229.png" alt="image-20201109145331229" style="zoom:80%;" />

使用下面的指令来查看宿主机和容器之间的数据卷是否挂载成功：

```shell
docker inspect xx（运行容器ID）
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201109145617462.png" alt="image-20201109145617462" style="zoom:80%;" />

（2）容器和宿主机之间数据共享

在容器的路径/dataVolumeContainer和宿主机目录/mydataVolume下创建文件或者在文件中书写内容，二者的数据可以共享。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201109145918657.png" alt="image-20201109145918657" style="zoom:80%;" />

（3）容器使用exit停止退出后，在主机中修改数据，当我们使用下面的指令重启容器后，容器可以同步主机修改的数据。

```shell
# 查询关闭的容器ID
docker ps -l
#重新启动关闭的容器
docker start xx(容器ID)
#进入容器中,进入/dataVolumeContainer文件夹中查看数据同步状况
docker attach xx(容器ID)
```

（4）带权限的数据共享指令

```shell
# 为容器卷添加上只读权限，容器中不能写数据到数据卷，但是宿柱苣可以写操作。
docker run -it -v /mydataVolume:/dataVolumeContainer:ro centos

# 查看数据卷挂载情况
docker inspect xx（运行容器ID）
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201109150601589.png" alt="image-20201109150601589" style="zoom:80%;" />

在容器中不能够创建新的文件：

![image-20201109150714733](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201109150714733.png)

2、DockerFile添加

```
# 将DockerFile文件使用下面的方式进行类比
JavaEE Hello.java ---> Hello.class
Docker images ---> DockerFile
```

（1）在根目录下（/）新建一个mydocker文件并进入

在该目录创建一个数据卷文件Dockerfile，在该文件中使用VOLUME指令来给镜像添加一个或者多个数据卷：

```shell
# volume test
FROM centos
VOLUME ["/dataVolumeContainer1","/dataVolumeContainer2"]
CMD echo "finished,--------success1"
CMD /bin/bash
```

上面的VOLUME代码可以理解为：

```shell
VOLUME["/dataVolumeContainer1","/dataVolumeContainer2"]

# 和下面等价
docker run -it -v /host1:/dataVolumeContainer1 -v /host2:/dataVolumeContainer2 centos /bin/bash
```

在DockerFile中使用VOLUME这样的方式创建数据卷，是因为宿主机目录是依赖于特定宿主机的，并不能够保证在所有的宿主机上都存在这样的特定目录。因此，出于可移植和分享的考虑，用 `-v 主机目录:容器目录` 这种方法不能够直接在Dockerfile中实现。

（2）使用build指令生成镜像文件

```shell
docker build -f /mydocker/DockerFile -t zzyy/centos .

#参数说明
-f:指定要使用的Dockerfile路径
-t:tag,镜像的名字及标签
.:指定镜像构建过程中的上下文环境的目录
```

我们可以在docker的镜像中看到我们新生成的镜像文件：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201109155617438.png" alt="image-20201109155617438" style="zoom:80%;" />

（3）运行该镜像对应的容器

```shell
[root@hadoop101 ~]# docker run -it zzyy/centos
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201109155733353.png" alt="image-20201109155733353" style="zoom:80%;" />

（4）查看宿主机目录地址

```shell
docker inspect xx（容器ID）
```

![image-20201109160003676](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201109160003676.png)

当我们在宿主机或者容器卷目录地址中新建文件或者书写文件，可以发现二者可以共享数据。

## 3、数据卷容器

1、介绍

命名的容器挂载数据卷，其它容器通过挂载这个(父容器)实现数据共享，挂载数据卷的容器，称之为数据卷容器。

2、数据见传递共享数据（--volumes-from）

（1）总体介绍

以上一步意见的镜像zzyy/centos为模板并运行容器dc01/dc02/dc03，该镜像中已经具有数据卷/dataVolumeContainer1 和/dataVolumeContainer2。

（2）先启动一个父容器dc01

```shell
docker run -it --name dc01 zzyy/centos	
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201109161633470.png" alt="image-20201109161633470" style="zoom:80%;" />

（3）dc02/dc03继承dc01后启动

```shell
docker run -it --name dc02 --volumes-from dc01 zzyy/centos
```

在dc01的/dataVolumeContainer2路径下添加文件，可以在dc02/dc03的/dataVolumeContainer2中看到，数据在dc01/dc02/dc03之间共享了。

（4）删除父容器dc01

```shell
docker rm -f dc01
```

查看dc02/dc03的/dataVolumeContainer2目录，可以看到共享的数据仍然存在，在dc02或者dc03的/dataVolumeContainer2路径下添加文件，可以发现二者之间可以共享数据。

综上所述：容器之间配置信息的传递，数据卷的生命周期一直持续到没有容器使用它为止。

# 6、DockerFile解析

## 1、介绍

1、概述

DockerFile是用来构建Docker镜像的构建文件，是由一系列命令和参数构成的脚本。

构建的三个步骤如下：

```
（1）编写Dockerfile文件
（2）docker build(获取一个我们自定义的镜像)
（3）docker run

类似于下面的三个步骤：
（1）maven build
（2）编译成我们的jar包
（3）java -jar xx(微服务)
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201109163157783.png" alt="image-20201109163157783" style="zoom:80%;" />

2、Docker构建过程解析

（1）Dockerfile内容基础知识

+ 每条保留字指令都必须为大写字母且后面要跟随至少一个参数。
+ 指令按照从上往下，顺序执行。
+ #表示注释。
+ 每条指令都会创建一个新的镜像层，并对镜像进行提交。

（2）Docker执行Dockerfile的大致流程

+ docker从基础镜像运行一个容器。
+ 执行一条指令并对容器做出修改。
+ 执行类似docker commit的操作提交一个新的镜像层。
+ docker再基于刚提交的镜像运行一个新容器。
+ 执行dockerfile的下一条指令直到所有指令执行完毕。

（3）总结

从应用软件的角度来看，Dockerfile、Docker镜像与Docker容器分别代表软件的三个不同阶段：

+ Dockerfile是软件的原材料。

+ Docker镜像是软件的交付品。

+ Docker容器则可以认为是软件的运行态。

Dockerfile面向开发，Docker镜像成为交付标准，Docker容器则涉及部署与运维，三者缺一不可，合力充当Docker体系的基石。

![image-20201110145239859](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201110145239859.png)

下面详细的解释如下：

+ Dockerfile，需要定义一个Dockerfile，Dockerfile定义了进程需要的一切东西。Dockerfile涉及的内容包括执行代码或者是文件、环境变量、依赖包、运行时环境、动态链接库、操作系统的发行版、服务进程和内核进程(当应用进程需要和系统服务和内核进程打交道，这时需要考虑如何设计namespace的权限控制)等等。

+ Docker镜像，在用Dockerfile定义一个文件之后，docker build时会产生一个Docker镜像，当运行 Docker镜像时，会真正开始提供服务。

+ Docker容器，容器是直接提供服务的。

3、Dockefile的保留字指令

（1）FROM：基础镜像，当前镜像是属于那个镜像的。

（2）MAINTAINER：镜像维护者的姓名和邮箱地址。

（3）RUN：容器构建时需要运行的命令。

（4）EXPOSE：当前容器对外保留的端口。

（5）WORKDIR：指定在创建容器后，终端默认登录进来的工作目录，一个落脚点。

（6）ENV：用来在构建镜像过程中设置环境变量。

（7）ADD：将宿主机目录下的文件拷贝到镜像且该指令会自动处理URL和解压tar压缩包。

（8）COPY：类似ADD，拷贝文件和目录到镜像中。将从构建上下文目录中<源路径>的文件/目录复制到新的一层镜像内<目标路径>位置。

```shell
COPY src dest
COPY ["src","dest"]
```

（9）VOLUME：容器数据卷，用于数据的保存和持久化工作。

（10）CMD：指定一个容器启动时要运行的命令。Dockefile中可以有多个CMD指令，但只有最后一个生效，CMD会被docker run 之后的参数替换。

```shell
#CMD容器启动命令
#shell格式
CMD <命令>
#exec格式
CMD ["可执行文件","参数1"，"参数2",....]
```

（11）ENTRYPOINT：指定一个容器启动时要运行的命令，该指令的目的和CMD一样，都是在指定容器启动程序及参数。

（12）ONBUILD：当构建一个被继承的Dockerfile时的运行命令，父镜像在被子镜像继承后父镜像的onbuild会被触发。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201110151005739.png" alt="image-20201110151005739" style="zoom:67%;" />

## 2、案例

1、Base镜像（scratch）

Docker Hub中99%的镜像都是通过base镜像中安装和配置需要的软件构建出来的。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201110152027640.png" alt="image-20201110152027640" style="zoom:67%;" />

2、自定义镜像mycentos

（1）目标

自定义mycentos目的使我们自己的镜像具备如下：

+ 登陆后的默认路径
+ vim编辑器
+ 查看网络配置ifconfig支持

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201110152423131.png" alt="image-20201110152423131" style="zoom:67%;" />

（2）在/mydocker目录下创建Dockerfile文件

```shell
FROM centos
MAINTAINER xiaolun<xiaolun167@126.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "success--------------ok"
CMD /bin/bash
```

（3）构建新镜像

```shell
[root@hadoop101 mydocker]# docker build -f /mydocker/Dockerfile2 -t mycentos:1.3 .
```

（4）运行新镜像

```shell
[root@hadoop101 mydocker]# docker run -it mycentos:1.3
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201110154029425.png" alt="image-20201110154029425" style="zoom:67%;" />

（5）查看镜像的变更历史

```
docker history xx(镜像id)
```

![image-20201110154253446](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201110154253446.png)

3、CMD/ENTRYPOINT指令区别

（1）CDM会被docker run xx 的参数替换的理解

使用下面的指令启动tomcat，

```shell
[root@hadoop101 mydocker]# docker run -it -p 8888:8080 tomcat ls -l

# CMD后面的参数被ls -l替换,会进入到WORKDIR $CATALINA_HOME文件夹中，并将该文件夹下面的文件信息显示出来。
CMD ["catalina.sh", "run"]
CMD ls -l
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201110155332048.png" alt="image-20201110155332048" style="zoom:67%;" />

（2）ENTRYPOINT操作

+ curl命令解释

curl命令可以用来执行下载、发送各种HTTP请求，指定HTTP头部等操作。如果系统没有curl可以使用yum install curl安装，也可以下载安装。

使用命令：

```http
curl http://www.baidu.com
```

执行后，百度界面的的html就会显示在屏幕上了这是最简单的使用方法。如果下载的是HTML文档，那么缺省的将只显示文件头部，即HTML文档的header。要全部显示，请加参数 -i。

+ 制作CMD版的查询IP信息的容器

```shell
FROM centos
RUN yum install -y curl
CMD [ "curl", "-s", "http://ip.cn" ]
```

如果我们希望显示HTTP头信息，加上-i 参数，会报出可执行文件找不到的报错，executable file not found。

跟在镜像名后面的是 command，运行时会替换 CMD 的默认值。因此。这里的 -i 替换了原来的 CMD，而不是添加在下面指令的后面：

```shell
curl -s http://ip.cn
```

后面。而 -i 根本不是命令，所以自然找不到。那么如果我们希望加入 -i 这参数，我们就必须重新完整的输入这个命令：

```shell
docker run myip curl -s http://ip.cn -i
```

+ 制作ENTRYPOINT版的查询IP信息的容器

```shell
FROM centos
RUN yum install -y curl
ENTRYPOINT [ "curl", "-s", "http://ip.cn" ]
```

当指定ENTRYPOINT后，CMD的含义就发生了变换，不再是直接运行其命令，而是将CMD的内容作为参数传给ENTRYPOINT指令，如下所示：

```
ENTRYPOINT <CMD>
```

4、ONBUILD指令介绍

（1）操作父镜像

+ 编写父镜像的Dockerfile文件

```dockerfile
FROM centos
RUN yum install -y curl
CMD [ "curl", "-s", "http://ip.cn" ]
# 子类继承后build后会被触发
ONBUILD RUN echo "father onbuild -----------"
```

+ 构建父镜像

```shell
#myip_father在子类中出现
docker build -f /mydocker/Dockerfile3 -t myip_father .
```

（2）操作子镜像

+ 编写子镜像的Dockerfile文件

```dockerfile
# 基类为父镜像的名字：myip_father
FROM myip_father
RUN yum install -y curl
CMD [ "curl", "-s", "http://ip.cn" ]
```

+ 构建子镜像

```shell
docker build -f /mydocker/Dockerfile4 -t myip_son .
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201110173152483.png" alt="image-20201110173152483" style="zoom:67%;" />

5、自定义镜像Tomcat

（1）创建文件夹/mydockerfile/tomcat

在该目录下创建文件c.txt，然后将jdk（jdk-8u251-linux-x64.tar.gz）和tomcat（apache-tomcat-8.0.50.tar.gz）安装的压缩包拷贝到上一步的目录中。

（2）在/mydockerfile/tomcat目录下创建Dockerfile文件

```dockerfile
FROM         centos
MAINTAINER    xiaolun<xiaolun@126.com>
#把宿主机当前上下文的c.txt拷贝到容器/usr/local/路径下
COPY c.txt /usr/local/cincontainer.txt
#把java与tomcat添加到容器中
ADD apache-tomcat-8.0.50.tar.gz /usr/local/
ADD jdk-8u251-linux-x64.tar.gz /usr/local/
#安装vim编辑器
RUN yum -y install vim
#设置工作访问时候的WORKDIR路径，登录落脚点
ENV MYPATH /usr/local
WORKDIR $MYPATH
#配置java与tomcat环境变量
ENV JAVA_HOME /usr/local/jdk1.8.0_251
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-8.0.50
ENV CATALINA_BASE /usr/local/apache-tomcat-8.0.50
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin
#容器运行时监听的端口
EXPOSE  8080
#启动时运行tomcat
# ENTRYPOINT ["/usr/local/apache-tomcat-9.0.8/bin/startup.sh" ]
# CMD ["/usr/local/apache-tomcat-8.0.50/bin/catalina.sh","run"]
CMD /usr/local/apache-tomcat-8.0.50/bin/startup.sh && tail -F /usr/local/apache-tomcat-8.0.50/bin/logs/catalina.out
```

（3）根据Dockerfile文件构建镜像

```shell
docker build -t xiaoluntomcat8
```

（4）运行容器

```shell
docker run -d -p 9080:8080 --name myt9 -v /mydockerfile/tomcat/test:/usr/local/apache-tomcat-8.0.50/webapps/test -v /mydockerfile/tomcat/tomcat9logs/:/usr/local/apache-tomcat-8.0.50/logs --privileged=true xiaoluntomcat8
```

备注：

Docker挂载主机目录Docker访问出现cannot open directory .: Permission denied。

解决办法：在挂载目录后多加一个--privileged=true参数即可

（5）验证

输入网址：

```http
http://192.168.10.101:9080/
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201111144826245.png" alt="image-20201111144826245" style="zoom:67%;" />

正常访问tomcat官网。

（6）将测试的web服务test发布

文件的总体结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201111145108319.png" alt="image-20201111145108319" style="zoom:67%;" />

web.xml内容：

```xml
<?xml version="1.0" encoding="UTF-8"?>

<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"

  xmlns="http://java.sun.com/xml/ns/javaee"

  xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"

  id="WebApp_ID" version="2.5">

  <display-name>test</display-name>

</web-app>
```

a.jsp内容：

```xml
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>

<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">

<html>

  <head>

    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">

    <title>Insert title here</title>

  </head>

  <body>

    -----------welcome------------

    <%="i am in docker tomcat self "%>

    <br>

    <br>

    <% System.out.println("=============docker tomcat self");%>

  </body>

</html>
```

测试结果：
<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201111145244579.png" alt="image-20201111145244579" style="zoom:67%;" />

6、总结

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201111145330850.png" alt="image-20201111145330850" style="zoom:67%;" />