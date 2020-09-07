# 1 介绍

web应用程序编写完毕后，若想提供给外界访问，需要提供一个服务器来统一管理。

**静态web的缺点**

web页面无法动态更新，所有的用户看到的都是同一个界面；无法和数据库交互，导致数据无法持久化。

**动态web的缺点**

假如服务器的动态web资源出现了错误，我们需要重新编写我们的后台程序，然后重新发布（停机维护）。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085531.png" alt="image-20200611175414155" style="zoom:80%;" />

**Web服务器**

服务器是一个被动的操作，用来处理用户的请求和给用户一些响应信息。主要有Tomcat。

**安装软件的步骤**

+ 安装或解压。
+ 了解配置文件和目录结构。
+ 这个软件的作用。

## 1.1 Http讲解

Http（超文本传输协议）是一个简单的请求-响应协议，运行在TCP之上。

+ 文本：html,字符串等。
+ 超文本：图片，音乐，视频包括地图等。
+ 默认端口：80。
+ Https:（security）端口443。

**两个时代**

HTTP1.0时代客户端与web服务器连接后，只能获取一个web资源，之后会断开连接。

HTTP1.1时代客户端与web服务器连接后，可以获得多个web资源。

**面试题**

当你在浏览器的地址栏中输入地址并回车的一瞬间，到页面展示的过程中，经历了什么？

## 1.2 Tomcat 详解

**下载**

网址：https://tomcat.apache.org/download-90.cgi。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085542.png" alt="image-20200611212344987" style="zoom:80%;" />

**启动和配置**

+ 文件夹作用

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085752.png" alt="image-20200611212612916" style="zoom:80%;" />

+ 启动和关闭

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085756.png" alt="image-20200611213523409" style="zoom:80%;" />

访问测试：在启动后，在界面输入http://localhost:8081/将会弹出tomcat界面。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085801.png" alt="image-20200611213840246" style="zoom:80%;" />

`server.xml`为服务器核心配置文件。

+ 在`server.xml`可以配置启动的端口号

```xml
 <Connector port="8081" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8444" />
```

+ 在`server.xml`可以配置主机的名称

```xml

      <Host name="www.xiaolun.com"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">
```

默认主机名为:localhost -> 127.0.0.1。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085806.png" alt="image-20200611215532924" style="zoom:80%;" />

这里更改了底层。

### 1.2.1 面试题

**谈谈网站是如何进行访问的？**

1. 输入一个域名，回车。

2. 检查本机的`C:\Windows\System32\drivers\etc\hosts`配置的文件中是否有这个映射，如果有的话，直接返回ip地址，可以直接访问；如果没有，去DNS服务找，找到的话就返回，找不到就返回找不到。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085810.png" alt="image-20200611220415859" style="zoom:80%;" />

**发布一个web网站**

发布一个网站时，不会先模仿。将自己写的网站，放到服务器（Tomcat）中指定的web应用下的文件夹（`webapps`）下，就可以访问了。

网站应该有的结构：

```java
-- webapps : tomcat服务器的web目录。
    -ROOT
    -xiaolun
     	-WEB-INF
    		-classes : java程序
            -lib : web应用所依赖的jar包
        	-web.xml ： 网站配置文件
    	-index.xml (默认的首页)
    	-static
        	-css
            -js
            -img
```

## 1.3 Maven环境搭建

在`Javaweb`中开发中，需要导入大量的jar包，我们需要手动导入，我们可以使用项目架构管理工具Maven来帮我们自动导入和配置jar包。

核心思想：约定大于配置。`Maven`会规定好你如何去编写我们的`Java`代码。

### 1.3.1 Maven下载和安装

1. **下载网址**

   http://maven.apache.org/

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085814.png" alt="image-20200611222913874" style="zoom: 67%;" />

建议：电脑上的所有环境都放到一个文件夹中，方便管理。

2. **配置环境变量**

+ M2_HOME

`maven`目录下的`bin`目录  `D:\apache-tomcat-9.0.12\8.0\apache-maven-3.6.3\bin`

+ MAVEN_HOME

`maven`的目录  `D:\apache-tomcat-9.0.12\8.0\apache-maven-3.6.3`

+ 在系统的path目录下

`%MAVEN_HOME%\bin`

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085817.png" alt="image-20200611224352356" style="zoom:80%;" />

测试maven是否安装成功。

**阿里云镜像**

加速我们的下载。

```xml
<mirrors>
   <mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf>        
    </mirror>
  </mirrors>
```

**本地仓库**

建立一个本地仓库，不用每次都下。

```xml
 <localRepository>D:\apache-tomcat-9.0.12\8.0\apache-maven-3.6.3\maven_repo</localRepository>
```

### 1.3.2 在IDEA中使用Maven

1. 选择Maven项目

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085820.png" alt="image-20200612092112739" style="zoom:80%;" />

2. 为文件命名

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085830.png" alt="image-20200612092353896" style="zoom:80%;" />

3. 选择配置环境

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085823.png" alt="image-20200612092700281" style="zoom:67%;" />

在选择Maven地址的时候，IDEA能够自动识别的原因，是我们设置了环境变量。

4. 等待项目初始化完成

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085837.png" alt="image-20200612093922656" style="zoom: 33%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085846.png" alt="image-20200612094159845" style="zoom: 50%;" />

在创建完成后，看一眼Maven的配置，因为在IDEA中经常会出现这样一个问题，就是项目自动创建完成后，Maven Home会使用IDEA中默认的，我们需要手动改成本地的。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085839.png" alt="image-20200612185128471" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085855.png" alt="image-20200612184244214" style="zoom:80%;" />

+ 通过上面两个图发现，一共有两个1.8 的JDK版本，我们要选对。
+ VM Option 的值为：

```java
//这也是我们为什么在仓库maven_repo里添加archetype-catalog.xml文件的原因。
-DarchetypeCatalog=local
```

**项目结构**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085858.png" alt="image-20200612095124332" style="zoom:80%;" />

**标记文件夹功能**

第一种方法：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085900.png" alt="image-20200612095626465" style="zoom: 67%;" />

当选择源码目录时，java包会变蓝，在右击java包后可以新建java类。然后将resource变成资源目录就可以了。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085905.png" alt="image-20200612100033484" style="zoom:80%;" />

第二种方法：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085911.png" alt="image-20200612100410874" style="zoom:80%;" />

#### 1.3.2.1 IDEA中配置Tomcat

1. Run ->Edit Configuration

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085907.png" alt="image-20200612100940768" style="zoom:80%;" />

2. 找到Templates

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085914.png" alt="image-20200612101217110" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085917.png" alt="image-20200612101850731" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085920.png" alt="image-20200627111524671" style="zoom:67%;" />

**解决警告问题**

产生这个问题的原因是，访问一个网站，需要指定一个文件夹名字。IDEA中没有默认找webapp文件，所以我们要手动添加。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085923.png" alt="image-20200612101941273" style="zoom: 67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085926.png" alt="image-20200612102443336" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085930.png" alt="image-20200612101941273" style="zoom: 67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085933.png" alt="image-20200612102443336" style="zoom:80%;" />



**运行Tomcat**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085936.png" alt="image-20200612105225296" style="zoom:80%;" />



<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085940.png" alt="image-20200612105349512" style="zoom:80%;" />

**pom文件**

```xml
<!--  package:项目的打包方式
  jar:java应用
  war:javaweb应用
-->
  <packaging>war</packaging>
```

+ maven的高级之处在于，它会帮你导入这个jar包所依赖的其他jar包。

#### 1.3.2.2 问题总结

1. 由于maven的约定大于配置，可能会遇到我们写的配置文件，无法导出或者无法生效的问题，解决方案如下：

```xml
<!--在build中配置resources,来防止我们的资源导出失败的问题-->
<build>
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>
    </build>
```

2. 遇到archetype-catalo.xml文件找不到的情况报错和解决方法如下：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085943.png" alt="image-20200612115456027" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085945.png" alt="image-20200612121057903" style="zoom: 50%;" />

3. 插件下载不成功报错和解决方法如下：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085953.png" alt="image-20200612184037306" style="zoom: 50%;" />

#### 1.3.2.3 修改web项目中web.xml

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085956.png" alt="image-20200612205428935" style="zoom:80%;" />

我们应该参考Tomcat中最新的文件配置,因为这个文件最和tomcat兼容，文件路径为：

```xml
D:\apache-tomcat-9.0.12\8.0\apache-tomcat-8.5.34\webapps\ROOT\WEB-INF\web.xml
```

对应的文件如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                      http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1"
         metadata-complete="true">
</web-app>
```

# 2 Servlet

## 2.1 介绍

Servlet接口有两个实现类，有一个是HttpServlet。

Servlet是sun公司开发动态web的一门技术。如果你需要开发一个servlet程序，需要完成下面两个小步骤：

（1）编写一个类，实现servlet接口。

（2）把开发好的Java类部署到web服务器中。

即：把实现了Servlet接口的Java程序叫做，Servlet。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085959.png" alt="image-20200612214504500" style="zoom:80%;" />

## 2.2 创建项目

**关于Maven父子工程的理解**

父项目中含有一个modele:

```xml
<modules>
    <module>servlet-01</module>
</modules>
```

子项目中暗含有parent。

1. **导入依赖**

```xml
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

    <!--03 test-->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
    </dependency>
```

2. **编写普通类实现Servlet接口**

```java
public class HelloServlet extends HttpServlet {

    //doGet/doPost只是实现请求的方式，可以相互调用，业务逻辑一致。
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("进入了doGet方法");
        PrintWriter writer = resp.getWriter(); //响应流
        writer.print("hello servlet");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req,resp);
    }
}
```

3. **编写Servlet的映射**

需要在web服务器中注册我们填写的Servlet，给浏览器一个访问的路径。

```xml
    <!--在web.xml中注册servlet-->
    <servlet>
        <servlet-name>hello</servlet-name>
        <servlet-class>com.xiaolun.HelloServlet</servlet-class>
    </servlet>

    <!--servlet的请求路径
    我们在前端请求一个/hello,就会走 </servlet>中的com.xiaolun.HelloServlet类
    该类中，有处理请求的一些方式。
    -->
    <servlet-mapping>
        <servlet-name>hello</servlet-name>
        <!--一个请求路径-->
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
```

+ 在mapping问题中，一个Servlet可以对应一个或多个路径（/hello/*）。

```xml
<!--默认请求路径-->
<url-pattern>/*</url-pattern>
<!--可以自定义后缀实现请求映射
即可以随便写这样的xxx.xiaolun路径
*前面不能加映射路径，比如/hello/*报错-->
<url-pattern>*.xiaolun</url-pattern>
```

4. **配置Tomcat**

参考上文，来配置Tomcat,注意我们配置的tomcat发布的路径为`/s1`。

5. **测试输出**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901090004.png" alt="image-20200612213624334" style="zoom:80%;" />

## 2.3 ServletContext

`web`容器在启动的时候，它会为每个`web`程序都创建一个对应的`ServletContext`对象，它代表了当前的`web`应用。

### 2.3.1 共享数据

在一个`Servlet`中保存的数，需要在另一个`Servlet`中获得。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901090007.png" alt="image-20200612221210650" style="zoom:80%;" />



1. **放置数据的类**

```java
public class GetServlet extends HttpServlet {
    @Override// 获取HelloServlet中的上下文对象
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext context = this.getServletContext();
        String username = (String)context.getAttribute("username");
        System.out.println("username----->"+username);

        //解决网页中输出中文的问题
        resp.setContentType("text/html");
        resp.setCharacterEncoding("utf-8");
        resp.getWriter().print("名字"+username);
    }

    @Override
    protected void doPut(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
}
```

2. **读取数据的类**

```java
//读取的类
public class HelloServlet extends HttpServlet {

    //doGet/doPost只是实现请求的方式，可以相互调用，业务逻辑一致。
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//        PrintWriter writer = resp.getWriter(); //响应流
//        writer.print("hello servlet");
        ServletContext context = this.getServletContext();

        String username = "xiaolun";
        //将一个数据保存到ServletContext中。
        context.setAttribute("username", username);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req,resp);
    }
}
```

3. **对读取的数据注册**

```xml
<!--02-->
<servlet>
    <servlet-name>getContext</servlet-name>
    <servlet-class>com.xiaolun.GetServlet</servlet-class>
</servlet>

<servlet-mapping>
    <servlet-name>getContext</servlet-name>
    <url-pattern>/getContext</url-pattern>
</servlet-mapping>
```

4. **测试**

首先启动项目，然后输入地址：

```http
http://localhost:8081/s1/hello
```

目的是让username获取数据，不然获取读取数据的类获得的上下文对象为null，然后输入地址：

```http
http://localhost:8081/s1/getContext
```

控制台输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901090010.png" alt="image-20200612223343548" style="zoom:80%;" />

### 2.3.2 获取初始化参数

+ **后台代码**

```java
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext context = this.getServletContext();
        String url = context.getInitParameter("url");
        resp.getWriter().print(url);
    }
```

+ **配置文件**

```xml
<context-param>
        <param-name>url</param-name>
        <param-value>jdbc:mysql://localhost:3306/mybatis</param-value>
</context-param>
```

控制台输出结果：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901090015.png" alt="image-20200612224205992" style="zoom:80%;" />

### 2.3.3 请求转发

+ **后台代码**

```java
//中转站   
@Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext context = this.getServletContext();
        //转发的请求xiagnyign路径
        RequestDispatcher requestDispatcher = context.getRequestDispatcher("/getContext");
        //接收的请求，转发的响应。调用forward方法实现请求转发。
        requestDispatcher.forward(req, resp);
    }
```

当浏览器A输入访问上面后台的访问地址B时，后台B会向后台C进行转发，将后台C的数据返回给A。此时，浏览器上的地址一直没有变换。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901090017.png" alt="image-20200612225605703" style="zoom:80%;" />

**区别**

+ 目标不同：转发是服务器行为（到的目录比重定向深），重定向是客户端的行为 。
+ 转发只有一次请求，但是重定向有两次请求。其中，第一次，客户端`request`一个网址，然后服务器响应，并`response`回来，告诉浏览器，你应该去别一个网址。

```java
 /**
 *  resp.sendRedirect("/s1/success.jsp");//重定向（地址改变），进入登录界面
 *  req.getRequestDispatcher("/success.jsp")//对于请求转发，括号内里面只有一个jsp地址。无/s1前缀。
 */
```

### 2.3.4 读取资源文件

所有的文件被打包到同一个路径下：calsses，我们俗称这个路径为classpath。

```java
@Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext context = this.getServletContext();
        // /指向了当前的web应用target中的servlet文件夹。第一个杠不能省略。相对定位。
        InputStream resourceAsStream = context.getResourceAsStream("/WEB-INF/classes/db.properties");
        Properties prop = new Properties();
        prop.load(resourceAsStream); //读取流
        String username = prop.getProperty("username");// 获取属性
        resp.getWriter().print(username);

    }
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901090020.png" alt="image-20200613082235192" style="zoom:80%;" />

# 3 HttpServletResponse

使用`HttpServletRequest`可以获得客户端请求过来的参数，`HttpServletResponse`会给客户端返回响应信息。

## 3.1 下载文件

```java
@Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
       // 1.获取下载文件的路径
        String realPath = "D:\\a22IDEA\\javaweb-01-test\\servlet-01\\src\\main\\resources\\1.png";
        //2. 获取下载的文件名
        String fileName = realPath.substring(realPath.lastIndexOf("\\")+1);
        //3. 让浏览器支持我们要下载的东西，中文文件名使用URLEncoder进行UTF-8编码。
        resp.setHeader("Content-Disposition", "attachment; filename="+ URLEncoder.encode(fileName, "UTF-8"));
        //4. 获取下载文件的输入流
        FileInputStream in = new FileInputStream(realPath);
        //5.创建缓冲区
        int len = 0;
        byte[] buffer = new byte[1024];
        //6.获取OutputStream对象
        ServletOutputStream out = resp.getOutputStream();
        //7.将FileOutputStream流写入到buffer缓冲区，使用OutputStream将缓冲区的数据输出到客户端。
        while((len = in.read(buffer)) > 0){
            out.write(buffer,0,len);
        }

        in.close();
        out.close();
    }
```

## 3.2 重定向

Web资源B收到客户端请求A后，B通知A去访问Web资源C，这个过程叫重定向。

**测试1**

```java
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        /**
         * 重定向等价于这两句，s1是虚拟映射地址
         *  resp.setHeader("Location", "/s1/getContext");
         *  resp.setStatus(302);
         */
        resp.sendRedirect("/s1/getContext");
    }
```

+ 当我们在浏览器输入：

```http
http://localhost:8081/s1/hello
```

地址会变到：

```http
http://localhost:8081/s1/getContext
```

**测试2**

模拟用户登录过程。

1. jsp登录界面

```jsp
<html>
<body>
    <%--
        1.这里是提交的路径，需要找到项目的路径。
        2.${pageContext.request.contextPath}代表当前的项目
    	3.这里的/login对应web.xml文件中的注册的 <url-pattern>/login</url-pattern>，也就是下面的jsp登录响应界面类的注册路径。
    --%>
    <form action="${pageContext.request.contextPath}/login" method="get">
        用户名：<input type="text" name="username"/><br/>
        密码： <input type="password" name="password"/><br>
        <input type="submit" value="提交">
    </form>
</body>
</html>
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901090026.png" alt="image-20200613100841594" style="zoom:80%;" />

2. jsp登录响应界面

```java
  @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //处理请求
        String username = req.getParameter("username");
        String password = req.getParameter("password");
        System.out.println(username + "" + password);
        //重定向，进入登录界面
        resp.sendRedirect("/s1/success.jsp");
    }
```

3. 登录成功界面

```jsp
<html>
<body>
        <h1>登录成功</h1>
</body>
</html>
```

# 4 HttpServletRequest

HttpServletRequest代表客户的请求，用户通过http协议访问服务器，http请求中的所有信息会被封装到HttpServletRequest对象中，通过该对象我们可以获得客户端的所有信息。

应用：针对复选框的值如何在后台获取而测试的一种方法。

1. **jsp前端界面**

```jsp
<html>
<body>
    <form action="${pageContext.request.contextPath}/login" method="get">
        用户名：<input type="text" name="username"/><br/>
        密码： <input type="password" name="password"/><br>
        爱好：
        <input type="checkbox" name="hobbys" value="女孩">女孩
        <input type="checkbox" name="hobbys" value="代码">代码
        <input type="checkbox" name="hobbys" value="电影">电影
        <input type="checkbox" name="hobbys" value="啤酒">啤酒
        <input type="submit" value="提交">
    </form>
</body>
</html>
```

2. **测试类**

```java
  @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //前后端输出编码格式
        req.setCharacterEncoding("utf-8");
        resp.setCharacterEncoding("utf-8");

        String username = req.getParameter("username");
        String password = req.getParameter("password");
        //使用getParameterValues接收前端传过来的多个参数
        String[] hobbys = req.getParameterValues("hobbys");
        System.out.println(username + "" + password);
        for (String hobby : hobbys) {
            System.out.println(hobby);
        }
        /**
         *  resp.sendRedirect("/s1/success.jsp");//重定向（地址改变），进入登录界面
         *  req.getRequestDispatcher()//对于请求转发，括号内里面只有一个jsp地址。无/s1前缀。
         */
        req.getRequestDispatcher("/success.jsp").forward(req, resp);
    }
```

# 5 会话

举例：用户打开浏览器，点击了很多超链接而访问了多个web资源，然后关闭浏览器，我们叫这个过程为会话。

**有状态会话**

一个同学来过教室，下次再来教室时，我们会知道这个同学，称之为有状态会话。

在一个网站，证明你来过的方法？

客户端  --------------> 服务端

1. 服务端给客户端一个信件，客户端下次访问服务端时带上信件（cookie）就可以了。
2. 服务器登记你来过了，下次你来的时候，进行匹配（session）。

**应用场景**

网站登录之后，你下次不用登录了。

## 5.1 保存会话的两种方式

### 5.1.1 Cookie

`cookie`是客户端技术（响应，请求），使用步骤：

+ 从请求中拿到Cookie信息（如果没有Cookie的话，响应resp会给请求设置一个Cookie）。
+ 服务器响应给客户端cookie。

**测试**

```java
//保存用户上一次访问的时间。
@Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        req.setCharacterEncoding("utf-8");
        resp.setCharacterEncoding("utf-8");

        PrintWriter out = resp.getWriter();

        /**
         *  1. Cookie，服务器端从客户端获取。
         *  2. req.getCookies(); //返回数组，说明Cookie可能存在多个
         */
        Cookie[] cookies = req.getCookies(); //返回数组，说明Cookie可能存在多个
        //判断Cookie是否存在
        if (cookies != null){
            //如果存在怎么办
            out.write("last time:");
            for (int i = 0; i < cookies.length; i++) {
                   if (cookies[i].getName().equals("LastLoginTime")){
                       long LastLoginTime = Long.parseLong(cookies[i].getValue());
                       Date date = new Date(LastLoginTime);
                       out.write(date.toLocaleString());
                   }
            }
        }else {
            out.write("first go here");
        }
        //服务器给客户端响应一个cookie
        Cookie cookie = new Cookie("LastLoginTime", System.currentTimeMillis()+"");
           //cookie有效期为一天
        cookie.setMaxAge(24*60*60);
        resp.addCookie(cookie);
    }
```

+ 可以发现浏览器在不关闭的情况下，服务端可以记得客户端上一次的访问时间。

**删除Cookie**

+ 不设置有效期，关闭浏览器，自动失效。
+ 设置有效期时间为0。

### 5.1.2 Session

`session`是服务器技术，利用该技术，可以保存用户的会话信息。我们可以将信息或数据放到session中。

服务器会为每一个用户（浏览器）创建一个session对象，一个session独占一个浏览器，只要浏览器没关，这个session就存在。

**cookie和session的区别**

+ cookie是把用户的数据写入用户的浏览器中，浏览器进行保存（可以保存多个）。
+ session把用户的数据写道用户独占的session中，服务器保存（保存重要的信息，减少服务器资源的浪费）。

**使用场景**

在整个网站中经常会使用的数据，我们将它保存到Session中。

**测试**

```java
// 简单的测试方法 
@Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setCharacterEncoding("UTF-8");
        resp.setCharacterEncoding("UTF-8");
        resp.setContentType("text/heml;character = utf-8");

        //得到session
        HttpSession session = req.getSession();

        //在session中存入数据
        session.setAttribute("name", "xiaolun");

        //获取session的id
        String id = session.getId();

        //判断session是不是新创建的
        if (session.isNew()) {
            resp.getWriter().write("你刚开始创建这个session，id为:"+id);
        }else {
            resp.getWriter().write("session已经存在了，id值为："+ id);
        }

        //注销session,创建时重新生成一个。
        session.invalidate();
    }
```

# 6 JSP

全称为：`Java Server Pages`（java服务器页面），是sun公司创建的一种动态网页技术标准。`JSP`页面中可以嵌入`Java`代码，为用户提供动态数据。

`JSP`最终会被转化成为一个类，本质上是一个`Servlet`。

## 6.1 语法和指令

### 6.1.1 基本语法

```jsp
 <%--JSP表达式
        作用：用来将程序输出到客户端
        格式：<%= 变量或者表达式%>
    --%>
    <%= new Date()%>
    <br>

    <%--JSP脚本片段--%>
    <%
        int sum = 0;
        for (int i = 0; i < 10; i++) {
            System.out.println("你好"); //在控制台输出
        }
        out.print("<h1>sum = "+sum + "<h1>");
    %>

    <%--JSP声明
    会被编译到JSP生成的Java类中，它的作用域更高，其他的就会被生成到方法中。
    --%>
    <%!
        static {
            System.out.println("nihao");
        }
        private int globalVar = 0;
    %>
```

## 6.2 JavaBean

实体类，JavaBean有特定的写法，必须要有一个无参构造，属性必须私有化，必须有对应的get/set方法，一般用来和数据库的字段做映射。

# 7 MVC三层架构

MVC就是Model、View、Controller(模型层，视图层，控制层)。

**Model**

+ 业务处理：业务逻辑（Service）。
+ 数据持久层：CRUD(Dao)。

**View**

+ 展示数据。
+ 提供链接等。

**Controller**

+ 接收用户的请求，响应给客户端内容或者是重定向/转发。
+ 交给业务层处理对应的代码。
+ 控制视图的跳转。

```java
//登录示例
登录-->Controller层接收用户的登录请求-->Controller层处理用户的请求（获取登录用户的参数：username,password）-->交给业务层Service处理登录业务（判断用户名密码是否正确）-->Dao层查询用户名和密码是否正确-->数据库。
```



<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901090034.png" alt="image-20200613172025233" style="zoom:80%;" />

# 8 过滤器Filter

可以过滤网站的数据。

## 8.1 简单项目

1. **需要过滤的请求**

```java
//解决乱码问题 
@Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("你好啊，世界");
    }
```

2. **web.xml配置过滤器**

```java
  <!--配置过滤器
        只要是/servlet的任何请求，都会经过这个过滤器。
     -->
    <filter>
        <filter-name>CharacterEncodingFilter</filter-name>
        <filter-class>com.xiaolun.filter.CharacterEncodingFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>CharacterEncodingFilter</filter-name>
        <url-pattern>/servlet/*</url-pattern>
    </filter-mapping>
```

3. **实现过滤器接口**

```java
// 导入包不要错：import javax.servlet.*;
public class CharacterEncodingFilter implements Filter {
    @Override //web启动时就初始化了，等到过滤。
    public void init(FilterConfig filterConfig) throws ServletException {
        System.out.println("CharacterEncodingFilter开始进行初始化");
    }

    @Override //解决中文乱码
    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws IOException, ServletException {
        resp.setContentType("text/html;character=utf-8");
        resp.setCharacterEncoding("utf-8");
        req.setCharacterEncoding("utf-8");

        System.out.println("-------->执行前");
        /**
         * 1. chain：起到放行的作用
         * 2. 让我们的请求继续往前走，若不写，程序到这里会被拦截住。起中转的作用。
         */
        chain.doFilter(req,resp);
        System.out.println("-------->执行后");

    }

    @Override //销毁
    public void destroy() {
        System.out.println("CharacterEncodingFilter开始进行销毁");
    }
}
```

## 8.2 权限拦截

**场景**

用户登录之后才能进入主页，注销之后就不能进入主页了。

1. **登录界面**

```jsp
<form action="/s1/servlet/login" method="get">
    用户名：<input type="text" name="username"/><br/>
    <input type="submit" value="提交">
</form>
```

2. **登录处理的Servlet**

```java
@Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("login请求进行来了");
        //获取前端请求的参数
        String username = req.getParameter("username");
        System.out.println("username--------->"+username);
        if (username.equals("admin")) {
//我们可以设置一个工具类util,将USER_SESSION设置为一个常量。        
// public static final String USER_SESSION = "USER_SESSION" 
           req.getSession().setAttribute("USER_SESSION", req.getSession().getId());
            resp.sendRedirect("/s1/sys/success.jsp");
        }else {
            resp.sendRedirect("/s1/error.jsp");
        }
    }
```

+ 主要目的是对登录用户的界面跳转操作，其中

```java
resp.sendRedirect("/s1/sys/success.jsp");
```

有前缀/sys主要是因为文件目录的结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901090038.png" alt="image-20200613192624237" style="zoom:80%;" />

3. **注销处理**

```java
 @Override //注销功能
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        Object user_session = req.getSession().getAttribute("USER_SESSION");

        if (user_session != null){
            //如果session不为null，说明有用户登录，我们移除session。
            req.getSession().removeAttribute("USER_SESSION");
            resp.sendRedirect("/s1/login.jsp"); //重新回到登录界面
        }else {
            resp.sendRedirect("/s1/login.jsp"); //重新回到登录界面
        }
    }
```

4. **设置过滤器**

```java
@Override //注销功能
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        Object user_session = req.getSession().getAttribute("USER_SESSION");

        if (user_session != null){
            //如果session不为null，说明有用户登录，我们移除session。
            req.getSession().removeAttribute("USER_SESSION");
            resp.sendRedirect("/s1/login.jsp"); //重新回到登录界面
        }else {
            resp.sendRedirect("/s1/login.jsp"); //重新回到登录界面
        }
    }
```

```xml
<!--过滤器注册-->
<filter>
        <filter-name>LoginFilter</filter-name>
        <filter-class>com.xiaolun.filter.LoginFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>LoginFilter</filter-name>
        <url-pattern>/sys/*</url-pattern>
    </filter-mapping>
```

+ 主要解决的是，当用户注销后，界面跳转到登录界面，假如用户在浏览器端仍输入：

```http
http://localhost:8081/s1/sys/success.jsp
```

我们要对即使注销了仍然可以登录的情况进行拦截，将这种用户跳转到报错error.xml界面。

同时，我们要注意拦截器路径设置为`/sys/*`，刚好对应浏览器端中的`sys`路径内容。

# 9 文件传输

## 9.1 文件传输原理

**文件jar包下载**

```xml
<!-- https://mvnrepository.com/artifact/commons-io/commons-io -->
<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.7</version>
</dependency>

<!-- https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload -->
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.4</version>
</dependency>
```

**文件上传注意事项**

1. 为保证服务器安全，上传文件应该放到外界无法直接访问的目录下，比如WEB-INF目录下。
2. 为防止文件覆盖现象的发生，要为上传文件产生一个唯一的文件名。比如时间戳、位运算或者md5加密算法。

3. 要限制上传文件的最大值。不然占用更多的资源。

4. 可以限制文件上传的类型，在接收上传文件名时，判断后缀名是否合法。

**需要用到的类详解**

`ServletFileUpload`负责处理上传的文件数据，并把表单中每个输入项封装成一个`FileItem`对象，在使用`ServletFileUpload`对象解析请求时需要`DiskFileItemFactory`对象。所以，我们需要在解析工作前构造好`DiskFileItemFactory`对象，通过`ServletFileUpload`对象的构造方法或者`setkFileItemFactory()`设置`ServletFileUpload`对象的`fileItemFactory`属性。

```java
public class FileServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //判断上传的文件是普通表单还是带文件的表单
        if( !ServletFileUpload.isMultipartContent(req)){
            return; //终止方法运行，说明这是一个普通的表单，直接返回。
        }

        /**
         * 1. 通过这个if说明是文件了
         * 2. 创建上传文件的保存路径，建议保存到web-inf路径下，用户无法访问。
         **/
        String uploadPath = this.getServletContext().getRealPath("/WEB-INF/upload");
        File uploadFile = new File(uploadPath);
        //如果上传文件不存在,就创建这个目录。
        if (!uploadFile.exists()){
            uploadFile.mkdir();
        }

        /**
         * 1. 缓存，临时文件。
         * 2. 临时路径，假如文件超过了预期的大小，我们就把它放到一个临时文件中，过几天自动删除，或者提醒用户转为永久。
         */
        String tmpPath = this.getServletContext().getRealPath("/WEB-INF/tmp");
        File file = new File(tmpPath);
        //如果上传文件不存在， 创建这个目录。
        if (!file.exists()){
            file.mkdir();
        }

        /**
         * 处理上传的文件，一般都需要通过借助Apache上传组件来获取，commons-fileupload，它依赖于commons-io组件。
         */
        try {
            //1.创建`DiskFileItemFactory`对象，处理文件上传路径或者大小限制的。
            // 将请求消息实体中的每一个项目封装成单独的DiskFileItem (FileItem接口的实现) 对象的任务。
            DiskFileItemFactory factory = getDiskFileItemFactory(file);
            //2.获取ServletFileUpload，ServletFileUpload负责处理上传的文件数据,并将表单中每个输入项封装成
            //一个FileItem对象中。
            ServletFileUpload upload = getServletFileUpload(factory);
            //3.处理上传文件
            String msg = uploadParseRequest(upload,req,uploadPath);
        } catch (Exception e) {
            e.printStackTrace();
        }

        //servlet请求转发消息
        req.setAttribute("msg", "文件上传成功");
        req.getRequestDispatcher("/info.jsp").forward(req, resp);
    }

    /**
     *
     * @param upload
     * @param req
     * @param uploadPath
     * @return
     * @throws Exception
     */
    private static String uploadParseRequest(ServletFileUpload upload, HttpServletRequest req, String uploadPath) throws Exception {
        List<FileItem> fileItems = fileItems = upload.parseRequest(req);

        //表单有好多项，我们需要遍历。
        for (FileItem fileItem : fileItems) {
            //判断上传的文件是普通的表单还是带文件的表单
            if (fileItem.isFormField()){ //正常处理普通的表单。
                //getFieldName()指的是前端表单控件的name。
                String name = fileItem.getFieldName();
                //处理乱码问题
                String value = fileItem.getString("UTF-8");
                System.out.println(name + ":" + value);
            }else {
                /**
                 * 1.处理文件表单。
                 * 2.拿到文件名字getName()对应表单中的name属性。
                 */
                String uploadFileName = fileItem.getName();
                System.out.println("上传的文件名为："+uploadFileName);
                //上传文件名字不合法，让它不上传。
                if (uploadFileName.trim().equals("") || uploadFileName == null){
                    continue;
                }

                /**
                 * 1.获取上传的文件名。
                 * 2.如果文件后缀名，不是我们所需要的，就直接return，不处理，告诉用户文件类型不对。
                 * 3.C:\Program Files\Apache Software Foundation\Tomcat 8.5\webapps\s1\WEB-INF\upload\21eb681e-d03e-4389-8b35-e15d761d309c
                 */
                String fileName = uploadFileName.substring(uploadFileName.lastIndexOf("/") + 1);
                String fileExName = uploadFileName.substring(uploadFileName.lastIndexOf(".") + 1);
                System.out.println("文件信息【文件名"+fileName+ "----文件类型："+fileExName);
                /**
                 * 1.使用UUID(唯一识别的通用码)，保证文件名唯一。
                 * 2.UUID.randomUUID()随机生成一个唯一识别的通用码。
                 * 3.创建实体类的时候，实现这个序列化接口：implements Serializable。
                 * 是一个标记接口，当虚拟运行到这里的时候，会实现一些方法。
                 */
                String uuidPath = UUID.randomUUID().toString();

                /**1.文件处理完毕，文件存放。
                 * 2.文件真实存在的路径 reanlPath。
                 */
                String realPath = uploadPath +"/" + uuidPath;
                //给每一个文件创建一个对应的文件夹
                File realPathFile = new File(realPath);
                if ( !realPathFile.exists()){
                    realPathFile.mkdir();
                }
                //-----------------------------------------
                /**
                 * 1.文件存放完毕，文件传输。
                 * 2.获得文件上传的流。
                 * 3.创建要给文件输出流。
                 */
                InputStream inputStream = fileItem.getInputStream();
                FileOutputStream fos = new FileOutputStream(realPath + "/" + fileName);

                //创建一个缓冲区
                byte[] buffer = new byte[1024*1024];
                //判断是否读取完毕
                int len = 0;
                //如果大于0说明还有数据
                while ((len = inputStream.read(buffer)) > 0){
                    fos.write(buffer,0,len);
                }

                //关闭流
                fos.close();
                inputStream.close();

                fileItem.delete();//上传成功，清除临时文件
            }
        }
        return null;
    }

    /**
     *
     * @param factory
     * @return
     */
    private static ServletFileUpload getServletFileUpload(DiskFileItemFactory factory) {
        ServletFileUpload upload = new ServletFileUpload(factory);
        upload.setProgressListener(new ProgressListener() {
            //pBytesRead已经读取到的文件大小
            //pContentLength文件大小
            @Override
            public void update(long pBytesRead, long pContentLength, int pItems) {
                System.out.println("总大小："+pContentLength+"已上传："+pBytesRead);
            }
        });
        //处理乱码问题
        upload.setHeaderEncoding("UTF-8");
        //设置单个文件的最大值
        upload.setFileSizeMax(1024*1024*10);
        //设置总共能够上传文件的大小
        //1024=1Kb*1024=1M*1-=10M
        upload.setSizeMax(1024*1024*10);
        return upload;
    }

    /**
     *
     * @param file
     * @return
     */
    private static DiskFileItemFactory getDiskFileItemFactory(File file){
        //1.创建`DiskFileItemFactory`对象，处理文件上传路径或者大小限制的。
        DiskFileItemFactory factory = new DiskFileItemFactory();
        //通过这个工厂设置一个缓冲区，当上传的文件大于这个缓冲区的时候，将他放到临时文件中。
        factory.setSizeThreshold(1024*1024); //大小为1M.
        factory.setRepository(file);//临时文件保存目录。
        return factory;
    }
}
```

+ 控制台输出

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901090044.png" alt="image-20200615170447796" style="zoom:80%;" />

## 9.2 邮件发送原理及实现

SMTP协议发送邮件，POP3协议接收邮件。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901090046.png" alt="image-20200615172906229" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901090052.png" alt="image-20200615173436171" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901090055.png" alt="image-20200615172906229" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901090122.png" alt="image-20200615173436171" style="zoom:80%;" />

1. **添加pom依赖**

```xml
 <!-- https://mvnrepository.com/artifact/javax.mail/mail -->
    <dependency>
      <groupId>javax.mail</groupId>
      <artifactId>mail</artifactId>
      <version>1.4.7</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/javax.activation/activation -->
    <dependency>
      <groupId>javax.activation</groupId>
      <artifactId>activation</artifactId>
      <version>1.1.1</version>
    </dependency>
```

2. **测试类**

```java
public class SendEmail {

    //简单邮件：没有附件和图片，纯文本邮件
    //复杂邮件，含有附件和图片。
    //要发送的邮件，需要获得协议和支持。开启SMTP/POP3协议。
    //授权码：vghnhyejdnvzcjcd
    public static void main(String[] args) throws Exception {
        Properties prop = new Properties();
        prop.setProperty("mail.host", "smtp.qq.com"); //设置qq邮箱服务器
        prop.setProperty("mail.transport.protocol", "smtp"); //邮件发送协议
        prop.setProperty("mail.smtp.auth", "true"); //需要验证用户名和密码

        //关于qq邮箱，需要设置SSL加密，其他邮箱不需要下面的代码。
        MailSSLSocketFactory sf = new MailSSLSocketFactory();
        sf.setTrustAllHosts(true);
        prop.put("mail.smtp.ssl.enable", "true");
        prop.put("mail.smtp.ssl.socketFactory", sf);

        //使用JavaMail发送邮件的5个步骤
        //1.创建定义整个应用程序所需环境信息的Session对象,qq才有下面的session代码，其他不需要。
        Session session = Session.getDefaultInstance(prop,new Authenticator(){
            @Override
            protected PasswordAuthentication getPasswordAuthentication() {
                //发件人用户名和授权码
                return new PasswordAuthentication("3493826518@qq.com", "vghnhyejdnvzcjcd");
            }
        });
        //开启session的debug模式，可以查看程序发送邮件的状态。
        session.setDebug(true);
        //2.通过session对象得到transport对象
        Transport ts = session.getTransport();
        //3.使用邮箱的用户名和授权码连接邮件服务器
        ts.connect("smtp.qq.com", "3493826518@qq.com","vghnhyejdnvzcjcd");
        //4.创建邮件,写邮件（收件人，邮件主题，内容）
        //需要传递session
        MimeMessage message = new MimeMessage(session);
        //指明邮件的发件人
        message.setFrom(new InternetAddress("3493826518@qq.com"));
        //指明邮件的收件人，现在发件人和收件人是一样的，那是自己和自己发。
        message.setRecipient(Message.RecipientType.TO , new InternetAddress("3493826518@qq.com"));
        //邮件的标题
        message.setSubject("简单的邮件标题");
        //邮件的内容
        message.setContent("你好啊","text/html;charset=UTF-8");
        //5. 发送邮件。
        ts.sendMessage(message,message.getAllRecipients());
        ts.close();
    }
}
```

+ 控制台输出

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901090127.png" alt="image-20200615181116989" style="zoom:80%;" />

### 9.2.1 SpringBoot中整合Email

首先添加下面的依赖，之后由于没有发现 JavaMailSender类而导致项目学习难以进行。

```html
<!-- 03sprignboot整合邮件依赖包 -->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-mail</artifactId>
</dependency>
```

## 9.3 网站注册及发送邮件

**MIME**(多用途互联网邮件扩展类型)

其中包含两个类，`MimeBodyPart`类表示的是一个MIME消息，它和`MimeMessage`类一样都是`Part`接口继承过来的；`MimeMultipart`类是抽象类`Multipart`实现的子类，它用来组合多个`MIME`消息，一个`MimeMultipart`对象可以包含多个代表MIME消息的`MimeBodyPart`对象。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901090130.png" alt="image-20200615194057312" style="zoom:80%;" />

**测试1**

使用场景：将文字和图片一起上传。

```java
 public static void main(String[] args) throws Exception {
        Properties prop = new Properties();
        prop.setProperty("mail.host", "smtp.qq.com"); //设置qq邮箱服务器
        prop.setProperty("mail.transport.protocol", "smtp"); //邮件发送协议
        prop.setProperty("mail.smtp.auth", "true"); //需要验证用户名和密码

        MailSSLSocketFactory sf = new MailSSLSocketFactory();
        sf.setTrustAllHosts(true);
        prop.put("mail.smtp.ssl.enable", "true");
        prop.put("mail.smtp.ssl.socketFactory", sf);

        Session session = Session.getDefaultInstance(prop,new Authenticator(){
            @Override
            protected PasswordAuthentication getPasswordAuthentication() {
                return new PasswordAuthentication("3493826518@qq.com", "vghnhyejdnvzcjcd");
            }
        });
        session.setDebug(true);
        Transport ts = session.getTransport();
        ts.connect("smtp.qq.com", "3493826518@qq.com","vghnhyejdnvzcjcd");
        MimeMessage message = new MimeMessage(session);
        message.setFrom(new InternetAddress("3493826518@qq.com"));
        message.setRecipient(Message.RecipientType.TO , new InternetAddress("3493826518@qq.com"));
        message.setSubject("简单的邮件标题");

        //邮件的内容，采用下面的方式。
//        message.setContent("你好啊","text/html;charset=UTF-8");
        //--------------------------------------------------------------------------------------
        //准备图片数据
        MimeBodyPart image = new MimeBodyPart();
        //图片需经过数据处理（相对路径找不到图片，报错）
        DataHandler dh = new DataHandler(new FileDataSource("D:\\a22IDEA\\javaweb-01-test\\servlet-01\\src\\1.jpg"));
        image.setDataHandler(dh);
        // 为图片设置一个唯一编号，便于下文引用。
        image.setContentID("bz.jpg");

        //准备正文数据
        MimeBodyPart text= new MimeBodyPart();

        /**
         * 文本+图片 的混合“节点”封装成一个普通“节点”。
         *  src='cid:bz.jpg'等号之间不要留间隔，不然会报错。/> 的/带不带无所谓。
         */
        text.setContent("这是一封邮件正文带图片的<img src='cid:bz.jpg'>邮件","text/html;charset=UTF-8");

        //描述数据关系
        MimeMultipart mm = new MimeMultipart();
        mm.addBodyPart(image);
        mm.addBodyPart(text);
        mm.setSubType("related");

        //设置到消息中，保存修改
        message.setContent(mm);
        message.saveChanges();

        //--------------------------------------------------------------------------------------
        ts.sendMessage(message,message.getAllRecipients());
        ts.close();
    }
```

**测试2**

使用场景：将文字、图片和附件一起上传。

```java
 //准备文本数据
        MimeBodyPart text= new MimeBodyPart();
        text.setContent("这是一封邮件正文带图片的<img src='cid:bz.jpg'>邮件","text/html;charset=UTF-8");

        //准备图片数据
        MimeBodyPart image = new MimeBodyPart();
        DataHandler dh = new DataHandler(new FileDataSource("D:\\a22IDEA\\javaweb-01-test\\servlet-01\\src\\main\\resources\\1.jpg"));
        image.setDataHandler(dh);
        image.setContentID("bz.jpg");

        //准备附件
        MimeBodyPart attach = new MimeBodyPart();
        attach.setDataHandler(new DataHandler(new FileDataSource("D:\\a22IDEA\\javaweb-01-test\\servlet-01\\src\\main\\resources\\1.txt")));
        //为附件添加名字，不同于图片中的“setContentID”。文件名在邮箱附件上显示。
        attach.setFileName("testTxt.txt");

        //将文本，图片，附件数据进行拼接,描述数据关系
        MimeMultipart allFile = new MimeMultipart();
        allFile.addBodyPart(text);
        allFile.addBodyPart(image);
        allFile.addBodyPart(attach);
        //类型设置为mixed
        allFile.setSubType("mixed");

        //设置到消息中，保存修改
        message.setContent(allFile);
        message.saveChanges();
```

+ 控制台输出

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901090137.png" alt="image-20200615212642893" style="zoom:80%;" />