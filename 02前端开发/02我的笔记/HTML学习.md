# 1 网页基本信息

1. 初始HTML

HTML(Hyper Text Markup Language) 即超文本标记语言，其中超文本包括：文字，图片，音频，视频，动画等。

2. W3C标准

+ 结构化标准语言 （HTML,XML）
+ 表现标准语言（CSS）
+ 行为标准（DOM,ECMAScript）

## 1.1 标签

1. <body></body>等成对出现的标签，叫做开放标签或闭合标签，单独呈现的空元素，如“<hr/>”，需要用“/”来关闭空元素。

```html
<!--DOCTYPE告诉浏览器，我们要使用什么规范-->
<!DOCTYPE html>
<html lang="en">
<!--head表示网页的头部-->
<head>
<!--meta描述型标签，用来描述网站的一些信息-->
    <meta charset="UTF-8">
    <meta name="keywords" content="狂神说，西部开源">
    <meta name="description" content="来这里学习吧">
    <title>Title</title>
</head>
<body>
<!--body表示网页的主体-->
</body>
</html>
```

### 1.1.1 基本标签

```html
<body>
        <!--01标题标签-->
        <h1>一级标签</h1>
        <h2>二级标签</h2>

        <!--02段落标签-->
        <p>跑的快，跑的快</p>
        <p>真奇怪</p>

        <!--03换行标签，间距很小-->
        跑的快，跑的快<br/>
        真奇怪<br/>

        <!--04水平线标签-->
        跑的快，跑的快<hr/>
        真奇怪

        <!--05样式标签-->
        <h1>字体样式标签</h1>
        粗体:<strong>i love you </strong>
        斜体:<em> i love you </em>
        <br/>

        <!--06特殊符号标签-->
        空&nbsp;格：
        大于号：&gt
        小于号：&lt
        版权符号：&copy;
</body>
```

### 1.1.2 图片标签

```html
<body>
<img src = "../src/resources/image/1.jpg" alt="科比" title="悬停文字"/>
</body>
```

+ src:图片地址，推荐相对地址，使用“../”找到上一级目录。
+ 原因未知，报出异常，找不到图片文件。

### 1.1.3 链接标签

```html
<body>
<!--顶部标签锚标记，使用name作为标记-->
<a name="top">顶部</a>
<a href="https://www.baidu.com" target="_blank">点击我跳转</a>
<a href="a01Test.html">点击我跳转</a>
<br/>
        <!--图像超链接-->
<a href="a01Test.html">
  <img src = "../src/resources/image/1.jpg" alt="科比" title="悬停文字"/>
</a>
    
<a href="#top">回到顶部</a>

<a href="mailto:3493826518@qq.com">点击联系我</a>
</body>
```

+ href:表示要跳转的界面，target:表示窗口在哪里打开，其中，属性 `_blank`表示在新标签打开网页
      `_self`表示在自己的网页中打开。
+ 可以构建图像超链接，点击图像进行界面的跳转。
+ 锚链接，使用“#标记”，需要一个锚标记和跳转的地址，<`a name="top">顶部</a>`中的`top`b表示一个标记，点击链接，可以跳转到相应界面。
+ 功能性链接：使用`mailto`属性进行跳转。

### 1.1.4 行内元素和块元素

块元素是指无论内容多少，该元素独占一行。（p,h1-h6）

内容撑开宽度，左右都是行内元素的排在一行。（a，strong,em）

### 1.1.5 列表标签

列表是信息资源的一种展示方式，它可以是信息结构化和条理化，并以列表的形式展示出来，以便浏览者能够快读的获取相应信息。

列表的分类：

+ 无序列表
+ 有序列表
+ 自定义列表

```html
<body>
<!--有序列表-->
<ol>
    <li>Java</li>
    <li>后台</li>
    <li>前端</li>
</ol>
<hr/>
<!--无序列表
应用：导航，侧边栏
-->
<ul>
    <li>Java</li>
    <li>后台</li>
    <li>前端</li>
</ul>

<!--自定义列表
<dl>：标签
<dt>：列表标题
<dd>:列表内容
应用范围：网站底部
-->
<hr/>
<dl>
    <dt>科目</dt>
    <dd>数学</dd>
    <dd>语文</dd>
</dl>
</body>
```

### 1.1.6 表格标签

+ 特点是简单通用，结构稳定。
+ 基本结构
  + 单元格
  + 行
  + 列
  + 跨行
  + 跨列

```html
<body>
<!--表格table
行：tr:table rows
列：td:
-->
<table border="1px">
    <tr>
        <!--  colspan : 跨列-->
        <td colspan="3">java</td>
    </tr>
    <tr>
        <!--rowspan:跨行-->
        <td rowspan="2">java</td>
<!--        <td>pathon</td>-->
<!--        <td>c语言</td>-->
    </tr>
    <tr>
        <td>java</td>
        <td>pathon</td>
        <td>c语言</td>
    </tr>
</table>
```

### 1.1.7  媒体元素

+ 视频元素 video
+ 音频元素 audio

```html
<body>
<!--音频和视频
controls：控制条
autoplay:自动播放
-->
<video src="../src/resources/video/..mp4" controls autoplay></video>
<audio src="../src/resources/video/..mp3" controls autoplay></audio>
</body>
```

### 1.1.8 网页结构分析

| 元素名  | 描述                                                 |
| :-----: | ---------------------------------------------------- |
| header  | 标题头部区域的内容（用于页面或页面中的一块区域）     |
| footer  | 标记脚部区域的内容（用于整个页面或页面中的一块区域） |
| section | Web页面中的一块独立区域                              |
| article | 独立的文章内容                                       |
|  aside  | 相关内容或应用（常用于侧边栏）                       |
|   nav   | 导航类辅助栏                                         |

```html
<body>
<header>
    <h2>网页头部</h2>
</header>

<section>
    <h4>网页主体</h4>
</section>
<footer>
    <h2>网页脚部</h2>
</footer>
</body>
```

### 1.1.9 iframe内联框架

```html
<body>
<iframe src="" frameborder="0"  width="1000px" height="800px"></iframe>
<a href="a02basicLabel.html">点击跳转</a>
</body>
```

+ `src`:引用页面地址。
+ `name`：框架标识名。
+ 通过"`<a>`"标签，可以向"`<iframe>`"标签中添加东西，也可以在"`<iframe>`"中`src`属性中添加地址。

### 1.1.10 表单

1. 创建简单项目

```html
<body>
    <h1>注册</h1>
    <form action="a02basicLabel.html" method="get">
        <p>名字: <input type="text" name="username"></p>
        <p>密码: <input type="password" name="pwd"></p>
        <input type="submit">
        <input type="reset">
    </form>
</body>
```

+ `action`:表单提交的位置,可以是网站,也可以是一个请求处理地址。

+ `method:post,get`提交方式。

  + `get`在提交的方式中可以看到我们的`url`，不安全,但是高效。

    网址为：(http://localhost:63342/HTML-study/a02basicLabel.html?username=xiaolun&pwd=123),
    其中,url中的"username=xiaolun&pwd=123"分别和 `"<input>"`标签中的`name="username"和name="pwd"`对应。

  + post:比较安全,传输大文件。

+ 文本输入框:input type="text"。

+ name="username"给input标签起一个名字，属性会在会在url中看到 。

+ 密码框:type="password"。

+ 提交:type="submit"
  。

+ 重置:type="reset"
  。

2. 表单元素格式

|   属性    | 描述                                                         |
| :-------: | ------------------------------------------------------------ |
|   type    | 指定元素类型。text,password,checkbox,radio,submit,reset,file,hidden,image和button,默认为text。 |
|   name    | 指定表单元素的名称（提交表单时，必填，传输数据时，会附带表单属性）。 |
|   value   | 元素的初始值。type为radio时，必须指定一个值。                |
|   size    | 指定表单元素的初始宽度。当type为text或passeord时，表单元素的大小以字符为单位。对于其他类型，宽度以像素为单位。 |
| maxlength | type为text或password时，输入的最大字符数。                   |
|  checked  | type为radio或checkbox时，指定按钮是否被选中。                |

#### 1.1.10.1 单选框按钮

```html
<p>性别
    <input type="radio" value="boy" name="sex"/>男
    <input type="radio" value="girl" name="sex"/>女
</p>
```

+ `value="boy"`:单选框的值（其中标签外界的"男"，只是起到修饰一下`value`的作用），取值的时候，只能取到“boy”这个值，不能取到“男”这个值。
+ `name="sex"`:名字相同表示一个组，在表单提交时，url（..&sex=boy）中会将单选框中的值给带走。

#### 1.1.10.2 复选框按钮

```html
 <p>爱好：
     <input type="checkbox" value="sleep" name="hobby"/>睡觉
     <input type="checkbox" value="code" name="hobby"/>敲代码
     <input type="checkbox" value="chat" name="hobby"/>聊天
 </p>
```

+ 提交表单时，url为“`&hobby=sleep&hobby=code`”，可以将表单中的复选框中的数据带走。

#### 1.1.10.3 按钮

```html
<p>按钮：
    <input type="button" name="btn1" value="点击变长">
    <!--图片按钮,点击后和submit同-->
    <input type="image" src="../src/resources/image/1.jpg">
</p>
```

+ value：表示按钮上面的字。
+ 按钮类型
  + type="button" ：普通按钮
  + type="image"  ：图像按钮（点击后和submit同，可以提交表单。）
  + type="submit"  ：提交按钮
  + type="reset"      ：重置按钮

#### 1.1.10.4 下拉框

```html
<p>下拉框：
    <select name="列表名称" id="">
        <option value="china">中国</option>
        <option value="US" selected>美国</option>
        <option value="eth">瑞士</option>
    </select>
</p>
```

+  selected:默认选中
  。
+ 提交表单时，URL中有：`&`列表名称=US`
  `    。

#### 1.1.10.5 文本域和文件域

```html
<p>反馈（文本域）：
    <textarea name="textarea" cols="10" rows="10">文本内容</textarea>
</p>

<p>文件域：
    <input type="file" name="files"/>
    <input type="button" value="上传" name="upload"/>
</p>
```

+ 文本域中：cols="10" rows="10"表示行和列，提交表单时，URL包含“`&textarea=wenben`”将文本域内容提交出去。
+ 文件域中，提交表单时，URL包含`&files=大.jpg`。

#### 1.1.10.6 滑块和搜索框

```html
<!-- 滑块 -->
<p>音量：
   <input type="range" name="voice" min="0" max="100" step="1"/>
</p>

 <!-- 搜索框-->
<p>搜索：
    <input type="search" name="search" />
</p>
```

#### 1.1.10.7 表单的应用

1. 只读

```html
<p>名字: <input type="text" name="username" value="admin" readonly></p>
```

+ 会发现使用`readonly`,输入框中的名字就改不动了。

2. 隐藏域

```html
<p>密码: <input type="password" name="pwd" value="123" hidden></p>
```

+ `hidden`的使用，会发现密码框找不到了，但是属性值还是存在，可以使用它来传递一些默认值。

3. 禁用

```html
 <p>性别
    <input type="radio" value="boy" name="sex" checked disabled/>男
    <input type="radio" value="girl" name="sex"/>女
</p>
```

+ `disabled`使用后，该标签就不能选择，颜色变成灰色。

4. 增强鼠标可用性

```html
<p>
    <label for="mark">你点我试试</label>
    <input type="text" id="mark"/>
</p>
```

+ 在网页中当我们点击"你点我试试"字样时会选中单选框文本域`text`。
+ `for`的值是另一个标签的id值。

#### 1.1.10.8 表单初级验证

1. placeholder提示信息

```html
<p>名字: <input type="text" name="username" placeholder="请输入用户名"></p>
```

+ 用在输入的标签中。

2. required 非空判断

```html
<p>名字: <input type="text" name="username" placeholder="请输入用户名" required></p>
```

3. patten 正则表达式

```html
  <!-- 正则判断-->
<p>自定义邮箱：
 	<input type="text"name="diymail" pattern="^\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$">
</p>
```















