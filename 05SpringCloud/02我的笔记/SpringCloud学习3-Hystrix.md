# Hystrix断路器

## 1、介绍

参考博客：

```http
https://www.cnblogs.com/rjzheng/p/10340176.html
```

1、概述

（1）分布式系统遇到的问题

复杂分布式体系结构中的应用程序有数十个依赖关系，每个依赖关系在某个时候不可避免地失败。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111523.png" alt="image-20200820155139706" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111524.png" alt="image-20200820155211411" style="zoom:80%;" />

（2）Hystrix概述

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111525.png" alt="image-20200820155400019" style="zoom:80%;" />

（3）作用

服务降级，服务熔断和接近实时地监控。

（4）官网资料

```http
https://github.com/Netflix/Hystrix/wiki/How-To-Use
```

（5）停更进维

```http
https://github.com/Netflix/Hystrix
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111526.png" alt="image-20200820155548307" style="zoom:80%;" />

2、重要概念

（1）服务降级-fallback

​		服务器忙，请稍候再试，不让客户端等待并立刻返回一个友好提示。触发降级的情况：

+ 程序运行异常

+ 超时

+ 服务熔断触发服务降级

+ 线程池/信号量打满也会导致服务降级

（2）服务熔断

​		类比保险丝达到最大服务访问后，直接拒绝访问，拉闸限电，然后调用服务降级的方法并返回友好提示。

​		服务的降级->进而熔断->恢复调用链路。

（3）服务限流

​		秒杀高并发等操作，严禁一窝蜂的过来拥挤，大家排队，一秒钟N个，有序进行。

## 2、案例

### 1、新建cloud-provider-hystrix-payment8001

项目结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111527.png" alt="image-20200820161243888" style="zoom:50%;" />

1、添加pom

```java
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```

2、添加yml

```yaml
server:
  port: 8001

spring:
  application:
    name: cloud-provider-hystrix-payment

eureka:
  client:
    register-with-eureka: true    #标识不向注册中心注册自己
    fetch-registry: true   #表示自己就是注册中心，职责是维护服务实例，并不需要去检索服务
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/
```

为了节省时间，不将cloud-eureka-Server7002启动，只启动cloud-eureka-Server7001，采用单节点测试。

3、添加主启动

```java
@SpringBootApplication
@EnableEurekaClient
public class PaymentHystrixMain8001 {
   public static void main(String[] args) {
      SpringApplication.run(PaymentHystrixMain8001.class,args);
   }
}
```

4、添加业务类

（1）service

```java
@Service
public class PaymentService {

	//成功
	public String paymentInfo_OK(Integer id){
		return "线程池："+Thread.currentThread().getName()+"   paymentInfo_OK,id：  "+id+"\t"+"哈哈哈"  ;
	}

	//失败
	public String paymentInfo_TimeOut(Integer id){
		int timeNumber = 3;
		try { TimeUnit.SECONDS.sleep(timeNumber); }catch (Exception e) {e.printStackTrace();}
		return "线程池："+Thread.currentThread().getName()+"   paymentInfo_TimeOut,id：  "+id+"\t"+"呜呜呜"+" 耗时(秒)"+timeNumber;
	}
}
```

(2)controller

```java
@RestController
@Slf4j
public class PaymentController {

   @Resource
   private PaymentService paymentService;

   @Value("${server.port}")
   private String serverPort;

   @GetMapping("/payment/hystrix/ok/{id}")
   public String paymentInfo_OK(@PathVariable("id") Integer id){
      String result = paymentService.paymentInfo_OK(id);
      log.info("*******result:"+result);
      return result;
   }
   @GetMapping("/payment/hystrix/timeout/{id}")
   public String paymentInfo_TimeOut(@PathVariable("id") Integer id){
      String result = paymentService.paymentInfo_TimeOut(id);
      log.info("*******result:"+result);
      return result;
   }
}
```

4、测试

先启动cloud-eureka-Server7001，再启动cloud-provider-hystrix-payment8001，访问：

```http
http://localhost:8001/payment/hystrix/ok/5
```

正常输出；访问：

```http
http://localhost:8001/payment/hystrix/timeout/5
```

正常输出。

即该module已正确搭建，以上述为根基平台，从正确->错误->降级熔断->恢复。

### 2、高并发测试

1、Jmeter压测测试

（1）开启Jmeter，来20000个并发压死8001，20000个请求都去访问paymentInfo_TimeOut服务。  

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111528.png" alt="image-20200820161640997" style="zoom:67%;" />

（2）再来一个访问：

```http
http://localhost:8001/payment/hystrix/ok/5/http://localhost:8001/payment/hystrix/timeout/5
```

（3）测试

上面两个访问都在转圈圈，处于等待之中。原因：tomcat的默认的工作线程数被打满了，没有多余的线程来分解压力和处理。

上面还是服务提供者cloud-provider-hystrix-payment8001自己测试，假如此时外部的消费者cloud-consumer-feign-hystrix-order80也来访问，那消费者只能干等，最终导致消费端cloud-consumer-feign-hystrix-order80不满意，服务端cloud-provider-hystrix-payment8001直接被拖死。

### 3、新建cloud-consumer-feign-hystrix-order80

1、添加pom

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```

2、添加yml

```java
server:
  port: 80

spring:
  application:
    name: cloud-provider-hystrix-order

eureka:
  client:
    register-with-eureka: true    #表识不向注册中心注册自己
    fetch-registry: true   #表示自己就是注册中心，职责是维护服务实例，并不需要去检索服务
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/
```

3、启动类

```java
@SpringBootApplication
@EnableFeignClients
@EnableEurekaClient
public class OrderHystrixMain80 {
	public static void main(String[] args) {
		SpringApplication.run(OrderHystrixMain80.class,args);
	}
}
```

4、业务类

（1）PaymentHystrixService接口类

```java
@Component
@FeignClient(value = "CLOUD-PROVIDER-HYSTRIX-PAYMENT")
public interface PaymentHystrixService {
	@GetMapping("/payment/hystrix/ok/{id}")
	public String paymentInfo_OK(@PathVariable("id") Integer id);

	@GetMapping("/payment/hystrix/timeout/{id}")
	public String paymentInfo_TimeOut(@PathVariable("id") Integer id);
}
```

（2）OrderHystrixController实现类

```java
@RestController
@Slf4j
public class OrderHystrixController {

	@Resource
	private PaymentHystrixService paymentHystrixService;

	@Value("${server.port}")
	private String serverPort;

	@GetMapping("/consumer/payment/hystrix/ok/{id}")
	public String paymentInfo_OK(@PathVariable("id") Integer id) {
		String result = paymentHystrixService.paymentInfo_OK(id);
		log.info("*******result:" + result);
		return result;
	}

	@GetMapping("/consumer/payment/hystrix/timeout/{id}")
	public String paymentInfo_TimeOut(@PathVariable("id") Integer id) {
		String result = paymentHystrixService.paymentInfo_TimeOut(id);
		log.info("*******result:" + result);
		return result;
	}
}
```

5、测试

（1）先启动cloud-eureka-Server7001，再启动cloud-provider-hystrix-payment8001，最后启动cloud-consumer-feign-hystrix-order80，访问：

```http
http://localhost/consumer/payment/hystrix/ok/5
```

正常输出。

同样，在高并发的情况下，消费者cloud-consumer-feign-hystrix-order80再去访问上面的网址时，会处于转圈等待状态或者消费端报超时错误。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111529.png" alt="image-20200820163338776" style="zoom:80%;" />

（2）故障的原因

cloud-provider-hystrix-payment8001同一层次的其他接口服务被困死，因为tomcat线程里面的工作线程已经被挤占完毕，消费者cloud-consumer-feign-hystrix-order80再去访问上面的网址时（调用cloud-provider-hystrix-payment8001），响应缓慢，转圈圈。

6、解决要求

（1）超时导致服务器变慢（转圈），超时不再等待。

（2）出错（宕机或程序运行出错），出错要有兜底。

+ 对方服务（cloud-provider-hystrix-payment8001）超时了，调用者（cloud-consumer-feign-hystrix-order80）不能一直卡死等待，必须有服务降级。

+ 对方服务（cloud-provider-hystrix-payment8001）down机了，调用者（cloud-consumer-feign-hystrix-order80）不能一直卡死等待，必须有服务降级。

+ 对方服务（cloud-provider-hystrix-payment8001）OK，调用者（cloud-consumer-feign-hystrix-order80）自己出故障或有自我要求（自己的等待时间小于服务提供者），自己处理降级。

## 3、服务降级

### 1、配置说明

（1）降低配置注解：@HystrixCommand。

（2）cloud-provider-hystrix-payment8001先从自身找问题：设置自身调用超时时间的峰值，峰值内可以正常运行，超过了需要有兜底的方法处理，作服务降级fallback。

### 2、服务提供端fallback

针对的是cloud-provider-hystrix-payment8001-fallback。

一般服务降级应用在客户端（消费端），但是也可以应用在服务提供端（cloud-provider-hystrix-payment8001）。

1、主启动类激活，添加新注解

```java
@EnableCircuitBreaker
```

2、业务类启用

路径：com.xiaolun.springcloud.service.PaymentService

```java
@Service
public class PaymentService {

	//成功
	public String paymentInfo_OK(Integer id) {
		return "线程池：" + Thread.currentThread().getName() + "   paymentInfo_OK,id：  " + id + "\t" + "成功";
	}

	//失败
	@HystrixCommand(fallbackMethod = "paymentInfo_TimeOutHandler", commandProperties = {
			//线程超时时间设置为3秒钟
			@HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "3000")
	})
	public String paymentInfo_TimeOut(Integer id) {
//		int tes = 10/0;
		int timeNumber = 5;
		try {
			TimeUnit.SECONDS.sleep(timeNumber);
		} catch (Exception e) {
			e.printStackTrace();
		}
		return "线程池：" + Thread.currentThread().getName() + "   paymentInfo_TimeOut,id：  " + id + "\t" + "失败" ;
	}

	/**
	 * 兜底方法 @HystrixCommand报异常后如何处理
	 * 一旦调用服务方法失败并抛出了错误信息后，会自动调用@HystrixCommand标注好的fallbackMethod调用类中的指定方法
	 */
	public String paymentInfo_TimeOutHandler(Integer id) {
		return "线程池：" + Thread.currentThread().getName() + "   系统繁忙, 请稍候再试  ,id：  " + id;
	}
}
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111530.png" alt="image-20200820191229510" style="zoom:50%;" />

3、测试

先启动cloud-eureka-Server7001，再启动cloud-provider-hystrix-payment8001，输入网址

```http
http://localhost:8001/payment/hystrix/ok/5
```

正常显示，输入网址：

```http
http://localhost:8001/payment/hystrix/timeout/5
```

会进行异常处理（服务降级）界面的返回。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111531.png" alt="image-20200820191722977" style="zoom:50%;" />

在业务类中制造了两个异常：

```
1、int tes = 10/0;
2、我们只接受3秒钟，运行5秒钟，超时异常。
```

可以发现，当前服务不可用时，会有服务的降级，兜底方案是	paymentInfo_TimeOutHandler。

### 3、客户端fallback

针对的是cloud-consumer-feign-hystrix-order80-fallback，主要是为了客户端能够更好的保护自己，依样画葫芦进行客户端降级保护。

注：我们自己配置过的热部署方式对java代码的改动明显，但对`@HystrixCommand`内属性的修改建议重启微服务。

1、设置yml

```yaml
feign:
  hystrix:
    enabled: true #如果处理自身的容错就开启。开启方式与生产端不一样。
```

2、启动类中添加注解

```java
@EnableHystrix
```

3、业务类

```java
@RestController
@Slf4j
public class OrderHystrixController {

	@Resource
	private PaymentHystrixService paymentHystrixService;

	@Value("${server.port}")
	private String serverPort;

	@GetMapping("/consumer/payment/hystrix/ok/{id}")
	public String paymentInfo_OK(@PathVariable("id") Integer id) {
		String result = paymentHystrixService.paymentInfo_OK(id);
		log.info("*******result:" + result);
		return result;
	}

	@GetMapping("/consumer/payment/hystrix/timeout/{id}")
	@HystrixCommand(fallbackMethod = "paymentTimeOutFallbackMethod", commandProperties = {
			/**
			 * 客户端只等待服务提供者1.5秒，此时服务提供者
			 * 3秒钟以内是正常的业务逻辑，在客户端也会服务降级
			 */
			@HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "1500")
	})
	public String paymentInfo_TimeOut(@PathVariable("id") Integer id) {
		String result = paymentHystrixService.paymentInfo_TimeOut(id);
		return result;
	}

	//兜底方法
	public String paymentTimeOutFallbackMethod(@PathVariable("id") Integer id) {
		return "我是消费者80，对付支付系统繁忙请10秒钟后再试或者自己运行出错请检查自己";
	}
}
```

4、测试

先启动cloud-eureka-Server7001，再启动cloud-provider-hystrix-payment8001，最后启动cloud-consumer-feign-hystrix-order80，输入网址：

```http
http://localhost/consumer/payment/hystrix/timeout/5
```

浏览器显示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111532.png" alt="image-20200820202149957" style="zoom:80%;" />

在客户端可以实现服务的降级。

### 4、全局服务降级

通过服务提供者和客户端的服务降级，我们可以发现一个问题，那就是：每个业务方法对应一个兜底的方法，会造成代码的膨胀。我们需要一种全局配置来减少冗余，使统一和自定义的兜底方法分开。

1、添加全局注解@DefaultProperties

路径：cloud-consumer-feign-hystrix-order80下的com.xiaolun.springcloud.controller.OrderHystrixController。

```java
@RestController
@Slf4j //全局兜底方法
@DefaultProperties(defaultFallback = "payment_global_fallbackMethod")
public class OrderHystrixController {
	@Resource
	private PaymentHystrixService paymentHystrixService;
	@Value("${server.port}")
	private String serverPort;

	@GetMapping("/consumer/payment/hystrix/ok/{id}")
	public String paymentInfo_OK(@PathVariable("id") Integer id) {
		String result = paymentHystrixService.paymentInfo_OK(id);
		log.info("*******result:" + result);
		return result;
	}

	@GetMapping("/consumer/payment/hystrix/timeout/{id}")
	@HystrixCommand
	public String paymentInfo_TimeOut(@PathVariable("id") Integer id) {
		int res = 10 / 0; //测试服务降级用例
		String result = paymentHystrixService.paymentInfo_TimeOut(id);
		return result;
	}

	//自定义的兜底方法
	public String paymentTimeOutFallbackMethod(@PathVariable("id") Integer id) {
		return "我是消费者80，对付支付系统繁忙请10秒钟后再试或者自己运行出错请检查自己";
	}

	//全局配置
	public String payment_global_fallbackMethod() {
		return "Global处理异常信息，请稍后重试";
	}
}
```

说明：

（1）全局代码配置的说明

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111533.png" alt="image-20200820203751490" style="zoom: 50%;" />

（2）@DefaultProperties的说明

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111535.png" alt="image-20200820204135278" style="zoom:67%;" />

2、测试

先启动cloud-eureka-Server7001，再启动cloud-provider-hystrix-payment8001，最后启动cloud-consumer-feign-hystrix-order80，输入网址：

```http
http://localhost/consumer/payment/hystrix/timeout/5
```

浏览器显示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111534.png" alt="image-20200820204029288" style="zoom:67%;" />

### 5、通配服务降级

在全局服务降级的配置中，只是解决了通用和自定义的方法分开，但是业务逻辑还是写在一起，造成代码的混乱。我们需要将业务逻辑分开。

1、案例

客户端（cloud-consumer-feign-hystrix-order80）去调用服务端（cloud-provider-hystrix-payment8001），碰上服务端宕机或关闭。

本次案例服务降级处理是在客户端80实现完成的，与服务端8001没有关系，只需要为Feign客户端定义的接口添加一个服务降级处理的实现类即可实现解耦。

2、未来我们可能遇到的异常

运行时错误，超时错误，宕机。

3、修改cloud-consumer-feign-hystrix-order80

根据cloud-consumer-feign-hystrix-order80已经有的PaymentHystrixService接口，重新新建一个类（PaymentFallbackService）实现该接口，统一为接口里面的方法进行异常处理。

项目结构

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111537.png" alt="image-20200820210931428" style="zoom:67%;" />

（1）添加实现PaymentFeignClientService接口的PaymentFallbackService类

```java
@Component
public class PaymentFallbackService implements PaymentHystrixService {
	@Override
	public String paymentInfo_OK(Integer id) {
		return "PaymentFallbackService   paymentInfo_OK ";
	}

	@Override
	public String paymentInfo_TimeOut(Integer id) {
		return "PaymentFallbackService paymentInfo_TimeOut";
	}
}
```

（2）设置yml

```java
feign:
  hystrix:
    enabled: true #如果处理自身的容错就开启。开启方式与生产端不一样。
```

（3）为PaymentFeignClientService接口添加fallback

```java
@Component
@FeignClient(value = "CLOUD-PROVIDER-HYSTRIX-PAYMENT",fallback = PaymentFallbackService.class)
public interface PaymentHystrixService {
	@GetMapping("/payment/hystrix/ok/{id}")
	public String paymentInfo_OK(@PathVariable("id") Integer id);

	@GetMapping("/payment/hystrix/timeout/{id}")
	public String paymentInfo_TimeOut(@PathVariable("id") Integer id);
}
```

（4）测试

​	先启动cloud-eureka-Server7001，再启动cloud-provider-hystrix-payment8001，最后启动cloud-consumer-feign-hystrix-order80，输入网址：

```http
http://localhost/consumer/payment/hystrix/ok/31
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111536.png" alt="image-20200820210551308" style="zoom:50%;" />

浏览器可以正常显示，故意关闭服务提供者cloud-provider-hystrix-payment8001，浏览器显示超时信息：PaymentFallbackService paymentInfo_TimeOut。

此时服务端cloud-provider-hystrix-payment8001已经down了，但是我们做了服务降级处理，让客户端在服务端不可用时也会获得提示信息，而不会挂起（用户重试等操作）耗死服务器。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111538.png" alt="image-20200820211131458" style="zoom:50%;" />

## 4、服务熔断

### 1、介绍

断路器：就是家里的保险丝。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111539.png" alt="image-20200820211320113" style="zoom:67%;" />

论文：

```http
https://martinfowler.com/bliki/CircuitBreaker.html
```



### 2、实操

针对cloud-provider-hystrix-payment8001进行修改。

1、修改com.xiaolun.springcloud.service.PaymentService

只需要在该类原来的基础上添加下面的内容即可。

```java
//服务熔断
@HystrixCommand(fallbackMethod = "paymentCircuitBreaker_fallback", commandProperties = {
      @HystrixProperty(name = "circuitBreaker.enabled", value = "true"),  //是否开启断路器
      @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold", value = "10"),   //请求次数
      @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds", value = "10000"),  //时间范围
      @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage", value = "60"), //失败率达到多少后跳闸
})
public String paymentCircuitBreaker(@PathVariable("id") Integer id) {
   if (id < 0) {
      throw new RuntimeException("id 不能负数");
   }
   String serialNumber = IdUtil.simpleUUID();

   return Thread.currentThread().getName() + "\t" + "调用成功,流水号：" + serialNumber;
}

public String paymentCircuitBreaker_fallback(@PathVariable("id") Integer id) {
   return "id 不能负数，请稍候再试。 id: " + id;
}
```

配置该参数的原因（同时也是官网的步骤）：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111540.png" alt="image-20200820212915781" style="zoom:67%;" />

2、修改com.xiaolun.springcloud.controller.PaymentController

只需要在原来的基础上添加下面的内容即可。

```java
//===服务熔断
@GetMapping("/payment/circuit/{id}")
public String paymentCircuitBreaker(@PathVariable("id") Integer id) {
		String result = paymentService.paymentCircuitBreaker(id);
		log.info("*******result:" + result);
		return result;
}
```

3、测试

先启动cloud-eureka-Server7001，再启动cloud-provider-hystrix-payment8001，输入正确网址：

```http
http://localhost:8001/payment/circuit/31
```

浏览器端能够正常显示，输入错误地址：

```http
http://localhost:8001/payment/circuit/-31
```

浏览器显示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111541.png" alt="image-20200820213332479" style="zoom:80%;" />

首先经过多次输入错误地址后，然后慢慢输入正确的地址，发现刚开始不满足条件，就算是正确的访问地址也不能进行访问，需要慢慢的恢复链路。

### 3、原理

1、熔断类型

（1）熔断打开：请求不再进行调用当前服务，内部设置时钟一般为MTTR(平均故障处理时间)，当打开时长达到所设时钟则进入熔断状态。

（2）熔断关闭：熔断关闭不会对服务进行熔断。

（3）熔断半开：部分请求根据规则调用当前服务，如果请求成功且符合规则则认为当前服务恢复正常，关闭熔断。

2、断路器开始起作用的情况

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111542.png" alt="image-20200820213639942" style="zoom:80%;" />

3、熔断器开启或者关闭的条件

（1）当满足一定阀值的时候（默认10秒内超过20个请求次数）。

（2）当失败率达到一定的时候（默认10秒内超过50%请求失败）。

（3）到达以上阀值，断路器将会开启。

（4）当开启的时候，所有请求都不会进行转发。

（5）一段时间之后（默认是5秒），这个时候断路器是半开状态，会让其中一个请求进行转发。如果成功，断路器会关闭，若失败，继续开启。重复4和5。

4、断路器打开之后

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111543.png" alt="image-20200820213847570" style="zoom:80%;" />

5、All配置

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111544.png" alt="image-20200820214028703" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111146.png" alt="image-20200820214139768" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111545.png" alt="image-20200820214203693" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111546.png" alt="image-20200820214221844" style="zoom:80%;" />

## 5、服务监控hystrixDashboard

### 1、概述

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111547.png" alt="image-20200820214350794" style="zoom:80%;" />

### 2、新建仪表盘9001

项目名：cloud-consumer-hystrix-dashboard9001。

项目结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111548.png" alt="image-20200820220851607" style="zoom:50%;" />

1、添加pom

```java
<dependencies>
        <!--新增hystrix dashboard-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
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

​		所有的微服务提供者都需要添加下面的依赖：

```xml
 <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
```

我们现在只是为cloud-provider-hystrix-payment8001添加依赖。

2、添加yml

```yaml
server:
  port: 9002
```

由于端口9001被系统占用的原因，我们使用9002进行测试。

3、主启动类

```java
@SpringBootApplication
@EnableHystrixDashboard
public class HystrixDashboardMain9001 {
    public static void main(String[] args) {
        SpringApplication.run(HystrixDashboardMain9001.class,args);
    }
}
```

4、新版本Hystrix需要在主启动类MainAppHystrix8001中指定监控路径

```java
	@Bean
	public ServletRegistrationBean getServlet() {
		HystrixMetricsStreamServlet streamServlet = new HystrixMetricsStreamServlet();
		ServletRegistrationBean registrationBean = new ServletRegistrationBean(streamServlet);
		registrationBean.setLoadOnStartup(1);
		registrationBean.addUrlMappings("/hystrix.stream");
		registrationBean.setName("HystrixMetricsStreamServlet");
		return registrationBean;
	}
```

不然，就会报出下面的错误：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111549.png" alt="image-20200820220211562" style="zoom:80%;" />

5、测试

​		先启动cloud-eureka-Server7001，再启动cloud-provider-hystrix-payment8001，最后启动cloud-consumer-hystrix-dashboard9001。

输入正确网址：http://localhost:8001/payment/circuit/31，再访问错误网址：http://localhost:8001/payment/circuit/-31，观察面板变化。

### 3、观察监控窗口

1、窗口观察

（1）7色1圈1线。

（2）整图说明1

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111550.png" alt="image-20200820221037379" style="zoom:80%;" />

（3）整图说明2

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111551.png" alt="image-20200820221104557" style="zoom:80%;" />

2、application.yml小叶子形状消失的解决方法。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111552.png" alt="image-20200820221554743" style="zoom:50%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111553.png" alt="image-20200820221739162" style="zoom:50%;" />