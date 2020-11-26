# 1、Docker常用安装

## 1、安装mydql

1、总体步骤

（1）搜索镜像

```shell
docker search xx(镜像名)
```

（2）拉取镜像

```
docker pull xx(镜像名)
```

（3）查看镜像

（4）启动镜像

（5）停止镜像

（6）移除镜像

2、操作

（1）在搜索镜像后，拉取mysql:5.6镜像

```shell
docker pull mysql:5.6
```

（2）运行mysql镜像

```shell
docker run -p 12306:3306 --name mysql -v /xiaolun/mysql/conf:/etc/mysql/conf.d -v /xiaolun/mysql/logs:/logs -v /xiaolun/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123 -d mysql:5.6
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201112194950871.png" alt="image-20201112194950871" style="zoom:67%;" />

在数据库中创建表：

```mysql
mysql> create table stu(id int,name varchar(20));
insert into stu values(1,"lkl");
```

（3）Datagrip连接

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201112195200159.png" alt="image-20201112195200159" style="zoom:67%;" />

## 2、安装redis

1、拉取镜像

```shell
docker pull redis:3.2
```

2、运行镜像

```shell
docker run -p 6379:6379 -v /xiaolun/myredis/data:/data -v /xiaolun/myredis/conf/redis.conf:/usr/local/etc/redis/redis.conf  -d redis:3.2 redis-server /usr/local/etc/redis/redis.conf --appendonly yes
```

然后使用下面的命令添加一个redis.conf文件，在该文件中包含redis的基本配置。

```shell
/xiaolun/myredis/conf/redis.conf
vim /xiaolun/myredis/conf/redis.conf/redis.conf
```

3、测试连接

```shell
docker exec -it xx(运行着Rediis服务的容器ID) redis-cli
```

4、测试持久化文件

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201112200802172.png" alt="image-20201112200802172" style="zoom:67%;" />

# 2、本地镜像发布到阿里云

## 1、发布镜像

1、本地镜像发送到阿里云流程图

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201112204241644.png" alt="image-20201112204241644" style="zoom:67%;" />

2、生成镜像

（1）方法1：使用前面介绍的Dockerfile

（2）方法2：从容器中创建一个新的镜像，本文采取这种形式。

```shell
#-a :提交的镜像作者；
#-m :提交时的说明文字；
docker commit -a xx -m xx 容器ID 仓库[:tag]

#举例
docker commit -a xiaolun -m "upload redis:3.3 from 3.2" 62dd984ebbf1 myredis:1.4
```

（3）本地镜像原型

运行着的redis:3.2。

（4）创建仓库的镜像

登录进阿里云网址：

```http
https://homenew.console.aliyun.com/
```

点击管理，创建命名空间`xiaolun_namespace`和仓库名称：

![image-20201112205120187](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201112205120187.png)

（5）使用下面的命令将镜像推送到阿里云上

```
$ sudo docker login --username=武汉小伦 registry.cn-hangzhou.aliyuncs.com
$ sudo docker tag [ImageId](上文生成的镜像) registry.cn-hangzhou.aliyuncs.com/xiaolun_namespace/xialun_redis1.4:[镜像版本号](1.4.1)
$ sudo docker push registry.cn-hangzhou.aliyuncs.com/xiaolun_namespace/xialun_redis1.4:[镜像版本号](1.4.1)
```

（6）公有云查询镜像

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201112205509099.png" alt="image-20201112205509099" style="zoom:50%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201112205552285.png" alt="image-20201112205552285" style="zoom:67%;" />

（7）将阿里云的镜像下载到本地

```shell
# docker pull 容器公网地址:版本号
docker pull registry.cn-hangzhou.aliyuncs.com/xiaolun_namespace/xialun_redis1.4:1.4.1
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201112205749561.png" alt="image-20201112205749561" style="zoom:67%;" />

# 3、SpringBoot微服务打包Docker镜像

1、创建SpringBoot项目

项目结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201114091633527.png" alt="image-20201114091633527" style="zoom: 67%;" />

（1）pom文件

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <version>2.3.1.RELEASE</version>
    </dependency>
</dependencies>
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <!--这里写上main方法所在类的路径-->
            <configuration>
                <mainClass>com.xiaolun.MainApp</mainClass>
            </configuration>
            <executions>
                <execution>
                    <goals>
                        <goal>repackage</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

（2）业务类

```java
package com.xiaolun.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @time: 2020-11-12 21:04
 * @author: likunlun
 * @description: controller测试
 */
@RestController
public class HelloController {
	@RequestMapping("/hello")
	public String hello() {
		return "hello,xiaolun!";
	}
}
```

（3）主启动类

```java
package com.xiaolun;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @time: 2020-11-12 21:00
 * @author: likunlun
 * @description: 启动类
 */
@SpringBootApplication
public class MainApp {
	public static void main(String[] args) {
		SpringApplication.run(MainApp.class, args);
	}
}
```

2、打jar包运行

```
clear -> package
```

3、编写Dockerfile文件

```dockerfile
FROM java:8
COPY *.jar /app.jar
CMD ["--server.port=8080"]
EXPOSE 8080
ENTRYPOINT ["java","-jar","app.jar"]
```

4、将打成的jar包和Dockerfile上传到服务器

5、构建镜像

```shell
[root@hadoop101 myidea]# docker build -t xiaolunjar1 .
```

6、发布运行

```shell
[root@hadoop101 myidea]# docker run -d -P --name xiaolun-springboot-web xiaolunjar1
```

![image-20201114092303001](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201114092303001.png)

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201114092312681.png" alt="image-20201114092312681" style="zoom:67%;" />

# 4、Docker网络

## 1、Docker0网络介绍

1、查看网址地址

```shell
ip addr
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201114101715812.png" alt="image-20201114101715812" style="zoom:80%;" />

2、查看容器内部网络地址

```shell
docker-tomcat docker exec -it tomcat01 ip addr
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201114101905024.png" alt="image-20201114101905024" style="zoom:80%;" />

会得到一个由docker0网络分配的地址 eth0@if8 。

我们每次启动一个docker容器，docker就会给docker容器分配一个ip，ip地址使用的docker0桥接模式，使用的技术是veth-pair技术。

![image-20201114102119715](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201114102119715.png)

我们发现这个容器生成的网卡都是一对对的。veth-pair 就是一对的虚拟设备接口，他们都是成对出现的，一端连着协议，一端彼此相连。正因为有这个特性 veth-pair 充当一个桥梁，连接各种虚拟网络设备的OpenStac，Docker容器之间的连接，OVS的连接，都是使用evth-pair技术。

3、测试tomcat01和tomcat02的ping指令。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201114102509864.png" alt="image-20201114102509864" style="zoom:80%;" />

4、总结

tomcat01和tomcat02使用的是一个公用路由器docker0。

所有的容器不指定网络的情况下，都是docker0路由的，docker会给我们的容器分配一个默认的可用ip。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201114102603860.png" alt="image-20201114102603860" style="zoom:80%;" />

# 5、Docker Compose

## 1、介绍

1、概述

DockerFile build 手动操作，只针对单个容器，Docker Compose可以轻松的管理容器。

2、安装

（1）执行下面的命令进行安装

```shell
#官方下载
sudo curl -L "https://github.com/docker/compose/releases/download/1.26.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
#授权
chmod +x /usr/local/bin/docker-compose
# 加速下载
curl -L https://get.daocloud.io/docker/compose/releases/download/1.5.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
#授权
chmod +x /usr/local/bin/docker-compose
```

（2）测试安装状态

```shell
docker-compose version
```

3、编写规则

（1）快速入门案例

```http
https://docs.docker.com/compose/gettingstarted/
```

（2）流程

+ 创建一个文件app.py。
+ 创建Dockerfile为应用打包成镜像。
+ 创建Docker-compose.yaml文件，定义整个服务所需要的环境功能。
+ 启动compose项目

```shell
docker-compose up
```

（3）yaml规则

一共有下面的三层结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201114135844592.png" alt="image-20201114135844592" style="zoom:67%;" />

我们还要注意下面的依赖关系：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201114140032945.png" alt="image-20201114140032945" style="zoom:67%;" />





