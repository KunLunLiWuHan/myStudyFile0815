# 1 网关GateWay

## 1、介绍

1、官网

上一代zuul1.x：https://github.com/Netflix/zuul/wiki

当前gateway：https://cloud.spring.io/spring-cloud-static/spring-cloud-gateway/2.2.1.RELEASE/reference/html/

2、概述

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111832.png" alt="image-20200820224005450" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111833.png" alt="image-20200820224020739" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111834.png" alt="image-20200820224035066" style="zoom:67%;" />

总结：Spring Cloud Gateway 使用的Webflux中的reactor-netty响应式编程组件，底层使用了Netty通讯框架

源码结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111835.png" alt="image-20200820224146599" style="zoom:67%;" />

2、作用

反向代理、鉴权、流量控制、熔断和日志监控。

微服务架构中网关位置：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111836.png" alt="image-20200820224302425" style="zoom:80%;" />

3、选择GateWay的原因

（1）neflix不太靠谱，zuul2.0一直跳票,迟迟不发布

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111837.png" alt="image-20200820224451355" style="zoom:67%;" />

（2）SpringCloud Gateway具有如下特性

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111838.png" alt="image-20200820224515503" style="zoom:67%;" />

（3）SpringCloud Gateway与Zuul的区别

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111839.png" alt="image-20200820224556320" style="zoom:67%;" />

4、Zuul1.x模型

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111840.png" alt="image-20200820224639932" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111841.png" alt="image-20200820224658138" style="zoom:80%;" />

5、GateWay模型

（1）WebFlux介绍

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111842.png" alt="image-20200820224825273" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111843.png" alt="image-20200820224840516" style="zoom:67%;" />

（2）说明

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111844.png" alt="image-20200820224924987" style="zoom:67%;" />

## 2、三大核心概念

1、Route（路由）：路由是构建网关的基本模块，它由ID，目标URI，一系列的断言和过滤器组成，如果断言为true则匹配该路由。

2、Predicate（断言）：参考的是java8的java.util.function.Predicate开发人员可以匹配HTTP请求中的所有内容（例如请求头或请求参数），如果请求与断言相匹配则进行路由。

3、Filter(过滤)：指的是Spring框架中GatewayFilter的实例，使用过滤器，可以在请求被路由前或者之后对请求进行修改。

4、总体

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111845.png" alt="image-20200820225101712" style="zoom:80%;" />

## 3、工作流程

1、官网总结

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111846.png" alt="image-20200820230813575" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111847.png" alt="image-20200820230833420" style="zoom:67%;" />

2、核心逻辑

路由转发+执行过滤器链。

## 4、入门配置

### 1、新建模块

新建cloud-gateway-gateway9527模块，项目结构为：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111848.png" alt="image-20200821104345526" style="zoom:50%;" />

1、添加pom

```xml
<dependencies>
        <!--新增gateway-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-gateway</artifactId>
        </dependency>
        <dependency>
            <groupId>com.xiaolun.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
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

不需要添加web/actuator依赖。

2、添加yml

```yaml
server:
  port: 9527

spring:
  application:
    name: cloud-gateway
  cloud:
  	#新增的网关配置
    gateway:
      routes: #路由可以配置多个
        - id: payment_routh            #路由的ID，没有固定规则但要求唯一，建议配合服务名
          uri: http://localhost:8001   #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**   #断言,路径相匹配的进行路由

        - id: payment_routh2
          uri: http://localhost:8001
          predicates:
            - Path=/payment/lb/**

eureka:
  instance:
    hostname: cloud-gateway-service
  client:
    service-url:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://eureka7001.com:7001/eureka
```

​		新增的网关配置，可以在cloud-provider-payment8001的访问地址外面包含一层9527，避免暴露接口，进而实现实现URL地址中无8001端口。

3、业务类无

4、启动类

```java
@SpringBootApplication
@EnableEurekaClient 
public class GateWayMain9527 {
	public static void main(String[] args) {
		SpringApplication.run( GateWayMain9527.class,args);
	}
}
```

5、测试

​		首先启动cloud-eureka-Server7001，然后启动cloud-provider-payment8001，最后启动cloud-gateway-gateway9527。

​		在我们添加网关前，我们的输入网址如下：

```http
http://localhost:8001/payment/get/5
```

当我们添加网关后，输入网址如下：

```http
http://localhost:9527/payment/get/5
```

可以将端口号8001换成9527。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111849.png" alt="image-20200821105354733" style="zoom:50%;" />

访问说明：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111850.png" alt="image-20200821105726336" style="zoom: 50%;" />

### 2、配置路由的两种方式

1、方式1

在配置文件yml中配置，参见前面步骤。

2、方式2

代码中注入RouteLocator的Bean。

（1）需求

通过9527网关访问到外网的百度新闻网址。

（2）添加配置类GateWayConfig

```java
@Configuration
public class GateWayConfig {
	/**
	 * 1、配置了一个id为 path_rote_xiaolun 的路由规则
	 * 当访问地址http://localhost:9527/guonei时会
	 * 自动转发地址到：http://www.baidu.com/guonei
	 */
	@Bean
	public RouteLocator customRouteLocator(RouteLocatorBuilder routeLocatorBuilder) {
		RouteLocatorBuilder.Builder routes = routeLocatorBuilder.routes();
		routes.route("path_rote_xiaolun",
				r -> r.path("/guonei").uri("http://news.baidu.com/guonei")).build();
		return routes.build();
	}
}
```

项目结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111851.png" alt="image-20200821110631501" style="zoom:67%;" />

## 5、配置动态路由

​		默认情况下Gateway会根据注册中心的服务列表，以注册中心上微服务名为路径创建动态路由进行转发，从而实现动态路由的功能，即通过微服务名实现动态路由。

1、示意图

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111852.png" alt="image-20200821111010973" style="zoom:67%;" />

2、修改yml-gateway配置

```yaml
spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      #第1处修改
      discovery:
        locator:
          enabled: true  #开启从注册中心动态创建路由的功能，利用微服务名进行路由
      routes: #路由可以配置多个
        - id: payment_routh            #路由的ID，没有固定规则但要求唯一，建议配合服务名
#          uri: http://localhost:8001   #匹配后提供服务的路由地址
          #第2处修改：lb://serviceName是spring cloud gateway在微服务中自动为我们创建的负载均衡uri
          uri: lb://cloud-payment-service
          predicates:
            - Path=/payment/get/**   #断言,路径相匹配的进行路由

        - id: payment_routh2
#          uri: http://localhost:8001
          uri: lb://cloud-payment-service
          predicates:
            - Path=/payment/lb/**
```

3、测试

​		首先启动cloud-eureka-Server7001，然后启动cloud-provider-payment8001/8002，最后启动cloud-gateway-gateway9527。

​		浏览器输入下面的网址：

```http
http://localhost:9527/payment/lb
```

此时，有8001/8002两个端口切换。

## 6、Predicate的使用

1、介绍

（1）启动我们的cloud-gateway-gateway9527，在控制台，会发现下面的配置：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111853.png" alt="image-20200821112318021" style="zoom:50%;" />

（2）Route Predicate Factories

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111854.png" alt="image-20200821112341149" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111855.png" alt="image-20200821112438434" style="zoom:50%;" />

​		说白了，Predicate就是为了实现一组匹配规则，让请求过来找到对应的Route进行处理。

（3）常用的Route Predicate

（a）After Route Predicate

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111856.png" alt="image-20200821112612337" style="zoom:67%;" />

我们可以通过下面的代码获取年份：

```java
ZonedDateTime zonedDateTime = ZonedDateTime.now();
System.out.println(zonedDateTime);

//输出
- After=2020-03-08T10:59:34.102+08:00[Asia/Shanghai]
```

（b）Cookie Route Predicate

​		该断言包含两个参数，一个是Cookie name，一个是正则表达式。路由规则会通过获取对应的Cookie name值和正则表达式进行匹配，如果匹配上会执行路由，反追不执行。

```yaml
- Cookie=username,xiaolun
```

​		下面我们通过curl的命令来进行微服务的调试。

```
curl  http://localhost:9527/payment/lb --cookie "username=xiaolun"
```

命令板打印输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111857.png" alt="image-20200821113759962" style="zoom:67%;" />

## 7、Filter的使用

### 1、介绍

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111858.png" alt="image-20200821115319360" style="zoom:50%;" />

（1）AddRequestParameter

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111859.png" alt="image-20200821114038443" style="zoom:67%;" />

### 2、自定义过滤器

​		定义的是一个全局的GlobalFilter。实现全局日志记录和统一网关鉴权等功能。

1、添加过滤器

```java
@Component
@Slf4j
public class MyLogGateWayFilter implements GlobalFilter, Ordered {
	@Override
	public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {

		log.info("*********come in MyLogGateWayFilter: " + new Date());
		String uname = exchange.getRequest().getQueryParams().getFirst("username");
		if (StringUtils.isEmpty(uname)) {
			log.info("*****用户名为Null 非法用户****");
			exchange.getResponse().setStatusCode(HttpStatus.NOT_ACCEPTABLE);//给对方一个回应
			return exchange.getResponse().setComplete();
		}
		return chain.filter(exchange); //续传下去
	}

	@Override //加载该过滤器的顺序，数字越小，优先级越高
	public int getOrder() {
		return 0;
	}
}
```

项目结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111900.png" alt="image-20200821115415359" style="zoom:50%;" />

2、测试

​		首先启动cloud-eureka-Server7001，然后启动cloud-provider-payment8001/8002，最后启动cloud-gateway-gateway9527。

输入网址：http://localhost:9527/payment/lb?username=z3，能够正确访问（在Predicate的使用一章中，由于忘了关闭- Cookie=username,xiaolun，导致该请求一致无法响应，可见，过滤器是在路由指定好之后，即客户端进来之后进行再一次过滤），输入网址http://localhost:9527/payment/lb?u=z3不能够正确访问。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111901.png" alt="image-20200821115731056" style="zoom:67%;" />

# 2 Zuul

## 1、介绍

1、概述

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111902.png" alt="image-20200825221905024" style="zoom:67%;" />

2、网址

```http
https://github.com/Netflix/zuul/wiki
```

## 2、路由基本配置

新建项目：cloud-Zuul-gateway9528

项目结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111903.png" alt="image-20200825222412658" style="zoom:67%;" />

1、添加pom

```xml
<dependencies>
        <!-- zuul路由网关 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <!-- actuator监控 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!-- 热部署插件 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
    </dependencies>
```

2、添加yml

```yaml
server:
  port: 9528

spring:
  application:
    name: microservicecloud-zuul-gateway

eureka:
  client:
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka
  instance:
    instance-id: gateway-9528.com
    prefer-ip-address: true


zuul:
#  ignored-services: cloud-payment-service
  prefix: /xiaolun
  ignored-services: "*"
  routes:
    mydept.serviceId: cloud-payment-service
    mydept.path: /mydept/**
```

3、主启动类

```java
package com.xiaolun;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableZuulProxy
public class ZuulMain9528 {
   public static void main(String[] args) {
      SpringApplication.run(ZuulMain9528.class, args);
   }
}
```

4、测试

​		依次启动cloud-eureka-Server7001、cloud-provider-payment8001（服务提供类）、cloud-Zuul-gateway9528（路由）。

​		当我们不用路由的时候，输入网址如下：

```http
http://localhost:8001/payment/get/5
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831111904.png" alt="image-20200825222736942" style="zoom: 67%;" />

​		当我们启用路由的时候，输入网址如下：

```http
http://myzuul.com:9528/cloud-payment-service/payment/get/5
```

​		`cloud-payment-service`是cloud-provider-payment8001的yml配置中的服务名称：

```yaml
spring:
  application:
    name: cloud-payment-service  #服务名称
```

注：

​		cloud-eureka-Server7001的浏览器显示中服务名称为大写，但是在启用路由请求的时候，要和cloud-provider-payment8001配置的服务名称保持一致，不然会报错404的错误。

## 3、路由映射规则

在路由简单配置中遇到的问题如下：

1、服务名称 cloud-payment-service暴露，我们在loud-Zuul-gateway9528添加下面的配置，使用其他地址来代替原有地址。

```yml
zuul:
  #表示只能使用mydept路径访问，原路径cloud-payment-service失效。
# ignored-services: cloud-payment-service
# ignored-services: "*" # 统一配置
  routes:
    mydept.serviceId: cloud-payment-service
    mydept.path: /mydept/**
```

此时访问网址：

```http
#错误（ignored-services配置所导致）
http://myzuul.com:9528/cloud-payment-service/payment/get/5

#正确
http://myzuul.com:9528/mydept/payment/get/5
```

2、我们可以在路径中加上前缀`/xiaolun`

```yml
zuul:
#  ignored-services: cloud-payment-service
  prefix: /xiaolun
  ignored-services: "*"
  routes:
    mydept.serviceId: cloud-payment-service
    mydept.path: /mydept/**
```

此时访问网址：

```http
http://myzuul.com:9528/xiaolun/mydept/payment/get/5
```

注：

​		虽然我们在代码中添加了热部署，但是当我们在zuul中对于一些代码的改动，Java的加载不是很成功，需要重启IDEA才能将改动的代码生效。