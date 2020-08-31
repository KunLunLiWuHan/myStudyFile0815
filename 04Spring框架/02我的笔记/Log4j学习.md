# 1 介绍

## 1、概述

​		Log4j（log for java）是一个开源的、轻量级的、用于日志管理的框架。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134535.png" alt="image-20200817140922131" style="zoom:80%;" />

## 2、作用

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134536.png" alt="image-20200817141547795" style="zoom:80%;" />

## 3、操作

（1）四个关键

+ 目的地appender
+ 布局layout
+ 控制单元logger
+ 级别level

总结：

+ 以什么样的格式，按照日志的优先级，将日志输出到哪里？
+ Appender、Layout、Logger三者之间的关系：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134537.png" alt="image-20200817165157439" style="zoom: 80%;" />

（2）`#`表示注释

```properties
#每天产生一个日志文件
log4j.appender.xiaolun.File=org.apache.log4j.DailyRollingFileAppender
log4j.appender.xiaolun.File.file=D:\\testFile.log
log4j.appender.xiaolun.File.DatePattern=.yyyy-MM-dd
#输出的样式，布局
log4j.appender.xiaolun.File.layout=org.apache.log4j.PatternLayout
log4j.appender.xiaolun.File.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss,SSS} %5p(%C:%M) - %m%n

#控制台配置日志文件；及时输出
log4j.appender.xiaolun.Console=org.apache.log4j.ConsoleAppender
log4j.appender.xiaolun.Console.layout=org.apache.log4j.PatternLayout
log4j.appender.xiaolun.Console.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss,SSS} %5p(%C:%M) - %m%n

# 11xiaolun.File 报错
# log4j:ERROR Could not find value for key log4j.appender.11xiaolun.File
log4j.rootLogger=debug,xiaolun.File,xiaolun.Console
```

`rootLogger`只针对于一个输出源的情况。

（3）输出源

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134538.png" alt="image-20200817163541800" style="zoom:80%;" />

当我们修改系统时间，我们会发现对应确实生成了两个文件：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134539.png" alt="image-20200817164404475" style="zoom:80%;" />

（4）布局

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134540.png" alt="image-20200817163654380" style="zoom:80%;" />

（5）常用PatternLayout介绍

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134541.png" alt="image-20200817163739077" style="zoom:80%;" />

## 4、代码测试

1、目录结构

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134542.png" alt="image-20200817163828412" style="zoom:80%;" />

2、添加依赖

```xml
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

3、实现代码

```java
import com.sun.org.slf4j.internal.Logger;
import com.sun.org.slf4j.internal.LoggerFactory;

public class UserDao {
	private static org.apache.log4j.Logger logger = org.apache.log4j.Logger.getLogger(UserDao.class); //log4j
//	private static Logger logger1 = LoggerFactory.getLogger(UserDao.class); //log4j

	public static void main(String[] args) {
		logger.debug("hello 0817");
		logger.info("hello 0817");
		logger.warn("hello 0817");
		logger.error("hello 0817");
	}
}
```

但我们运行的时候，控制台输出信息如下：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134543.png" alt="image-20200817162527464" style="zoom: 67%;" />

在本地磁盘中，`D:\\testFile.log`文件中也会生成相应的数据。

## 5、日志级别的种类

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134544.png" alt="image-20200817163337062" style="zoom:80%;" />

口诀：打印级别往后靠。

# 2 工作用法

1、实现代码

```java
public class UserDao {
   private static org.apache.log4j.Logger logger = org.apache.log4j.Logger.getLogger(UserDao.class); //log4j

   public static void main(String[] args) {

      int age = 0;
      try {
         logger.debug("11111111");
         age = 10/0; //报错
         logger.debug("22222222");
      }catch (Exception e){
         e.printStackTrace();
         logger.error(e.getMessage(),e.getCause());
      }
   }
}
```

控制台输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134545.png" alt="image-20200817164227364" style="zoom:80%;" />

# 3 多个输出源

1、log4j.properties文件

```properties
log4j.rootLogger=debug,xiaolun.File,xiaolun.Console
#定义多个输出源
log4j.logger.com.xiaolun=debug,xiaolun.File,xiaolun.Console
log4j.logger.com.xiaolun.UserDao=info,xiaolun.File,xiaolun.Console
```

控制台输出以info为准：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134546.png" alt="image-20200817165000656" style="zoom:80%;" />

口诀：级别取精确，输出为各自。

# 4 Log4j.xml介绍

## 1、实现代码

（1）目录结构

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134547.png" alt="image-20200817172624982" style="zoom:50%;" />

（2）代码

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">

<log4j:configuration>

    <!-- 日志输出到控制台 -->
    <appender name="xiaolun.File" class="org.apache.log4j.DailyRollingFileAppender">
        <param name="File" value="D:\\testFile.log"/>
        <!-- 设置是否在重新启动服务时，在原有日志的基础添加新日志 -->
        <param name="Append" value="true"/>
        <param name="DatePattern" value=".yyyy-MM-dd"/>
        <!-- 日志输出格式 -->
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%d{yyyy-MM-dd HH:mm:ss,SSS} %5p(%C:%M) - %m%n"/>
        </layout>
    </appender>

    <appender name="xiaolun.Console" class="org.apache.log4j.ConsoleAppender">
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%d{yyyy-MM-dd HH:mm:ss,SSS} %5p(%C:%M) - %m%n"/>
        </layout>
        <!-- 过滤器设置输出的级别-->
<!--        <filter class="org.apache.log4j.varia.LevelRangeFilter">-->
<!--            &lt;!&ndash; 设置日志输出的最小级别 &ndash;&gt;-->
<!--            <param name="levelMin" value="WARN"/>-->
<!--            &lt;!&ndash; 设置日志输出的最大级别 &ndash;&gt;-->
<!--            <param name="levelMax" value="WARN"/>-->
<!--            <param name="AcceptOnMatch" value="true"/>-->
<!--        </filter>-->
    </appender>
    <!-- 多输出源-->
    <logger name="com.xiaolun" additivity="true">
        <level value="warn"/>
        <appender-ref ref="xiaolun.File"/>
        <appender-ref ref="xiaolun.Console"/>
    </logger>

    <!-- 根logger的设置，若代码中未找到指定的logger，则会根据继承机制，使用根logger-->
    <root>
        <level value="info"></level>
        <appender-ref ref="xiaolun.File"/>
        <appender-ref ref="xiaolun.Console"/>
    </root>

</log4j:configuration>
```

## 2、总结

（1）log4j.xml优先于log4j.properties。

（2）filter可以实现精确匹配，避免往后靠的大于等于，可以OnlyOne过滤出我们需要的。

（3）`additivity="true"`（true表示开启多数据源）精确匹配，停止传播。

# 5 添加1-日志处理

## 1、介绍

​		Spring Boot默认选用slf4j,logback,同时帮我们配好了日志，我们直接使用即可。其日志级别：由低往高：

  TRACE < DEBUG < INFOR < WARN < ERROR  < FATAL < OFF  

​		Spring Boot默认的日志级别是INFOR (即只打印 INFOR 及之后级别的信息)；也可以自定义级别，只需要在全局配置文件中进行修改就可以：

```properties
 logging.level.com.study.NewHelloWorld = warn  
```

+ `logging`.主配置所在的包 = warn,此时只输出`warn`及以上级别的信息。

1. 输出控制台信息

```properties
logging.file=D：/spring.boot (绝对路径)，设置文件。
logging.path = D:/log/ (放到log文件夹中)，默认文件名为：spring.log
```

+ 通过`logging.file=spring.boot`（相对于项目根目录） 可以将日志信息放到Spring Boot文件中

2. 自定义日志格式

日志输出内容元素具体如下：

+ 时间日期：精确到毫秒
+ 分隔符：标识实际日志的开始
+ 线程名：方括号括起来。
+ Logger名：通常使用源代码的类名。
+ 日志内容

其中，Spring Boot使用的默认日志框架为Logback，并用INFO级别输出到控制台。

然后，日志依赖：该依赖内容就是Spring Boot默认的日志框架logback。

其次，logging.path和logging.file不能同时配置，如若同时使用，则只有logging.file生效。

格式：

```properties
logging.pattern.console=%d{yyyy-MM-dd} [%thread] %-5level %logger{50} - %msg%n
```

+ %d：日期时间（显示格式）
+ %thread：线程名
+ %-5level：显示日志级别（-5从左显示5个字符宽度）
+ %logger{50}：设置日志长度
+ %msg：日志消息
+ %n回车

3. 控制台代码 

```java
@RestController
public class HelloWorldcontroller {
//定义（import org.slf4j.Logger;）
Logger logger =  LoggerFactory.getLogger(HelloWorldcontroller.class);

//日志处理测试
@GetMapping("testLog")
public void testLog() {
logger.debug("debug******");	//输出信息
logger.info("info*******");
}
}
```

+ 可以发现，通过在网站输出信息，我们可以在控制台看到`logger`相应的`debug`和`info`信息。