# 2 异常处理和单元测试

## 2.1 异常处理方式

对于异常处理提供五种方式。

### 2.1.1 自定义异常错误界面

Spring Boot默认的处理异常的机制：默认已经提供了一套处理异常的机制，一旦程序中出现了异常，Spring Boot会向/error的url发送请求，Spring Boot中的`BasicExceptionController`会处理`/error`请求，然后跳转到默认显示异常的界面来展示异常信息。

1. 服务器端代码

```java
@Controller
public class DemoController {
    @RequestMapping("/show")
    public String showInfo(){
        String str = null;
        str.length(); //空指针异常
        return "index";
    }
}
```

2. 前端代码  

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>错误提示界面</title>
</head>
<body>
        出错了，请与管理员联系。。。
        <span th:text = "${exception}"></span>
</body>
</html>
```

+ `th:text = "${exception}`获取不到异常的值，不知道原因。但是可以跳转到`error.html`界面。
+ 如果我们需要将所有的异常统一跳转到自定义的错位界面，需要在`src/../templates`目录下创建`error.html`界面,注意名称必须叫`error.html`。

### 2.1.2 @ExceptionHandle注解处理异常

1. 服务器端代码

```java
@ExceptionHandler(value = {java.lang.NullPointerException.class})
public ModelAndView nullPointerException(Exception e){
    ModelAndView mv = new ModelAndView();
    mv.addObject("error",e.toString());
    mv.setViewName("error2");
    return mv;
}

```

+ `@ExceptionHandler`的`“value”`是一个处理异常类的值。
+ `nullPointerException`方法需要返回一个`ModelAndView`，目的是可以让我们封装异常信息以及视图的指定。
+ `Exception e`:会将产生的异常对象注入到方法中，然后再视图`error2.html`中进行数值的获取。
+ `<span th:text = "${error}"></span>` ：`error2.html`中的th中的数值要变成“error”，对应`“mv.addObject("error",e.toString());”`（将异常信息带回到界面）。
+ 需要对每一个异常都要书写一个方法，代码量很大；并且这个异常处理方法值针对于当前的`Controller`,其他的`Controller`需要重新添加。

### 2.1.3 @ControllerAdvice+@ExceptionHandle注解处理异常

```java
@ControllerAdvice
public class GlobalException {
    @ExceptionHandler(value = {java.lang.NullPointerException.class})
    public ModelAndView nullPointerException(Exception e){
        ModelAndView mv = new ModelAndView();
        mv.addObject("error",e.toString());
        mv.setViewName("error2");
        return mv;
    }
}
```

+ 需要创建一个能够处理异常的全局异常类，在该类上添加`@ControllerAdvice`注解。此时，所有的对应的异常`NullPointerException`信息都会汇集到这里来。

### 2.1.4 配置Simple MappingExceptionResolver处理异常

```java
@Configuration
public class GlobalException {
        @Bean
    public SimpleMappingExceptionResolver getSimpleMappingExceptionResolver(){
            SimpleMappingExceptionResolver resolver = new SimpleMappingExceptionResolver();
            Properties mappings = new Properties();
            mappings.put("java.lang.NullPointerException","error2");
            resolver.setExceptionMappings(mappings);
            return  resolver;
        }
}
```

+ `mappings.put("java.lang.NullPointerException","error2");` ：参数一：异常的类型，必须为异常的全名；参数二：视图名称。
+ `resolver.setExceptionMappings()`:设置异常和视图异常映射信息。
+ 缺点是不能携带异常信息到`“error2.html`”界面只能做异常信息和视图的映射；而`@ControllerAdvice+@ExceptionHandle`注解可以。

### 2.1.5 自定义HandlerExceptionResolver类处理异常

```java
@Configuration
public class GlobalException implements HandlerExceptionResolver {
    @Override
    public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
        ModelAndView mv = new ModelAndView();
        //判断不同异常类型，做不同视图跳转
        if (ex instanceof NullPointerException){
            mv.setViewName("error2");
         }
        mv.addObject("error",ex.toString());
        return mv;
    }
}
```

+ 需要在全局异常处理类中实现“`HandlerExceptionResolver`”接口。
+ 返回值为“`ModelAndView`”类型，通过这样的方式就可以将异常信息返回，弥补了`Simple MappingExceptionResolver`处理异常的缺点。

## 2.2 整合Junit单元测试

### 2.2.1 修改pom文件

```html
<dependencies>
    <!--01Web启动器-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!--02Thymeleaf启动器-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    <!--03Junit单元测试-->
    <dependency>
    <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
    </dependency>
</dependencies>
```

+ 主要是导入Junit单元测试的启动器。

### 2.2.2 编写业务代码

#### 2.2.2.1 Dao层代码

```java
@Repository //持久层
public class UserDaoImpl {
    public void saveUser(){
        System.out.println("inser into user....");
    }
}
```

#### 2.2.2.2 Service层

```java
@Service //业务层
public class UserServiceImpl {
    @Autowired
    private UserDaoImpl userDao;
    public void addUser(){
        this.userDao.saveUser();
    }
}
```

+ 在业务层调用持久层的接口，用来封装数据。

### 2.2.3 整合Junit做单元测试

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes = {App.class})
public class UserServiceTest {

    @Autowired
    private UserServiceImpl userService;
    
    @Test
    public void testAddUser() {
        this.userService.addUser();
    }
}
```

+ 对业务层对象进行测试。

+ @RunWith` :测试类启动器，其中`SpringJUnit4ClassRunner`：让`Junit`和S

  pring环境进行整合。

+ `@SpringBootTest(classes = {App.class})`：首先当前类为Sprign Boot测试类；其次是加载Spring Boot,启动 Spring Boot。

# 3 热部署

Spring Boot的热部署方式分为两种：SpringLoader插件和DevTools工具。

## 3.1 SpringLoader插件

### 3.1.1 以maven插件方式实现SpringLoader

```html
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <dependencies>
                <dependency>
                    <groupId>org.springframework</groupId>
                    <artifactId>springloaded</artifactId>
                    <version>1.2.6.RELEASE</version>
                </dependency>
            </dependencies>
        </plugin>
    </plugins>
</build>

```

+ 无法下载从外网，报错：“Dependency 'org.springframework:springloaded:1.2.6.RELEASE' not found”。

+ 使用maven命令`“spring-boot:run”`进行启动Spring Boot，不是使用启动类启动代码。(进入 cmd 环境中，切换到项目路径中，然后输入 mvn spring-boot:run 回车，项目就可以启动了)。

+ 缺陷：只能对java代码做部署处理，对于界面无能为力。同时，这种方式的热部署程序是在系统的后台以进程的形式运行。会报出“端口抢占”的错误。需要手动关闭进程。

  在后台进程中找到“java.exe”的进程，点击关闭掉。

+ 综上，这是不方便的。

### 3.1.2 在项目中直接使用jar包的形式

添加springloaded的jar包到项目中。启动方式有点改变，如下：

<img src="E:\01学习资料\04Spring框架\02我的笔记\Spring Boot进阶1.assets\image-20200413171324789.png" alt="image-20200413171324789" style="zoom:80%;" />

缺陷：只能对java代码做部署处理，对于界面无能为力，不会有“端口抢占”的异常，比第一种启动方式更好一点。

## 3.2 DevTools工具

Springloaded和DevTools工具区别在于：前者在创建项目时使用热部署的方式，而后者采用的是一种重新部署的方式。

### 3.2.1 修改pom文件

```html
<!--放到项目中-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <scope>runtime</scope>
    <optional>true</optional>
</dependency>
<!--粘贴到父工程-->
<build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <fork>true</fork>
                    <addResources>true</addResources>
                </configuration>
            </plugin>
        </plugins>
    </build>。
```

然后，开启自动编译：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083445.png" alt="image-20200710111607014" style="zoom:80%;" />

注册（更新）热部署，按住`ctrl + shift + alt + /`,选择`Registry，将下面的代码打勾，打勾完后，重启IDEA生效。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083446.png" alt="image-20200710111843633" style="zoom: 67%;" />

# 4 缓存技术

## 4.1 整合Ehcache

### 4.1.1 创建Ehcache等配置文件

#### 4.1.1.1 修改pom文件

1. 创建Ehcache等配置文件

文件名：ehcache.xml,存放位置：src/resource/ehcache.xml

```html
核心代码：
<!--08缓存支持启动器-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
<!--09Ehcache坐标-->
<dependency>
    <groupId>net.sf.ehcache</groupId>
    <artifactId>ehcache</artifactId>
</dependency>
```

+ 主要是添加缓存文件。

2. 修改application.properties文件

```properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/db2?useUnicode=true&characterEncoding=utf8&characterSetResults=utf8&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=123

#使用druid类型的连接池<version>1.1.8</version>要加上
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource 

#实体类发生更改而数据库表做出相应的更改且不破坏数据库现有的数据,
spring.jpa.hibernate.ddl-auto=update
#调试时，控制台输出sql语句
spring.jpa.show-sql=true 
spring.cache.ehcache.config=classpath:ehcache.xml 
```

+ ehcache缓存技术要加上classpath这个属性，不然会报错。

#### 4.1.1.2 编写ehcache.xml文件

```xml
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd">
    <diskStore path="java.io.tmpdir"/>

    <defaultCache
            maxElementsInMemory="10000"
            eternal="false"
            timeToIdleSeconds="120"
            timeToLiveSeconds="120"
            maxElementsOnDisk="10000000"
            diskExpiryThreadIntervalSeconds="120"
            memoryStoreEvictionPolicy="LRU">
        <persistence strategy="localTempSwap"/>
    </defaultCache>
    <!--自定义缓存策略-->
    <cache  name = "users"
            maxElementsInMemory="10000"
            eternal="false"
            timeToIdleSeconds="120"
            timeToLiveSeconds="120"
            maxElementsOnDisk="10000000"
            diskExpiryThreadIntervalSeconds="120"
            memoryStoreEvictionPolicy="LRU">
        <persistence strategy="localTempSwap"/>
    </cache>
</ehcache>
```

+ 自定义缓存策略的书写格式如上。

+ http://ehcache.org/ehcache.xsd：是进行修改原文件的结果，原文件可以通过“`net.sf.ehcache:ehcache:2.10.6”`jar包中的“`ehcashe-falsesefe.xml”`复制得到。

  Setting→Schemas and DTDs →Ingored Schemas and DTDs中添加“http://ehcache.org/ehcache.xsd”即可避免报错。

#### 4.1.1.3 修改启动文件

```java
@SpringBootApplication
@EnableCaching //要添加上
public class App {
    public static void main(String[] args) {
        SpringApplication.run(App.class,args);
    }
}
```

#### 4.1.1.4 编写实体类

```java
@Entity //实体类
@Table(name = "t_users")//数据库表名
public class Users implements Serializable { //实现序列化接口
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id") //指名表中属性名字
    private Integer id;

    @Column(name = "name")
    private String name;

    @Column(name = "age")
    private Integer age;

    @Column(name = "address")
    private String address;

    public Integer getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public Integer getAge() {
        return age;
    }

    public String getAddress() {
        return address;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "Users{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", age=" + age +
                ", address='" + address + '\'' +
                '}';
    }
```

+ 主要是该类要实现序列化接口“implements Serializable”。

5.1.1.4 编写Service类

1. 接口类

```java
public interface UsersService {
    List<Users> findUserAll();
    Optional<Users> findUserById(Integer id);
    Page<Users> findUserByPage(Pageable pageable);
    void saveUser(Users users); //插入
}
```

2. 接口实现类

```java
@Service
public class UsersServiceImpl implements UsersService{

    @Autowired
    private UsersRepository usersRepository;

    @Override
    public List<Users> findUserAll() {
        return this.usersRepository.findAll();
    }

    @Override
    @Cacheable(value = "users") 
    public Optional<Users> findUserById(Integer id) {
        return this.usersRepository.findById(id);
    }

    @Override
    public Page<Users> findUserByPage(Pageable pageable) {
        return this.usersRepository.findAll(pageable);
    }

    @Override
    public void saveUser(Users users) {
        this.usersRepository.save(users);
    }
}
```

+ `@Cacheable(value = "users")`：对当前查询的对象做缓存处理,"`users`"缓存策略名称（对应`ehcache.xml`文件中的自定义策略）

#### 4.1.1.5 测试类

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes = App.class)
public class UsersServiceTest {

    @Autowired
    private UsersService usersService;
    @Test
    public void testFindUserById(){
        //第一次查询
  System.out.println(this.usersService.findUserById(1));
        //第二次查询
  System.out.println(this.usersService.findUserById(1));
    }
}
```

+ 虽说上面写的是进行两次查询，但是第二次是在缓存中进行的，命令只执行一次。

+ 控制台输出：

`Hibernate: select users0_.id as id1_0_0_, users0_.address as address2_0_0_, users0_.age as age3_0_0_, users0_.name as name4_0_0_ from t_users users0_ where users0_.id=?`

`Optional[Users{id=1, name='张三三', age=10, address='北京海淀区'}]`

`Optional[Users{id=1, name='张三三', age=10, address='北京海淀区'}]`

### 4.1.2  注解@Cacheable和@CacheEvict的使用

#### 4.1.2.1 @Cacheable 使用

该注解可以把方法的返回值添加到enchace中做缓存。

value属性：指定一个ehcache配置文件中的缓存策略，如果没有给定的value值，则表示使用默认的缓存策略。

key属性:给存储的值起个名称，在查询时如果有名称相同的，则从已知缓冲中将数据返回。默认将当前对象作为key的值。

```java
@Override
@Cacheable(value = "users",key = "#pageable.pageSize") //以page的尺寸作为key值文件
public Page<Users> findUserByPage(Pageable pageable) {
    return this.usersRepository.findAll(pageable);
}
```

+ `key = "#pageable.pageSize`：本例不是以当前对象作为key的值，而是以对象的尺寸作为key，如果重新新建一个对象，但是“对象的尺寸”和第一次的key相同，则从缓冲中拿取数据。

#### 4.1.2.2 @CacheEvict的使用

该注解的作用主要是清除缓存。

1. 接口实现类代码

```java
@Override
@CacheEvict(value = "users",allEntries = true) 
public void saveUser(Users users) {
    this.usersRepository.save(users);
}
```

+ `CacheEvict(value = "users",allEntries = true)`： 清空缓存，属性`allEntries`是`boolean`类型，表示是否需要清除缓存中的所有元素。

2. 测试类代码：

```java
@Test
public void testFindAllById(){
    //第一次查询
  System.out.println(this.usersService.findUserAll().size());

    Users users = new Users(); //存入一个新数据
    users.setName("小伦");
    users.setAge(45);
    users.setAddress("南京");
    this.usersService.saveUser(users);

    //第二次查询
  System.out.println(this.usersService.findUserAll().size());
}
```

+ 在使用`@CacheEvict`注解之后，可以发现缓存被清空，存入一个数据后再查询，数据的大小会有变动。

## 4.2 整合Spring Data Redis

### 4.2.1 介绍

<img src="E:\01学习资料\04Spring框架\02我的笔记\Spring Boot进阶1.assets\image-20200413222303486.png" alt="image-20200413222303486"  />

开发中使用Redis作为缓存中间件，它是一个开源的数据结构存储系统，可以用作数据库、缓存和消息中间件。有一个中文官网，可以字上述可视化界面控制台上使用相应的命令，比如“`Append message hello`”给关键字“`message`”添加“`hello`”。

Spring Data Redis是属于Spring Data下的一个模块，作用是简化对于Redis的操作

### 4.2.2 整合

#### 4.2.2.1 修改pom坐标

```html
<!-- 04SpringBooT整合Redis依赖 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-redis</artifactId>
		<!-- 05SpringBooT整合test依赖 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
		</dependency>
```

+ 除了增加redis依赖外，还要增加数据库的依赖，不然会报错，原因未知。

#### 4.2.2.2 配置properties文件

```properties
#Redis配置
#Redis数据库索引，默认为0
spring.redis.database= 0
spring.redis.host=localhost
spring.redis.port=6379
# 最大活跃连接数，连接池在同一时间能够分配的最大活动连接的数量, 如果设置为非正数则表示不限制
spring.redis.jedis.pool.max-active=8
# 等待可用连接的最大时间，负数为不限制
spring.redis.jedis.pool.max-wait=-1
# 最大空闲连接数,连接池中容许保持空闲状态的最大连接数量,超过的空闲连接将被释放,
#如果设置为负数表示不限制
spring.redis.jedis.pool.max-idle=8
#最大空闲连接数,,连接池中容许保持空闲状态的最小连接数量,低于这个数量将创建新的连接,
#如果设置为0则不创建
spring.redis.jedis.pool.min-idle=0
#连接超时时间(毫秒)
spring.redis.timeout=5000

#数据库配置,不配置会报错
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/db2?useUnicode=true&characterEncoding=utf8&characterSetResults=utf8&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=123

#使用druid类型的连接池<version>1.1.8</version>要加上
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
```

+ 该文件主要是配置连接Redis的信息。

#### 4.2.2.3 创建配置类

```java
@Configuration
public class RedisConfig {
  
    @Bean
    @ConditionalOnMissingBean(name = "redisTemplate")
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) throws UnknownHostException {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(redisConnectionFactory);
        template.setDefaultSerializer(new GenericJackson2JsonRedisSerializer()); //设置序列化器
        return template;
    }
}
```

+ 完成对于Redis的整合的配置。
+ 默认情况下，如果保存对象，此时会使用默认的jdk序列化机制，序列化后的数据，保存到Redis,比如`stringRedisTemplate.opsForValue().set("emo-01",empById)`; 其中`empById`：表示数据库查询到的对象。会发现，Redis中数据不能识别，我们要化为`json`格式的。
+ 将数据以json格式保存步骤：
  + 自己将对象转为`json`
  + 使用redisTemplate默认的序列化规则。`template.setDefaultSerializer(new GenericJackson2JsonRedisSerializer())`。

#### 4.2.2.4 创建测试类

1. 测试1

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes = SpringAppRedis.class)
public class RedisTest {
	
	@Autowired
	private RedisTemplate<String, Object> redisTemplate;
	@Test	//添加一个字符
	public void testSet() {
		this.redisTemplate.opsForValue().set("key2", "北京尚学堂");
	}
	@Test	//获取一个字符
	public void testGet() {
	String value =	(String) this.redisTemplate.opsForValue().get("key2");
	System.out.println(value);
	}
}
```

+ 测试类有两个方法，一个是添加一个字符串，一个是获取一个字符串。
+ 其中`set("key2", "北京尚学堂");`方法可以将数据放到Redis中.其中`get("key2");`可以将键=”key2”的数据取出来,由于`value`值对应的`Object`类型,所以,需要将其进行成`String`类型的。

+ 测试2：内置StringRedisTemplate对象读取数据测试

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes = App.class)
public class RedisTest {
    @Autowired
    StringRedisTemplate stringRedisTemplate; //引入，简化操作Redis的2个template-操作字符串
    @Autowired
    RedisTemplate redisTemplate; //k-v都是对象的
    @Test
    public void testRedis01(){
//给Redis中添加数据，对应Redis中文网站中的命令操作
//        stringRedisTemplate.opsForValue().append("msg1","hello"); 
//   String msg = stringRedisTemplate.opsForValue().get("msg1"); //获取数据
//        System.out.println(msg);
//        stringRedisTemplate.opsForList().leftPush("mylist","1");
//        stringRedisTemplate.opsForList().leftPush("mylist","2");
    }
```

+ Redis常见的五大数据类型及对应的操作 ：

  String(字符串)：`stringRedisTemplate.opsForValue();`

   List(列表)：`stringRedisTemplate.opsForList();`   

  Set(集合)：`stringRedisTemplate.opsForSet();`   

  Hash(散列)：`stringRedisTemplate.opsForHash();`   

   ZSet(有序集合)：`stringRedisTemplate.opsForZSet();`

+ `leftPush`方法标识从list的左边（上边，或者说list的头部）压入数据。如果不存在该key，则会创建该key。rightPush从list的尾部压入数据，可以理解为追加数据。
+ `StringRedisTemplate`和`RedisTemplate redisTemplate`在IOC容器中本来就存在，可以注入到该测试类中使用。即测使用内置的 `StringRedisTemplate` 和`RedisTemplate`进行数据的读取测试。
+ `stringRedisTemplate.opsForValue().append("msg1","hello")`：给Redis中添加数据，对应Redis中文网站中的命令操作。

### 4.2.3 Redis操作实体对象

#### 4.2.3.1 创建实体类

```java
public class Users implements Serializable{//实体类,实现序列化接口
	private Integer id;
	private String name;
	private Integer age;
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public Integer getAge() {
		return age;
	}
	public void setAge(Integer age) {
		this.age = age;
	}
	@Override
	public String toString() {
	return "Users [id=" + id + ", name=" + name + ", age=" + age + "]";
	}
}
```

+ `implements Serializable`:实体类要实现序列化接口,否则在测试类中取`users`数据的时候取不到。
+ `public String toString()`:要重写该方法,否则,在测试取存入Redis的`Users`的序列化数据时,输出到控制台的是方法名。

#### 4.2.3.2 测试代码

```java
@Test//添加一个Users对象
public void testSetUsers() {
Users users = new Users();
users.setId(1);
users.setName("小白");
users.setAge(22);
this.redisTemplate.opsForValue().set("users", users);
}
	
@Test
public void testGetUsers() {
Users users = (Users)this.redisTemplate.opsForValue().get("users");
System.out.println(users);	
}
```

+ `testSetUsers()` 测试类作用是向Redis传入一个`Users`对象,和上文的实体类要实现序列化接口“`Serializable`”相对应。
+ `testGetUsers()`测试类作用是取出存入Redis中的`Users`对象,和上文的`public String toString()`该方法对应。
+ `JSON`格式存储实体对象使用JDK类型的序列化器,比`JSON`格式的存储要大好多。

# 5 整合Spring Data JPA

## 5.1 介绍

Spring Data 是Spring提供的一个操作数据的框架，而Spring Data JPA是Spring Data框架下基于JPA标准的一个模块。

核心：对数据进行操作。简化操作持久层的代码，只需要编写接口层的代码。

## 5.2 整合步骤 

### 5.2.1 修改pom文件

```html
<dependencies>
    <!--01Web启动器-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!--02Thymeleaf启动器-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    <!--03Junit单元测试-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
    </dependency>
    <!--04devtools工具-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
    </dependency>
    <!--05JPA-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <!--06mysql-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
    <!--07druid连接池-->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>1.1.8</version>
    </dependency>
</dependencies>
```

+ 配置druid连接池时，要加上版本信息`<version>1.1.8</version>`，不然可能jar包不能导入进来。
+ 使用JPA操作数据库。

### 5.2.2 编写application.properties文件

```properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/db2?useUnicode=true&characterEncoding=utf8&characterSetResults=utf8&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=123

#使用druid类型的连接池<version>1.1.8</version>要加上
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource 

#实体类发生更改而数据库表做出相应的更改且不破坏数据库现有的数据,
spring.jpa.hibernate.ddl-auto=update
#调试时，控制台输出sql语句
spring.jpa.show-sql=true
```

+ `spring.jpa.hibernate.ddl-auto=update`：实体类发生更改而数据库表做出相应的更改且不破坏数据库现有的数据。
+ `spring.jpa.show-sql=true`：调试时，控制台输出sql语句。
+ 要加上时区“`serverTimezone=UTC`”，不然会报错。

### 5.2.3 添加实体类

```java
@Entity //实体类
@Table(name = "t_users")//数据库表名
public class Users {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id") //指名表中属性名字
    private Integer id;

    @Column(name = "name")
    private String name;

    @Column(name = "age")
    private Integer age;

    @Column(name = "address")
    private String address;

    public Integer getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public Integer getAge() {
        return age;
    }

    public String getAddress() {
        return address;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "Users{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", age=" + age +
                ", address='" + address + '\'' +
                '}';
    }
}
```

+ `@Id`：导入的包要正确，即`import javax.persistence.Id`。
+ 创建一个实体类，用于定义要存储到未定义表名的数据库中，成员属性要生成相应的`get()`和`set()`方法。

### 5.2.4 编写DAO接口

```java
public interface UsersRepository extends JpaRepository<Users, Integer> {
}
```

+ 要创建存储库，只需扩展 `JapRepository` 接口即可。它默认提供了很多方法，此接口无需实现。它可以直接注入并在服务类中使用。

### 5.2.5 编写测试类

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes = {App.class})
public class UsersRepositoryTest {
    @Autowired
    private UsersRepository usersRepository;

    @Test
    public void saveTest(){
        Users users = new Users();
        users.setAddress("北京海淀区");
        users.setAge(10);
        users.setName("张三");
        this.usersRepository.save(users);
    }
}
```

+ 结果是在`db2`数据库中可以看到数据被加入到表“`t_users`”中了，事先并没有创建表“`t_users`”。

## 5.3 Spring Data JPA提供的核心接口

主要有五个核心接口：`Repository`接口、`Crud Repository`接口、`PagingAndSortingRepository`接口、`JpaRepository`接口和`JPASpecificationExectuor`接口。

### 5.3.1 Repository接口

提供了方法名称命名查询方式和基于`@Query`注解查询与更新。

#### 5.3.1.1 方法名称命名查询方式

```java
public interface UsersRepositoryByName extends Repository<Users,Integer> {
        List<Users> findByName(String name);
}
```

+ 方法的名称必须遵循驼峰式命名规则，`findBy`（关键字） + 属性名称（首字母要大写）+查询条件（首字母大写），比如：`findByNameEquals`()。

1. 测试类代码

```java
@Autowired
private UsersRepositoryByName usersRepositoryByName;

@Test
public void findByNameTest(){
    List<Users> list = this.usersRepositoryByName.findByName("张三");
    for(Users users : list){
        System.out.println(users);
    }
}
```

+ 注入的只是一个接口，并不是实现类。

+ 该方法可以将所需要的内容查到“`Users{id=1, name='张三', age=10, address='北京海淀区'}`”。

+ 比较简介的方式进行使用`foreach`遍历`List`。

+ 根据两个变量进行查询二者之间是“且”的关系：`List<Users> findByNameAndAge(String name,Integer age);` 

+ 模糊查询

  List<Users> findByNameLike(String name);

  比如：findByNameLike("张%")，名字中含有“张“的人。

  #### 6.3.1.2 基于@Query注解查询

  ```java
  Dao代码:
  public interface UsersRepositoryQuery extends Repository<Users,Integer> {
  
      @Query(value = "from Users where name = ?1") 
   List<Users> queryByNameUseHQL(String name);
  }
  ```

+ `@Query(value = "from Users where name = ?1") 等价于：@Query(value = "select u from Users u where u.name = ?1")`，注意“?1”中的参数“1”不能去掉。

  同时也要注意参数？和位置的绑定。

1. 测试类代码

```java
@Autowired
private UsersRepositoryQuery usersRepositoryQuery;
@Test
public void queryByNameUseHQLTest(){
    List<Users> list = this.usersRepositoryQuery.queryByNameUseHQL("张三");
    for(Users users : list){
        System.out.println(users);
    }
}
```

```java
@Query(value = "select *from t_users where name = ?1",nativeQuery = true)
List<Users> queryByNameUseSQL(String name);
```

+ 有`nativeQuery = true`时，是可以执行原生sql语句，所谓原生sql，也就是说这段sql拷贝到数据库中，然后把参数值给一下就能运行了。
+ 查询条件多时，推荐使用注解方式来查询数据。

#### 5.3.1.2  基于@Query注解更新

```java
DAO代码：
@Query(value = "update Users set name = ?1 where id = ?2")
@Modifying // 只完成一个更新操作
void updateUsersNameById(String name,Integer id);
```

+ 通过 @Modifying 注解完成更新操作。

1. 测试类代码

```java
@Test
@Transactional
public void updateUsersNameByIdTest(){
    this.usersRepositoryQuery.updateUsersNameById("张三三",1);
}
```

+ `@Test`和`@Transactional`一起使用时事务自动回滚，需要加上`@Rollback(false)`注解来解决，但是我的代码在没加`@Rollback(false)`时数据并没有回滚，而是成功修改了数据库数据。
+ 结果是修改数据库数据。

### 5.3.2 CrudRepository接口

主要作用是完成一些增删改查的操作，注意：`CrudRepository`接口继承了 `Repository`接口。

<img src="E:\01学习资料\04Spring框架\02我的笔记\Spring Boot进阶1.assets\image-20200413231359277.png" alt="image-20200413231359277" style="zoom:80%;" />

#### 5.3.2.1 添加数据

1. Dao代码

```java
public interface UsersRepositoryCrudRepository extends CrudRepository<Users,Integer> {
}
```

+ 只是实现了CrudRepository接口，其内封装了好多方法。

2. 测试类

```java
@Autowired
private UsersRepositoryCrudRepository usersRepositoryCrudRepository;
@Test
public void CrudRepositoryTest(){
    Users users = new Users();
    users.setAddress("武汉");
    users.setAge(10);
    users.setName("小豪");
    this. usersRepositoryCrudRepository.save(users);
}
```

+ 只需要加@Test注解就可以了，因为`@Transactional`在定义`CrudRepository`接口时就已经添加上了。

#### 5.3.2.2 查询数据

```java
@Test
public void findAllTest(){
    List<Users> users=  (List<Users>) this.usersRepositoryCrudRepository.findAll();
    for (Users users1:users) {
        System.out.println(users1);
    }
}
```

+ 查询全部数据，需要进行强制类型转换。

```java
Users users1 =  this.usersRepositoryCrudRepository.findById(1).get();
System.out.println(users1);
//上面等价于：
//Optional<Users> users1 =  this.usersRepositoryCrudRepository.findById(1);
```

+ 上述代码只是为了看出一个“等价于”。

#### 5.3.2.3 删除数据

```java
Dao代码：
@Test
public void DeleteTest(){
    this.usersRepositoryCrudRepository.deleteById(4);
}
```

+ 删除ID值为4的数据库记录。
+ deleteById()代码中默认开启了事务。

### 5.3.3 PagingAndSortingRepository接口

该接口提供了分页和排序的操作（针对于所有数据），同样的，该接口继承了 `CrudRepository`接口。

![image-20200413233046400](Spring Boot学习.assets/image-20200413233046400.png)

​					图6.2 PagingAndSortingRepository接口方法

#### 5.3.3.1 排序操作

```Java
public interface UsersRepositoryPagingAndSorting extends PagingAndSortingRepository<Users,Integer>  {
}
```

+ 继承PagingAndSortingRepository<Users,Integer>接口。

```java
@Autowired
private UsersRepositoryPagingAndSorting  usersRepositoryPagingAndSorting;
@Test
public void PagingAndSortingRepositorySortTest(){ //排序
    Sort.Order order = new Sort.Order(Sort.Direction.DESC,"id"); //对id进行降序排列
    Sort sort = new Sort(order);
    List<Users> list = (List<Users>) this.usersRepositoryPagingAndSorting.findAll(sort);
    for (Users user:list) {
        System.out.println(user);
    }
}
```

+ “`order`”导入的包为“`import org.springframework.data.domain.Sort;`”。
+ `Order`对象定义排序规则，sort对象封装了排序规则，然后将数据放到“`findAll(sort)`”中。

#### 5.3.3.2 分页操作

```java
@Test
public void PagingAndSortingRepositoryPagingTest(){
    Pageable pageable = new PageRequest(0,2);
    Page<Users> page = this.usersRepositoryPagingAndSorting.findAll(pageable);
    System.out.println("总条数"+page.getTotalElements());
    System.out.println("总页数"+page.getTotalPages());
    List<Users> list = page.getContent();
    for (Users user:list) {
        System.out.println(user);
    }
}
```

+ `pageable`封装了分页的参数，当前页，每页显示条数。注：当前页从0开始。
+ `PageRequest(page,size)`:`page`表示当前页，`size`表示每页显示的条数。

#### 5.3.3.3 分页后加排序操作

```java
@Test
public void PagingAndSortingRepositoryTest(){
    Sort.Order order = new Sort.Order(Sort.Direction.DESC,"id");
    Sort sort = new Sort(order);
    Pageable pageable = new PageRequest(0,2,sort);
    Page<Users> page = this.usersRepositoryPagingAndSorting.findAll(pageable);
    System.out.println("总条数"+page.getTotalElements());
    System.out.println("总页数"+page.getTotalPages());
    List<Users> list = page.getContent();
    for (Users user:list) {
        System.out.println(user);
    }
}
```

### 5.3.4 JpaRepository接口

该接口继承了`PagingAndSortingRepository`接口，对继承的接口中的方法的返回值做了一个适配。所以，是使用最多的一个接口。

```java
List<Users> list = (List<Users>) this.usersRepositoryPagingAndSorting.findAll(sort);
```

+ 对于排序的这个`findAll`()方法的返回值需要做强制类型转换，但是在使用`JpaRepository`接口时，就不需要强制类型转换，即“`List<Users> list = this. usersRepository.findAll(sort);`”

### 5.3.5 JpaSpecificationExectuor接口

该接口主要是提供了多条件查询的支持，并且可以在查询中添加分页和排序。

注意：该接口是单独存在的，完全独立。

![image-20200413233830411](Spring Boot学习.assets/image-20200413233830411.png)

​						图6.3 JpaSpecificationExectuor接口方法

#### 5.3.5.1  DAO层代码

```java
public interface UsersRepositorySpecification extends JpaRepository<Users,Integer>,JpaSpecificationExecutor<Users> {
}
```

+ 实现两个接对应上面所说的JpaSpecificationExecutor()。

1. 单个条件查询代码

```java
@Autowired
private UsersRepositorySpecification usersRepositorySpecification;

/**
 * JpaSpecificationExectuor接口-单条件测试
 */
@Test
public void JpaSpecificationExectuor1Test() {
    //Specification：用于封装查询条件
    Specification<Users> spec = new Specification<Users>() {
        @Override
        public Predicate toPredicate(Root<Users> root, CriteriaQuery<?> query, CriteriaBuilder criteriaBuilder) {
            Predicate pred = criteriaBuilder.equal(root.get("name"), "张三三");
            return pred;
        }
    };
    List<Users> list = this.usersRepositorySpecification.findAll(spec);
    for (Users user : list) {
        System.out.println(user);
    }
}
```

2. 多条件查询代码

```java
@Test
public void JpaSpecificationExectuor2Test() {
    Specification<Users> spec = new Specification<Users>() {
        @Override
        public Predicate toPredicate(Root<Users> root, CriteriaQuery<?> query, CriteriaBuilder criteriaBuilder) {
            List<Predicate> list = new ArrayList<>();
            list.add(criteriaBuilder.equal(root.get("name"), "张三三"));
            list.add(criteriaBuilder.equal(root.get("age"), 10));
            Predicate[] arr = new Predicate[list.size()];
            return criteriaBuilder.and(list.toArray(arr)); 
        }
    };
    List<Users> list = this.usersRepositorySpecification.findAll(spec);
    for (Users user : list) {
        System.out.println(user);
    }
}
```

+ 查询复合条件为：“`where name = "张三三" and age = 10`”。

+ `criteriaBuilder.and(list.toArray(arr))`：可变参数以数组方式传递。

+ 多条件查询的第二种方法：在`criteriaBuilder.and()`直接放需要查询的条件，即`criteriaBuilder.and(criteriaBuilder.equal(root.get("name"), "张三三"), criteriaBuilder.equal(root.get("age"), 10));`。

  当遇到既有“and”又有“or”的情况，可以对上述`criteriaBuilder.and()`进行复合，即“`criteriaBuilder.and(criteriaBuilder.or(),x,y)`

## 5.4 关联映射操作

### 5.4.1 一对多的关联关系

举例：角色和用户是一对多的关系。

#### 5.4.1.1 Users类

```java
@Entity //实体类
@Table(name = "t_users")//数据库表名
public class Users {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id") //指名表中属性名字
    private Integer id;
    @Column(name = "name")
    private String name;
    @Column(name = "age")
    private Integer age;
    @Column(name = "address")
    private String address;
    
    @ManyToOne(cascade = CascadeType.PERSIST)  
    @JoinColumn(name = "roles_id")     
private Roles roles; //1对多
    public Integer getId() {
        return id;
    }
    public String getName() {
        return name;
    }

    public Integer getAge() {
        return age;
    }
    public String getAddress() {
        return address;
    }
    public void setId(Integer id) {
        this.id = id;
    }
    public void setName(String name) {
        this.name = name;
    }
    public void setAge(Integer age) {
        this.age = age;
    }
    public void setAddress(String address) {
        this.address = address;
    }
    public Roles getRoles() {
        return roles;
    }
    public void setRoles(Roles roles) {
        this.roles = roles;
    }
    @Override
    public String toString() {
        return "Users{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", age=" + age +
                ", address='" + address + '\'' +
                '}';
    }
```

+ `(cascade = CascadeType.PERSIST)`：既添加用户，又添加角色，给当前设置的实体操作另一个实体的权限(级联持久化)，持久保存拥有方实体时，也会持久保存该实体的所有相关数据。
+ `JoinColumn(name = "roles_id")` ：维护一个外键，外键在`Users`这一侧。(本表中指向另一个表的外键)

#### 5.4.1.2 Roles类

```java
@Entity
@Table(name = "t_roles")
public class Roles {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "roleId")
    private Integer roleId;

    @Column(name = "roleName")
    private String roleName;

    //拥有方能够自动维护跟被拥有方的关系
    @OneToMany(mappedBy = "roles") //被映射，
    private Set<Users> users = new HashSet<>(); //1对多

    public Integer getRoleId() {
        return roleId;
    }

    public String getRoleName() {
        return roleName;
    }

    public void setRoleId(Integer roleId) {
        this.roleId = roleId;
    }

    public void setRoleName(String roleName) {
        this.roleName = roleName;
    }

    public Set<Users> getUsers() {
        return users;
    }

    public void setUsers(Set<Users> users) {
        this.users = users;
    }

    @Override
    public String toString() {
        return "Roles{" +
                "roleId=" + roleId +
                ", roleName='" + roleName + '\'' +
                '}';
    }

```

+ `(mappedBy = "roles")`:被映射，表示声明自己不是一对多的关系维护端，由对方来维护，`mappedBy`的值应该为一方的表名，加入`mappedBy`属性可以避免生成一张中间表。

#### 5.4.1.3 测试类

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes = {App.class})
public class OneToManyTest {

    @Autowired
    private UsersRepository usersRepository;
    /**
     * 1对多关系的添加
     */
    @Test
    public void saveTest(){
        //创建一个用户
        Users users = new Users();
        users.setAddress("天津");
        users.setAge(10);
        users.setName("小刚");
        //创建一个角色
        Roles roles = new Roles();
        roles.setRoleName("管理员");
        //关联(操作用户，同时添加角色)
        roles.getUsers().add(users);  //建立角色和用户之间的关系
        users.setRoles(roles);  //建立用户和角色之间的关系
        //保存
        this.usersRepository.save(users);
    }
```

+ 需要经过上面四个步骤: 创建一个用户, 创建一个角色, 关联(操作用户，同时添加角色), 保存。

+ 测试代码：

  `Hibernate: insert into t_roles (role_name) values (?)`

  `Hibernate: insert into t_users (address, age, name, roles_id) values (?, ?, ?, ?)`

  可以看出二者进行了级联。

# 6 整合JdbcTemplate

## 6.1 创建项目

### 6.1.1 创建操作数据库相应的类

```java
public class Users {
	private int id;
	private String name;
	private String sex;
	private Date birthday; //省略getxx()和setxx()方法
}
```

### 6.1.2 创建controller类

```java 
@Autowired
	private UserService userservice;

	@RequestMapping("/save")
	@ResponseBody  //不希望到页面去，加上注解
	public String save() {
		Users users = new Users();//创建对象
//		users.setName("123");
		users.setName("王喜");//中文操作数据库乱码
		users.setSex("true");
		users.setBirthday(new Date());//生日：当前日期
		
		userservice.saveUser(users);
		return "success";
}
```

+ 在这里给User类的成员属性赋值。
+ 插入中文时，内容会变成问号“？”。

### 6.1.3 创建dao层

```java
@Repository//Dao层添加这个注解（创建对象）
public class UserDao {
	@Autowired
	private JdbcTemplate jdbcTemplate;//注入模板
	
	public void addUser(Users user) {//添加用户
		//不用管id的值
		jdbcTemplate.update("insert into personDemo(Name,Sex,Birthday) values(?,?,?)", new Object[] {user.getName(),user.getSex(),user.getBirthday()});
	}
}
```

+ 将`controller`操作的User类的属性值取出来，为数据库`demo`的表`personDemo`增添一条记录。

### 6.1.4 创建service层

```java
@Service//对应的是业务
public class UserService {
	@Autowired
	private  UserDao userDao;
	
	public void saveUser(Users users) {
		//接收来自controller的对象
		userDao.addUser(users);	
	}
}
```

+ 接收来自controller的数据，将其返回到业务层。

# 7 整合Freemarker

Freemarker是一个模板引擎，使用纯Java编写，可以取代Jsp，同时也可以使用它来生成电子邮件、配置文件、XML映射等，或者直接生成HTML。

## 7.1 项目1

### 7.1.1 引入依赖

```html
<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-freemarker</artifactId>
</dependency>
```

### 7.1.2 创建controller类

```java
@RequestMapping("/freemarker")//freemarker测试
public String welcome(Model model) {
	model.addAttribute("name","千峰教育");
	return "show";  //跳转到show.ftl文件中
}
```

### 7.1.3 创建show.ftl文件

```html
<html>
<head>
</head>
<body>
		${name}
</body>
</html>
```

+ `show.ftl`文件在`templatess`文件夹下。
+ 输入;http://localhost:8080/freemarker，界面输出：千峰教育。

## 7.2 创建项目2

1. application.yml配置

```yaml
spring:
  freemarker:
    cache: false #缓存设置，开发阶段设置为false，因为经常改
    suffix: .html #模板后缀名，默认为.ftl
    charset: UTF-8 #文件编码
    template-loader-path: classpath:/templates/ #模板存放路径
```

2. 编写controller类

```java
List <Entiry xx> articles = articleRestService.getAll; //将数据库中插到的数据进行赋值
model.addAttribute("articles",aritcles);
return "freemarkerTest"; //模板名称，实际目录为：resources/templates/freemarkerTest.html
```

3. 编写html文件

```html
<tr>
	<td>作者</td>
	<td>教程名称</td>
	<td>内容</td>
</tr>
<#list articles as article>   
<tr >
	<td>${article.author}</td>
	<td>${article.title}<</td>
	<td>${article.content}<</td>
</tr>
</#list>
```

+ 遍历数组

# 8 整合QuartZ

## 8.1 创建项目

### 8.1.1 创建任务类

```java
@Component //创建对象
public class MyJob {
	@Scheduled(fixedRate = 1000)//间隔1秒打印，相当于触发器
	public void run() {//打印当前时间
		System.out.println(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(new  Date()));
	}
}
```

### 8.1.2 启动类中添加注解

```java
@EnableScheduling//允许任务调度
@Spring BootApplication
public class SpringApp {
	public static void main(String[] args) {
		SpringApplication.run(SpringApp.class, args);
	}
}
```

+ 本项目没有增加相关依赖。

# 9 整合Listener

## 9.1 完成Listener组件注册-扫描方式

### 9.1.1 编写功能类

```java
@WebListener
public class FirstListener implements ServletContextListener {
	@Override
	public void contextDestroyed(ServletContextEvent sce) {
	}
	@Override
	public void contextInitialized(ServletContextEvent sce) {
		System.out.println("listener ....init.....");
	}
}
```

+ @`WebListener`:将一个实现了特定监听器接口的类定义为监听器，这样我们在web应用中使用监听器时，也不再需要在`web.xml`文件中配置监听器的相关描述信息了。
+ System.out.println("listener ....init.....")：控制台应该会输出数据，但是我的控制台没有输出。

## 9.2 完成Listener组件注册-方法实现

### 9.2.1 编写功能类

```java
public class SecondListener implements ServletContextListener {
	@Override
	public void contextDestroyed(ServletContextEvent sce) {
	}
	@Override
	public void contextInitialized(ServletContextEvent sce) {
		System.out.println("SecondListener ....init.....");
	}	
}
```

+ 没有添加@WebListener注解。

### 9.2.2 编写启动类

```java
@Spring BootApplication
public class SpringAppListern2 {
	public static void main(String[] args) {
		SpringApplication.run(SpringAppListern2.class, args);
	}
	//注册Listener
	@Bean
	public ServletListenerRegistrationBean<SecondListener> getServletListenerRegistrationBean(){
		ServletListenerRegistrationBean<SecondListener> bean = new ServletListenerRegistrationBean<>(new SecondListener());
		return bean;
}
}
```

+ 相比于注解而言增加了注册类。
+ 控制台有输出: SecondListener ....init.....。

# 10 拦截器

# 10.1 自定义拦截器

有些时候我们需要自己配置SpringMvc而不是默认，比如说增加一个拦截器，这个时候就得通过继承WebMvcConfigurerAdapter，然后重写父类中的方法进行扩展。

```java
@Configuration//声名这是一个配置
public class MyInterceptor extends WebMvcConfigurerAdapter{

	@Override
	public void addInterceptors(InterceptorRegistry registry) {
		//定义一个拦截器
		HandlerInterceptor inter = new HandlerInterceptor() {
			@Override
			public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)throws Exception {
				System.out.println("自定义拦截器。。。。");
				return true;
			}
			@Override
			public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
					ModelAndView modelAndView) throws Exception {
				HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
			}
			@Override
			public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler,
					Exception ex) throws Exception {
				HandlerInterceptor.super.afterCompletion(request, response, handler, ex);
			}		
		};
		registry.addInterceptor(inter).addPathPatterns("/**");//拦截器注册,后面是路径，拦截所有
	}
}
```

+ "/**：拦截器注册,后面是路径，拦截所有。
+ 输入网址http://localhost:8080/person测试，在网页上会显示输出的内容，此外，控制台会输出"自定义拦截器。。。。"的字样。









