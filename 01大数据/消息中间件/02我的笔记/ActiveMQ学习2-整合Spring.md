## 1、ActiveMQ的Broker

1、介绍

（1）概述

相当于一个ActiveMQ服务器实例。

说白了，Broker其实就是实现了用代码的形式启动ActiveMQ将MQ嵌入到Java代码中，以便随时用随时启动，在用的时候再去启动这样能节省了资源，也保证了可用性。

2、按照不同的config配置文件来启动activemq。

参考Redis

回忆阳哥之前讲的Redis，不同config配置文件来模拟不同的实例。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200902114346964.png" alt="image-20200902114346964" style="zoom:80%;" />

```shell
./activemq start xbean:file:/myActivemq/activemq/conf/activemq02.xml 
```

3、嵌入式Broker

用ActiveMQ Broker作为独立的消息服务器来构建Java应用。

ActiveMQ也支持在vm中通信基于嵌入的broker，能够无缝的集成其他java应用。

和Linux上的ActiveMQ是一样的,Broker相当于一个Mini版本的ActiveMQ。

（1）添加pom文件

```xml
<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-databind</artifactId>
  <version>2.10.1</version>
</dependency>
```

解决下面的报错：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200902115012241.png" alt="image-20200902115012241" style="zoom:67%;" />

（2）EmbedBroker代码

```java
package com.xiaolun.activemq.Embed;
import org.apache.activemq.broker.BrokerService;

public class EmbedBroker {

   public static void main(String[] args) throws Exception {
      //ActiveMQ也支持在vm中通信基于嵌入的broker
      BrokerService brokerService = new BrokerService();
      brokerService.setUseJmx(true);
      brokerService.addConnector("tcp://127.0.0.1:61616");
      brokerService.start();
			//添加该代码，将线程阻塞
      System.in.read();
   }
}
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200902142252767.png" alt="image-20200902142252767" style="zoom:50%;" />

（3）测试

依次启动EmbedBroker，JmsConsumer和JmsProdude，可以发现消费者可以接收来自生产者发送的消息。

## 2、Spring整合ActiveMQ

1、添加用于整合的pom文件

```xml
  <dependency>
        <groupId>org.apache.activemq</groupId>
        <artifactId>activemq-pool</artifactId>
        <version>5.15.10</version>
    </dependency>
 
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jms</artifactId>
        <version>5.2.1.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aop</artifactId>
        <version>5.2.1.RELEASE</version>
    </dependency>
```

2、添加配置文件resources/applicationContext.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <!--  开启包的自动扫描  -->
    <context:component-scan base-package="com.xiaolun.activemq.spring"/>
    <!--  配置生产者  -->
    <bean id="connectionFactory" class="org.apache.activemq.pool.PooledConnectionFactory" destroy-method="stop">
        <property name="connectionFactory">
            <!--  真正可以生产Connection的ConnectionFactory,由对应的JMS服务商提供   -->
            <bean class="org.apache.activemq.spring.ActiveMQConnectionFactory">
                <property name="brokerURL" value="tcp://192.168.10.101:61616"/>
            </bean>
        </property>
        <property name="maxConnections" value="100"/>
    </bean>

    <!--  这个是队列目的地,点对点的Queue  -->
    <bean id="destinationQueue" class="org.apache.activemq.command.ActiveMQQueue">
        <!--  通过构造注入Queue名  -->
        <constructor-arg index="0" value="spring-active-queue"/>
    </bean>

    <!--  这个是队列目的地,  发布订阅的主题Topic-->
    <bean id="destinationTopic" class="org.apache.activemq.command.ActiveMQTopic">
        <constructor-arg index="0" value="spring-active-topic"/>
    </bean>

    <!--  Spring提供的JMS工具类,他可以进行消息发送,接收等  -->
    <bean id="jmsTemplate" class="org.springframework.jms.core.JmsTemplate">
        <!--    传入连接工厂    -->
        <property name="connectionFactory" ref="connectionFactory"/>
        <!--    传入目的地，ref="destinationQueue" 表示将目的地队列配置到spring容器中，然后将其交由JmsTemplate操作这个队列    -->
<!--        <property name="defaultDestination" ref="destinationQueue"/>-->
        <property name="defaultDestination" ref="destinationTopic"/>
        <!--    消息自动转换器    -->
        <property name="messageConverter">
            <bean class="org.springframework.jms.support.converter.SimpleMessageConverter"/>
        </property>
    </bean>
</beans>
```

3、队列

（1）生产者代码

```java
package com.xiaolun.activemq.spring;

import org.apache.xbean.spring.context.ClassPathXmlApplicationContext;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.jms.core.JmsTemplate;
import org.springframework.stereotype.Service;

@Service
public class SpringMQ_Producer {

   @Autowired
   private JmsTemplate jmsTemplate;

   public static void main(String[] args) {
      ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
      SpringMQ_Producer springMQ_producer = applicationContext.getBean(SpringMQ_Producer.class);

      springMQ_producer.jmsTemplate.send(session -> session.createTextMessage("***Spring和ActiveMQ的整合case111....."));
      System.out.println("********send task over");
   }
}
```

下面的代码作用相同：

```java
@Autowired
private JmsTemplate jmsTemplate;

//等价于上面的
private JmsTemplate jmsTemplate;

@Autowired
public void setJmsTemplate(JmsTemplate jmsTemplate) {
	this.jmsTemplate = jmsTemplate;
}
```

运行该程序，可以看到前端控制台输出为：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200902153342849.png" alt="image-20200902153342849" style="zoom:80%;" />

（2）消费者代码

```java
package com.xiaolun.activemq.spring;

import org.apache.xbean.spring.context.ClassPathXmlApplicationContext;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.jms.core.JmsTemplate;
import org.springframework.stereotype.Service;

@Service
public class SpringMQ_Consumer {
   @Autowired
   private JmsTemplate jmsTemplate;

   public static void main(String[] args) {
      ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
      SpringMQ_Consumer springMQ_consumer = applicationContext.getBean(SpringMQ_Consumer.class);
      String returnValue = (String) springMQ_consumer.jmsTemplate.receiveAndConvert();
      System.out.println("****消费者收到的消息:   " + returnValue);
   }
}
```

运行程序后，前端控制台输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200902153713969.png" alt="image-20200902153713969" style="zoom:67%;" />

4、主题-阻塞方式

（1）在配置文件applicationContext.xml中的添加支持topic的配置

```xml
 <!--  这个是队列目的地,  发布订阅的主题Topic-->
    <bean id="destinationTopic" class="org.apache.activemq.command.ActiveMQTopic">
        <constructor-arg index="0" value="spring-active-topic"/>
    </bean>
    
<!-- 将主题交给JmsTemplate来支配 -->
    <property name="defaultDestination" ref="destinationTopic"/>
```

（2）生产者/消费者代码和队列中的一样。

5、主题-监听方式

项目结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200902155550250.png" alt="image-20200902155550250" style="zoom:67%;" />

类似于前面setMessageListenner监听消息那样。

（1）在配置文件中添加监听的注解

```xml
 <!--  配置Jms消息监听器  -->
    <bean id="defaultMessageListenerContainer" class="org.springframework.jms.listener.DefaultMessageListenerContainer">
        <!--  Jms连接的工厂     -->
        <property name="connectionFactory" ref="connectionFactory"/>
        <!--   设置默认的监听目的地     -->
        <property name="destination" ref="destinationTopic"/>
        <!--  指定自己实现了MessageListener的类     -->
        <property name="messageListener" ref="myMessageListener"/>
    </bean>
<!--    <bean id="myMessageListener" class="com.xiaolun.activemq.spring.MyMessageListener"></bean>-->
```

（2）创建消息监听类

```java
package com.xiaolun.activemq.spring;

import org.springframework.stereotype.Component;
import javax.jms.JMSException;
import javax.jms.Message;
import javax.jms.MessageListener;
import javax.jms.TextMessage;

/**
 * 实现MessageListener的类,需要把这个类交给xml配置里面的DefaultMessageListenerContainer管理
 * 注解 @Component 等价于 <bean id="myMessageListener" class="com.xiaolun.activemq.spring.MyMessageListener"></bean>
 */
@Component
public class MyMessageListener implements MessageListener {

	@Override
	public void onMessage(Message message) {
		if (message instanceof TextMessage) {
			TextMessage textMessage = (TextMessage) message;
			try {
				System.out.println("消费者收到的消息" + textMessage.getText());
			} catch (JMSException e) {
				e.printStackTrace();
			}
		}
	}
}
```

此时，消费者配置了自动监听，就相当于在spring里面后台运行，有消息就运行我们实现监听类里面的方法。即此时不用启动消费者，当启动主题生产者生产消息后，后台运行的消费者能够获取消息。

## 3、SpringBoot整合ActiveMQ

### 1、队列

项目结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200903105249554.png" alt="image-20200903105249554" style="zoom: 67%;" />

#### 1、生产者

1、添加pom文件增，加SpringBoot支持JMS的包。

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
        <version>2.2.1.RELEASE</version>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-activemq</artifactId>
        <version>2.2.1.RELEASE</version>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <version>2.2.1.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <version>2.2.1.RELEASE</version>
    </dependency>
</dependencies>
```

2、添加yml文件

```yaml
#Springboot启动端口
server:
  port: 7777

#ActiveMQ配置
spring:
  activemq:
    broker-url: tcp://192.168.10.101:61616 #ActiveMQ服务器IP
    user: admin #ActiveMQ连接用户名
    password: admin #ActiveMQ连接密码
  jms:
    #指定连接队列还是主题
    pub-sub-domain: false # false = Queue |  true = Topic

#定义服务上的队列名
myQueueName: springboot-activemq-queue
```

3、配置Bean

类似于Spring的ApplicationContext.xml文件。

```java
package com.xiaolun.springboot.activemq.config;

import org.apache.activemq.command.ActiveMQQueue;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.jms.annotation.EnableJms;
import org.springframework.stereotype.Component;

@Component
@EnableJms //开启Springboot的Jms
public class ConfigBean {
	@Value("${myQueueName}")
	private String myQueueName;

	@Bean
	public ActiveMQQueue queue() {
		//创建一个ActiveMQQueue
		return new ActiveMQQueue(myQueueName);
	}
}
```

4、业务类-生产类代码

```java
package com.xiaolun.springboot.activemq.queue;

import org.apache.activemq.command.ActiveMQQueue;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jms.core.JmsMessagingTemplate;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

import java.util.UUID;

@Component
public class Queue_Producer {
	@Autowired
	//JmsMessagingTemplate是Springboot的Jms模板,Spring的是JmsTemplate
	private JmsMessagingTemplate jmsMessagingTemplate;

	@Autowired
	//把ConfigBean类的ActiveMQQueue注入进来
	private ActiveMQQueue activeMQQueue;

	//发送Queue的方法
	public void producerMsg() {
		jmsMessagingTemplate.convertAndSend(activeMQQueue, "**************" + UUID.randomUUID().toString());
	}

}
```

5、主启动类

```java
package com.xiaolun.springboot.activemq;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.scheduling.annotation.EnableScheduling;

@SpringBootApplication
public class MainApp {
	public static void main(String[] args) {
		SpringApplication.run(MainApp.class);
	}
}
```

6、测试类

```java
package com.xiaolun.springboot.activemq;

import com.xiaolun.springboot.activemq.queue.Queue_Producer;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.test.context.web.WebAppConfiguration;

@SpringBootTest(classes = MainApp.class)
@RunWith(SpringJUnit4ClassRunner.class)
@WebAppConfiguration
public class TestActiveMQ {
	@Autowired
	private Queue_Producer queue_producer;

	@Test
	public void testSend() {
		queue_producer.producerMsg();
	}
}
```

启动测试类进行测试即可。

8、新需求

要求每隔3秒钟,往MQ推送消息。

（1）修改生产者Queue_Producer代码

添加下面的业务类：

```java
//间隔3秒投递,SpringBoot的Scheduled用来定时执行
	@Scheduled(fixedDelay = 3000)
	public void producerMsgScheduled() {
		jmsMessagingTemplate.convertAndSend(activeMQQueue, "**************Scheduled" + UUID.randomUUID().toString());
		System.out.println("Scheduled定时投递");
	}
```

（2）在主启动类中添加下面的注解

```java
@EnableScheduling
```

（3）测试

此时，不是启动测试类，而是直接启动主启动类，会发现控制台没3秒发送一条消息。

#### 2、消费者

项目结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200903110126623.png" alt="image-20200903110126623" style="zoom:67%;" />

1、添加pom文件，同上面的队列生产者。

2、添加yml文件

```yaml
server:
  port: 8888

spring:
  activemq:
    broker-url: tcp://192.168.10.101:61616
    user: admin
    password: admin
  jms: #指定连接的是队列(Queue)还是主题(Topic)
    pub-sub-domain: false #false代表队列,true代表主题

#定义连接的队列名
myQueueName: springboot-activemq-queue
```

3、业务类-消费者代码

```java
package com.xiaolun.springboot.activemq.consumer;

import org.springframework.jms.annotation.JmsListener;
import org.springframework.stereotype.Component;

import javax.jms.JMSException;
import javax.jms.TextMessage;

@Component
public class Queue_Consumer {
	//监听接收的方法
	@JmsListener(destination = "${myQueueName}")
	public void consumerMsg(TextMessage textMessage) throws JMSException {
		String text = textMessage.getText();
		System.out.println("***消费者收到的消息:    " + text);
	}
}
```

4、主启动类

```java
package com.xiaolun.springboot.activemq;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MainApp_Consumer {
	public static void main(String[] args) {
		SpringApplication.run(MainApp_Consumer.class, args);
	}
}
```

5、测试

依次启动消费者SpringBoot-mq_consumer，生产者SpringBoot-mq_produce，可以发现消费者没三秒钟接收一次生产者的消息。

### 2、主题

#### 1、生产者

项目结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200903110531425.png" alt="image-20200903110531425" style="zoom:67%;" />

1、添加pom文件，同上面的队列生产者。

2、添加yml文件

```yaml
server:
  port: 7777

spring:
  activemq:
    broker-url: tcp://192.168.10.101:61616
    user: admin
    password: admin
  jms:
    pub-sub-domain: true

myTopicName: springboot-activemq-topic
```

3、配置类

```java
package com.xiaolun.springboot.topic.config;

import org.apache.activemq.command.ActiveMQTopic;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.jms.annotation.EnableJms;
import org.springframework.stereotype.Component;

@Component
@EnableJms
public class ActiveMQConfigBean {
	@Value("${myTopicName}")
	private String topicName;

	@Bean
	public ActiveMQTopic activeMQTopic() {
		return new ActiveMQTopic(topicName);
	}
}
```

4、业务类-生产者代码

```java
package com.xiaolun.springboot.topic.produce;

import org.apache.activemq.command.ActiveMQTopic;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jms.core.JmsMessagingTemplate;
import org.springframework.scheduling.annotation.EnableScheduling;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

import java.util.UUID;

@Component
public class Topic_Producer {

	@Autowired
	private JmsMessagingTemplate jmsMessagingTemplate;

	@Autowired
	private ActiveMQTopic activeMQTopic;

	@Scheduled(fixedDelay = 3000)
	public void producer() {
		jmsMessagingTemplate.convertAndSend(activeMQTopic, "主题消息:    " + UUID.randomUUID().toString());
	}

}
```

5、主启动类

```java
package com.xiaolun.springboot.topic;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.scheduling.annotation.EnableScheduling;

@SpringBootApplication
@EnableScheduling
public class MainApp_TopicProducer {
	public static void main(String[] args) {
		SpringApplication.run(MainApp_TopicProducer.class);
	}
}
```

在这里，并没有配置测试类，我们先配置下面的消费者，然后再进行测试。

#### 2、消费者

项目结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200903110950049.png" alt="image-20200903110950049" style="zoom:67%;" />

1、添加pom，同上面的队列生产者。

2、添加yml文件

```yaml
server:
  port: 8888

spring:
  activemq:
    broker-url: tcp://192.168.10.101:61616
    user: admin
    password: admin

  jms:
    pub-sub-domain: true

myTopicName: springboot-activemq-topic
```

3、业务类-消费者代码

```java
package com.xiaolun.springboot.topic.consumer;

import org.springframework.jms.annotation.JmsListener;
import org.springframework.stereotype.Component;

import javax.jms.JMSException;
import javax.jms.TextMessage;

@Component
public class Topic_Consumer {

	@JmsListener(destination = "${myTopicName}")
	public void consumer(TextMessage textMessage) throws JMSException {
		System.out.println("订阅着收到消息:    " + textMessage.getText());
	}
}
```

4、主启动类

```java
package com.xiaolun.springboot.topic;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MainApp_TopicConsumer {
	public static void main(String[] args) {
		SpringApplication.run(MainApp_TopicConsumer.class);
	}
}
```

5、测试

先启动消费者springboot-mq-topic-consumer，后启动生产者springboot-mq-topic-produce，可以发现消费者每3秒钟定时接收来自生产者发送的消息。