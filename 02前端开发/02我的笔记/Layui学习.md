# 1 使用简介

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">
  <title>开始使用layui</title>
  <link rel="stylesheet" href="../layui/css/layui.css">
</head>
<body>
 
<!-- 你的HTML代码 -->
 
<script src="../layui/layui.js"></script>
<script>
//一般直接写在一个js文件中
layui.use(['layer', 'form'], function(){
  var layer = layui.layer
  ,form = layui.form;
  
  layer.msg('Hello World');
});
</script> 
</body>
</html>
```

+ 首先引入样式及核心文件：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130130.png" alt="image-20200618113412358" style="zoom:67%;" />

```html
<!--layui/css/layui.css表示 Layui中内置样式-->
<link rel="stylesheet" href="/layui/css/layui.css">
在<body></body>标签上导入：
<!--layui/layui.js”表示Layui中核心的js插件（模块化使用）一般项目开发中使用这个插件-->
<script src="/layui/layui.all.js"></script>
<script>
```

`layui.use(['layer', 'form'], function(){}`中`layui.use()`等同于JS中的`window.onload = function(){}`，即页面加载完成后再执行。

同时使用`['layer', jquery]`来进行模块化编程，其中括号内为要初始的模块，引入相应的模块。具体的业务代码要在`function(){}`中写入。

+ 一般引入`JS`，在`< body>`的最后面引入，因为代码是自上而下执行的，这样可以等页面的`DOM`加载完成，避免不生效和异常问题。
+ 需要将`html`文件放到`static`目录下，通过l`ocalhost:8080/xxx.html`来访问，在`templates`中需要引入`thymeleaf`依赖，不能使用`@ResponseBody`。
+ `Layui`需要服务器开启才生效，不然会报出无法解析文件的错误，而JS直接导入即可。

## 1.1 引入Thymeleaf

```javascript
<html lang="en" xmlns:th="http://www.thymeleaf.org">

<link th:href="@{/layui/css/layui.css}" rel="stylesheet" />
<script th:src="@{/layui/layui.js}"></script>
```

# 2 图表

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Layui图表测试</title>
    <link rel="stylesheet" href="/layui/css/layui.css">
</head>
<body>
    <!--layui-icon是一个内联元素，通过该元素可以对一个图标进行定义和对应；&#xe7e0表示一个字符实体，即是图标对应的 unicode 字符。-->
    <div class="layui-icon">&#xe7e0;</div>
<script src="/layui/layui.js"></script>
</body>
</html>
```

# 3 内置动画

```html
<meta charset="utf-8">
<link rel="stylesheet" href="/layui/css/layui.css">
</head>
<body>
    <!--layui-anim：表示动画类；layui-anim-rotate表示具体的动画。-->
<div class="layui-icon layui-icon-face-smile layui-anim layui-anim-rotate" style="font-size: 30px; color: #ff2839;"></div>
<script src="/layui/layui.js"></script>
<script>  
</script>
</body>
```

+ 在< /head>文件中不要添加一些其他的文件，不然动画效果会显示不出来。
+ 要添加一个笑脸图标，然后进行动画效果的演示，在没有加任何东西的情况下，动画效果不会显示。

# 4 按钮

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831130131.png" alt="image-20200618114105855" style="zoom:80%;" />

+ `class="layui-btn"`，建立一个基础按钮。通过追加格式为`layui-btn-{type}`的`class`来定义其它按钮风格，即将`button`和`i`标签组合组成字体图标。`layui-icon-rate-solid`为图标标志。

```html
<button type="button" class="layui-btn">
    <i class="layui-icon layui-icon-rate-solid">&#xe608;</i> 增加</button>
```

# 5 导航

## 5.1 水平导航

```html
<!--测试1：水平导航（layui-nav）-->
<ul class="layui-nav" lay-filter="">
    <li class="layui-nav-item"><a href="">最新活动</a></li>
    <!--layui-this：当前页面被选中的项-->
    <li class="layui-nav-item layui-this"><a href="">产品</a></li>
    <!--导航项中的具体项-->
    <li class="layui-nav-item"><a href="">大数据</a></li>
    <li class="layui-nav-item">
        <a href="javascript:;">解决方案</a>
        <!--导航项中的具体项-->
        <dl class="layui-nav-child"> <!-- 二级菜单 -->
            <dd><a href="">移动模块</a></dd>
            <dd><a href="">后台模版</a></dd>
            <dd><a href="">电商平台</a></dd>
        </dl>
    </li>
    <li class="layui-nav-item"><a href="">社区</a></li>
</ul>
```

+ 通过点击相应的选项卡，它对应的索引值会跟着改变，并且将索引值结合“layui-this”传递给对应的内容，以此来实现相应的选项卡和内容对应。

## 5.2 垂直导航

```html
<ul class="layui-nav layui-nav-tree layui-nav-side" lay-filter="test">
    <li class="layui-nav-item layui-nav-itemed">
        <a href="javascript:;">默认展开</a>
        <dl class="layui-nav-child">
            <dd><a href="javascript:;">选项1</a></dd>
            <dd><a href="javascript:;">选项2</a></dd>
            <dd><a href="">跳转</a></dd>
        </dl>
    </li>
    <li class="layui-nav-item">
        <a href="javascript:;">解决方案</a>
        <dl class="layui-nav-child">
            <dd><a href="">移动模块</a></dd>
            <dd><a href="">后台模版</a></dd>
            <dd><a href="">电商平台</a></dd>
        </dl>
    </li>
    <li class="layui-nav-item"><a href="">产品</a></li>
    <li class="layui-nav-item"><a href="">大数据</a></li>
</ul>
```

+ 导航依赖 `element` 模块，否则无法进行功能性操作。

# 6 Tab选项卡

```html
<div class="layui-tab"  lay-allowClose="true" lay-filter = "myTab">
    <ul class="layui-tab-title">
        <li class="layui-this">网站设置</li>
        <li>用户管理</li>
        <li>权限分配</li>
        <li>商品管理</li>
        <li>订单管理</li>
    </ul>
    <div class="layui-tab-content">
        <div class="layui-tab-item layui-show">内容1</div>
        <div class="layui-tab-item">内容2</div>
        <div class="layui-tab-item">内容3</div>
        <div class="layui-tab-item">内容4</div>
        <div class="layui-tab-item">内容5</div>
    </div>
</div>
```

# 7 进度条

```html
<div lay-filter = "myProgress" class="layui-progress layui-progress-big" lay-showPercent="true">
    <!--ay-showPercent="true"是否显示进度值-->
    <div  class="layui-progress-bar" lay-percent="10%"></div>
</div>
<button id="addProg">进度条增加</button>

<script src="/layui/layui.js"></script>
<script>
    layui.use(['element','jquery'],function(){
        var element = layui.element;
        var $ = layui.jquery;
        $("#addProg").click(function () {
      <!--示更新进度条进度，其中lay-filter不要写错， “value”指更新进度条的进度的值。在这里，我们可以设置一个定时器进行更新操作。-->      element.progress('myProgress','50%');
        });
    });
```

# 8 面板

面板是指一个独立的容器，可以用来装元素。

# 9 时间线

# 10 表单

```html
<!--03复选框-->
        <div class="layui-form-item">
            <label class="layui-form-label">复选框</label>
            <div class="layui-input-block">
                <input type="checkbox" name="like[read]" title="阅读" checked>
                <!--开关风格-->
                <input type="checkbox" name="switch" lay-text="ON|OFF" lay-skin="switch">
            </div>
        </div>
```

```html
<!--04下拉框-->
        <div class="layui-form-item">
            <label class="layui-form-label">下拉框</label>
            <div class="layui-input-block">
                <select name="city" lay-verify="" lay-search>
                    <option value="010">layer</option>
                    <option value="021">form</option>
                    <option value="0571" selected>layim</option>
                </select>
            </div>
        </div>
        <!--提交按钮，位置有点诡异-->
        <button class="layui-btn" lay-submit></button>
    </form>
```

```ini
1、通用样式：
1)	Layui不仅仅为表单提供了一套内置的样式，也为表单提供了一系列的操作方法。
2)	class="layui-form"表示该容器使用表单样式，是表单中具体样式的基础（注，前面为<form>不能移除）。
3)	class="layui-form-label"表示表单项中的label标签，文本说明。
4)	class="layui-input-block"表示input容器，block块元素（占一行）。
5)	class="layui-input-inline"表示input容器，inline表示行内块，不占一行。
6)	class="layui-input"：表示一个input标签。
7)	class=”layui-inline”;表示不占一行，一般用于一项中存在多个form元素，使用该class进行包裹。
```

+ `return false`阻止表单提交，因为表单提交后会刷新界面，一般使用`Ajax`提交表单数据，然后阻止表单提交。只有使用`layui`进行表单提交才能触发数据校验。即使用`layui`的表单提交事件，在事件中使用`Ajax`提交数据，然后阻止表单提交。

## 10.1 layui中下拉框的理解

应用场景，当我们操作表格中的编辑页面时，跳到编辑的表单页面，在表单页面html中，有一个关于工步类型的下拉框，（表格 ----> 表单）包含三个value参数:

```html
  <!--为工步类型增加下拉框操作-->
<div class="layui-input-inline">
    <select id="workstepTypeEcho" lay-filter="workstepType" name="workstepType" lay-search="">
        <option value="">请选择：</option>
        <option value="1">生产工步</option>
        <option value="2">检测工步</option>
    </select>
</div>
```

在表格页面中的工步类型数据，我希望回显到**表单**页面中,也就是对下拉框数值进行赋值。使用的操作为：

```javascript
  function selectShow(valueNumSelect) {
            console.log("valueNumSelect-------->"+ valueNumSelect);
            if (valueNumSelect == 0){
                console.log("执行了一次生产工步");
                // 使用下面的赋值方法，报错： $("#workstepTypeEcho").find("option[value='1']").attr("selected",true);
                // form.render("select");
               //$('#id').val(option value的值)
                $("#workstepTypeEcho").val(1);
            }else {
                $("#workstepTypeEcho").val(2);
            }
        }
```

其中，`valueNumSelect`是使用ajax方法`url:"/workstep/detail"`查询后带回来的数据，注意：

```javascript
 // "workstepType": workstep.workstepType,   //现在该输入框的类型是一个下拉框，就不让其将数据进行回显了。
```

在数据进行回显之后，接下来就是表单数据的提交，对于下拉框中的数据进行提交到后台，我们采取这样的方式：

```javascript
 form.on('submit(workstep-detail-form-submit)', function (data) {
     //data.field.workstepType 拿取得是下拉框中数据编号value对应的值，但是拿到的是String数据，需要将其转化为Int类型的数据。
     //但是数据库中的数据0/1，和下拉框中的value值1/2,相差1，我们需要进行减去1。
     data.field.workstepType = Number(data.field.workstepType) - 1;

     $.ajax({
         type: "POST",
         data:JSON.stringify(data.field),
         contentType: "application/json;charset=UTF-8",
         url: 'workstep/update',
```

+ 后台传过来的是数据0/1，但前台中展示的是“生产工步/检测工步”，这主要通过前台来实现的，方法如下，templet的方式：

```html
{field: 'keyworkstepMark', title: '关键工步标识', width: 150,sort:true,
    templet:function(d) {
        if(d.keyworkstepMark == "0"){return "<div>非关键工步</div>" }
        if(d.keyworkstepMark == "1"){return "<div style='color: red'>关键工步</div>"}
}
<!-- tomplet函数转义
style='color: red' 这个属性改变了字体颜色。
-->
```

## 10.1 更新渲染

全局渲染，`form.render(type,filter)`:重新渲染表单，也可以指定类型，并且可以在同类型中根据lay-filter进行区分。

部分渲染，`form.render('select', 'test2');` //更新 lay-filter="test2" 所在容器内的全部 select 状态。

# 11 表格

`class="layui-table"`表示表格的样式，表格分为静态表格和动态表格。静态表格是指使用HTML写的页面固定数据和标签等，动态表格是使用JS自动渲染出来的表格，根据数据以及特定的属性渲染出相应的表格。

## 11.1 数据导出

1. 导出当前表格页面的数据。

```javascript
//表单导出点击事件
$('#exportBtn').click(function () {

    var  tableExport  = table.render({  //进入该界面就开始渲染界面，接收后台的数据
        elem: '#demo'
        ,title:'tableTest'
        , url: '/list' //数据接口
        , page: true //开启分页
        , cols: [[ //表头
            {field: 'id', title: 'ID', sort: true, fixed: 'left'}
            , {field: 'orderNo', title: '订单编号'}
            , {field: 'ordername', title: '订单名称', sort: true}
            , {field: 'ordertype', title: '订单类型'}
            , {field: 'orderstatus', title: '订单状态'}
            , {fixed: 'right', width: 200, align: 'center', toolbar: '#barDemo'}
        ]],
        done: function (res, curr, count) { //表格数据格式测试
            console.log(res.data);
            //['名字','性别','年龄']
            table.exportFile(tableExport.config.id, res.data,'xls');
            // table.exportFile(['订单编号','订单名称','订单类型'], res.data,'xls'); // 失效
        }
    });
```

2. 导出全部的表格数据。



# 12 文件上传

1. 在新建工步的时候文件上传

```html
<!--文件上传按钮-->
<button type="button" class="layui-btn layui-btn-sm" id="isAdd_uploadFileId">
    <i class="layui-icon">&#xe67c;</i>上传文件</button>
<button type="button" class="layui-btn layui-btn-sm" id="add_uploadFileId" style="display: none">
    <i class="layui-icon">&#xe67c;</i>上传文件</button>
```

```javascript
//是否进行文件上传
$('#isAdd_uploadFileId').click(function () {
    //add_fileUploadId(undefined)判断相等时，不要带引号。
    console.log("add_fileUploadId === 'undefined'-------"+(add_fileUploadId === undefined));
    if($('#addWorkstepNum').val() === '' || add_fileUploadId === undefined){
        layer.msg("上传文件失败，请先保存工步");
    }else{
        $('#add_uploadFileId').click();  //点击后上传
    }
});

upload.render({
    elem: '#add_uploadFileId'
    ,url: 'workstep/upload'
    ,data: { //在url中添加上传的id数据。
        WorkstepId: function () {
            return add_fileUploadId; //定义的全局变量，获取工具行点击事件中的记录行id。
        }
    }
    ,accept: 'file' //普通文件
    ,exts: 'jpg|png|gif|bmp|jpeg|pdf|txt|xlsx|docx'
    ,done: function(res) {
        layer.msg('上传成功');
        console.log(res);
    }
});
```

首先判断一下工步是否保存，如果保存了的话后台就已经有了该工步Id，在此基础上，我们就可以新建文件，传到后台。

