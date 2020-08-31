# 1 微服务

微服务就是一个项目可以由多个小型服务构成。spring boot可以快速开发微服务模块。

+ 简化J2EE开发。
+ 整个spring技术栈的整合（整合springmvc）。
+ 整个J2EE技术栈的整合(整合mybatis ) 。

# 2 安装软件及执行原理

## 2.1 resources目录结构 

包括`static`:静态资源（`js,css` 图片、音频、视频）；`templates`:模板文件（模板引擎：`freemarker`,`thymeleaf`；默认不支持`jsp`）；`application.properties`:配置文件，可以对端口等服务端进行配置。

用于Spring Boot内置了`tomcat`,并且不需要打成`war`包再执行。此外，Spring Boot将各个应用/第三方框架，设置成一个个场景“`starter`”,以后要用那个，只需要引入那个场景，选完之后，Spring Boot会将该场景所需要的所有依赖自动注入。

## 2.2 @SpringBootApplication注解介绍

`@SpringBootApplication`：Spring Boot的主配置类，该注解包含`@SpringBootConfiguration`和`@EnableAutoConfiguration`注解。

### 2.2.1 SpringBootConfiguration注解介绍

该注解包含`@Configuration`注解，`@Configuration`注解表示“配置类”（Spring Boot推荐使用配置类代替配置文件（`spring.xml`）），加了`@Configuration`注解的类，会自动纳入Spring容器中。

配置类举例：

```java
@Configuration
public class A{ //表示A是一个用于配置的类，相当于一个配置文件。
}
```

### 2.2.2 @EnableAutoConfiguration注解介绍

该注解包含`@AutoConfigurationPackage`和`@Import(AutoConfigurationImportSelector.class)`，使Spring Boot可以自动配置。可以找到`@Spring BootApplication`所在类的包。（原则：约定优于配置）

+  `@AutoConfigurationPackage`：作用：就会将改包及所有的子包纳入到spring容器中（即将你写的代码纳入到spring容器中），这是自己写的代码。
+ `@Import(AutoConfigurationImportSelector.class)`：引入第三方依赖（包含~包、配置）。spring boot在启动时，会根据`META-INF/spring.factories`找到相应的第三方依赖，并将该依赖引入该项目。

总结：

1. 自己写的代码，Spring Boot通过`@Spring BootConfiguration`自动帮我们配置。
2. 三方依赖通过`spring-boot-autoconfigure-2.1.3.RELEASE.jar`中的`META-INF/spring.factories`进行声名，然后通过`@EnableAutoConfiguration`开始使用即可。

## 2.3 解决安装问题

1. 要Javaweb学习文件的软件配置。
2. 当遇到依赖变红，我们首先使用clean和complie命令，之后使用reimport然后download  xx ，在这样用完之后还是有依赖爆红，我们可以采用下面的方式：即将所有的依赖都删除，然后再重新添加上即可解决。

# 3 配置原理

## 3.1 自动装配原理

### 3.1.1 源码介绍

研究`org.springframework.boot.autoconfigure.web.servlet.HttpEncodingAutoConfiguration`。通过观察该源码，发现：

首先，`@Configuration`：标识此类是个配置类，以及将此类纳入`springioc`容器；其次，`@EnableConfigurationProperties(HttpProperties.class)`：通过`HttpProperties`将编码设置为`UTF-8`（即自动装配utf-8,如何修改编码：通过`HttpProperties`的 `prefix`+属性名 进行修改（配置文件，~或者`properties`中进行修改））。

即：该注解给了默认编码`UTF-8`，并且提供了`prefix`+属性名，进行修改方式进行修改。

同时该注解中包含条件注解：

```java
@ConditionalOnProperty(prefix = "spring.http.encoding", value = "enabled", matchIfMissing = true)
```

+ 当属性满足要求时，此条件成立。要求为：如果没有配置`spring.http.encoding.enable =xxx(matchIfMissing)` 表示如果在`propertie`中没有配置即`true`，表示此条件成立)则成立。

 总结：

+ 即每一个`XXXAutoConfiguration`都有很多条件`@ConditionalOnXXX`，当这些条件满足时，则此装配自动生效（比如，UTF-8）。但是我们可以手工修改自动装配：`XXProperties`文件中的 `prefix`.属性名 = `value`

+ 全局配置文件中的`key`，来源于某个`Properties`文件中的~+属性名

  使用`application.properties`中`debug = true`可以知道spring boot开启了那些自动装配、禁止了那些自动装配。其中：

  `Positive match`:列表中标识 Spring Boot自动开启的装配`Negative match:`列表中标识没有开启的装配。

### 3.1.2 main（）方法

 程序入口main（）方法 -> `SpringApplication`方法 ->`initialize`()方法 ->调用`setInitializers`()方法 -> `getSpringFactoriesInstances`（）方法 ->`loadFactoryNames`()方法 -> `ClassLoader.getSystemResources(FACTORIES_RESOURCE_LOCATION) = “META-INF/spring.factories`”(也就是依赖包下的`spring-boot-autoconfigure-2.1.3.RELEASE.jar下的META-INF/spring.factories`)

然后根据自动配置文件的类，进行实例化，也就是创建对象。

举例：

`META-INF/spring.factories -> org.springframework.boot.autoconfigure.data.redis -> RedisAutoConfiguration类 -> RedisProperties.class -> @ConfigurationProperties(prefix = "spring.redis")`，这里也就是Redis的默认配置，可以通过全局文件中配置来进行属性的替换，来覆盖默认配置。

## 3.2 配置文件机yml使用

Spring Boot自动配置（约定，使用端口8080），可以使用配置文件对默认的配置进行修改。

系统默认全局配置文件：`application.properties`和`application.yml`，其中`yaml`不是一个标记文档(`makeup language)`，和`xml`文件许多不同，`xml`文件的格式为： 

```HTML
<server>
	<port>8082</port>
</server>
```

 `yml`通过垂直的方式来指定层次关系，默认不写引号，其中，其中双引号“”会将转义字符进行转义。

###  3.2.1 类型安全的配置

绑定properties配置文件中的内容:

```matlab
@Component	//将此javabean放到spring容器中
@ConfigurationProperties(prefix = "student")
public class student1{
}
```

+ 通过`prefix = "student"`前缀，对`properties`中的内容进行引用

类型安全配置类和注值类型进行比较

 

|                  | @ConfigurationProperties(yml/properties) | @value(“xx”) | 举例                                                         |
| ---------------- | :--------------------------------------: | :----------: | :----------------------------------------------------------- |
| 注值             |                 批量注入                 |   单值注入   |                                                              |
| 松散语法         |                   支持                   |    不支持    | userName(驼峰语法) = user-name                               |
| springEL         |                  不支持                  |     支持     | 将properties配置文件的值赋值给name                           |
| JSR303数据  校验 |                   支持                   |    不支持    | email地址                                                    |
| 注入复杂类型     |                   支持                   |    不支持    | 简单类型：8个基本类型/String/Date。  例如复杂类型：Map类型。 |

`@PropertySource()`：默认会加载`application.properties/application.yml`文件中的数据。

```java
@PropertySource(value = {{"classpath:school.properties"})//加载school.properties
```

+ 它只能加载`properties`不能加载`yml`。
+ 类型安全的配置，在上述`student1`类中要书写相应的`get`()和`set`()方法。

##  3.3 多环境的切换（profile配置）

 针对不同的环境对不同的配置所提供的。

默认Spring Boot会读取application.properties环境

多个环境配置：

```properties
application-环境名.properties	//主配置环境
application-dev.properties	//开发环境
application-test.properties	//测试环境
```

+ 如果要选择某一个具体的环境，需要在`application.properties`中指定：

  `spring.profiles.active`= 环境名。

### 3.3.1 动态切换环境

 在上文将环境写好的情况下，可以通过运行参数指定环境或者命令行方式的形式来动态切换环境。

1. 通过运行参数指定环境

 run Configuration -> Arguments ->program arguments

将其放到里面：--application-环境名.properties

2. 命令行方式

 java -jar 项目名.jar --spring.profiles.active=环境名

+ 如果将Spring Boot中的a`pplication.properties`给注释掉，Spring Boot仍然会读取其他`application-环境名.properties`中的配置

## 3.4  配置文件位置及优先级

properties和yml中的配置，相互补充；如果冲突，则properties优先级高。spring boot默认能够读取的application.prperties/application.yml，这2个文件可以存在以下4个位置：

|         file:项目根目录/config         |
| :------------------------------------: |
|            file:项目根目录             |
| classpath:项目根目录/config(构建目录)  |
| classpath:项目根目录(现在项目中的路径) |

注意：

+ 如果某项配置冲突，则优先级从上往下。

+ 若不冲突，则配置互补可互补使用。

## 3.5  外部配置文件及加载顺序问题

### 3.5.1 配置项目名：

```yaml
 server:  
 	servlet:  
 		context-path: /01-Spring Boot-NewHelloWorld 
```

+ 电脑上网址输入：[http://localhost:8080/01-Spring Boot-NewHelloWorld/ServiceTest2](http://localhost:8080/01-springboot-NewHelloWorld/ServiceTest2) ,这是由于增加了地址前缀的缘故。  

### 1.1.2. 补救方法

| 通过增加项目外部的配置文件的方式。                           |
| ------------------------------------------------------------ |
| run Configuration -> Arguments ->program  arguments：--spring.config.location=D:\java\StsDownloadPlugin\application.properties  文件路径。 |
| 通过命令行，调用外部配置文件的方式                           |
| java -jar 01-Spring Boot-NewHelloworld-0.0.1-SNAPSHOT.jar（打成jar包名称）  --spring.config.location=D:\java\StsDownloadPlugin\application.properties（文件路径） |
| 适用于大面积文件需要修改，因为外部的配置文件优先级大于内部。 |
| 通过项目运行参数补救                                         |
| run Configuration -> Arguments ->program  arguments：  --server.port = 8883(举例，直接写) |
| 适用于单个参数需要修改。                                     |
| 打成jar包的步骤：  右击项目-->Run As-->Maven clean ->Maven build...->goal输入package即可。 |

+ 命令行报错；没有主清单属性，需要在pom文件中增加下述依赖。

```html
<build>       
    <plugins>                             
        <plugin> 
            <groupId>org.springframework.boot</groupId>  
            <artifactId>spring-boot-maven-plugin</artifactId>                             </plugin>       
    </plugins>  
</build>  
```

## 3.6  创建父工程

不用每一个工程中都做父级依赖，我们自己创建一个父工程，该工程对jar包进行了管理，其方法为：

  New File -> ParentFile -> maven  module -> Spring Boot-Children1 -> next -> finish  

添加父依赖：

```html
| <dependencyManagement>                    
    <dependencies>                             
        <dependency>   
            <groupId>org.springframework.boot</groupId>                   				<artifactId>spring-boot-dependencies</artifactId>                                     <version>2.1.3.RELEASE</version>                                      <!--  指定版本问题 -->                                     					<type>pom</type>                                    
            <scope>import</scope>                             
        </dependency>                    
    </dependencies>  
</dependencyManagement> 
```

+ 指定版本问题要写，不然要报错。

```html
<build>
		<plugins>
				<plugin>
					<groupId>org.apache.maven.plugins</groupId>
					<artifactId>maven-compiler-plugin</artifactId>
								<configuration>
										<source>1.8</source>
										<target>1.8</target>
								</configuration>
						</plugin>
		</plugins>
</build>
```

+ 指定`Java`版本号。

```java
@RequestMapping("/hello1/{msg}")
@ResponseBody
    public String show(@PathVariable String msg) {
    return "show"+ msg;
}
```

+ @`ResponseBody` :返回的是`Restful`内容，不使用该注解会进行跳转。
+ @`RestController`:如果每个功能返回的都是Restful内容，那么可以该注解。
+ 网站输入：http://localhost:8080/hello1/smile（传递的值），输入为：`showsmile`。

# 4 @ImportResource、配置类、占位符表达式

## 4.1  @ImportResource配置xml文件

spring boot自动装配/自动配置，所以spring等配置文件，默认会被spring boot自动给配置好。

如果要自己编写spring等配置文件，spring boot默认不识别，如果需要识别，则需要在Spring Boot主配置类上通过@`ImportResource`指定默认文件的路径，也就是在@`SpringBootApplication`上面加@`ImportResource(locations= {"classpath:spring.xml"})`。`

配置文件分两种：

+ `xml`文件，通过注解配置,但是不推荐上述的手写spring配置文件,Spring Boot推荐注解形式：

+ 写配置类，然后加注解`@Configuration @Bean`

```java
@Bean	//id就是方法名 stuService2
public StudentService stuService(){
//	StudentService stuService = new StudentService();
	return new StudentService();	
}
```

+ 新建xml文件时，-> spring bean configration file。
+ 在断网时，也会出现无法注入@Bean的情况。
+ 上述返回值等价于：<bean class="xxxx"">。

## 4.2 配置类

java 配置是通过 @`Configration` 和 @`Bean` 注解实现 `XML` 配置,即：

1、创建业务逻辑层，里面有个`StudentService`类；

2、创建配置类`AppCofig`,上面加上@`Configuration`；

3、添加@`Bean`。

举例：如上表的举例。

4、在`controller`包中使用

```java
@RestController
public class HelloWorldcontroller {	
@Autowired
	ApplicationContext Context;//spring ioc容器
	@GetMapping("ServiceTest2")
	public void ServiceTest() {
		System.out.println(Context.containsBean("stuService"));
	}
}	
```

+ `Context.containsBean("stuService")`:确认`Bean`是否被注入。

## 4.3 占位符表达式

spring boot全局文件中的占位符表达式：

|                      随机数值                       |
| :-------------------------------------------------: |
|                 ${random.uuid}:uuid                 |
|             ${random.value}:随机字符串              |
|              ${random.int}:随机整形数               |
|              ${random.long}:随机长整数              |
|          ${random.int(10)}:10以内的整型数           |
|      ${random.int[1024,65536]}:指定随机数范围       |
| 1、举例：student.name = ${random.value}//随机字符串 |

 

### 4.3.1 引用变量值

在yml中引用properties的值

```yaml
properties文件中：
student.uesr.name = zl123
yml文件中：
student:
	name:  ${student.uesr.name：无名} 
```

+ 引用（无名：引用内容不存在的默认值,`yml`文件和`properties`相互补充。

# 5 处理Web静态资源

## 5.1 介绍

Spring Boot提供了`spring-boot-starter-web`，为web开发予以支持，同时也提供了嵌入的`Tomcat`以及`springmvc`的依赖。

Spring Boot开发Web项目,会引用静态资源（例如html,css,js），在Spring Boot中加载静态资源和普通的web应用中不太一样。

默认情况下，静态内容提供位置:classpath的/static,/public或者/META-INF/resources文件夹或从ServletContext根目录。

+ "classpath:/META-INF/resources/", 
+ "classpath（类路径）:/resources/",
+ "classpath:/static/",
+ "classpath:/public/" 。

配置代码：

```properties
spring.resources.static-locations = classpath:/static/
```

推荐：Spring Boot约定：Spring Boot将一些目录结构设置成静态资源存放目录，我们的静态资源直接放入到这些目录即可（`webapp`），位置在`ResourceProperties`类中的`CLASSPATH_RESOURCE_LOCATIONS`属性设置中存放着。

## 5.2 WebMvcAutoConfiguration源码解读

`WebMvcAutoConfiguration`类中的welcomePageHandlerMapping()->getIndexHtml()->location+”index.html”,即任意一个静态资源目录的Index.html就是欢迎页，访问：http://localhost:8080/（地址）。

同时，网站中，网站标签的Logo是固定名字：favicon.ico

源码包含：**/favicon.ico（**任何静态资源目录），faviconRequestHandler（），阅读源码得知，只需要将favicon.ico文件放入任意静态资源目录中。

访问http://localhost:8080/时会发现Logo已经被换掉了。

总结：

+ 用静态资源，只需要将静态资源放入以上目录就即可。

+ 其他特定的文件（欢迎页、ico）,只需要根绝约定（index.icon,favicon.ico），放入该目录即可。

## 5.3 自定义消息转换器

```java
@Bean
public StringHttpMessageConverter stringHttpMessageConverter(){
	StringHttpMessageConverter converter = new 		StringHttpMessageConverter (Charset.forName(“UTF-8”));//ISO-8859-1
	return converter;
} 

```

同时，通过测试该类中`test`()方法(返回“hello,你好”)，在ISO-8859-1时会乱码。Spring Boot默认配置了消息转换器。

注意：

+ 在以上目录存放资源文件后，访问时，http://localhost:8080/hello.html时，`hello.html`不需要加前缀，直接访问即可。

#  6 整合外置tomcat及使用JSP开发

以前的Spring Boot默认自带一个内置的tomcat，不需要打war包，直接通过jar包即可运行。但是，如果要整合jsp开发，就需要单独配置一个外置的tomcat，需要打war包。

1. Spring Boot整合Jsp开发步骤：

+ 新建boot项目， 选择war包。

+ 建立基本的web项目所需要的目录结构

  webapp/WEB-INF

  webapp/WEB-INF/web.xml(自动配置，不需要)

  webapp/index.jsp

+ 创建tomcat实例，并部署项目。

+ 访问。

2. 总结：

程序打包发布发布到独立的tomcat需要经历下面的步骤

+ 需要打成war包。
+ 增加依赖。
+ 启动类继承Spring BootServletInitializer,重写configure方法。

## 6.1 添加依赖

```html
<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-tomcat</artifactId>
		<scope>provided</scope>
</dependency>
```

+ provided:将项目打包时，不需要将内置的tomcat一起打包，但是使用外置的tomcat访问不到。
+ 外置Tomcat为localhost为8081，所以外置apache访问端口号：http://localhost:8081/。
+ 电脑上apache安装地址：C:\Program Files\Apache Software Foundation\Tomcat 8.5。

# 7 整合MongDB

## 7.1 介绍

MongDB是一个基于分布式文件存储的数据库。由`C++`语言编写。介于关系数据库和非关系型数据库之间的产品。旨在为WEB应用提供可扩展的高性能数据存储方案。支持的数据结构非常松散，是类似`json`的`bson`格式。

## 7.2 控制台操作

```sql
进入命令管理器，输入：mongo启动：MongDB
show dbs：展示列表
输入：db.createUser({user: 'root',pwd: '123',roles:[ 'root']})
返回：Successfully added user: { "user" : "root", "roles" : [ "root" ] }
输入：db.auth('root','123')
返回：1,暗示登录成功。
```

```sql
db.createUser({ //个人登录信息。
  user: 'admin',  // 用户名
  pwd: '123',  // 密码
  roles:[{
    role: 'root',  // 角色
    db: 'admin'  // 数据库
  }]
})
```

# 8 整合Neo4J

## 8.1 Neo4J介绍及安装

`Neo4J`是一个高性能的，`NOSQL`图形数据库，它是一个嵌入式、基于磁盘的、具备完全的事务特性的持久化引擎，但是他将结构化数据存储在网络上，而不表中。一个对象看成一个网络上的节点，节点之间有关系。

## 8.2 Cypher语言的学习

1. 示例语句

```sql
create (A:Person {name:’Jack’})-[:Friend]->(B:Person{name:’Tom’})-[:Friend]->(B:Person {name:lucy}),(A)-[:know]->(C)
```

+ A，B是朋友关系，B，C是朋友关系，A认识C，但是C不认识A。
+ （）里面是节点，[]里面是关系，{}里面属性，>表示关系的方向。

2. 要查询A的一个叫“tom”的朋友语句:

```sql
match (a)-[:Friend]->(b)
where b.name = ‘tom’
return b
```

# 9  Spring Boot的事务管理

在Spring Boot中推荐使用@`Transactional`注解来声名事务。

当我们使用了 `spring-boot-starter-jdbc` 或 `spring-boot-starter-data-jpa`依赖的时候，Spring Boot会自动默认分别注入了`DataSourceTransactionManager`或`JpaTransactionManager`

事务使用在`service`上的，因为`servcie`上面是业务，主要是因为保证业务成功都成功。

## 9.1 创建项目（Ecp）

```java
@Transactional 
	public void saveUser(Users users) {
		//接收来自controller的对象
		userDao.addUser(users);
		int i = 6/0; //测试该类中有功能出错 
	}
```

+ 在Spring Boot中整合`JdbcTemplate`中`Spring Boot-JdbcTemplate`项目上，在`service`层中添加@`Transactional`

+ 在该类中，因为有错误的出现，事务管理可以不让新增的数据存入数据库，造成数据的回滚。

