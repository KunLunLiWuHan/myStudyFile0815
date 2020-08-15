# 1 介绍

技术要求

JUC+JWM+git、github+Nginx+RabbitMQ

​		微服务架构是一种架构模式，它提倡将单一应用程序划分为一组小的服务，服务之间相互协调，互相配合，为用户提供最终价值。每个服务运行在独立的进程中，服务和服务间采用轻量级的通信机制互相协作（通常基于HTTP/RESTful API）。每个服务围绕着具体业务进行构建，并且能够被独立地部署到生产环境、类生产环境等。另外，应当尽量避免统一地、集中式的服务管理机制，对具体的一个服务而言，应根据业务的要求，选择合适的语言、工具等进行构建。

​		SpringCloud是分布式微服务架构的一站式解决方案，是多种微服务架构落地技术的集合体，俗称微服务全家桶。

​		1. 牵涉到的内容如下：

+ 服务注册与发现。
+ 服务调用。
+ 服务熔断。
+ 负载均衡
+ 服务降级。
+ 服务消息队列。
+ 配置中心管理。
+ 服务网关。
+ 服务监控。
+ 全链路追踪。
+ 自动化构建部署。
+ 服务定时任务调度操作。

2. 京东系统网络架构

<img src="SpringCloud学习.assets\image-20200709215645948.png" alt="image-20200709215645948" style="zoom:80%;" />

3. 微服务技术栈

<img src="SpringCloud学习.assets\image-20200709220006945.png" alt="image-20200709220006945" style="zoom:80%;" />

3. 主要学习的技术

<img src="SpringCloud学习.assets\image-20200709215907790.png" alt="image-20200709215907790" style="zoom:80%;" />

## 1.1 Boot和Cloud版本选型

1. 通过官网发现，Boot官网强烈推荐我们将其升级到2.X版本。

网址：https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Release-Notes

<img src="SpringCloud学习.assets\image-20200709215432199.png" alt="image-20200709215432199" style="zoom:80%;" />

2. 二者的版本依赖

网址：https://spring.io/projects/spring-cloud#overview

<img src="SpringCloud学习.assets\image-20200709220301841.png" alt="image-20200709220301841" style="zoom:80%;" />

**更详细的版本对应方法**

网址：https://start.spring.io/actuator/info

<img src="SpringCloud学习.assets\image-20200709220754277.png" alt="image-20200709220754277" style="zoom:80%;" />

3. 依赖关系确定

同时使用boot和cloud，需要照顾cloud，应该由cloud来确定boot版本。

<img src="SpringCloud学习.assets\image-20200709221224242.png" alt="image-20200709221224242" style="zoom:80%;" />

因此，定稿版为：

```ini
-- 主要是和阳哥保持一致
cloud Hoxton.SR1
boot 2.2.2.RELEASE
cloud alibaba 2.1.0.RELEASE
Java 8
Maven 3.5及以上
Mysql 5.7及以上
```

## 1.2 SpringCloud升级,部分组件停用

```ini
1,服务注册中心：Eureka停用,可以使用zookeeper、Consul和Nacos(推荐)作为服务注册中心。
2,服务调用1：Ribbon准备停更,代替为LoadBalance
3,服务调用2：Feign改为OpenFeign
4,服务降级：Hystrix停更,改为resilence4j或者阿里巴巴的sentienl(推荐)
5.服务网关：Zuul改为gateway
6,服务配置：Config改为Nacos
7,服务总线：Bus改为Nacos
```

# 2 新建父工程Project 空间

1. 创建项目

<img src="SpringCloud学习.assets\image-20200709225304456.png" alt="image-20200709225304456" style="zoom: 50%;" />

2. 确定字符编码

<img src="SpringCloud学习.assets\image-20200709230044133.png" alt="image-20200709230044133" style="zoom:50%;" />

3. 注解生效激活

<img src="SpringCloud学习.assets\image-20200709230242581.png" alt="image-20200709230242581" style="zoom:50%;" />

4. Java编译版本选8

<img src="SpringCloud学习.assets\image-20200709230424600.png" alt="image-20200709230424600" style="zoom:50%;" />

5. File Type过滤

<img src="SpringCloud学习.assets\image-20200709230741761.png" alt="image-20200709230741761" style="zoom: 50%;" />

5. Maven跳过单元测试

<img src="SpringCloud学习.assets\image-20200709233325879.png" alt="image-20200709233325879" style="zoom:50%;" />

6. 父工程创建完成后，执行`mvn:install`将父工程发布到仓库中方便子工程继承。

<img src="SpringCloud学习.assets\image-20200709233547164.png" alt="image-20200709233547164" style="zoom:80%;" />

## 2.1 创建子模块Payment

**步骤**

+ 创建module

+ 改pom
+ 写yml
+ 主启动
+ 业务类：建表SQL,entities,dao（包括实现的方式：mapper）,service,controller。

<img src="SpringCloud学习.assets\支付消费模块图.jpg" alt="支付消费模块图" style="zoom:80%;" />

### 2.1.1 业务类

**项目结构**

<img src="SpringCloud学习.assets\image-20200710110140113.png" alt="image-20200710110140113" style="zoom:50%;" />

#### 	1、sql

```sql
CREATE TABLE `payment` (
  `id` bigint NOT NULL AUTO_INCREMENT COMMENT 'ID',
  `serial` varchar(200) CHARACTER SET utf8 COLLATE utf8_general_ci DEFAULT '' COMMENT '支付流水号',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

#### 	2、实体类

```java
@Data
@NoArgsConstructor
@AllArgsConstructor //主实体
public class Payment implements Serializable {
    private Long id; //对应sql中的bignit
    private String serial;
}

@Data
@NoArgsConstructor
@AllArgsConstructor //返回前端通用的json实体块
public class CommonResult<T> {
    //404 not found
    private Integer code; //类似404的一个编码，先判断是否为200
    private String message; //传递的消息(success)
    private T date; //数据

    //有可能data为空。
    public CommonResult(Integer code, String message) {
        this.code = code;
        this.message = message;
    }
}
```

​		`useGeneratedKeys`设置为 true 时，表示如果插入的表id以自增列为主键，则允许 JDBC 支持自动生成主键，并可将自动生成的主键id返回。

#### 	3、Dao

```java
@Mapper //推荐使用
public interface PaymentDao {
    int create(Payment payment); //写操作，新增
    Payment getPaymentById(@Param("id") Long id); //读操作
}
```

mapper配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.xiaolun.springcloud.dao.PaymentDao">
    <insert id="create" parameterType="Payment" useGeneratedKeys="true" keyProperty="id">
        insert into payment(serial) values(#{serial});
    </insert>

    <resultMap id="BaseResultMap" type="com.xiaolun.springcloud.entities.Payment">
        <id column="id" property="id" jdbcType="BIGINT"></id>
        <id column="serial" property="serial" jdbcType="VARCHAR"></id>
    </resultMap>
    <select id="getPaymentById" parameterType="Long" resultMap="BaseResultMap">
        select * from payment where id = #{id};
    </select>

</mapper>
```

#### 4、service

```java
public interface PaymentService {
    int create(Payment payment);
    Payment getPaymentById(@Param("id") Long id);
}

@Service
public class PaymentServiceImpl implements PaymentService {

    @Autowired
    private PaymentDao paymentDao;

    @Override
    public int create(Payment payment) {
        return  paymentDao.create(payment);
    }

    @Override
    public Payment getPaymentById(Long id) {
        return paymentDao.getPaymentById(id);
    }
}
```

#### 	5、controller

```java
@RestController
@Slf4j
public class PaymentController {
    @Autowired
    private PaymentService paymentService;

    @PostMapping(value = "/payment/create") //post:写操作
    public CommonResult create(@RequestBody Payment payment){
        int result = paymentService.create(payment);
        log.info("--------插入结果------"+result);
        if (result > 0){
            return new CommonResult(200,"插入数据库成功",result);
        }else {
            return new CommonResult(444,"插入数据库失败",null);
        }
    }

    @GetMapping(value = "/payment/get/{id}")  //get:读操作
    public CommonResult getPaymentById(@PathVariable("id") Long id){
        Payment payment = paymentService.getPaymentById(id);
        log.info("--------查询结果------"+payment);
        if (payment != null){
            return new CommonResult(200,"查询成功",payment);
        }else {
            return new CommonResult(444,"没有对应记录，查询id:"+id,null);
        }
    }
}
```

#### 测试

查询测试：

postman中输入http://localhost:8001/payment/get/2，返回结果:

```json
{
    "code":200,
    "message":"查询成功",
    "date":{
        "id":2,
        "serial":"xiaokun01"
    }
}
```

插入测试：

postman中输入http://localhost:8001/payment/create?serial=xiaopang，返回结果:

```json
{
    "code": 200,
    "message": "插入数据库成功",
    "date": 1
}
```

## 2.2 创建子模块Order

<img src="SpringCloud学习.assets\支付消费模块图2.jpg" style="zoom:80%;" />

​		因为这里是消费者类,主要是消费,那么就没有service和dao,需要调用payment模块的方法。并且这里还没有微服务的远程调用,那么如果要调用另外一个模块,则需要使用基本的api调用。

​	`RestTemplate`提供了多种便捷访问远程Http服务的方法，是一种简单便捷的访问`RestFul`服务模板类，是`Spring`提供的用于访问`Rest`服务的客户端模板工具类。

官网地址：https://docs.spring.io/spring-framework/docs/5.2.2.RELEASE/javadoc-api/org/springframework/web/client/RestTemplate.html

<img src="SpringCloud学习.assets\image-20200710172957810.png" alt="image-20200710172957810" style="zoom:80%;" />

**使用**

使用`RestTemplate`访问Restful接口十分简单:

```ini
(url,requestMap,ResponseBean.class)
-- Rest访问地址，请求参数，HTTP响应转换被转换成的对象类型。
```

**项目结构**

<img src="SpringCloud学习.assets\image-20200710180122741.png" alt="image-20200710180122741" style="zoom:80%;" />

#### 1、配置类

```java
@Configuration
public class ApplicationContextConfig {

    @Bean
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }
}
```

#### 2、controller

```java
@RestController
@Slf4j
public class OrderController {

    public static final String PAYMENT_URL = "http://localhost:8001";

    @Autowired
    private RestTemplate restTemplate;

    @GetMapping("/consumer/payment/create")
    public  CommonResult<Payment> create(Payment payment){
        return restTemplate.postForObject(PAYMENT_URL+"/payment/create",payment,CommonResult.class);
    }

    @GetMapping("/consumer/payment/get/{id}")
    public  CommonResult<Payment> getPaymentById(@PathVariable("id") Long id){
        return restTemplate.getForObject(PAYMENT_URL+"/payment/get/"+id,CommonResult.class);
    }
}
```

**测试**

​		插入数据网址：http://localhost/consumer/payment/create?serial=4，会遇到后端接收不到数据的情况，需要在接收的实体类中添加`@RequestBody`注解。

​		查询数据网址：http://localhost/consumer/payment/get/2，正常显示。

注意：IDEA中现在有两个应用正在运行，需要开启下面的模式（RunDashBoard）。

<img src="SpringCloud学习.assets\image-20200710180404792.png" alt="image-20200710180404792" style="zoom:67%;" />

### 2.2.1 工程重构

​	即新建一个模块,将重复代码抽取到一个公共模块中。

<img src="SpringCloud学习.assets\image-20200710180913574.png" alt="image-20200710180913574" style="zoom:80%;" />

**项目结构**

<img src="SpringCloud学习.assets\image-20200710182332505.png" alt="image-20200710182332505" style="zoom:80%;" />

1. 创建子模块，将上图的重复代码抽取到一个公共模块中。

2. 将其他模块中的公共类给删除。
3. 在maven中clear工程重构项目，然后install。
4. 其他模块中引入该模块，即在其他模块的pom文件中添加下面的代码。

```xml
<dependency>
    <groupId>com.xiaolun.springcloud</groupId>
    <artifactId>cloud-api-commons</artifactId>
    <version>${project.version}</version>
</dependency>
```

# 3 服务注册中心Eureka

## 3.1 介绍

​		当服务很多时,单靠代码手动管理是很麻烦的,需要一个公共组件,统一管理多服务,包括服务是否正常运行等。

​		服务治理：SpringCloud封装了Metflix公司开发的Eureka模块来进行服务治理。在传统的rpc远程调用框架中，管理每个服务与服务之间的依赖关系比较复杂，管理比较复杂，所以需要服务治理，用于管理服务之间的依赖关系，可以实现服务调用、负载均衡、容错等，进而实现服务发现和注册。

​		服务注册：Eureka采用CS的设计架构，Eureka Server作为服务注册功能的服务器，它是服务注册中心，而系统中的其他微服务，使用Eureka的客户端连接到Eureka Server 并维持心跳链接。这样系统的维护人员就可以通过Eureka Server来监控系统中的各个微服务是否正常运行。

​		在服务注册和发现中，有一个注册中心，当服务启动的时候，会把当前自己服务器的信息（比如服务器地址通讯地址等）以别名的方式注册到注册中心上，另一方（消费者|服务提供者）以该别名的方式去注册中心上获取到实际的服务通讯地址，然后再实现本地PC掉用RPC远程调用，框架的核心设计思想：在于注册中心，因为使用注册中心管理每个服务与服务之间关系的依赖（服务治理理念）。在任何rpc远程框架中，都会有一个注册中心（存放服务地址相关信息（接口地址））。

![image-20200710203333432](SpringCloud学习.assets\image-20200710203333432.png)

​		Eureka包含两个组件：Eureka server 和 Eureka client。

 		其中，Eureka server提供服务注册服务，各个微服务节点通过配置启动后，会在Eureka server 中进行注册，这样Eureka server 中的服务注册表将会存储所欲可用服务节点的信息，服务节点的信息可以在界面中直观看到。

 		Eureka client通过注册中心进行访问。这是一个Java客户端，可以简化Eureka server的交互，客户端同时也具备一个内置的、使用轮询（round-robin）负载算法的负载均衡器。在应用启动之后，将会向Eureka server发送心跳（默认周期为30秒），如果Eureka server在多个心跳周期内没有接到某个节点的心跳，Eureka server将会从服务注册表中将这个服务节点删除（默认90秒）。

## 3.2 Eureka Server服务端安装

**步骤**

1. IDEA生成Eureka Server端服务注册中心（类似物业公司）。
2. `Eureka client`端`cloud-provider-payment8001`将注册进`Eureka Server`成为服务提供者`provider`，类似猿辅导提供授课服务。
3. `Eureka client`端`cloud-consumer-order80`将注册进`Eureka Server`成为服务消费者`consumer`，类似猿辅导上课的学生。

### 1、 生成Eureka Server端服务注册中心

**项目结构**

<img src="SpringCloud学习.assets\image-20200710210142133.png" alt="image-20200710210142133" style="zoom:80%;" />

1. **配置文件**（包含pom文件）

```yaml
server:
  port: 7001
eureka:
  instance:
    hostname: locahost  #eureka服务端的实例名称
  client:
    register-with-eureka: false  # false表示不向注册中心注册自己
    fetch-registry: false  #false表示自己是注册中心，职责是维护服务实例，不需要检索服务
    service-url:  #设置与Eureka Server交互的地址查询服务和注册服务都需要依赖的这个地址。
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```

2. 主启动类上需要加上`@EnableEurekaServer`，

标明自己是服务注册中心，管理注册，配置等服务。

3. **测试**

输入网址：http://localhost:7001/，弹出下面的界面表示配置成功。

<img src="SpringCloud学习.assets\image-20200710210516155.png" alt="image-20200710210516155" style="zoom:80%;" />

### 2、支付微服务8001入驻Eureka Server

1. **添加配置文件**（包含pom文件）

```yaml
eureka:
  client:
    register-with-eureka: true # 表示将自己注册进Eureka Server中（默认为true）
    #表示从Eureka Server抓取已有的注册信息，默认为true.单节点无所谓，集群必须为true才能配合ribbon实现负载均衡。
    fetch-registry: true
    service-url:
      defaultZone: http://localhost:7001/eureka  #入驻地址
```

2. 主启动类上添加注解`@EnableEurekaClient`
3. **测试**

首先要启动Eureka Server,然后再启动入驻的微服务，在http://localhost:7001/界面上显示：

<img src="SpringCloud学习.assets\image-20200710212025424.png" alt="image-20200710212025424" style="zoom:80%;" />

<img src="SpringCloud学习.assets\image-20200710212210166.png" alt="image-20200710212210166" style="zoom:80%;" />

### 3、订单微服务80入驻Eureka Server

该模块的注入和支付微服务8001入驻Eureka Server的过程一致。

1. **测试**

<img src="SpringCloud学习.assets\image-20200710212728918.png" alt="image-20200710212728918" style="zoom:80%;" />

网址输入：http://localhost/consumer/payment/get/5，能够正常显示查询到的信息。

## 3.3 Eureka集群

<img src="SpringCloud学习.assets\image-20200710213356545.png" alt="image-20200710213356545" style="zoom:80%;" />

​		

```ini
1,就是pay模块启动时,注册自己,并且自身信息也放入eureka
2.order模块,首先也注册自己,放入信息,当要调用pay时,先从eureka拿到pay的调用地址
3.通过HttpClient调用
 	并且还会缓存一份到本地,每30秒更新一次
```

​		微服务RPC远程服务调用的核心是**高可用**，因此我们需要搭建Eureka注册中心集群，实现负载均衡和故障容错（**互相注册，相互守望**）。

<img src="SpringCloud学习.assets\image-20200710213751807.png" alt="image-20200710213751807" style="zoom:80%;" />

### 3.3.1 集群环境搭建

**项目结构**

<img src="SpringCloud学习.assets\image-20200710221421792.png" alt="image-20200710221421792" style="zoom:80%;" />

1. 新建子模块`cloud-eureka-Server7002`
2. 将`cloud-eureka-Server7001`中的pom文件粘贴到`cloud-eureka-Server7002`模块中.
3. 修改主机的host配置文件。

<img src="SpringCloud学习.assets\image-20200710214917046.png" alt="image-20200710214917046" style="zoom:80%;" />

实现不同的端口映射到同一个地址,其中eureka7001是一号机，eureka7002是二号机。

4. **测试**

输入地址：http://localhost:7001/，指向7002这个服务注册中心；输入http://eureka7002.com:7002/，发现指向7001这个服务注册中心。

<img src="SpringCloud学习.assets\image-20200710220928437.png" alt="image-20200710220928437" style="zoom:80%;" />

<img src="SpringCloud学习.assets\image-20200710220957443.png" alt="image-20200710220957443" style="zoom:80%;" />

### 3.3.2 订单支付两微服务注册到Eureka集群中

<img src="SpringCloud学习.assets\image-20200710221341719.png" alt="image-20200710221341719" style="zoom:80%;" />

1. 主要修改cloud-consumer-order80和cloud-provider-payment8001这两个模块中的yml文件，将单机版的入驻地址改成集群版的。

```yaml
 	   # defaultZone: http://localhost:7001/eureka  #入驻地址
       defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/ # 集群版
```

2. **测试**

+ 先启动EurekaServer，7001/7002服务。
+ 再启动服务提供者8001模块。
+ 最后启动消费者80模块。

输入地址：http://eureka7001.com:7001/,显示：

<img src="SpringCloud学习.assets\image-20200710222516903.png" alt="image-20200710222516903" style="zoom: 50%;" />

输入地址：http://localhost/consumer/payment/get/5，能够完成正常查询。

### 3.3.3 将支付8001模块配置为集群模式

实现的效果如下图：

<img src="SpringCloud学习.assets\image-20200710222932761.png" alt="image-20200710222932761" style="zoom: 67%;" />

**项目结构**

<img src="SpringCloud学习.assets\image-20200710225850657.png" alt="image-20200710225850657" style="zoom:50%;" />

1. 创建8002模块，pom文件，yml文件和主启动类上复制8001模块的，但是注意端口号要改成8002，服务名称不用改，用一样的。

<img src="SpringCloud学习.assets\image-20200710230054643.png" alt="image-20200710230054643" style="zoom:80%;" />

​		此时访问`order80`模块,发现并没有负载均衡到两个payment,模块中,而是只访问8001模块，我们需要在`oreder80`模块中，不要将订单访问地址写死(不然，订单只会访问8001端口。此时，订单每次访问时，都会去Eureka中获取地址，轮询交替获取，这样就达到负载均衡的效果了)，而是写成下面的形式：

```java
//
//    public static final String PAYMENT_URL = "http://localhost:8001";
    public static final String PAYMENT_URL = "http://CLOUD-PAYMENT-SERVICE"; //只认微服务名称
```

然后访问网址：http://localhost/consumer/payment/get/5，将会报出下面的错误：

<img src="SpringCloud学习.assets\image-20200710225304566.png" alt="image-20200710225304566" style="zoom: 50%;" />

这是因为8001和8002两个微服务向外暴露的是微服务名称，而不是地址和端口，订单服务识别不了。我们需要使用该注解赋予RestTemplate负载均衡的能力。

<img src="SpringCloud学习.assets\image-20200710225152520.png" alt="image-20200710225152520" style="zoom: 50%;" />

```java
@Bean
@LoadBalanced //使用该注解赋予RestTemplate负载均衡的能力
public RestTemplate getRestTemplate(){
    return new RestTemplate();
}
```

此时，Ribbon和Eureka整合后，消费者consumer可以直接调用服务，而不用关心地址和端口号了。

#### actuator微服务信息完善

主机名称-服务名称修改和访问信息有IP信息提示。

```yaml
eureka:
    instance:
      instance-id: payment8001 #主机名称的修改
      prefer-ip-address: true #访问路径可以显示IP地址
```

此时输入：http://eureka7002.com:7002/，显示：

<img src="SpringCloud学习.assets\image-20200711085118751.png" alt="image-20200711085118751" style="zoom: 50%;" />

### 3.3.4 服务发现Discovery

​		对于注册进Eureka里面的微服务，可以通过服务发现来获得该服务的信息。（自己微服务的信息向外面写好（类似于关于我们的功能））。

1. 在controller中添加注解，并书写方法

```java
 //服务发现client端，获得相应的服务信息
    //依赖org.springframework.cloud.client.discovery.DiscoveryClient;
    @Autowired
    private DiscoveryClient discoveryClient;
    

 @GetMapping("/payment/discovery")
    public Object discovery(){
        //拿到所有注册的信息
        List<String> services = discoveryClient.getServices();
        for (String element : services) {
            log.info("-----element-----"+element);
        }
        //拿到指定服务名称的所有服务的注册信息。
        List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
        for (ServiceInstance instance : instances) {
            log.info(instance.getServiceId()+"\t"+instance.getHost()+"\t"+instance.getPort()+"\t"+instance.getUri());
        }
        return this.discoveryClient;
    }
```

2. 在启动类中添加注解:

```java
@EnableDiscoveryClient
```

3. 测试

输入：http://localhost:8001/payment/discovery，显示：

<img src="SpringCloud学习.assets\image-20200711091556145.png" alt="image-20200711091556145" style="zoom:80%;" />

控制台输出：

<img src="SpringCloud学习.assets\image-20200711091756809.png" alt="image-20200711091756809" style="zoom: 50%;" />

### 3.3.5 Eureka自我保护

#### 1、介绍

​		保护模式主要用于一组客户端和Eureka Server之间存在网络分区场景下的保护，一旦进入保护模式，Eureka Server将会尝试保护其服务注册表中的信息，不在删除服务注册表中的数据，也不会注销任何微服务。

​		如果Eureka Server首页看到这段提时，说明Eureka 进入到了保护模式：

<img src="SpringCloud学习.assets\image-20200711092443912.png" alt="image-20200711092443912" style="zoom:67%;" />

​		一句话：某时刻某一个微服务不能用了，Eureka不会立即处理，依旧会对该微服务的信息进行保存，输入CAP里面的AP分支。

​		产生Eureka自我保护模式的原因：为了让EurekaClient可以正常运行，但是与Eureka Server网络不通的情况下，Eureka Server不会立即将EurekaClient服务剔除。

​		默认情况下，如果EurekaServer在一定的时间内没有接收到某个微服务实例的心跳，EurekaServer将会注销该实例（默认90秒），但是当网络分区故障发生的时候（延时，卡顿，拥挤等），微服务和EurekaServer之间就无法正常通讯了，剔除行为就变得十分危险（微服务本身是健康的，不应该注销这个微服务）。Eureka通过自我保护模式来解决上述问题。

<img src="SpringCloud学习.assets\image-20200711093303369.png" alt="image-20200711093303369" style="zoom:80%;" />

#### 2、禁止自我保护

默认自我保护机制是开启的。

1. Eureka服务端7001模块关闭自我保护。

```yaml
 server:
    enable-self-preservation: false # 关闭自我保护机制，保证不可用服务即使剔除
    eviction-interval-timer-in-ms: 2000 #清理间隔
```

将7001的配置改成单机版，好启动。

2. 客户端8001模块开启健康检查

```yaml
 # Eureka客户端向服务端发送的时间间隔（续约更新时间间隔），单位为秒（默认30S）
    lease-renewal-interval-in-seconds: 1
    # Eureka服务端在收到最后一次心跳后等待时间上限（续约到期时间），超时剔除服务（默认90秒）。
    lease-expiration-duration-in-seconds: 2
```

3. 测试

先启动7001,后启动8001,输入网址：http://eureka7001.com:7001/，然后关闭8001,可以看到服务立即被剔除了。

<img src="SpringCloud学习.assets\image-20200711095924055.png" alt="image-20200711095924055" style="zoom:80%;" />

#### 3、Eureka停更

网址：https://github.com/Netflix/eureka/wiki

<img src="SpringCloud学习.assets\image-20200711100231485.png" alt="image-20200711100231485" style="zoom:80%;" />

# 4 Zookeeper

<img src="SpringCloud学习.assets\image-20200711100730926.png" alt="image-20200711100730926" style="zoom:80%;" />

Zookeeper是一个分布式协调工具，可以实现注册中心功能。

关闭Linux服务器的防火墙后启动Zookeeper服务器。

Zookeeper服务器取代Eureka服务器，Zookeeper作为注册中心。

## 4.1 支付服务注册进Zookeeper

