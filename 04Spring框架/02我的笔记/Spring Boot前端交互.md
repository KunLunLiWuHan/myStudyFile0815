# 1 thymeleaf数据交互

## 1 服务端数据

### 1.1 修改pom文件

```html
<!--文件名：SB-validate-->
<dependencies>
    <!--01web启动器-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!--02thymeleaf启动器-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
</dependencies>
```

+ `th:action`定义后台控制器路径，类似`“<form>”`标签的`action`属性。

## 1.2 编写添加用户代码

### 1.2.1 创建实体类pojo

```java
public class Users {
    private String name;
    private String password;
    private Integer age;
} //自行添加上get/set/构造方法。
```

+ `add.html`表单对应的参数名应该为和后台的Pojo属性名要一致，才能够传递数据。

### 1.2.2 编写controller类

```java
@Controller
public class UserController {
    @RequestMapping("/addUser")
    public String showPage(){
        return "add";
    }

    @RequestMapping("/save")
    public String savePage(Users users){
        System.out.println(users);
        return "ok";
    }
}
```

+ @`RequestMapping`("/save")刚好到达`ok.html`界面，在这之前Users里面的数据已经通过`get`和`set`方法进行获取完毕。

### 1.2.3 编写界面类

```java
add.html代码;
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="UTF-8">
    <title>添加用户</title>
</head>
<body>
    <form th:action = "@{/save}" method="post">
        用户姓名：<input type="text" name = "name"/><br/>
        用户密码：<input type="password" name = "password"/><br/>
        用户年龄：<input type="text" name = "age"/><br/>
        <input type="submit" value="ok"/>
    </form>
</body>
</html>

ok.html代码：
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>操作成功</title>
</head>
<body>
        ok....
</body>
</html>
```

## 1.3 对表单做数据校验

Spring Boot中使用了Hibernate-validate校验框架。

### 1.3.1 校验步骤

#### 1.3.1.1 在实体类中添加校验规则

```java
public class Users {
    @NotBlank //数据非空
    private String name;
    @NotBlank //数据非空
    private String password;
}
```

#### 1.3.1.2 在controller中开启数据校验

```java
@RequestMapping("/save")
public String savePage(@Valid Users users,BindingResult result){
   if (result.hasErrors()){ //返回true，校验失败
        return "add"; //返回到add.html
   }
    System.out.println(users);
    return "ok";
}
```

+ **@Valid**` 开启对`Users对象的数据校验
+ `BindingResult`:封装了校验的结果，`result.hasErrors`()){ //返回`true`，校验失败。
+ 校验不成功，返回到`add.html`界面。
+ @`NotBlank`(message = "用户名不能为空") ，自定义输出信息。

#### 1.3.1.3 在页面汇总获取提示信息

```html
<body>
    <form th:action = "@{/save}" method="post">
        用户姓名：<input type="text" name = "name"/><font color="red" th:errors = "${users.name}"></font><br/>
        用户密码：<input type="password" name = "password"/><font color="red" th:errors = "${users.password}"></font><br/>
        用户年龄：<input type="text" name = "age"/><font color="red" th:errors = "${users. age }"></font><br/>
        <input type="submit" value="ok"/>
    </form>
```

+ `<font color="red" th:errors = "${users.name}"></font>`：在校验时，将错误的提示信息放到`BindingResult` `result`中，名字和属性名一样，`users.name`拿到的就是该对象下的`name`值。

+  输入localhost:8080/addUser时会报出异常。

  这是因为，当输出上面网址的时候，我们会访问add.html界面，并没有输入任何信息，故此时<`font color="red" th:errors = "${users.name}"></font>`代码中，并没有`users`的信息，即`users = null`。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083546.png" style="zoom:80%;" />

## 1.4 解决异常

```java
public String showPage(Users users){
    return "add";
}
```

+ 在跳转页面的方法中注入一个`Users`对象。由于`springmvc`会将该对象放到`model`中传递，`key`的名称会使用该对象的驼峰式的命名规则来作为`key`。

  参数的变量名需要与对象的名称相同。即将首字母小写“`user`”。

+ `users`对应`add.html`中的`users`

+ (@`ModelAttribute`("aa") `Users users`):可以为传递的对象更改名称，将当前传递的`key`变为`aa`,那么在页面中获取的对象的~也需要修改为”aa” ，即`th:errors = "${aa.name}"`。此时其他输入参数也要添加`(@ModelAttribute("aa") Users users)`，不然其他也报错。

|                         其他校验规则                         |
| :----------------------------------------------------------: |
|  @NotBlank:判断字符串是否为null或者是空串（去掉首尾空格）。  |
| @NotEmpty:判断字符串是否为null或者是空串（不去掉首尾空格）。 |
|             @Length(min = 2,max = 6):长度范围。              |
|      @Min（value =15）：判断数值最小值，相对应的@Max。       |
|                  @Email:判断邮箱是否合法。                   |

# 2 Ajax学习

## 2.1 介绍

​		Ajax即为异步的JavaScript和XML的缩写（Asynchronous JavaScript and XML），不是一种编程语言，而是一种用于创建更好更快以及交互性更强的Web应用程序的技术，浏览器与服务器通信而无须刷新当前页面的技术。Ajax帮助开发人员管理浏览器中“异步请求对象”（向服务端发送请求协议包或者接收的响应数据等）。

​		开发人员利用`Ajax`可以做：注册时，输入用户名自动检测用户是否已经存在；登陆时，提示用户名密码错误；删除数据行时，将行ID发送到后台，后台在数据库中删除，数据库删除成功后，在页面`DOM`删除。

**现在设计的产品链**

​		H5+网页+客户端+手机端（Android、IOS）+小程序。

**刷新和局部刷新**

​		区别：对于全局刷新，浏览器在得到服务器返回的数据后，只能展示得到的数据，不能同时展示浏览器之前的内容，而局部刷新可以展示之前数据。

​		全局刷新工作原理：

  1. 必须由浏览器负责将“请求协议包”推送到服务端；

  2. 导致服务器端将“响应协议包”直接推送到浏览器的内存；

  3. 导致浏览器内存中原有的数据被覆盖；

  4. 此时浏览器在展示数据时，只能展示得到响应数据，无法展示原有的数据。

     局部刷新工作原理：

5. 必须禁止由浏览器向服务端发送请求（不能使用超链接、from、window.location）；

6. 由浏览器内存中一个脚本对象代替浏览器将“请求协议包”发送到服务端；

7. 导致服务端返回的“响应包”直接推送到这个脚本对象上；

8. 导致脚本对象内容被覆盖，此时浏览器内存中绝大多数内容没有受到影响；

9. 开发人员从脚本对象上取出响应数据，更新到浏览器中指定标签上；

10. 此时浏览器在展示数据时，就可以既展示响应结果又可以展示原有内容。

**WebSocket介绍**

​		HTTP协议是一个无连接的，单向的应用层协议，采用请求/响应的额模型。通信请求只能由客户端发起，服务器对请求做出应答处理。这种通信模型有一个弊端：HTTP协议无法实现服务器主动给客户端发起消息。

如果服务器有连续的状态变化，客户端获知就非常麻烦。

​		`WebSocket`连接允许客户端和服务器之间进行全双工通信，以便任何一方都可以通过建立的连接将数据推向另一端。`WebSocket`只需要建立一次连接，就可以一直保持连接状态。

**异步调用**

​		在项目中，当访问其它接口较慢或者做耗时任务时，不想程序一直卡在耗时任务上，想程序能够给并行执行，我们可以使用多线程来并行的处理任务，Spring Boot提供了异步处理方式@Async。

**同步请求和异步请求**

​		同步请求：在当前异步请求对象工作期间，浏览器只能处于等待状态，直到当前异步请求对象工作完毕，浏览器才可以委派其他的异步请求对象发送请求，（举例：取菜、取酒一起去）。而异步请求时，浏览器可以委派其他的异步请求对象发送请求。

**异步请求对象工作状态**

| xmlHttp.readyState | 状态                                                         | 发生位置                                                     |
| :----------------: | ------------------------------------------------------------ | ------------------------------------------------------------ |
|         0          | 异步请求对象已创建完毕，还没有调用open()方法。               | `var xmlHttp   = new XmlHttpRequest();`                      |
|         1          | 异步请求对象已初始化完毕，（载入）已调用send()方法，正在发送请求 | `xmlHttp.open(“post/get”,“/myWeb/onServlet?uname  =smith”，false)` |
|         2          | （载入完成）send()方法完成，浏览器已收到全部响应内容。       | `xmlHttp.send()`                                             |
|         3          | 此时异步请求对象正在解析响应包中响应体内容                   |                                                              |
|         4          | 异步请求对象解析数据完毕，此时数据符合脚本规范的数据，开发人员可以提取响应数据。 |                                                              |



**程序设计步骤讲解**

1. 前端的页面要设计好；
2. 设计数据库（数据库设计比较难）；
3. 前端让他能够自动运行，独立化工程；
4. 数据接口如何对接：json或者对象all in one；
5. 前后端联调测试。

## 2.2 开发步骤

1. 在浏览器内存中，创建一个脚本对象

该对象就是异步请求对象

```javascript
var num = 1;  //（脚本对象）       
var num = Ajax创建 //（异步请求对象)

//创建对象
var xmlHttp = new XmlHttpRequest();  
```

使用Ajax的方式创建一个异步请求对象：

```javascript
oBut.onclick = function (){
   if(window.XMLHttpRequest){
       var oAjax = newXMLHttpRequest();
	}else {
    	Var oAjax = new ActiveXObject(“Microsoft.XMLHTTP”); 。 
	}
}
```

​		`window.XMLHttpRequest`在IE6下虽说没有定义`“XMLHttpRequest”`，但是不会弹出错误，原因是浏览器会将其看作一个属性，而不会报错，弹出`“undefined”`，刚好是`“false”`。

2. 为异步请求对象添加“工作状态监听器”，帮助开发人员确认何时从异步请求对象身上得到服务端返回的响应数据。

```javascript
xmlHttp.onreadystatechange = function(){
}; 
```

+ `function`:处理函数，每当`xmlHttp`(异步请求对象)工作状态发生一次变化时，`function(){}`就被调用一次。

+ `function`函数里面放入标签，这也是开发的难点。

+ 获取数据以事件的形式存在，主要作用是接收从服务器端返回的数据，类似函数`“window.onload = function(){}`”,当页面加载完成后执行函数。

  同时使用Ajax方式为异步请求对象添加“工作状态监听器”：

```javascript
oAjax.onreadystatechange = function(){
    if((oAjax.readyState == 4) &&( oAjax.status == 200) ){
         alert(oAjax.responseText);表示从服务中读取的文本
	}
};
```

​		通过判断`“oAjax.readyState == 4”`来确定是否浏览器和服务器的交互过程进行到哪一步了，为“4”时，交互过程就完成了，即读取完成，`“oAjax.status == 200”`表示读取成功。

3. 初始化异步请求对象

主要作用为：

+ 通知异步请求对象采用何种方式（POST/GET）发送“请求协议包”;
+ 通知异步请求对象本次要访问的资源文件地址（URL）;
+ 通知异步请求对象在浏览器工作期间，浏览器是否会等他（同步请求/异步请求）；

```javascript
xmlHttp.open(“post/get”,“/myWeb/onServlet?uname =smith”，false)
```

​		上面语句主要作用是连接服务器。(发送方式，URL(上式为地址+数据),是否同步（false同步，绝大多数为true）);也有可能在地址上需要刷新缓存通过加上“t=+new Date().getTime()”时间戳来完成。

4. 命令异步请求对象代替浏览器发送请求。

```javascript
xmlHttp.send();//命令异步请求对象代替浏览器发送请求。
```

​	此时数据包就推送到服务器上了。

5. 数据回调处理

```javascript
xmlHttp.onreadystatechange = function(){
    //每当事件处理函数被调用时，都需要询问异步请求对象当前工作状态
    if(xmlHttp.readyState  == 4 && xmlHttp.Status == 200){ //状态码要正确
        var data = xmlHttp.responseText; //读取响应数据
        callback(data);
     }
};

//局部刷新实现函数
function callback(para){
	$.(“div”).text(para); //定位标签，对标签中的数据做修改。
}
```

​		从异步请求对象中得到响应数据，进行局部刷新。responseText:是属性（异步请求对象将解析好的数据放到该对象中），返回的是字符串。回调函数做刷新，我们来做（将上面的字符串用来更新标签）。

## 2.3 jQuery封装

体现思路：不需要开发人员亲自实现Ajax的四个步骤，只需要开发人员提供Ajax开发步骤中需要的参数，可以让开发人员专注局部刷新函数的实现$.ajax():是jQuery对Ajax开发提供的标准简化版。

**引入工程**

```html
<!--使用jquery需要先导入jquery的js文件。->
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
```

+ 引入jquery后代码自动提示功能的实现。
+ 并在写入后，将鼠标光标放于该URL中，按Alt+enter，选择载入该模块。在File -->Settings --> Languages&Frameworks --> JavaScript --> Libraries看是否加载成功。

### 2.3.1 $.ajax()

```javascript
var option = {
    type：// 赋值：type:’get/post’
    url:// 赋值：url:‘myweb/test’
     data:
    赋值：
    //方式1：
    data:”uname=mike&password=123”//将参数交给uname和password。
    //方式2：
    data:{
        “uname”:”mike”,
        “passwork”:”123”
    }
    async: //赋值：async:true(异步请求)	
    success://赋值：success: callback
    error:// 赋值：error:errorFun
    dataType://   赋值：dataType:‘text’(转化为脚本字符串)
    dataType:‘text’//(通过eval函数转化为json对象)
}
```

`ajax()`只接收一个`json`对象作为参数，开发人员可以将`Ajax`实现需要的参数在`json`对象中进行声明。

```ini
url ：本次要访问的资源文件地址，要求异步请求对象将该地址写入到请求协议包中的请求行上。

type：通知异步请求对象采用何种方式（POST/GET）发送请求协议包,其中type属性可以不声明，此时异步请求对象使用GET方式发送协议包，传入到open方法中。

data: 通知异步请求对象本次携带的请求参数。该属性可以接收以字符串形式拼接的参数，也可以接收以json格式描述的参数(针对参数较多的情况)。

success:指定异步请求对象工作完毕后，需要调用的“局部刷新函数名”,只能写局部刷新函数名，不要括号。

error:通知异步请求对象在接收到“404，405，500”等状态码时，需要调用的方法名，可写可不写。开发测试时会写，上线不需要写。

async:设置同步请求与异步请求。绝大多数情况下，都是使用异步请求，此时该属性不用声明。

dataType: （将服务器返回的数据转化丞指定类型）通知jQuery,将异步对象中读取到的数据转化为何种数据类型，然后将数据交给局部刷新函数callback。
	text：返回纯文本字符串。
	json：自动将返回的纯文本字符串进行了json.parse操作，如果parse失败，提示出错误信息。
	
contentType :即将发送信息到服务器的内容编码类型。
```

注： 

+ url不能省略，且不能携带请求参数。

+ dataType可以不声明，默认将异步对象中读取到的数据作为一个字符串输出到局部刷新函数callback。

###  2.3.2 $.get()与$.post()

$.get():通知异步请求对象用GET方式发送请求；$.post():通知异步请求对象用POST方式发送请求。

$.get()使用方法举例：

$.get(请求地址，请求参数，局部刷新函数名，需要的数据类型（text/json)）

* 如果只需要得到字符串类型数据，那么最后一个参数不用写。若还没有请求参数：$.get(请求地址，局部刷新函数名)。

## 2.4 创建项目

### 2.4.1 简单前端交互

1. **项目结构**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083547.png" alt="image-20200629094214937" style="zoom:80%;" />

1. **前端界面**

```jsp
  <script src="${pageContext.request.contextPath}/statics/js/jquery-3.4.1.min.js"></script>
    <script>
    // $(function () { //该函数不能加上，不然myFunction找不到
              function myFunction(){
                  $.ajax({
                    url:"${pageContext.request.contextPath}/a1",
                    data:{"name":$("#username").val()},
    // contentType: 'application/json; charset=UTF-8',（这种情况下不要加这个，不然后台接收不到数据）
                    success:function (data) {
                      console.log(data);
                    }
                  });
              }
          // });
    </script>
  </head>
  <body>
  <%--用户焦点失去的时候，发起一个请求，同时携带信息到后台--%>
    用户名：<input type="text" id="username" onblur="myFunction()" />
  </body>
</html>
```

我们要想静态资源通过过滤器，我们应该在springmvc-servlet.xml文件中假如下面的代码：

```xml
    <!--静态资源过滤-->
    <mvc:default-servlet-handler></mvc:default-servlet-handler>
```

2. **测试**

```java
@Controller
public class AjaxController {

    @RequestMapping("a1")
    @ResponseBody
    public String a1(@RequestParam String name){
        System.out.println("name-------->"+name);
        return "ok";
    }
}
```

+ 当前端界面的输入框焦点失去的时候，确实可以返回数据。

#### 2.4.1.1 返回List实体对象

1. **前端界面**

```jsp
 <head>
    <title>index界面</title>
    <script src="${pageContext.request.contextPath}/statics/js/jquery-3.4.1.min.js"></script>
    <script>
    $(function () {
          $('#btn').click(function () {
            $.ajax({
                url:"${pageContext.request.contextPath}/a2",
                //data: {  // 前端传数据的形式：http://localhost:8080/a2?uname=mike&passwork=123
                	//"uname":"mike",
                	//"passwork":"123"
                	//},
                dataType:'json',  //返回json对象
                success:function (data) {
                  console.log(data);
                  var html= "";
                  for (let i=0;i<data.length;i++){
                    //在这里追加数据
                    html += "<tr>"+
                            "<td>"+data[i].name + "</td>"+
                            "<td>"+data[i].sex + "</td>"+
                            "<td>"+data[i].age + "</td>"
                            +"</tr>"
                  }
                  //将前端页面进行渲染
                  $("#content").html(html);
                }
            });
          })
          });
    </script>
  </head>
  <body>
  <input type="button" value="加载数据" id="btn">
  <table>
    <tr>
      <td>姓名</td>
      <td>性别</td>
      <td>年龄</td>
    </tr>
    <tbody id="content">
          <%--使用后台传入的数据来填充--%>
    </tbody>
  </table>
  </body>
</html>
```

2. **后台代码**

```java
@RequestMapping("a2")
@ResponseBody
public List<User> a2(){
    List<User> userList = new ArrayList<User>();
    User user = new User("xiaolun",18,"男");
    User user2 = new User("xiaolun",18,"男");

    userList.add(user);
    userList.add(user2);
    return userList;
}
```

+ 控制台输出如下，前端的Ajax确实能够将后台数据带回来进行渲染。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083548.png" alt="image-20200629100312505" style="zoom:80%;" />

#### 2.4.1.2 验证用户名

**项目结构**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083549.png" alt="image-20200629102152972" style="zoom:80%;" />



1. **前端界面**

```jsp
<head>
    <title>index界面</title>
    <script src="${pageContext.request.contextPath}/statics/js/jquery-3.4.1.min.js"></script>
    <script>
        function a1() {
            $.ajax({
                url:"${pageContext.request.contextPath}/a3",
                data:{"name":$("#name").val()},
                success:function (data) {
                    console.log(data);
                    if (data.toString() === 'ok'){
                        $('#userInfo').css('color','green');
                    }else {
                        $('#userInfo').css('color','red');
                    }
                    $('#userInfo').html(data);
                }
            });
        }
    </script>
</head>
<body>
用户名：<input type="text" id="name" onblur="a1()">
<span id="userInfo"></span>
</body>
```

2. **后台代码**

```java
@RequestMapping("a3")
@ResponseBody
public String a3(@RequestParam String name){
    System.out.println("name-------->"+name);
    String msg = "";
    if (name != null){
        if ("admin".equals(name)){
            msg = "ok";
        }else {
            msg = "用户名有误";
        }
    }
    return msg;
}
```

+ 控制台输出

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083550.png" alt="image-20200629102345847" style="zoom:67%;" />

## 2.5 Ajax进行数据交互的方式总结

### 2.5.1 未引入layui模板情况

**方式1：后台的实体类接收前端发来的数据@RequestBody**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083551.png" alt="image-20200630155321607" style="zoom:67%;" />

1. 前端界面

```javascript
<!-- 引入jquery-->
<script type="text/javascript" src="/jquery-3.4.1.min.js"></script>
    <script>
        $(function () {
            var data = { //实体类要和其保持一致。
                "name":"test",
                "age":1
            };
            $('#btn1').click(function () {
                $.ajax({
                    type: 'POST',
                    url: '/test1',
                    data: JSON.stringify(data),    // 转化为字符串
                    contentType: 'application/json; charset=UTF-8',
                    dataType: 'text',  
                    success: function(data) {   // 这里的data就是json格式的数据
                        console.log("成功后返回数据------"+data)
                    },
                    error: function(data) {
                        console.log("失败后返回数据------"+data)
                    }
                });
            });
        });
    </script>
```

2. 实体类

```java
@Data //和前端封装的json数据要保持一致。
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private String name;
    private int age;
}
```

3. controller类

```java
@Controller
public class AjaxController {

    @RequestMapping("test1")
    @ResponseBody //@RequestBody主要用来接收前端传递给后端的json字符串中的数据的(请求体中的数据的。注意，要使用POST格式请求数据。
    public Object test1(@RequestBody User user){
//        return "ok";  //当设置dataType: 'text', 能够将数据正常返回到前端。

        return new User("xiaobai",12);//当设置dataType: 'json, 能够将数据正常返回到前端。
    }
}
```

+ 前端调试输出（POST方式的输出，携带参数）：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083552.png" alt="image-20200630155917160" style="zoom: 80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083553.png" alt="image-20200630155855900" style="zoom:80%;" />

**方式2：简单接收前端发来的数据@RequestParam**

对于前端发过来的单个数据参考2.4.1小节即可。

对于前端发过来的多个数据（推荐这一种）：

**前端代码**

```javascript
 var data = {
                "name":"test",
                "age":1
            };
            $('#btn1').click(function () {
                $.ajax({
                    type: 'post',
                    url: '/test1',
                    data: data,      // 不要使用JSON.stringify(data)
                    dataType: 'text',
                    success: function(data) {   // 这里的data就是json格式的数据
                        console.log("成功后返回数据------"+data)
                    },
```

**后端代码**

```java
@RequestMapping("test1")
@ResponseBody 
public Object test1(@RequestParam String age,@RequestParam String name){}
```

### 2.5.2 引入layui模板情况。

​		主要注意一下数据封装的格式就行了。

# 3 JSON数据

## 3.1 介绍

JSON(JavaScript Object Notation,Js对象标记)是一种轻量级的数据交互方式，完全独立于编程语言的文本格式来存储和表示数据。在JS语言中，一切皆是对象。因此，任何支持JS的类型都可以通过JSON来表示，例如字符串，数字，对象，数组等。

1. 语法

+ 数据放在名称/值对中
+ 数据由逗号分割
+ 花括号保存对象
+ 方括号保存数组

举例：{"name":"qinghua"}

其中，JSON的值可以为：

+ 数字（整数或浮点数）
+ 字符串（在双引号中）
+ 逻辑值（true或false）
+ 数组（在方括号中）
+ 对象（在花括号中）
+ Null

2. JSON是JS对象的字符串表示法，它使用一个文本表示一个JS对象的信息，本质是一个字符串。

```javascript
var obj = {a:'hello',b:'world'}#一个对象
var json = {"a":"hello","b":"world"}#一个Json字符串
```

3. JSON和JS的对象互转

+ JSON字符串转化为JS对象

```javascript
var obj = JSON.parse({"a":"hello","b":"world"}); #结果是{a:'hello',b:'world'}
```

+ JS转换为字符串

```javascript
var json = JSON.stringfy({a:'hello',b:'world'});#结果为{"a":"hello","b":"world"}
```

+ IDEA代码

```javascript
<script type="text/javascript">
    var user = { #对象
        name:"xiaolun",
        age:"3"
    };
    console.log(user);
    var json = JSON.stringify(user); #JSON字符串
    console.log(json);
    var obj = JSON.parse(json); #对象
    console.log(obj);
</script>
```

## 3.2 创建项目

### 3.2.1 创建实体类

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private String name;
    private int age;
    private String Sex;
}
```

### 3.2.2 创建controller类

1. 正常数据返回

```java
    @RequestMapping("/json1")
    @ResponseBody
    public String json1(){
        ObjectMapper mapper = new ObjectMapper();
        //创建一个对象
        User user = new User("xiaolun",3,"男");
        try {
            String str = mapper.writeValueAsString(user);
            return str; 
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }
        return  null;
}
//返回值简化版
return new ObjectMapper().writeValueAsString(new User("xiaolun",3,"男"));
```

+ `mapper`：这是一个jackson的对象映射器，就是一个类，使用它可以直接将对象转换为json字符串。
+ `writeValueAsString`：Java对象转化为`json`字符串。
+ 正常情况下，访问“/json1”地址时，数据会走视图解析器，但是`json`需要返回的是一个字符串，由于使用了 @ResponseBody注解，将`str`以`json`的格式返回。

2. 返回时间类型数据测试

```java
        时间测试
        @RequestMapping("/time1")
        @ResponseBody
        public String time1(){
            ObjectMapper mapper = new ObjectMapper();
            //关闭时间戳功能
            mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS,false);
            //自定义格式对象
            SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
            //让mapper指定时间日期
            mapper.setDateFormat(sdf);
            Date date = new Date(); //写一个日期对象
            try {
                return mapper.writeValueAsString(date);
            } catch (JsonProcessingException e) {
                e.printStackTrace();
            }
            return null;
  }
```

+ 由于我们需要大量的使用上面的代码，我们可以将上面内容封装成一个类进行使用。

```java
public class JsonUtils {

    //方法1
    public static String getJson(Object object){
        return getJson(object,"yyyy-MM-dd HH:mm:ss");
    }

    //方法2
    public static String getJson(Object object,String dataFormat){
        ObjectMapper mapper = new ObjectMapper();
        //关闭时间戳功能
        mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS,false);
        //自定义格式对象
        SimpleDateFormat sdf = new SimpleDateFormat(dataFormat);
        //让mapper指定时间日期
        mapper.setDateFormat(sdf);
        try {
            return mapper.writeValueAsString(object);
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }
        return null;
    }
```

现在时间类数据的返回代码书写为：

```java
@RequestMapping("/time1")
@ResponseBody
public String time1(){
    Date date = new Date(); //写一个日期对象
    try {
        return JsonUtils.getJson(date);
    } catch (Exception e) {
        e.printStackTrace();
    }
    return null;
}
```
