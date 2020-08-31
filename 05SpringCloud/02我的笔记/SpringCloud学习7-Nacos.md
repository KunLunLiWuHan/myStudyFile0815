# 1 SpringCloud Alibaba入门简介

1、Alibaba出现的原因

Spring Cloud Netflix项目进入维护模式

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112350.png" alt="image-20200822083536157" style="zoom:50%;" />

新组件功能将以其他替代的方式实现：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112351.png" alt="image-20200822083655086" style="zoom:50%;" />

2、概述

官网：https://github.com/alibaba/spring-cloud-alibaba/blob/master/README-zh.md

3、作用

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112352.png" alt="image-20200822083818255" style="zoom:67%;" />

4、操作


Sentinel：把流量作为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性。

Nacos：一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台。

RocketMQ：一款开源的分布式消息系统，基于高可用分布式集群技术，提供低延时的、高可靠的消息发布与订阅服务。

Dubbo：Apache Dubbo™ 是一款高性能 Java RPC 框架。

Seata：阿里巴巴开源产品，一个易于使用的高性能微服务分布式事务解决方案。

Alibaba Cloud ACM：一款在分布式架构环境中对应用配置进行集中管理和推送的应用配置中心产品。

Alibaba Cloud OSS: 阿里云对象存储服务（Object Storage Service，简称 OSS），是阿里云提供的海量、安全、低成本、高可靠的云存储服务。您可以在任何应用、任何时间、任何地点存储和访问任意类型的数据。

Alibaba Cloud SchedulerX: 阿里中间件团队开发的一款分布式任务调度产品，提供秒级、精准、高可靠、高可用的定时（基于 Cron 表达式）任务调度服务。

Alibaba Cloud SMS: 覆盖全球的短信服务，友好、高效、智能的互联化通讯能力，帮助企业迅速搭建客户触达通道。

5、SpringCloud alibaba学习资料获取

网址：https://spring.io/projects/spring-cloud-alibaba#overview

API：https://spring-cloud-alibaba-group.github.io/github-pages/greenwich/spring-cloud-alibaba.html

# 2 SpringCloud Alibaba Nacos服务注册和配置中心

## 1 介绍

1、概述

​		Nacos（Nacos：Dynamic Naming and Configuration Service）的前四个字母分别为Naming和Configuration的前两个字母，最后的s为Service。

​		一个更易于构建云原生应用的动态服务发现，配置管理和服务管理中心，Nacos就是注册中心+配置中心的组合，等价于Nacos = Eureka+Config+Bus。

2、下载地址

https://github.com/alibaba/Nacos

https://nacos.io/zh-cn/index.html

3、各种注册中心比较

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112353.png" alt="image-20200822084933671" style="zoom:67%;" />

## 2 安装并运行Nacos

1、本地Java8+Maven环境已经OK，进入下面的官网下载

https://github.com/alibaba/nacos/releases/tag/1.1.4

2、解压安装包，直接运行bin目录下的startup.cmd

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112354.png" alt="image-20200822090956847" style="zoom:80%;" />

3、命令运行成功后直接访问下面的网址，输入默认的账号和密码nacos

​		http://localhost:8848/nacos

​		进入下面的界面

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112355.png" alt="image-20200822091051942" style="zoom:80%;" />

## 3 Nacos作为服务注册中心演示

### 1、基于Nacos的服务提供者

项目名：cloudalibaba-provider-payment9001/9002。

项目结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112356.png" alt="image-20200822101901662" style="zoom:50%;" />

1、添加pom

（1）父pom

```xml
 <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>2.1.0.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
```

（2）本模块pom

```xml
 <dependencies>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
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
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.62</version>
        </dependency>
    </dependencies>
```

2、添加yml

```yaml
server:
  port: 9003 #9001端口被占用

spring:
  application:
    name: nacos-payment-provider
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #配置Nacos地址

management:
  endpoints:
    web:
      exposure:
        include: '*'
```

3、主启动类

```java
@EnableDiscoveryClient //可以注册进nacos
@SpringBootApplication
public class PaymentMain9001 {
	public static void main(String[] args) {
		SpringApplication.run(PaymentMain9001.class,args);
	}
}
```

4、业务类-controller

```java
@RestController
public class PaymentController {
   @Value("${server.port}")
   private String serverPort;

   @GetMapping(value = "/payment/nacos/{id}")
   public String getPayment(@PathVariable("id") Integer id) {
      return "nacos registry, serverPort: " + serverPort + "\t id" + id;
   }
}
```

5、以同样方式建立cloudalibaba-provider-payment9002。

6、测试

​		依次启动cloudalibaba-provider-payment9001/9012，浏览器输入下面的网址：
​		http://localhost:9011/payment/nacos/1

可以正常访问cloudalibaba-provider-payment9001中的服务。

### 2、基于Nacos的服务消费者

项目名称：cloudalibaba-consumer-nacos-order83

项目结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112357.png" alt="image-20200822110915068" style="zoom:50%;" />

1、添加pom

```xml
  <dependencies>
        <!--SpringCloud ailibaba nacos -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
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

nacos支持负载均衡的原因：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112358.png" alt="image-20200822111055167" style="zoom:67%;" />

2、添加yml文件

```yaml
server:
  port: 83


spring:
  application:
    name: nacos-order-consumer
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848


service-url:
  nacos-user-service: http://nacos-payment-provider
```

3、主启动

```java
@EnableDiscoveryClient
@SpringBootApplication
public class OrderNacosMain83
{
    public static void main(String[] args)
    {
        SpringApplication.run(OrderNacosMain83.class,args);
    }
}
```

4、业务类

（1）config

```java
@Configuration
public class ApplicationContextConfig {
   @Bean
   @LoadBalanced
   public RestTemplate getRestTemplate() {
      return new RestTemplate();
   }
}
```

（2）controller

```java
@RestController
@Slf4j
public class OrderNacosController
{
	@Resource
	private RestTemplate restTemplate;

	@Value("${service-url.nacos-user-service}")
	private String serverURL;

	@GetMapping(value = "/consumer/payment/nacos/{id}")
	public String paymentInfo(@PathVariable("id") Long id)
	{
		return restTemplate.getForObject(serverURL+"/payment/nacos/"+id,String.class);
	}

}
```

5、测试

​		依次启动cloudalibaba-provider-payment9001/9012，cloudalibaba-consumer-nacos-order83，在nacos的可视化界面中，可以看到：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112359.png" alt="image-20200822111356555" style="zoom:50%;" />

输入下面网址：

http://localhost:83/consumer/payment/nacos/13

实现cloudalibaba-consumer-nacos-order83访问cloudalibaba-provider-payment9001/9002，轮询负载。

### 3、服务中心对比

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112400.png" alt="image-20200822112102822" style="zoom:50%;" />

Nacos支持AP和CP模式的切换：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112401.png" alt="image-20200822112132697" style="zoom:67%;" />

```http
curl -X PUT '$NACOS_SERVER:8848/nacos/v1/ns/operator/switches?entry=serverMode&value=CP'
```

## 4、Nacos作为服务配置中心演示

### 1、新建cloudalibaba-config-nacos-client3377

项目结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112402.png" alt="image-20200822144758391" style="zoom:50%;" />

1、添加pom

```xml
 <dependencies>
        <!--nacos-config-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
        </dependency>
        <!--nacos-discovery-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <!--web + actuator-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--一般基础配置-->
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

（1）application.yml

```yaml
spring:
  profiles:
    active: dev
```

（2）bootstrap.yml

```yaml
server:
  port: 3377

spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #服务注册中心地址
      config:
        server-addr: localhost:8848 #配置中心地址
        file-extension: yaml #指定yaml格式的配置


#${spring.application.name}-${spring.profile.active}-&{spring.cloud.nacos.config.file-extension}
```

3、主启动类

```java
@EnableDiscoveryClient
@SpringBootApplication
public class NacosConfigClientMain3377 {
	public static void main(String[] args) {
		SpringApplication.run(NacosConfigClientMain3377.class, args);
	}
}
```

4、业务类-controller

```java
@RestController
@RefreshScope //支持nacos的动态刷新功能
public class ConfigClientController {
	@Value("${config.info}")
	private String configInfo;

	@GetMapping("/config/info")
	public String getConfigInfo() {
		return configInfo;
	}
}
```

5、在Nacos中添加配置信息DataId

（1）Nacos中的DataId的组成格式与SpringBoot配置文件中的匹配规则

官网： 

​		https://nacos.io/zh-cn/docs/quick-start-spring-cloud.html

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112403.png" alt="image-20200822145209049" style="zoom:67%;" />

公式：

```yaml
${spring.application.name}-${spring.profile.active}-&{spring.cloud.nacos.config.file-extension}

#prefix默认为spring.application.name的值
#spring.profile.active既为当前环境对应的profile,可以通过配置项spring.profile.active 来配置
#file-exetension为配置内容的数据格式，可以通过配置项spring.cloud.nacos.config.file-extension配置
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112404.png" alt="image-20200822145702282" style="zoom:50%;" />

（2）在默认的Group中新建Data Id

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112405.png" alt="image-20200822145408467" style="zoom:50%;" />

在下面的配置内容中增加下面的内容：

```yaml
config:
    info: nacos config center,version = 1
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112406.png" alt="image-20200822145436154" style="zoom:50%;" />

6、测试

​		启动前需要在nacos客户端-配置管理-配置管理栏目下有没有对应的yaml配置文件，然后再启动cloud-config-nacos-client3377，输入下面的网址调用接口查看配置信息：

```http
http://localhost:3377/config/info
```

在浏览器界面显示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112407.png" alt="image-20200822145937218" style="zoom:67%;" />

​		然后，修改下Nacos中的yaml配置文件，再次调用查看配置的接口，就会发现配置已经刷新-自带动态刷新。

### 2、分类配置

1、遇到的问题

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112408.png" alt="image-20200822150140776" style="zoom:50%;" />

2、Namespace+Group+Data ID三者关系

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112409.png" alt="image-20200822150254900" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112410.png" alt="image-20200822150306364" style="zoom:50%;" />

3、DataID方案

（1）指定spring.profile.active和配置文件的DataID来使不同环境下读取不同的配置。

​		接下来我们新建dev/test配置DataID，此时就有这样的布局：默认空间+默认分组+新建dev和test两个DataID。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112411.png" alt="image-20200822151033675" style="zoom:50%;" />

（2）测试

​		通过spring.profile.active属性来进行多环境下配置文件的读取：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112412.png" alt="image-20200822151149909" style="zoom:50%;" />

输入网址：

```http
http://localhost:3377/config/info
```

能够读取nacos-config-client-test.yaml文件中的配置。

4、Group方案

通过Group来实现环境区分。

（1）新建Group

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112413.png" alt="image-20200822152118034" style="zoom:50%;" />

在config下增加一条group的配置即可，比如这里配置为TEST_GROUP。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112414.png" alt="image-20200822152210889" style="zoom:50%;" />

（2）bootstrap+application文件中要开启对应配置

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112415.png" alt="image-20200822152342933" style="zoom:50%;" />

5、Namespace方案

（1）新建dev/test的Namespace

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112416.png" alt="image-20200822152539628" style="zoom:50%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112417.png" alt="image-20200822152609431" style="zoom:67%;" />

（2）回到服务管理-服务列表查看

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112418.png" alt="image-20200822152737245" style="zoom:50%;" />

（3）在dev命名空间添加如下的配置

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112419.png" alt="image-20200822153007387" style="zoom:50%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112420.png" alt="image-20200822153204576" style="zoom:50%;" />

（4）bootstrap+application文件中要开启对应配置

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112421.png" alt="image-20200822153225197" style="zoom:50%;" />

## 5、Nacos集群和持久化配置（重要）

### 1、架构说明

1、官网架构图

https://nacos.io/zh-cn/docs/cluster-mode-quick-start.html

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112422.png" alt="image-20200822153354472" style="zoom:50%;" />

理解：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112423.png" alt="image-20200822153500851" style="zoom: 67%;" />

2、说明

官网：

https://nacos.io/zh-cn/docs/deployment.html

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112424.png" alt="image-20200822153549909" style="zoom:50%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112425.png" alt="image-20200822153617755" style="zoom:67%;" />

![image-20200822153605457](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112426.png)

### 2、持久化切换配置

​		Nacos默认自带的是嵌入式数据库derby，我们需要将数据从derby到mysql进行切换配置，步骤如下：

1、D:\03Enviroment\18Nacos\nacos\conf目录下找到sql脚本nacos-mysql.sql，然后放到Navicat中执行脚本。

​		首先，新建数据库nacos_config，然后新建查询即可。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112427.png" alt="image-20200822195807757" style="zoom:80%;" />

2、D:\03Enviroment\18Nacos\nacos\conf目录下找到application.properties，添加数据库配置

```ini
spring.datasource.platform=mysql

db.num=1
db.url.0=jdbc:mysql://127.0.0.1:3306/nacos_config?serverTimezone=GMT%2B8&characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true
db.user=root
db.password=123
```

3、启动nacos

我使用的命令为：

```shell
D:\03Enviroment\18Nacos\nacos\bin>startup.cmd -m standalone

#单单执行这个会报错
startup.cm
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112428.png" alt="image-20200822200134678" style="zoom:50%;" />

​		启动nacos，可以看到是个全新的空记录界面，在里面新添加一个DataID，在数据库中会有相应的显示。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112429.png" alt="image-20200822200311129" style="zoom:50%;" />

### 3、Linux版Nacos+MySQL生产环境配置

需求：1个nginx+3个nacos注册中心+1个mysql。

1、nacos的下载安装。

安装到的集群位置如下：

```ini
192.168.10.101/myncos/nacos
```

2、Linux服务器上mysql数据库配置

（1）启动mysql服务，然后使用下面的命令创建数据库

```mysql
CREATE DATABASE IF NOT EXISTS nacos_config;
```

（2）运行mysql的脚本文件nacos-mysql.sql

```mysql
> source /mynacos/nacos/conf/nacos-mysql.sql
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112430.png" alt="image-20200824110340896" style="zoom:50%;" />

3、nacos配置

（1）配置/mynacos/nacos/conf/application.properties，将下面的内容粘贴

```properties
spring.datasource.platform=mysql
 
db.num=1
db.url.0=jdbc:mysql://127.0.0.1:3306/nacos_config?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true
db.user=root
db.password=123
```

（2）nacos的集群配置mynacos/nacos/conf/cluster.conf

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112431.png" alt="image-20200824110722869" style="zoom: 67%;" />

因为是集群配置，因此，不能写127.0.0.1。

（3）编辑Nacos的启动脚本/mynacos/nacos/bin/startup.sh，使它能够接受不同的启动端

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112432.png" alt="image-20200824110928646" style="zoom:50%;" />

修改内容如下：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112433.png" alt="image-20200824111044283" style="zoom:50%;" />

添加下面的内容：

```ini
-Dserver.port=${SERVER_PORT}
```

![image-20200824111106597](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112434.png)

使用下面的方式启动：

```ini
./startup.sh -p 3333
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112435.png" alt="image-20200824111205846" style="zoom:50%;" />

执行下面的命令查看启动的nacos集群数：

```shell
ps -ef|grep nacos|grep -v grep|wc -l
```

我们下面的目录查看启动情况：

```shell
vim /mynacos/nacos/logs/start.out
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112436.png" alt="image-20200824111420611" style="zoom:80%;" />

4、配置Nginx，由它作为负载均衡器

（1）修改nginx配置文件/usr/local/nginx/conf/nginx.conf

```ini
upstream cluster{
        server 127.0.0.1:3333;
        server 127.0.0.1:4444;
        server 127.0.0.1:5555;
   }
    server {
        listen       1111;
        server_name  localhost;

        location / {
            proxy_pass http://cluster;
        }
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112437.png" alt="image-20200824111608315" style="zoom:67%;" />

（2）按照指代文件启动

```shell
[root@hadoop101 nginx]# ./sbin/nginx -c conf/nginx.conf
```

5、测试

（1）通过nginx访问nacos

```http
//192.168.10.101为虚拟ip,最后添加nacos,不然会报错。注意，只能启动一个该窗口，不然会报错。
https://192.168.10.101:1111/nacos
```

（2）在nacos界面中新建一个配置

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112438.png" alt="image-20200824112256836" style="zoom:50%;" />



<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112439.png" alt="image-20200824112321527" style="zoom:67%;" />

（3）微服务cloudalibaba-provider-payment9012启动注册进nacos集群

修改yml配置文件：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112440.png" alt="image-20200824112434167" style="zoom:67%;" />

```yaml
server-addr: 192.168.10.101:1111 
```

启动该服务，我们可以在网页中看着该服务已经注册到nacos中了：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112441.png" alt="image-20200824112519549" style="zoom:67%;" />

6、总结

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112442.png" alt="image-20200824112545610" style="zoom:50%;" />