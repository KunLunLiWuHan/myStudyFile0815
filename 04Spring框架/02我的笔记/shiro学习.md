# 1 介绍

Apache Shiro是一个Java的安全（权限）框架。Shiro可以非常容易地开发出足够好地应用，不仅可以用在JavaSE环境中，也可以使用在JavaEE环境中。

Shiro可以完成认证、授权、加密、会话管理、Web集成，缓存等。

1. **功能**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134635.png" alt="image-20200520090316084" style="zoom:80%;" />

+ Authentication:身份认证、登录、验证用户是不是拥有相应的身份。
+ Authorization:授权，即授权验证，验证某个已认证的用户是否拥有某个权限，即判断用户能否进行什么操作，如：验证某个用户是否拥有某个角色。
+ Session Manager:会话管理，即用户登录后就是第一次会话，在没有退出之前，它的所有信息都在会话中；会话可以是普通的JavaSE环境，也可以是Web环境。
+ Cryptography:加密，保护数据的安全性。
+ Web Support：Web支持，可以非常容易的集成到Web环境中。
+ Caching:缓存，比如用户登录后，其用户信息，拥有的角色、权限不必每次都去查，可以提高效率。
+ Concurrency:Shiro支持多线程应用的并发控制，即，如在一个线程中开启另一个线程，能够将权限自动传递过去。
+ Testing:提供测试支持。
+ Run As:允许一个用户假装成另一个用户的身份进行访问。
+ Remember Me:记住我，这个是非常常见的功能，即一次登录后，下次再来的话就不用登录了。

2. **Shiro架构（外部）**

从外部来看Shiro，即从应用程序角度来观察如何使用Shiro完成工作：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134636.png" alt="image-20200520091314599" style="zoom:80%;" />

+ Subject:应用程序直接交互对象是Subject，也就是说Shiro的对外核心就是Subject，Subject代表了当前的用户，这个用户不一定是一个具体的人，与当前应用交互的任何东西都是Subject，如网络爬虫，机器人等，与Subject的所有交互都会委托给SecurityManager，Subject其实就是一个门面，SecurityManager才是真正的执行者。
+ SecurityManager：安全管理器，即所有的与安全相关的操作都会与SecurityManage交互，并且它管理着所有的Subject，可以看出它是Shiro的核心，它负责与Shiro其他的组件进行交互，相当于SpringMVX的DispatcherServlet的角色。
+ Realm:Shiro从Realm中获取安全数据（用户，权限，角色），就是说SecurityManager要验证的用户身份，那么它需要从Realm中获取相应的用户进行比较，来确定用户的身份是否合法；也需要从Realm中得到用户相应的角色，权限，进行验证用户的操作是否能够进行，可以将Realm看成DataSource。

# 2 Subject分析

## 2.1 快速创建项目

1. **导入依赖**

```html
<dependencies>
    <!-- https://mvnrepository.com/artifact/org.apache.shiro/shiro-core -->
    <dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-core</artifactId>
    <version>1.5.3</version>
    </dependency>

    <!-- configure logging -->
    <dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>jcl-over-slf4j</artifactId>
    <version>1.7.21</version>
    </dependency>
    <dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-log4j12</artifactId>
    <version>1.7.21</version>
    </dependency>

    <dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
    </dependency>
</dependencies>
```

+ 查询`shiro-core`的版本号，在百度上搜索：shiro-core maven即可。
+ 项目创建的是一个Maven项目。
+ https://github.com/apache/shiro.git官网上查找快速开始项目。

2. **配置文件**

**log4j.properties文件**

```properties
log4j.rootLogger=INFO, stdout

log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d %p [%c] - %m %n

# General Apache libraries
log4j.logger.org.apache=WARN

# Spring
log4j.logger.org.springframework=WARN

# Default Shiro logging
log4j.logger.org.apache.shiro=INFO

# Disable verbose logging
log4j.logger.org.apache.shiro.util.ThreadContext=WARN
log4j.logger.org.apache.shiro.cache.ehcache.EhCache=WARN
```

**shiro.ini文件**

```ini
[users]
# user 'root' with password 'secret' and the 'admin' role
root = secret, admin
# user 'guest' with the password 'guest' and the 'guest' role
guest = guest, guest
# user 'presidentskroob' with password '12345' ("That's the same combination on
# my luggage!!!" ;)), and role 'president'
presidentskroob = 12345, president
# user 'darkhelmet' with password 'ludicrousspeed' and roles 'darklord' and 'schwartz'
darkhelmet = ludicrousspeed, darklord, schwartz
# user 'lonestarr' with password 'vespa' and roles 'goodguy' and 'schwartz'
lonestarr = vespa, goodguy, schwartz

# -----------------------------------------------------------------------------
# Roles with assigned permissions
# 
# Each line conforms to the format defined in the
# org.apache.shiro.realm.text.TextConfigurationRealm#setRoleDefinitions JavaDoc
# -----------------------------------------------------------------------------
[roles]
# 'admin' role has all permissions, indicated by the wildcard '*'
admin = *
# The 'schwartz' role can do anything (*) with any lightsaber:
schwartz = lightsaber:*
# The 'goodguy' role is allowed to 'drive' (action) the winnebago (type) with
# license plate 'eagle5' (instance specific id)
goodguy = winnebago:drive:eagle5
```

+ 文件目录

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134637.png" alt="image-20200520110445936" style="zoom:67%;" />

3. **java文件**

**Quickstart.java文件**

```java
public class Quickstart {

    //使用日志框架进行输出
    private static final transient Logger log = LoggerFactory.getLogger(Quickstart.class);
    public static void main(String[] args) {

        //通过工厂模式，将ini文件加载进来（套路三句，独立的）
        Factory<SecurityManager> factory = new IniSecurityManagerFactory("classpath:shiro.ini");
        SecurityManager securityManager = factory.getInstance();
        SecurityUtils.setSecurityManager(securityManager); //打多少文件不需要这样做

        Subject currentUser = SecurityUtils.getSubject(); //获取当前用户对象
        Session session = currentUser.getSession(); //通过当前用户拿到session

        session.setAttribute("someKey", "aValue"); //session存值取值操作
        String value = (String) session.getAttribute("someKey");
        if (value.equals("aValue")) {
            log.info("Subject-> session: " + value);
        }

        //测试当前用户是否被认证
        if (!currentUser.isAuthenticated()) {
            //Token:令牌，如果被认证，就可以拿到令牌。
            UsernamePasswordToken token = new UsernamePasswordToken("lonestarr", "vespa");
            token.setRememberMe(true); //设置记住我
            try {
                currentUser.login(token);  //执行了登录操作
            } catch (UnknownAccountException uae) {
                log.info("There is no user with username of " + token.getPrincipal());
            } catch (IncorrectCredentialsException ice) {
                log.info("Password for account " + token.getPrincipal() + " was incorrect!");
            } catch (LockedAccountException lae) {
                log.info("The account for username " + token.getPrincipal() + " is locked.  " +
                        "Please contact your administrator to unlock it.");
            }
            catch (AuthenticationException ae) {
                //unexpected condition?  error?
            }
        }
        //打印认证信息
        log.info("User [" + currentUser.getPrincipal() + "] logged in successfully.");

        //测试角色
        if (currentUser.hasRole("schwartz")) {
            log.info("May the Schwartz be with you!");
        } else {
            log.info("Hello, mere mortal.");
        }

        //测试权限（粗粒度权限）
        if (currentUser.isPermitted("lightsaber:wield")) {
            log.info("You may use a lightsaber ring.  Use it wisely.");
        } else {
            log.info("Sorry, lightsaber rings are for schwartz masters only.");
        }

        //（细粒度权限）
        if (currentUser.isPermitted("winnebago:drive:eagle5")) {
            log.info("You are permitted to 'drive' the winnebago with license plate (id) 'eagle5'.  " +
                    "Here are the keys - have fun!");
        } else {
            log.info("Sorry, you aren't allowed to drive the 'eagle5' winnebago!");
        }
        //注销
        currentUser.logout();
        System.exit(0);
    }
}
```

+ 控制台输出：

```java
2020-05-20 10:50:33,739 INFO [org.apache.shiro.session.mgt.AbstractValidatingSessionManager] - Enabling session validation scheduler... 
2020-05-20 10:50:34,181 INFO [Quickstart] - Subject-> session: aValue 
2020-05-20 10:50:34,191 INFO [Quickstart] - User [lonestarr] logged in successfully. 
2020-05-20 10:50:34,192 INFO [Quickstart] - May the Schwartz be with you! 
2020-05-20 10:50:34,193 INFO [Quickstart] - You may use a lightsaber ring.  Use it wisely. 
2020-05-20 10:50:34,193 INFO [Quickstart] - You are permitted to 'drive' the winnebago with license plate (id) 'eagle5'.  Here are the keys - have fun! 
```

# 3 SpringBoot整合Shiro

## 3.1 创建项目

### 3.1.1 编写pom文件

```html
<!--01web-->
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!-- 02Thymeleaf依赖 -->
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>

<!-- 03 shiro依赖 -->
<dependency>
<groupId>org.apache.shiro</groupId>
<artifactId>shiro-spring</artifactId>
<version>1.5.3</version>
</dependency>
```

### 3.1.2 编写前端界面

1. **index.html界面**

```html
@Controller
public class MyController {

    @RequestMapping({"/" , "/index"})
    public String toIndex(Model model){
        model.addAttribute("msg", "hello,shiro");
        return "index";
    }

    @RequestMapping("/update")
    public String update(){
        return "/update";
    }

    @RequestMapping("/add")
    public String add(){
        return "/add";
    }
}
```

2. **add.html测试页面**

```html
<body>
    <h1>add</h1>
</body>
```

3. **update.html测试界面**

```html
<body>
    <h1>update</h1>
</body>
```

### 3.1.3 创建controller

```java
@Controller
public class MyController {

    @RequestMapping({"/" , "/index"})
    public String toIndex(Model model){
        model.addAttribute("msg", "hello,shiro");
        return "index";
    }

    @RequestMapping("/update")
    public String update(){
        return "/update";
    }

    @RequestMapping("/add")
    public String add(){
        return "/add";
    }
}
```

### 3.1.4 配置类

1. **UserRealm类**

```java
public class UserRealm extends AuthorizingRealm {

    //授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        System.out.println("执行了授权doGetAuthorizationInfo方法");
        return null;
    }

    //认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.out.println("执行了认证doGetAuthenticationInfo方法");
        return null;
    }
}
```

2. **ShiroConfig类**

```java
@Configuration
public class ShiroConfig {

    //shiroFilterFactoryBean 3
    @Bean
    public ShiroFilterFactoryBean shiroFilterFactoryBean(@Qualifier("SecurityManager") DefaultSecurityManager getDefaultSecurityManager){
        ShiroFilterFactoryBean bean = new ShiroFilterFactoryBean();
        //设置安全管理器
        bean.setSecurityManager(getDefaultSecurityManager);
        return bean;
    }

    //DefaultSecurityManager 2
    @Bean(name = "SecurityManager")
    public DefaultWebSecurityManager getDefaultSecurityManager(@Qualifier("userRealm") UserRealm userRealm){
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        //关联UserRealm
        securityManager.setRealm(userRealm);
        return securityManager;
    }

    //创建 realm对象，需要自定义 1
    @Bean
    public UserRealm userRealm(){
        return new UserRealm();
    }
}
```

+ 使用`DefaultWebSecurityManager`而不是`DefaultSecurityManager`。
+ 注解`@Configuration`不要丢失。

### 3.1.5 测试

在浏览器输入http://localhost:8080/index.html，弹出界面：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134638.png" alt="image-20200520155917223" style="zoom:50%;" />

点击上面连接，会跳转到相应的界面：http://localhost:8080/update。

## 3.2 Shiro实现登录拦截

1. **添加shiro的内置过滤器**

```java
 @Bean
    public ShiroFilterFactoryBean shiroFilterFactoryBean(@Qualifier("SecurityManager") DefaultSecurityManager getDefaultSecurityManager){
        ShiroFilterFactoryBean bean = new ShiroFilterFactoryBean();
        //设置安全管理器
        bean.setSecurityManager(getDefaultSecurityManager);
        //添加shiro的内置过滤器
        /**
         * anon:无需认证即可访问。
         * authc:必须认证才能访问
         * user:必须拥有记住我才能访问
         * perms:拥有对某个资源的权限才能访问。
         * role:拥有某个角色的权限才能访问。
         */
        Map<String, String> filterMap = new LinkedHashMap<>();
        filterMap.put("/add", "anon"); //将add设置为所有人都能访问
        filterMap.put("/update", "authc"); //将update设置为认证后能访问
        bean.setFilterChainDefinitionMap(filterMap);

        bean.setLoginUrl("/toLogin"); //设置登录页面
        return bean;
    }
```

2. **编写controller类**

```java
  @RequestMapping("/toLogin")
    public String toLogin(){
        return "/login";
    }
```

+ 跳转到登录页面的controller。

3. **编写前端登录页面login.html**

```html
<body>
    <p>用户名： <input type="text" name="username"></p>
    <p>密 码： <input type="text" name="password"></p>
    <p><input type="submit"></p>
</body>
```

4. **测试**

浏览器输入：http://localhost:8080/index，跳转到首页：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134639.png" alt="image-20200520173449238" style="zoom:50%;" />

其中`add`链接所有人都可以进入，`update`设置的需要认证才能进入，此时会跳转到已经编写好的登录页面中：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134640.png" alt="image-20200520173631279" style="zoom:50%;" />

表明拦截成功。

## 3.3 Shiro实现用户认证

1. **编写controller类**

```java
 @RequestMapping("/login")
    public String login(String username,String password,Model model){

        //获取当前用户
        Subject subject = SecurityUtils.getSubject();
        //封装用户的登录数据
        UsernamePasswordToken userToken = new UsernamePasswordToken(username, password);
        try{
            subject.login(userToken); //执行登录方法，如果没有异常，就说明ok了。
            return "index"; //try-catch语句都要写return语句
        }catch (UnknownAccountException e){ //用户名不存在
            model.addAttribute("msg", "用户名错误");
            return "login";
        }catch (IncorrectCredentialsException e){ //密码不存在
            model.addAttribute("msg", "密码错误");
            return "login";
        }
    }
```

2. **配置UserRealm类**

```java
//认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        System.out.println("执行了认证doGetAuthenticationInfo方法");
        //用户名，密码，数据中取
        String name = "root";
        String password = "123";
        UsernamePasswordToken userToken = (UsernamePasswordToken) token;
        if (!userToken.getUsername().equals(name)){
            return null; //抛出异常 UnknownAccountException
        }

        //密码认证，shiro做
        return new SimpleAuthenticationInfo("",password,"");
    }
```

3. **前端登录界面login.html**

```html
<body>
    <p th:text="${msg}" style="color: red"></p>
    <form th:action="@{/login}">
        <p>用户名： <input type="text" name="username"></p>
        <p>密 码： <input type="text" name="password"></p>
        <p><input type="submit"></p>
    </form>
</body>
```

4. **测试**

浏览器输入：http://localhost:8080/index，在首页中点击需要认证的链接“update”，会跳转到认证界面：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134641.png" alt="image-20200520212713366" style="zoom:67%;" />

在输入正确的用户名和密码后，就可以进入到正确的`update`所对应的界面。

## 3.4 Shiro整合Mybatis

1. **添加依赖**

```html
    <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>

<!-- 04 MyBatis依赖-->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.2</version>
        </dependency>
        <!-- 05 mysql依赖包-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.11</version>
        </dependency>

        <!-- 06 druid-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.0</version>
        </dependency>

        <!-- 07 lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>


        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
        </dependency>
    </dependencies>
```

2. **编写yml文件**

```yml
#数据源配置
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    username: root
    password: 123
    url: jdbc:mysql://localhost:3306/db2?useUnicode=true&characterEncoding=utf8&characterSetResults=utf8&serverTimezone=UTC
  type: com.alibaba.druid.pool.DruidDataSource


mybatis:
  type-aliases-package:  com.xiaolun.pojo
  mapper-locations: classpath:mapper/*.xml
```

3. **实体类User**

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private int id;
    private String name;
    private String pwd;
}
```

4. **创建mapper接口**

```java
@Mapper
@Repository
public interface UserMapper {
    User queryUserByName(String name); //通过用户名查询
}
```

5. **创建mapper配置类**

```html
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.xiaolun.mapper.UserMapper">
<!--    User queryUserByName(String name); //通过用户名查询-->
    <select id="queryUserByName" parameterType="String" resultType="User">
        select * from db2.user where name = #{name}
    </select>
</mapper>
```

6. **创建service层接口**

```java
public interface UserService {
    User queryUserByName(String name); //通过用户名查询
}
```

7. **创建service层接口实现类**

```java
public interface UserService {
    User queryUserByName(String name); //通过用户名查询
}
```

8. **创建测试类**

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes = {ShiroSpringbootApplication.class})
public class UserServiceTest {

    @Autowired
    UserServiceImpl userService;

    @Test
    public void testAddUser() {
        System.out.println(userService.queryUserByName("xiaolun"));
    }
}
```

+ 数据结果是可以拿到数据库中的数据。

9. **修改UserRealm中的获取数据方式**

```java
 //认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        System.out.println("执行了认证doGetAuthenticationInfo方法");
        //用户名，密码，数据中取
//        String name = "root";
//        String password = "123";

        UsernamePasswordToken userToken = (UsernamePasswordToken) token;

        //连接真实数据库
        User user = userService.queryUserByName(userToken.getUsername());
        if (user == null){ //没有这个人
            return null; //UnknownAccountException
        }
        //密码认证，shiro做
        return new SimpleAuthenticationInfo("",user.getPwd(),"");
    }
```

9. **测试**

通过在浏览器端输入网址，可以获得数据库中的数据。

## 3.5 请求授权

在没有进行授权的时候：



1. **修改实体类User**

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private int id;
    private String name;
    private String pwd;
    private String perms; //权限
}
```

+ 增加权限标签，数据库为：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134642.png" alt="image-20200521185630363" style="zoom:67%;" />

2. **修改controller**

```java
//  授权配置 
@RequestMapping("/noauth")
@ResponseBody
public String unauthorized(){
    return "未经授权，无法访问界面";
}
```

3. **修改UserRealm类**

```java
public class UserRealm extends AuthorizingRealm {

    @Autowired
    UserService userService;
    
    //授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        System.out.println("执行了授权doGetAuthorizationInfo方法");

        SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();

        //拿到当前登录的这个对象
        Subject subject = SecurityUtils.getSubject();
        //拿到参数中传递过来的user
        User currentUser = (User) subject.getPrincipal();
        //设置当前用户的权限
        System.out.println("currentUser.getPerms()-> "+ currentUser.getPerms());
        info.addStringPermission(currentUser.getPerms());

        return info;
    }

    //认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        System.out.println("执行了认证doGetAuthenticationInfo方法");

        UsernamePasswordToken userToken = (UsernamePasswordToken) token;

        //连接真实数据库
        User user = userService.queryUserByName(userToken.getUsername());
        if (user == null){ //没有这个人
            return null; //UnknownAccountException
        }
        //密码认证，shiro做
        return new SimpleAuthenticationInfo(user,user.getPwd(),""); //参数中传递user
    }
}
```

+ 主要偏重授权代码的编写。

4. **修改配置类ShiroConfig**

```java
 @Bean
    public ShiroFilterFactoryBean shiroFilterFactoryBean(@Qualifier("SecurityManager") DefaultSecurityManager getDefaultSecurityManager){
        ShiroFilterFactoryBean bean = new ShiroFilterFactoryBean();
        //设置安全管理器
        bean.setSecurityManager(getDefaultSecurityManager);
        //拦截：
        Map<String, String> filterMap = new LinkedHashMap<>();

        //授权，正常情况下，没授权会跳转到未授权界面
        filterMap.put("/add", "perms[user:add]"); //user用户，拥有add权限
        filterMap.put("/update", "perms[user:update]"); //user用户，拥有update权限

        bean.setFilterChainDefinitionMap(filterMap);
        bean.setLoginUrl("/toLogin"); //设置登录页面
        //未授权界面
        bean.setUnauthorizedUrl("/noauth");

        return bean;
    }
```

5. **测试**

输入网址：http://localhost:8080/index,进入登录界面，点击add链接，跳转到如下登录界面：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134643.png" alt="image-20200521182444678" style="zoom:67%;" />

输入正确信息后，登录成功，进行界面的跳转：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134644.png" alt="image-20200521182633264" style="zoom:67%;" />

再点击`add`链接按钮，由于`root`用户只有`update`权限，所以

会跳转到授权`update.html`界面,反之，跳转到如下未授权界面：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134645.png" alt="image-20200521182716173" style="zoom:67%;" />

## 3.6 Shiro整合Thymeleaf

1. **导入依赖**

```html
<!-- 08 thymeleaf-shiro整合-->
    <dependency>
    <groupId>com.github.theborakompanioni</groupId>
    <artifactId>thymeleaf-extras-shiro</artifactId>
    <version>2.0.0</version>
</dependency>
```

2. **修改前端index.html界面**

```html
<body>
    <h1>首页</h1>
    <!--  登录成功后将该标签隐藏(从session中判断值)  -->
    <div th:if="${session.loginUser == null}">
        <a th:href="@{/toLogin}">登录</a>
    </div>

    <p th:text="${msg}"></p>
    <hr>

    <div shiro:hasPermission="user:add">
        <a th:href="@{/add}">add</a>
    </div>

    <div shiro:hasPermission="user:update">
        <a th:href="@{/update}">update</a>
    </div>
</body>
```

3. **配置UserRealm类**

```java
//认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        System.out.println("执行了认证doGetAuthenticationInfo方法");

        UsernamePasswordToken userToken = (UsernamePasswordToken) token;

        //连接真实数据库
        User user = userService.queryUserByName(userToken.getUsername());
        if (user == null){ //没有这个人
            return null; //UnknownAccountException
        }

        //到这一步时，可以将用户查出来，也就时登录成功。
        Subject currentSubject = SecurityUtils.getSubject();
        Session session = currentSubject.getSession();
        session.setAttribute("loginUser", user); //将user放到session中，以便用于前端判断

        //密码认证，shiro做
        return new SimpleAuthenticationInfo(user,user.getPwd(),""); //参数中传递user
    }
```

+ 主要是获取后台的session值，以便前端进行调用。

4. **配置ShiroConfig类**

```java
  //整合shiroDialect:用以整合shiro Thymeleaf
    public ShiroDialect shiroDialect(){
        return new ShiroDialect();
    }
```

5. **测试**

浏览器输入：http://localhost:8080/index，进入首页：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134646.png" alt="image-20200521192814084" style="zoom:50%;" />

点击登录进入登录界面，输入`root`用户的账号，进入到登录后的界面：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134647.png" alt="image-20200521192932687" style="zoom:50%;" />

可以发现，登录链接消失了。达到了预期的效果。