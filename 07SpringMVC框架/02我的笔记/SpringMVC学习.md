# 1 介绍

SpringMVC的Web框架围绕DispatcherServlet设计，DispatcherServlet的作用是将请求分发到不同的处理器。

​		当用户发起请求时，会被前置控制器拦截，然后根据请求参数生成代理请求，找到请求对应的实际控制器，控制器处理请求，创建数据模型，访问数据库，将模型响应交给中心控制器，控制器使用模型与试图渲染视图结果，将结果返回给中心处理器，再将结果返回给请求者。

<img src="SpringMVC学习.assets\image-20200627122212018.png" alt="image-20200627122212018" style="zoom:80%;" />

**原理的详细介绍**

<img src="SpringMVC学习.assets\image-20200627122317805.png" alt="image-20200627122317805" style="zoom:80%;" />

<img src="SpringMVC学习.assets\image-20200627122355488.png" alt="image-20200627122355488" style="zoom:80%;" />

<img src="SpringMVC学习.assets\image-20200627122434797.png" alt="image-20200627122434797" style="zoom:80%;" />

# 2 第一个SpringMVC

## 2.1 新建项目

**项目结构**

<img src="SpringMVC学习.assets\image-20200627112157287.png" alt="image-20200627112157287" style="zoom:67%;" />

**步骤**

1. 新建一个module,添加web支持。

在module上右键添加web支持：

<img src="SpringMVC学习.assets\image-20200627094410585.png" alt="image-20200627094410585" style="zoom:67%;" />

此时，项目结构出来了web的支持，此时结构图如下：

<img src="SpringMVC学习.assets\image-20200627094439726.png" alt="image-20200627094439726" style="zoom:80%;" />

2. 导入SpringMVC的依赖。

首先，在pom文件中导入依赖。

```xml
 <!--  导入核心包  -->
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.2.6.RELEASE</version>
        </dependency>

        <!--01 javax.servlet-->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.1</version>
        </dependency>

        <!--02 javax.servlet.jsp-->
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>javax.servlet.jsp-api</artifactId>
            <version>2.3.3</version>
        </dependency>
    </dependencies>
```

这样可能不生效，在控制台可能会出现404的错误，此时我们需要在WEB-INF文件夹下新建lib目录，让后将上面的依赖全部导入到里面去。

<img src="SpringMVC学习.assets\image-20200627111959608.png" alt="image-20200627111959608" style="zoom: 50%;" />

<img src="SpringMVC学习.assets\image-20200627112059082.png" alt="image-20200627112059082" style="zoom:67%;" />

3. 配置web.xml，注册DispatcherServlet。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!--
       1.注册和配置DispatcherServlet。是springmvc的核心，请求转发器（前端控制器）
    -->
        <servlet>
            <servlet-name>springmvc</servlet-name>
            <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
            <!--1. DispatcherServlet需要关联一个springmvc的配置文件:springmvc-servlet.xml,
            同时配置该文件的位置
                2. 实际上我们可以不通过 contextConfigLocation 来配置springmvc的文件(将下面的<init-param>内容注释)，
                而是用默认的配置文件，此时配置文件名称和地址为：/WEB-INF/<servlet-name>-servlet.xml
             -->
            <init-param>
                <param-name>contextConfigLocation</param-name>
                <param-value>classpath:springmvc-servlet.xml</param-value>
            </init-param>
            <!--启动级别
				1.容器是否应该在web应用程序启动的时候就加载这个servle。值越小，servlet的优先级越高，就越先被加载。
			-->
            <load-on-startup>1</load-on-startup>
        </servlet>

        <!--
            /匹配所有的请求（不包括jsp）
            /*匹配所有的请求（包括jsp）
        -->
        <servlet-mapping>
            <servlet-name>springmvc</servlet-name>
            <url-pattern>/</url-pattern>
        </servlet-mapping>
</web-app>
```

4. 配置Springmvc-servlet.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">
    <!--
        配置视图解析器:把handler方法返回值解析成实际的物理视图。
        web/WEB-INF/jsp/hello.jsp
		1.获得了ModelAndView中的数据。
		2.解析了ModelAndView中视图的名字。
		3.拼接视图名字，找到视图。
		4.将数据渲染到前端。
    -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
        <!--前缀-->
        <property name="prefix" value="/WEB-INF/jsp/"></property>
        <!--后缀-->
        <property name="suffix" value=".jsp"></property>
    </bean>

<!--   配置要扫描的包-->
    <context:component-scan base-package="com.xiaolun"></context:component-scan>
  
</beans>
```

+ 文件位置在resources文件夹下，该文件创建时，使用下面的配置：

<img src="SpringMVC学习.assets\image-20200627112527939.png" alt="image-20200627112527939" style="zoom:67%;" />

5. index.jsp首页

```jsp
<a href="helloWorld">success page</a>
```

主要实现的是点击该链接后，页面进行跳转的操作。

6. 跳转后的界面

```jsp
 <h1> success 界面</h1>
```

7. 测试

```java
@Controller//加上这个注解类的所有方法，如果返回值是String，并且有具体的页面可以跳转，那么这个类就会被视图解析器解析。
public class HelloController {
    /**
     * 1. 使用@RequestMapping注解来映射请求的URL
     * 该注解里面的url("helloWorld")和链接里面的注解一致。
     * 2.返回值会通过视图解析器解析成实际的物理视图，对于InternalResourceViewResolver视图解析器会做如下
     * 如下的解析：prefix + returnVal + 后缀得到实际的物理视图，然后做转发操作。
     * （/WEB-INF/jsp/success.jsp）
     *  3. ("/helloWorld")反斜杠带不带都可以跳转。
     *  4. @RequestMapping注解用于映射url到控制器类或一个特定的处理程序方法，用于类和方法上。
     */
    @RequestMapping("/helloWorld")
    public String hello(){
        System.out.println("hello world------------");
        return "success";
    }
}
```

我们可以使用Model来实现后台返回数据到前台。

```java
//后台代码    
@RequestMapping("hello2")
    public String hello2(Model model){
        //封装数据
        model.addAttribute("msg","hello.....");
        return "success1"; //被视图层解析
    }
```

```jsp
<%--前端页面接受传过来的数据--%>
${msg}
```

## 2.2 RestFul风格

该风格是一个资源定位及资源操作的风格，不是标准和协议。通过该风格设计的软可以更加简洁，更有层次，更易于实现缓存等机制。

**资源操作**

使用POST、DELETE、PUT、GET对资源进行操作。

**传统方法操作资源**

通过不同的参数(通过链接不同来区分)来实现不同的效果，方法单一，只能使用POST、GET。

```HTTP
http://127.0.0.1/item/queryItem,action?id=1 查询，CET
http://127.0.0.1/item/saveItem,action       新增，POST
http://127.0.0.1/item/updateItem,action     更新，POST
http://127.0.0.1/item/deleteItem,action?id=1 删除，CET/POST
```

**RestFul风格操作资源**

通过不同的请求方法来实现不同的效果。

```http
http://127.0.0.1/item/1  查询，CET
http://127.0.0.1/item   新增，POST
http://127.0.0.1/item   更新，PUT
http://127.0.0.1/item/1   删除，DELETE
```

**测试**

```java
@Controller
public class RestFulController {

    /**
     *1. ("/add/{a}/{b}") Restful风格
     *2. @PathVariable 注解可以让方法参数的值对应绑定到一个URl模板变量上。
     * 加了这个路径变量，参数a就可以获取从("/add/{a}/{b}")中的a值。
     *3.原始风格：http://localhost:8080/add?a=1&b=2
     * Restful风格：http://localhost:8080/add/1/2
     */
    @RequestMapping("/add/{a}/{b}")
    public String test(@PathVariable int a, @PathVariable int b, Model model){
        int res = a + b;
        model.addAttribute("msg",res);
        return  "test";
    }
}
```

```http
Restful风格：http://localhost:8080/add/1/2   前台输入代码
```

## 2.3 结果跳转方式

**ModelAndView**

设置给对象，后台会根据view的名称，和视图解析器跳转到指定的界面。

页面：（视图解析器前缀）+viewName+(视图解析器后缀)

springMVC实现转发和定向，无需视图解析器。测试前，可以将视图解析器注释掉。

```java
 @RequestMapping("/test1")
    public String test1(){
        //转发1（添加视图解析器的返回路径）
        return  "success";
    }
    @RequestMapping("/test2")
    public String test2(){
        //转发2（添加视图解析器的返回路径）
        return  "forward:/WEB-INF/jsp/success.jsp";
    }
    @RequestMapping("/test3")
    public String test3(){
        //重定向（添加视图解析器的返回路径）
        return  "redirect:/index.jsp";
    }
```

+ **项目结构**

<img src="SpringMVC学习.assets\image-20200628114825778.png" alt="image-20200628114825778" style="zoom:80%;" />

## 2.4 接收请求参数和数据回显

1. **提交的域名称和处理方法的参数名一致**

提交数据：

```http
http://localhost:8080/hello?name=xiaolun
```

处理方法：

```java
@RequestMapping("/hello")
public String test1(String name){
	return  "hello";
}
```

2. **提交的域名称和处理方法的参数名不一致**

提交数据：

```http
http://localhost:8080/hello?name=xiaolun
```

处理方法：

```java
//@RequestParam("name"):name是与名称。推荐要加上，这样的话，我们知道该数据一定从前端接收。
@RequestMapping("/hello")
public String test1(@RequestParam("name") String username){
	return  "hello";
}
```

3. **提交的是一个对象**

要求提交的表单与和对象的属性名一致(当不一致的时候，后台得到参数的数值为null)，参数使用对象即可。

```java
@RequestMapping("/hello")
public String test1(User user){
	return  "hello";
}
```

## 2.5 乱码问题

1. **前端表单**

```jsp
<%--      <form action="/testEncoding" method="get">--%>
      <form action="/testEncoding" method="post">
          <input type="text" name="name">
          <input type="submit">
      </form>
```

2. **后端测试**

```java
@Controller
public class EncodingController {

    @RequestMapping("testEncoding")
    public String test(@RequestParam String name, Model model){
        System.out.println("name的值------>"+name);
        model.addAttribute("msg",name);
        return "test";
    }
}
```

+ 在表单中，当使用`get`方式提交数据的时候，后台能够接受数据且正常显示；但是当使用`post`方式提交数据的时候，后台不能正常接受数据（在Java端就已经出现了乱码，当然，返回到前端的数据也是乱码）。
+ 控制台输出：

```ini
name的值------>?°????
```

+ 前端界面输出：

<img src="SpringMVC学习.assets\image-20200628125654817.png" alt="image-20200628125654817" style="zoom:80%;" />

3. 使用SpringMVC给我们提供的过滤器，在web.xml文件中进行配置。

```xml
 <filter>
     <filter-name>encoding</filter-name>
     <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
     <init-param>
         <param-name>encoding</param-name>
         <param-value>utf-8</param-value>
     </init-param>
</filter>
<filter-mapping>
    <filter-name>encoding</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

## 2.6 Jackson的使用

​		Jackson是比较好的json解析工具了，使用该工具可以让Controller返回Json数据。当然，我们也可以使用阿里巴巴的fastjson解析工具。

1. 导入jackson的jar包，之后还要按照上面的步骤将其添加到lib中。

```xml
 <!--03 jackson依赖-->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.11.1</version>
</dependency>
```

2. **创建实体类**

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private String name;
    private int age;
    private String sex;
}
```

3. **测试**

**（1）返回toString()方法**

```java
@RequestMapping("json1")
@ResponseBody //表示响应不会走视图解析器，会直接返回一个字符串。
public String json1(){

    //创建一个对象
    User user = new User("xiaolun",18,"男");
    return user.toString();
}
```

+ 前端界面输出

<img src="SpringMVC学习.assets\image-20200628154958044.png" alt="image-20200628154958044" style="zoom:80%;" />

**（2）使用jackson解析工具**

```java
@RequestMapping("json1")
@ResponseBody //表示响应不会走视图解析器，会直接返回一个字符串。
public String json1() throws JsonProcessingException {
    //jackson
    ObjectMapper mapper = new ObjectMapper();
    //创建一个对象
    User user = new User("xiaolun",18,"男");
    String str = mapper.writeValueAsString(user);
    return str;
}
```

+ 前端界面输出为json字符串：

<img src="SpringMVC学习.assets\image-20200628155308597.png" alt="image-20200628155308597" style="zoom:80%;" />

+ 乱码解决方法，在配置文件springmvc-servlet.xml中引入下面的代码

```xml
 <!--JSON乱码问题配置-->
<!--xmlns:mvc这个配置代码中一定要有，不然 <mvc:annotation-driven> 这样的方法出不来-->
 xmlns:mvc="http://www.springframework.org/schema/mvc"
    <mvc:annotation-driven>
        <mvc:message-converters>
            <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                <constructor-arg value="UTF-8"></constructor-arg>
            </bean>
            <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
                <property name="objectMapper">
                    <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                        <property name="failOnEmptyBeans" value="false"></property>
                    </bean>
                </property>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
```

<img src="SpringMVC学习.assets\image-20200628160456882.png" alt="image-20200628160456882" style="zoom:80%;" />

**（3）使用jackson返回List对象**

```java
 @RequestMapping("json1")
    @ResponseBody //表示响应不会走视图解析器，会直接返回一个字符串。
    public String json1() throws JsonProcessingException {
        //jackson
        ObjectMapper mapper = new ObjectMapper();
        List<User> userList = new ArrayList<User>();
        //创建一个对象
        User user = new User("xiaolun",18,"男");
        User user2 = new User("xiaolun",18,"男");
        User user3 = new User("xiaolun",18,"男");
        User user4 = new User("xiaolun",18,"男");

        userList.add(user);
        userList.add(user2);
        userList.add(user3);
        userList.add(user4);
        String str = mapper.writeValueAsString(userList);
        return str;
    }
```

+ 前端界面输出

```json
[{"name":"xiaolun","age":18,"sex":"男"},{"name":"xiaolun","age":18,"sex":"男"},{"name":"xiaolun","age":18,"sex":"男"},{"name":"xiaolun","age":18,"sex":"男"}]
```

其中中括号表示一个集合，单个表示一个对象。

**（4）jackson对于时间格式数据的返回**

**（4.1）未定义格式数据的返回：**

```java
ObjectMapper mapper = new ObjectMapper();
Date date = new Date();
//ObjectMapper，时间解析后默认格式为Timestamp(时间戳)
return mapper.writeValueAsString(date);
```

+ 前端界面输出

<img src="SpringMVC学习.assets\image-20200628161453911.png" alt="image-20200628161453911" style="zoom:80%;" />

**（4.2-1）自定义格式数据的返回：**

```java
ObjectMapper mapper = new ObjectMapper();
Date date = new Date();

//自定义时间格式
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
//ObjectMapper，时间解析后默认格式为Timestamp(时间戳)
return mapper.writeValueAsString(sdf.format(date));
```

<img src="SpringMVC学习.assets\image-20200628162340820.png" alt="image-20200628162340820" style="zoom:80%;" />

**（4.2-2）自定义格式数据的返回：**

```json
 ObjectMapper mapper = new ObjectMapper();
        //不使用时间戳
        mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS,false);
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        mapper.setDateFormat(sdf);
        Date date = new Date();
        return mapper.writeValueAsString(date);
```

**（4.2-3）提取工具类进行时间数据的返回**

```java
//工具类
public class JsonUtils {
    public static String getJson(Object object){
        return getJson(object,"yyyy-MM-dd HH:mm:ss");
    }

    public static String getJson(Object object,String dateFormat){
        ObjectMapper mapper = new ObjectMapper();
        //不使用时间戳
        mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS,false);
        SimpleDateFormat sdf = new SimpleDateFormat(dateFormat);
        mapper.setDateFormat(sdf);

        try {
            return mapper.writeValueAsString(object);
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }
        return null;
    }
}
```

```java
//测试类
@RequestMapping("json1")
@ResponseBody
public String json1() throws JsonProcessingException {
    Date date = new Date();

    return JsonUtils.getJson(date);
}
```

## 2.7 fastjson的使用

fastjson中三个主要的类：

+ JSONObject代表json对象，可以通过各种形式的get()方法来获取json对象中的数据。
+ JSONArray代表json对象数组，内部有list接口中的方法来完成操作。
+ JSON代表JSONObject和JSONArray之间的转化。

**创建项目**

1. 引入依赖

```xml
<!---引入fastjson的依赖-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.60</version>
</dependency>
```

2. 测试

```java
@RequestMapping("json1")
    @ResponseBody
    public String json1() throws JsonProcessingException {
        //创建一个对象
        List<User> userList = new ArrayList<User>();
        User user = new User("xiaolun",18,"男");
        User user2 = new User("xiaolun",18,"男");
        User user3 = new User("xiaolun",18,"男");
        User user4 = new User("xiaolun",18,"男");

        userList.add(user);
        userList.add(user2);
        userList.add(user3);
        userList.add(user4);
        //java对象转jSON字符串
        String str = JSON.toJSONString(userList);

        String str2 = JSON.toJSONString(user2);
        System.out.println("str2------>"+str2);
        //json字符串转java对象
        User JS_user1 = JSON.parseObject(str2, User.class);
        System.out.println("JS_user1------>"+JS_user1);

        //java对象转json对象
        JSONObject jsonObject = (JSONObject) JSON.toJSON(user2);
        System.out.println("jsonObject------>"+jsonObject);

        //json对象转java对象
        User userObject = JSON.toJavaObject(jsonObject, User.class);
        System.out.println("userObject------>"+userObject);
        return str;
    }
```

+ 控制台输出

```java
str2------>{"age":18,"name":"xiaolun","sex":"男"}
JS_user1------>User(name=xiaolun, age=18, sex=男)
jsonObject------>{"sex":"男","name":"xiaolun","age":18}
userObject------>User(name=xiaolun, age=18, sex=男)
```

### 2.7.1 SprinBoot中整合FastJson

Spring Boot默认配置的是`Jackson`，首先我们需要引入`FastJson`的依赖。下面是配置fastJson的两种方式

**(1) 启动类继承WebMvcConfigurerAdapter**

```java
//重写下面的方法
void configureMessageConverters（List<HttpMessageConverter<?>> converters）{
    FastJsonHttpMessageConverter converter = new FastJsonHttpMessageConverter ();
    FastJsonConfig config = new FastJsonConfig ();
    config.setSerializerFeatures(SerializerFeature.PrettyFormat);
    converter.setFastJsonConfig(config);
    converter.add(converter);
}
```

**（2） @Bean注入**

```Java
@Bean //配置FastJson方式1@Bean注入
public HttpMessageConverters fastJsonMessageConverters() {
    //创建FastJson的消息转换器
    FastJsonHttpMessageConverter converter = new FastJsonHttpM												essageConverter ();
    //创建FastJson的配置对象
    FastJsonConfig config = new FastJsonConfig ();
    //Json数据进行格式化
    config.setSerializerFeatures(SerializerFeature.PrettyForma															t);
    //配置传给转换器，进行关联
    converter.setFastJsonConfig(config);
    HttpMessageConverter<?> con = converter;
    return new HttpMessageConverters(con);	
}
```

+ 乱码解决：将Spring Boot的`response`编码设置为UTF-8这个功能开启，即`spring.http.encoding.force=true`。

# 3 拦截器

​		过滤器是Servlet规范中的一部分，任何JavaWeb工程中都可以使用。

​		拦截器是AOP思想的具体应用，是SpringMVC框架自己的，只有使用SpringMVC框架后才可以使用。此外，拦截器只会拦截访问的控制器的方法，如果访问的是jsp/html/css/images/js是不会拦截的。

要实现拦截器，我们需要实现HandlerInterceptor接口。

## 3.1 创建简单项目

**项目结构**

<img src="SpringMVC学习.assets\image-20200629103816483.png" alt="image-20200629103816483" style="zoom:80%;" />

1. **拦截器java配置**

```java
public class MyInterceptor implements HandlerInterceptor {
    //return true; 执行下一个拦截器，放行访问路径。
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("---------------处理前-----------------");
        return true;
    }

    //可以配置日志
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("---------------处理后-----------------");
    }

    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("---------------清理-----------------");
    }
}
```

2. **springmvc-servlet.xml文件配置**

```xml
<!--拦截器配置-->
<mvc:interceptors>
    <mvc:interceptor>
    	<!--该请求下的所有请求-->
        <mvc:mapping path="/**"/>
        <bean class="com.xiaolun.config.MyInterceptor"></bean>
    </mvc:interceptor>
</mvc:interceptors>
```

3. **测试类**

```java
@Controller
public class Interceptor {

    @RequestMapping("t1")
    public String test(){
        System.out.println("Controller中的测试执行了------");
        return "success";
    }
}
```

+ 控制台输出：

```ini
---------------处理前-----------------
Controller中的测试执行了------
---------------处理后-----------------
---------------清理-----------------
```

此时，测试类会走视图解析器，跳转到“success.jsp”界面。