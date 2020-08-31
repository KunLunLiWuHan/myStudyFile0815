# 1 SpringCloud config分布式配置中心

## 1、介绍

（1）分布式系统面临的问题

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112037.png" alt="image-20200821115936393" style="zoom:67%;" />

（2）config是什么

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112038.png" alt="image-20200821120009728" style="zoom:67%;" />

（3）操作

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112039.png" alt="image-20200821120053815" style="zoom:67%;" />

（4）作用

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112040.png" alt="image-20200821120121502" style="zoom:67%;" />

（5）与Github整合配置

​		由于SpringCloud Config默认使用Git来存储配置文件（也有其它方式，比如支持svn和本地文件，但最推荐的还是Git，而且使用的是http/https访问的形式）。

（6）官网

https://cloud.spring.io/spring-cloud-static/spring-cloud-config/2.2.1.RELEASE/reference/html/

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112041.png" alt="image-20200821120223258" style="zoom:67%;" />

## 2、Config服务端配置与测试

### 1、运维人员管理文件

1、用你自己的账号在Github上新建一个名为sprincloud-config的新Repository。并将自己的新建的git地址复制。

2、本地硬盘上新建git仓库并clone远程代码。

```ini
D:\03Enviroment\02IDEA\02Code\cloud2020Git
```

以上两步类似于运维人员修改和管理配置文件的操作。

### 2、新建工程

项目名：cloud-config-center-3344。该模块为Spring Cloud的配置中心模块cloudConfig Center。

项目结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112042.png" alt="image-20200821150928941" style="zoom:67%;" />

1、添加pom

```xml
 <dependencies>
     	<!--配置中心服务器-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-config-server</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>com.xiaolun.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

2、添加yml

```yaml
server:
  port: 3344
spring:
  application:
    name: cloud-config-center #注册进Eureka服务的微服务名称
  cloud:
    config:
      server:
        git:
          uri:   https://github.com/KunLunLiWuHan/sprincloud-config.git #github上git仓库的名字 ，需要是前缀名为https的那个
          #搜索目录
          search-paths:
            - springcloud-config
      # 读取分支
      label: master
      
eureka:
  client:
    service-url:
      defaultZone:  http://localhost:7001/eureka
```

3、主启动类

```java
@SpringBootApplication
@EnableEurekaClient
@EnableConfigServer //开启服务中注解
public class ConfigCenterMain3344 {
	public static void main(String[] args) {
		SpringApplication.run(ConfigCenterMain3344 .class,args);
	}
}
```

4、windows下修改hosts文件，增加映射

```http
127.0.0.1 config-3344.com
```

5、测试

​		启动cloud-eureka-Server7001，然后cloud-config-center-3344，输入网址：http://config-3344.com:3344/master/config-dev.yml，可以从从Github上获取配置内容。

### 3、配置读取规则

1、官网

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112043.png" alt="image-20200821152016234" style="zoom:67%;" />

2、方式1-推荐使用

```ini
/{label}/{application}-{profile}.yml

#label：分支名
#application：服务名
#profiles：环境（dev/test/prod）
```

（1）master分支

```http
http://config-3344.com:3344/master/config-dev.yml
```

（2）dev分支

```http
http://config-3344.com:3344/dev/config-dev.yml
```

3、方式2

```ini
/{application}-{profile}.yml
```

如下：

```http
http://config-3344.com:3344/config-dev.yml
http://config-3344.com:3344/config-test.yml
```

4、方式3

```ini
/{application}-{profile}[/{label}]
```

如下：

```http
http://config-3344.com:3344/config/dev/master
```

## 3、Config客户端配置与测试

项目名：cloud-config-client-3355。

项目结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112044.png" alt="image-20200821152644164" style="zoom:67%;" />

1、添加pom

```xml
 <dependencies>
     	<!--config配置客户端-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>com.xiaolun.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

2、添加bootstap.yml

（1）介绍

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112045.png" alt="image-20200821152812311" style="zoom:67%;" />

（2）内容

```yaml
server:
  port: 3355

spring:
  application:
    name: config-client
  cloud:
  	#Config客户端配置
    config:
      label: master #分支名称
      name: config  #配置文件名称
      profile: dev  #读取后缀名称
      #上述三个综合：master分支上config-dev.yml的配置文件被读取成http://config-3344.com/master/config-dev.yml
      uri: http://localhost:3344
      
eureka:
  client:
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka
```

3、主启动类

```java
@SpringBootApplication
public class ConfigClientMain3355 {
	public static void main(String[] args) {
		SpringApplication.run(ConfigClientMain3355.class,args);
	}
}
```

4、业务类-controller

```java
@RestController
public class ConfigClientController {

	@Value("${config.info}")
	private String configInfo;

	@GetMapping("/configInfo")
	public String getConfigInfo(){
		return configInfo;
	}
}
```

注意，此时的`${config.info}`对应Github配置文件中config-dev.yml文件（只是我设定的是这个文件，也可以为其他），不然在启动该模块的时候加载会报错。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112046.png" alt="image-20200821153423140" style="zoom:67%;" />

5、测试

​		启动cloud-eureka-Server7001，然后cloud-config-center-3344，最后启动cloud-config-client-3355，首先输入网址：http://config-3344.com:3344/master/config-dev.yml为cloud-config-center-3344做自测，然后客户端cloud-config-client-3355输入网址：http://localhost:3355/configInfo来访问cloud-config-center-3344，可以从从Github上获取配置内容。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112047.png" alt="image-20200821153729551" style="zoom:50%;" />

## 4、Config客户端之动态刷新

### 1、介绍

​		Linux运维修改GitHub上的配置文件内容做调整，刷新cloud-config-center-3344，发现ConfigServer配置中心立刻响应，刷新cloud-config-client-3355，发现ConfigServer客户端没有任何响应，除非自己重启或者重新加载。避免每次更新配置都要重启客户端微服务cloud-config-client-3355，我们应该对该模块重新配置。

### 2、重构cloud-config-client-3355

1、pom中引入actuator监控

```xml
   <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
```

2、修改bootstrap.yml,暴露监控端口

```yaml
management:
  endpoints:
    web:
      exposure:
        include: "*"
```

3、业务类Controller添加注解@RefreshScope

```java
@RefreshScope
```

4、需要运维人员发送Post请求刷新3355（必须是Post请求）

```http
curl -X POST "http://localhost:3355/actuator/refresh"
```

5、测试

​		启动cloud-eureka-Server7001，然后cloud-config-center-3344，最后启动cloud-config-client-3355。运维人员修改Github，然后进行第4步的刷新cloud-config-client-3355操作（访问http://localhost:3355/configInfo），在客户端cloud-config-client-3355能够刷新到最新配置内容，避免了服务的重启。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112048.png" alt="image-20200821154943723" style="zoom:80%;" />

存在的问题：

​		当服务端较多的时候，每个微服务端都要执行一次post请求，手动刷新，不方便。

​		我们希望使用广播的方式，一次通知，处处生效，同时也可以实现精准打击的功能。

# 2  SpringCloud Bus 消息总线

## 1、介绍

（1）上一章讲解的加深和补充

Spring Cloud Bus配合Spring Cloud Config使用可以实现配置的动态刷新（分布式自动刷新配置功能）。

（2）概述

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112049.png" alt="image-20200821160310303" style="zoom:50%;" />



Bus支持两种消息代理：RabbitMQ和Kafka。

（3）作用

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112050.png" alt="image-20200821160459176" style="zoom:80%;" />



（4）总线介绍

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112051.png" alt="image-20200821160601399" style="zoom:80%;" />

B站视频：

```http
https://www.bilibili.com/video/av55976700?from=search&seid=15010075915728605208
```

## 2、RabbitMQ环境配置

1、安装Erlang

下载地址：http://erlang.org/download/otp_win64_21.3.exe

步骤：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112052.png" alt="image-20200821162919794" style="zoom:50%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112053.png" alt="image-20200821162941226" style="zoom:50%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112054.png" alt="image-20200821163007212" style="zoom:50%;" />

只需要注意安装的路径就可以了，其他为Next。

2、安装RabbitMQ

下载地址：https://dl.bintray.com/rabbitmq/all/rabbitmq-server/3.7.14/rabbitmq-server-3.7.14.exe

步骤：

（1）安装

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112055.png" alt="image-20200821163130586" style="zoom:67%;" />

（2）进入RabbitMQ安装目录下的sbin目录

```ini
D:\03Enviroment\16rabbitMQ\01install\rabbitmq_server-3.7.14\sbin
```

执行下面的命令，添加可视化插件：

```shell
rabbitmq-plugins enable rabbitmq_management
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112056.png" alt="image-20200821163318746" style="zoom:67%;" />

（3）启动RabbitMQ，在浏览器中输入下面的网址进行访问

```http
http://localhost:15672/
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112057.png" alt="image-20200821163455196" style="zoom:50%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112058.png" alt="image-20200821163802580" style="zoom:67%;" />

（4）输入默认的账号密码并登录即可

```ini
guest guest
```

## 3、动态刷新全局广播

1、新建cloud-config-client-3366

​		为了更好地演示广播效果，增加复杂度，我们以cloud-config-client-3355为模板再制作一个cloud-config-client-3366。

项目结构如下：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112059.png" alt="image-20200821180023662" style="zoom:50%;" />

2、设计思想

（1）利用消息总线触发一个客户端/bus/refresh,而刷新所有客户端的配置

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112100.png" alt="image-20200821180214431" style="zoom:50%;" />

（2）利用消息总线触发一个服务端ConfigServer的/bus/refresh端点,而刷新所有客户端的配置（推荐）

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112101.png" alt="image-20200821180332467" style="zoom:50%;" />

（3）推荐原因

（a）打破了微服务的职责单一性，因为微服务本身是业务模块，它本不应该承担配置刷新职责，破坏了微服务各节点的对等性。

（b）有一定的局限性。例如，微服务在迁移时，它的网络地址常常会发生变化，此时如果想要做到自动刷新，那就会增加更多的修改。

3、给cloud-config-center-3344配置中心服务端添加消息总线支持

（1）添加pom依赖

```xml
 <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-bus-amqp</artifactId>
        </dependency>
```

（2）修改yml配置

```yaml
server:
  port: 3344
spring:
  application:
    name: cloud-config-center #注册进Eureka服务的微服务名称
  cloud:
    config:
      server:
        git:
          uri:   https://github.com/KunLunLiWuHan/sprincloud-config.git #github上git仓库的名字
          #搜索目录
          search-paths:
            - springcloud-config
      # 读取分支
      label: master
  
  #添加位置1
  rabbitmq:
    host: localhost
    port: 5672
    username: guest
    password: guest

eureka:
  client:
    service-url:
      defaultZone:  http://localhost:7001/eureka

#添加位置2
# rabbit相关配置，暴露bus刷新配置的端点
management:
  endpoints: # bus刷新配置的端点
    web:
      exposure:
        include: 'bus-refresh'
```

4、给cloud-config-center-3355/3366客户端添加消息总线支持

（1）添加pom依赖

```xml
 <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-bus-amqp</artifactId>
        </dependency>
```

（2）修该yml配置

```yaml
server:
  port: 3355

spring:
  application:
    name: config-client
  cloud:
    config:
      label: master
      name: config
      profile: dev
      uri: http://localhost:3344
  
  #添加位置1
  rabbitmq:
    host: localhost
    port: 5672
    username: guest
    password: guest

eureka:
  client:
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka

#添加位置2
management:
  endpoints:
    web:
      exposure:
        include: "*"
```

5、测试

​	启动cloud-eureka-Server7001，然后cloud-config-center-3344，最后启动cloud-config-client-3355/3366。

（1）运维人员操作

修改Github上配置文件增加版本号，同时发送下面地post请求：

```http
curl -X POST "http://localhost:3344/actuator/bus-refresh"
```

达到一次发送，处处生效的目的。

（2）对于配置中心cloud-config-center-3344输入网址：http://config-3344.com/config-dev.yml，对于客户端cloud-config-client-3355/336输入网址：

http://localhost:3355/configInfo。对比运维人员发送命令之前和之后配置中心和客户端获取配置信息是否已经刷新。

## 4、动态刷新定点通知

1、需求

​		不想全部通知，只想定点通知，比如：只通知cloud-config-client-3355，不通知loud-config-client-3366。即指定具体某一个实例生效而不是全部。

```http
http://localhost:配置中心的端口号/actuator/bus-refresh/{destination}

#config-client:3355对应服务名称和端口号
curl -X POST "http://localhost:3344/actuator/bus-refresh/config-client:3355"
```

​		/bus/refresh请求不再发送到具体的服务实例上，而是发给config server并通过destination参数类指定需要更新配置的服务或实例。

## 5、总结

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112102.png" alt="image-20200821182349542" style="zoom:67%;" />