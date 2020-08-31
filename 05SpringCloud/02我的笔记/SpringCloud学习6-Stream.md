# 1 SpringCloud Stream消息驱动

## 1、介绍

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112116.png" alt="image-20200821185516133" style="zoom:50%;" />

1、概述

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112117.png" alt="image-20200821184329938" style="zoom:67%;" />

一句话：屏蔽底层消息中间件的差异，降低切换版本，统一消息的编程模型。

2、官网

英文官网：https://spring.io/projects/spring-cloud-stream#overview

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112118.png" alt="image-20200821184434632" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112119.png" alt="image-20200821184445888" style="zoom:50%;" />

API网址：https://cloud.spring.io/spring-cloud-static/spring-cloud-stream/3.0.1.RELEASE/reference/html/

中文官网：https://m.wang1314.com/doc/webapp/topic/20971999.html

3、标准MQ

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112120.png" alt="image-20200821184645092" style="zoom:67%;" />

（1）生产者/消费者之间靠消息媒介传递信息内容-Message。

（2）消息必须走特定的通道-消息通道MessageChannel。

（3）消息通道里的消息如何被消费呢，谁负责收发处理-消息通道MessageChannel的子接口SubscribableChannel,由MessageHandler消息处理器订阅。

4、选择Cloud Stream的原因

（1）Stream可以统一底层差异

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112121.png" alt="image-20200821184831492" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112122.png" alt="image-20200821184846330" style="zoom:50%;" />

（2）Binder

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112123.png" alt="image-20200821184927435" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112124.png" alt="image-20200821184954721" style="zoom:80%;" />

INPUT对应于消费者,OUTPUT对应于生产者。

（3）Stream中的消息通信方式遵循了发布-订阅模式

Topic主题进行广播：在RabbitMQ就是Exchange，在kafka中就是Topic。

5、Cloud Stream标准流程套路

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112125.png" alt="image-20200821185156608" style="zoom:50%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112126.png" alt="image-20200821185205177" style="zoom:50%;" />

（1）Binder

很方便的连接中间件，屏蔽差异。

（2）Channel

​		通道，是队列Queue的一种抽象，在消息通讯系统中就是实现存储和转发的媒介，通过对Channel对队列进行配置。

（3）Source和Sink

​		简单的可理解为参照对象是Spring Cloud Stream自身，从Stream发布消息就是输出，接受消息就是输入。

6、编码API和常用注解

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112127.png" alt="image-20200821185331374" style="zoom:50%;" />

## 2、案例说明

​		在项目中新建三个模块：cloud-stream-rabbitmq-provider8801,作为生产者进行发消息模块，cloud-stream-rabbitmq-consumer8802/8803。

### 1、消息驱动之生产者-cloud-stream-rabbitmq-provider8801

项目结构

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112128.png" alt="image-20200821221950900" style="zoom:50%;" />

1、添加pom

```xml
<dependencies>
        <!--添加stream依赖-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
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
  port: 8801

spring:
  application:
    name: cloud-stream-provider
  cloud:
    stream:
      binders: # 在此处配置要绑定的rabbitmq的服务信息；
        defaultRabbit: # 表示定义的名称，用于于binding整合
          type: rabbit # 消息组件类型
          environment: # 设置rabbitmq的相关的环境配置
            spring:
              rabbitmq:
                host: localhost
                port: 5672
                username: guest
                password: guest
      bindings: # 服务的整合处理
        output: # output表示是一个生产者，消息的发送
          destination: studyExchange # 表示这个名字是一个预定好的通道的名称，要使用的Exchange名称定义
          content-type: application/json # 设置消息类型，本次为json，文本则设置“text/plain”
          binder:  defaultRabbit # 设置要绑定的消息服务的具体设置

eureka:
  client: # 客户端进行Eureka注册的配置
    service-url:
      defaultZone: http://localhost:7001/eureka
  instance:
    lease-renewal-interval-in-seconds: 2 # 设置心跳的时间间隔（默认是30秒）
    lease-expiration-duration-in-seconds: 5 # 如果现在超过了5秒的间隔（默认是90秒）
    instance-id: send-8801.com  # 在信息列表时显示主机名称
    prefer-ip-address: true     # 访问的路径变为IP地址
```

注意：binder:  defaultRabbit会变红，但是不影响程序的运行。

3、主启动类

```java
@SpringBootApplication
public class StreamMQMain8801 {
	public static void main(String[] args) {
		SpringApplication.run(StreamMQMain8801.class, args);
	}
}
```

4、业务类

（1）发送消息接口

```java
public interface IMessageProvider {
	public String send();
}
```

（2）发送消息接口实现类

```java
@EnableBinding(Source.class) //定义消息的推送管道
public class MessageProviderImpl implements IMessageProvider
{
   @Resource
   private MessageChannel output; // 消息发送管道

   @Override
   public String send()
   {
      String serial = UUID.randomUUID().toString();
      output.send(MessageBuilder.withPayload(serial).build()); //构建消息
      System.out.println("*****serial: "+serial);
      return null;
   }
}
```

（3）controller

```java
@RestController
public class SendMessageController {
	@Resource
	private IMessageProvider messageProvider;

	@GetMapping(value = "/sendMessage")
	public String sendMessage() {
		return messageProvider.send();
	}

}
```

5、测试

​		先启动cloud-eureka-Server7001，然后启动cloud-stream-rabbitmq-provider8801，记得也要启动rabbitmq窗口，便于观察。输入网址：http://localhost:8801/sendMessage，可以看到rabbitmq有一个波峰变化。

### 2、消息驱动之消费者-cloud-stream-rabbitmq-consumer8802/8803

项目结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112129.png" alt="image-20200821222632239" style="zoom:50%;" />

1、添加pom

```xml
 <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
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
  port: 8802

spring:
  application:
    name: cloud-stream-consumer
  cloud:
    stream:
      binders: # 在此处配置要绑定的rabbitmq的服务信息；
        defaultRabbit: # 表示定义的名称，用于于binding整合
          type: rabbit # 消息组件类型
          environment: # 设置rabbitmq的相关的环境配置
            spring:
              rabbitmq:
                host: localhost
                port: 5672
                username: guest
                password: guest
      bindings: # 服务的整合处理
        input: # 这个名字是一个通道的名称
          destination: studyExchange # 表示要使用的Exchange名称定义
          content-type: application/json # 设置消息类型，本次为json，文本则设置“text/plain”
          binder: defaultRabbit  # 设置要绑定的消息服务的具体设置
          #group:  xiaolunB

eureka:
  client: # 客户端进行Eureka注册的配置
    service-url:
      defaultZone: http://localhost:7001/eureka
  instance:
    lease-renewal-interval-in-seconds: 2 # 设置心跳的时间间隔（默认是30秒）
    lease-expiration-duration-in-seconds: 5 # 如果现在超过了5秒的间隔（默认是90秒）
    instance-id: receive-8802.com  # 在信息列表时显示主机名称
    prefer-ip-address: true     # 访问的路径变为IP地址
```

3、主启动类

```java
@SpringBootApplication
@EnableEurekaClient
public class StreamMQMain8802 {
   public static void main(String[] args) {
      SpringApplication.run(StreamMQMain8802.class, args);
   }
}
```

4、业务类-controller

```java
@Component
@EnableBinding(Sink.class)
public class ReceiveMessageListenerController {
	@Value("${server.port}")
	private String serverPort;

	@StreamListener(Sink.INPUT)
	public void input(Message<String> message) {
		System.out.println("消费者1号，接受：" + message.getPayload() + "\t port:" + serverPort);
	}
}
```

5、clone一份cloud-stream-rabbitmq-consumer8803

参考博客：https://blog.csdn.net/beidaol/article/details/91042897

（1）复制cloud-stream-rabbitmq-consumer8803并粘贴到项目cloud2020中。

（2）将cloud-stream-rabbitmq-consumer8803的pom.xml和启动类中对应名字改掉。

（3）在项目cloud2020的pomx.xml中添加module。

```xml
<module>cloud-stream-rabbitmq-consumer8803</module>
```

6、测试

​	先启动cloud-eureka-Server7001，然后启动cloud-stream-rabbitmq-provider8801，最后启动cloud-stream-rabbitmq-consumer8802/8803，

输入网址：http://localhost:8801/sendMessag，可以看到cloud-stream-rabbitmq-consumer8802/8803接收到了消息。但是出现了重复消费的问题（对于同一份数据，二者都消费即接收），我们只希望一份数据只要一个服务消费就好了，cloud-stream-rabbitmq-consumer8802/8803之间应该是竞争的关系。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112130.png" alt="image-20200821223559178" style="zoom:67%;" />

## 3、分组消费与持久化

1、介绍

生产实际案例如下：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112131.png" alt="image-20200821223715099" style="zoom:50%;" />

​		微服务应用放置于同一个group中，就能够保证消息只会被其中一个应用消费一次。不同的组是可以消费的，同一个组内会发生竞争关系，只有其中一个可以消费。

2、分组

​		当我们在cloud-stream-rabbitmq-consumer8802/8803的yml文件中添加`group`属性，其中前者属性名为`xiaolunA`，后者为`xiaolunB`。

​		输入网址http://localhost:8801/sendMessag进行测试，还是重复消费。

当我们在yml文件中添加的属性都为`xiaolunA`，二者实现了轮询分组，每次只有一个消费者 cloud-stream-rabbitmq-provider8801模块的发的消息只能被8802或8803其中一个接收到，可以避免了重复消费。

3、持久化

​		在cloud-eureka-Server7001、cloud-stream-rabbitmq-provider8801、cloud-stream-rabbitmq-consumer8802/8803正常运行过程中，停止8802/8803（类似宕机），并去除掉8802的yml文件的分组属性group:xiaolunA，然后cloud-stream-rabbitmq-provider8801先发送4条消息到rabbitmq，然后再启动cloud-stream-rabbitmq-consumer8802，可以看到8802没有接收宕机后的数据，cloud-stream-rabbitmq-consumer8803在后台打印出来的rabbitmq中的数据（这是由于分组的作用）。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112132.png" alt="image-20200821224613875" style="zoom:67%;" />

# 2  SpringCloud Sleuth分布式请求链路追踪

## 1、介绍

（1）出现该技术的原因

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112133.png" alt="image-20200821224751783" style="zoom:67%;" />

（2）概述

官网：https://github.com/spring-cloud/spring-cloud-sleuth

Spring Cloud Sleuth提供了一套完整的服务跟踪的解决方案，在分布式系统中提供追踪解决方案并且兼容支持了zipkin。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112134.png" alt="image-20200821224849579" style="zoom:50%;" />

## 2、搭建链路监控步骤

1、下载

​		SpringCloud从F版起已不需要自己构建Zipkin server了，只需要调用jar包即可。

​		网址：https://dl.bintray.com/openzipkin/maven/io/zipkin/java/zipkin-server/，我们找到zipkin-server-2.12.9.exec.jar下载即可。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112135.png" alt="image-20200821225213672" style="zoom:67%;" />

2、运行jar

​		在jar包目录下执行下面的命令即可：

```ini
#路径：D:\03Enviroment\17zipkin
java -jar zipkin-server-2.12.9-exec.jar
```

​		控制台面板输出一个箭头标志。我们访问：http://localhost:9411/zipkin/会有下面的界面：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112136.png" alt="image-20200821225502857" style="zoom:50%;" />

3、术语

（1）完整的调用链路

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112137.png" alt="image-20200821225545179" style="zoom:67%;" />

转换成下面的：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112138.png" alt="image-20200821225617519" style="zoom:50%;" />

（2）名词解释

Trace:类似于树结构的Span集合，表示一条调用链路，存在唯一标识。

span:表示调用链路来源，通俗的理解span就是一次请求信息。

3、搭建项目

（1）服务提供者-cloud-provider-payment8001

（a）添加pom依赖

```xml
<!--   包含了sleuth zipkin 数据链路追踪-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zipkin</artifactId>
        </dependency>
```

（b）修改yml文件

```yaml
spring:
  application:
    name: cloud-order-service
  zipkin:
    base-url: http://localhost:9411
  sleuth:
      sampler:
        probability: 1 #采样率介于0~1之间，1表示全部采集
```

(c)业务类-controller

```java
 @GetMapping("/payment/zipkin")
    public String paymentZipkin()
    {
        return "hi ,i'am paymentzipkin server fall back，welcome to atguigu";
    }
```

（2）服务调用方-cloud-consumer-order80

（a）添加pom依赖和上述cloud-provider-payment8001同。

（b）修改yml文件和上述cloud-provider-payment8001同。

（c）业务类-controller

```java
// ====================> zipkin+sleuth
@GetMapping("/consumer/payment/zipkin")
public String paymentZipkin() {
   String result = restTemplate.getForObject("http://localhost:8001" + "/payment/zipkin/", String.class);
   return result;
}
```

（3）测试

​		依次启动cloud-eureka-Server7001，cloud-provider-payment8001，cloud-consumer-order80，输入网址：http://localhost/consumer/payment/zipkin，可以在http://localhost:9411查看耗时情况。

注意：当我们在测试过程中将zipkin关闭时，会出现下面的报错信息：

```ini
ResourceAccessException: I/O error on POST request for “http://localhost:9411/api/v2/spans“
```

