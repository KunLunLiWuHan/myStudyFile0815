# 1 网关GateWay

## 1、介绍

1、官网

上一代zuul1.x：https://github.com/Netflix/zuul/wiki

当前gateway：https://cloud.spring.io/spring-cloud-static/spring-cloud-gateway/2.2.1.RELEASE/reference/html/

2、概述

<img src="SpringCloud学习4.assets/image-20200820224005450.png" alt="image-20200820224005450" style="zoom:80%;" />

<img src="SpringCloud学习4.assets/image-20200820224020739.png" alt="image-20200820224020739" style="zoom:80%;" />

<img src="SpringCloud学习4.assets/image-20200820224035066.png" alt="image-20200820224035066" style="zoom:67%;" />

总结：Spring Cloud Gateway 使用的Webflux中的reactor-netty响应式编程组件，底层使用了Netty通讯框架

源码结构：

<img src="SpringCloud学习4.assets/image-20200820224146599.png" alt="image-20200820224146599" style="zoom:67%;" />

2、作用

反向代理、鉴权、流量控制、熔断和日志监控。

微服务架构中网关位置：

<img src="SpringCloud学习4.assets/image-20200820224302425.png" alt="image-20200820224302425" style="zoom:80%;" />

3、选择GateWay的原因

（1）neflix不太靠谱，zuul2.0一直跳票,迟迟不发布

<img src="SpringCloud学习4.assets/image-20200820224451355.png" alt="image-20200820224451355" style="zoom:67%;" />

（2）SpringCloud Gateway具有如下特性

<img src="SpringCloud学习4.assets/image-20200820224515503.png" alt="image-20200820224515503" style="zoom:67%;" />

（3）SpringCloud Gateway与Zuul的区别

<img src="SpringCloud学习4.assets/image-20200820224556320.png" alt="image-20200820224556320" style="zoom:67%;" />

4、Zuul1.x模型

<img src="SpringCloud学习4.assets/image-20200820224639932.png" alt="image-20200820224639932" style="zoom:67%;" />

<img src="SpringCloud学习4.assets/image-20200820224658138.png" alt="image-20200820224658138" style="zoom:80%;" />

5、GateWay模型

（1）WebFlux介绍

<img src="SpringCloud学习4.assets/image-20200820224825273.png" alt="image-20200820224825273" style="zoom:67%;" />

<img src="SpringCloud学习4.assets/image-20200820224840516.png" alt="image-20200820224840516" style="zoom:67%;" />

（2）说明

<img src="SpringCloud学习4.assets/image-20200820224924987.png" alt="image-20200820224924987" style="zoom:67%;" />

## 2、三大核心概念

1、Route（路由）：路由是构建网关的基本模块，它由ID，目标URI，一系列的断言和过滤器组成，如果断言为true则匹配该路由。

2、Predicate（断言）：参考的是java8的java.util.function.Predicate开发人员可以匹配HTTP请求中的所有内容（例如请求头或请求参数），如果请求与断言相匹配则进行路由。

3、Filter(过滤)：指的是Spring框架中GatewayFilter的实例，使用过滤器，可以在请求被路由前或者之后对请求进行修改。

4、总体

<img src="SpringCloud学习4.assets/image-20200820225101712.png" alt="image-20200820225101712" style="zoom:80%;" />

## 3、工作流程

1、官网总结

<img src="SpringCloud学习4.assets/image-20200820230813575.png" alt="image-20200820230813575" style="zoom:80%;" />

<img src="SpringCloud学习4.assets/image-20200820230833420.png" alt="image-20200820230833420" style="zoom:67%;" />

2、核心逻辑

路由转发+执行过滤器链。

## 4、入门配置

