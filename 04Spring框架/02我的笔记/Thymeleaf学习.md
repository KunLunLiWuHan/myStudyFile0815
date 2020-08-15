# 1 整合Thymeleaf

## 1.1 介绍和原理

像`JSP`这样的动态资源，Spring Boot默认不支持，而是推荐：模板引擎`thymeleaf`的方式（网页 = 模板+数据）进行动态资源的配置，`Thymeleaf`特点是通过特定的语法对`html`的标记做渲染。

在我们书写Thymeleaf代码时，主要看这两个文件，`ThymeleafAutoCongfutation`和`XXProperties`。

1. ThymeleafProperties源码

```java
blic static final String DEFAULT_PREFIX = "classpath:/templates/";
	public static final String DEFAULT_SUFFIX = ".html";
```

+ 使用`Thymeleaf`只需要将给你文件放入目录：`classpath:/templates/`；并且文件的后缀为：`.html`，其中“`classpath`”不能省略，不然会报错。
+ 在以前传统web项目中，静态资源修改后，是不需要重启的，但是在Spring Boot项目中，修改后，需要重启。

2. 自定义的yml文件

```yaml
spring:
  thymeleaf:
    cache: false
    check-template: true #检查模板是否存在
    enabled: true #是否启用
    encoding: utf-8 #模板编码
    excluded-view-names: #应该从解析中排除的视图名称列表（逗号分隔）
    mode: HTML5 #模板模式
    prefix: classpath:/templates/ #模板存放路径
    suffix: .html #模板后缀
```

+ 设置`thymeleaf`路径默认为`src/main/resources/templates`(应该加上`classpath`)。写不写无所谓。

## 1.2 创建入门项目

### 1.2.1 修改pom文件增加坐标

```html
<!-- 03Spring Boot整合Thymeleaf依赖 -->
	<dependency>
<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

+ 创建存放视图的目录

  html文件的目录位置：`src/sources/templates`.

  `templates`:该目录是安全的。意味着该目录下的内容是不允许外界直接访问的。

## 1.3 Thymeleaf的基本使用

### 1.3.1 创建controller类

```java
@Controller
public class DemoController {
	@RequestMapping("/showInfo")
	public String showInfo(Model model) {
		model.addAttribute("msg", "Thymeleaf第一个案例");
		return  "index"; 
	}
}
```

+ 如果`contoller`一旦被触发，我就去查找一个叫`index`的视图，`index`对应的是一个`index.html`页面，位置：`src/sources/templates/index.html`
+ @`Controller`：不能是`@RestController`。
+ 需要在前端输入网址;http://localhost:8080/showInfo,输入网址:http://localhost:8080/index.html时，找不到界面，因为`templates`目录是安全的。

注：

+ 使用注解@`RestController`或者@`ResponseBody`无法返回`html`界面，只返回数值。
+ 当要返回`templates`的`html`界面时，需要在`pom`文件中引入`thymeleaf`依赖，不然会报出无法解析的错误。

### 1.3.2 创建html文件

```HTML
<!DOCTYPE html>
<html lang="en" xmlns:th = "http://www.thymeleaf.org">
<head>
<meta charset="UTF-8">
<title>Thymeleaf入门</title>
</head>
<body>
		<span th:text = "Hello"></span>
		<hr/>
		<span th:text = "${msg}"></span>
</body>
</html>
```

+ `th:text = "${msg}"`，EL表达式，访问`controller`类中`model`的属性。

## 1.4 Thymeleaf语法详解

Thymeleaf是一个服务端Java模板引擎，能够处理HTML、XML、CSS和JavaScript等模板文件，可以直接当作静态模板来使用，主要目标是为开发工作流程带来优雅的自然模板，也是Java服务器端HTML5开发的理想选择

### 1.4.1 变量输出与字符串操作

1. **变量表达式${}**

+ 使用方法：th:xx = "${}"，获取对象属性，推荐这一种。

2. 变量表达式*{}

+ 首先通过th:objec获取对象，然后使用th:xx = "*{}"获取对象属性。

3. **链接表达式@{}**

+ 通过链接表达式@{}直接拿到应该用路径，然后拼接静态资源路径。

```javascript
<script th:src = "@{.../jquery.js}"></script>
```

4. th就是替换原有`html`的值：th:html属性名 = 值。

+ th:text :在页面中输出值，可对表达式或变量求值，并将结果显示在其被包含的 html 标签体内替换原有html文本。

+ th:value:可以将一个值放入到input标签的value中，用于属性赋值。

  举例：



```HTML
<input type="text" name="username" th:value ="${msg}"></input>

```

+ `${#strings.isEmpty(key)}:`判断字符串是否为空，如果为空，返回`true`，反之，返回`flase`。调用了`Thymeleaf`的内置对象。

  举例：

```HTML
<span th:text = "${#strings.isEmpty(msg)}"></span>
```

+ 注：
  + 调用**内置对象**一定要加“#”。
  + 大部分的内置对象，都以“s”结尾，比如，`strings,datas,numbers`等。

+ `${#strings.startsWith(msg,'T'`)}(endsWith：判断子串结尾。):判断字符串是否以子串“T”结尾，如果是，返回`true`,反之，返回`flase`。

```html
举例：
<span th:text = "${#strings.startsWith(msg,'T')}"></span>
```

+ `${#strings.length(msg)}`：返回字符串的长度.

+ `${#strings.indexOf(msg,'T')}`:查找子字符串的位置，返回该子串的下标，如果没找到，返回值为-1。

+ `${#strings.substring(msg,13,14)}`：截取子串，从13开始，到14结束。

  `${#strings.substring(msg,13)}`：从13开始截取，一直到最后。

+ `${#strings.toUpperCase(msg)}`：将字符串大写。

  `${#strings.toLowerCase(msg)}`：将字符串小写。

|                        日期格式化操作                        |
| :----------------------------------------------------------: |
| 1、${#dates.format(key)}:格式化日期，默认的以浏览器默认语言为格式化标准 |
| 2、${#dates.format(key,'yyyy/MM/dd')：自定义日期格式，显示为：2020/02/18。 |
| 3、${#dates.year(key)}、${#dates.month(key)}、${#dates.day(key)}：取年、月、日。 |

### 1.4.2 条件判断

1. th:if：条件判断

```html
<span th:if = "${sex}== '男'">
		性别：男
</span>
<span th:if = "${sex}== '女'">
		性别：女 
</span>
```

2. th:switch：条件判断

```html
controller中书写：model.addAttribute("id", "2");
html文件：
<div th:switch="${id}">
			<span th:case = "1">ID为1</span>
			<span th:case = "2">ID为2</span>
			<span th:case = "3">ID为3</span>
</div>	
```

### 1.4.3 迭代遍历

1.  **th:each**

+ 控制类

```java
@RequestMapping("/showInfo3")
public String showInfo3(Model model) {
    List<Users> list = new ArrayList();
    list.add(new Users(1,"张三",20));
    list.add(new Users(2,"李四",20));
    list.add(new Users(3,"王二",20));

    model.addAttribute("list", list);
    return  "index3"; 
}
```

+ html文件

```html
<table border="1">
    <tr>
        <th>ID</th>
        <th>Name</th>
        <th>Age</th>
    </tr>

    <tr th:each="u : ${list}">
        <td th:text="${u.userid}"></td>
        <td th:text="${u.username}"></td>
        <td th:text="${u.userage}"></td>
    </tr>
</table>
```

+ 操作的是对象。

2. th:each 状态变量

```html
<table border="1">
    <tr>
        <th>ID</th>
        <th>Name</th>
        <th>Age</th>
        <th>Index</th>
        <th>Count</th>
        <th>Size</th>
        <th>Even</th>
        <th>Odd</th>
        <th>First</th>
        <th>Last</th>	
    </tr>
    <tr th:each="u ,var: ${list}">
        <td th:text="${u.userid}"></td>
        <td th:text="${u.username}"></td>
        <td th:text="${u.userage}"></td>
        <td th:text="${var.index}"></td>
        <td th:text="${var.count}"></td>
        <td th:text="${var.size}"></td>
        <td th:text="${var.even}"></td>
        <td th:text="${var.odd}"></td>
        <td th:text="${var.first}"></td>
        <td th:text="${var.last}"></td>	
    </tr>
</table>
```

+ `var`为状态变量，位于当前迭代对象之后，加个逗号。
+ `${var.index}`：当前迭代对象的索引，从0开始。
+ `${var.count}`：当前迭代对象的结束器，从1开始。
+ `${var.size}`：迭代对象的数量。
+ `${var.even}`:当前循环次数是奇数还是偶数。偶数为true。
+ `${var.first}`：表示当前循环的是否是第一个。

3. th:each迭代Map

+ 控制类

```java
@RequestMapping("/showInfo4")
public String showInfo4(Model model) {
    Map<String,Users> map = new HashMap<>();
    map.put("u1",new Users(1,"张三",20));
    map.put("u2",new Users(2,"李四",20));
    map.put("u3",new Users(3,"王五",20));
    model.addAttribute("map", map);
    return  "index4"; 
}
```

+ 编写html文件

```html
<table border="1">
		<tr>
			<th>ID</th>
			<th>Name</th>
			<th>Age</th>
		</tr>
		<tr th:each="maps : ${map}">
	<td th:each="entry:${maps}" th:text="${entry.value.userid}">
	<td th:each="entry:${maps}" th:text="${entry.value.username}">
	<td th:each="entry:${maps}" th:text="${entry.value.userage}">
</table>
```

+ + 迭代Map时，取出当前迭代对象后再迭代一次。

### 1.4.4 域对象操作

####  1.4.4.1 HttpServletRequest

```java
request.setAttribute("req","HttpServletRequest");
```

```html
Request:<span th:text = "${#httpServletRequest.getAttribute('req')}">
	</span>
```

#### 1.4.4.2 HttpSession

```java
request.getSession().setAttribute("sess","HttpSession");
```

```html
Session:<span th:text = "${session.sess}">
	</span>
```

##### 1.4.4.3 ServletContext

```java
request.getSession().getServletContext().setAttribute("app","Application");
```

```html
Application:<span th:text = "${application.app}">
	</span>
```

+ 网页输出：

  Request:HttpServletRequest
   Session:HttpSessio

  Application:Applicatio

### 1.4.5 URL表达式

基本语法为：@{}

#### 1.4.5.1 URL类型-绝对路径

1. 控制类

```java
@Controller
public class DemoCOntroller {
	@RequestMapping("/{page}")
	public String showInfo(@PathVariable String page) {
		return  page; 
	}
}
```

+ @`RequestMapping`("/{page}")：特殊的注解方式，输入地址为；http://localhost:8080/index

2. html 文件

```html
<a th:href = "@{http://www.baidu.com}">绝对路径</a>
```

+ 路径要带上@{}语法。

#### 1.4.5.2 URL-相对路径

1. 相对于当前项目的根

```html
<a th:href = "@{/show}">相对路径</a>
```

+ 相对于项目上下文的相对路径。
+ 输入网址：http://localhost:8080/index，点击超链接“相对路径”，会跳转到：http://localhost:8080/show。而`show.html`文件再目录：`src/java/sources/templates`下。

2. 相对于服务器路径的根

```html
<a th:href="@{~/preject2/resourcename}">相对服务器的根</a>
```

+ 注意语法里面的波浪号“~”。

### 1.4.6 URL中参数的传递

1. 创建控制类

```java
@Controller
public class DemoCOntroller {
	@RequestMapping("/{page}")
	public String showInfo(@PathVariable String page,Integer id,String name) {
		System.out.println(id);
		System.out.println(name);
		return  page; 
	}
}
```

+ `page`:用于接收从网址上传递过来的数据。
+ `id,name`:用于接收从`html`文件中传递过来的数值。
+ 处理url中的参数的注解-PathVaribale 获取url中的数据

```java
@RestController
public class HelloController {
    @RequestMapping(value="/hello/{id}/{name}",method= RequestMethod.GET)
    public String sayHello(@PathVariable("id") Integer id,@PathVariable("name") String name){
        return "id:"+id+" name:"+name;
    }
}
```

+ + 在url有多个参数需要获取，在浏览器中输入地址： localhost：8080/hello/100/helloworld 然后会在html页面上打印出： id：100 name：helloworld 

2. html文件

```html
<a th:href="@{/show(id=1,name=张三)}">相对路径-传参</a>
```

+ (id=1,name=张三)：为准备传递的参数。
+ 网址输出：http://localhost:8080/show?id=1&name=张三

### 1.4.7 URL中参数的传递

```html
<a th:href="@{/path/{id}/show(id=1,name=张三)}">相对路径-传参-Restful风格</a>
```

+ 没事实验证。