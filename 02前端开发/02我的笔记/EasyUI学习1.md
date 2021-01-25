### 1、Dialog使用

参考网址：

```http
http://www.jeasyui.com/documentation
```

项目结构

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201201162243183.png" alt="image-20201201162243183" style="zoom: 67%;" />

1、代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Welcome-test</title>
    <!--需要引入下面的结构，注意首先引入jquery，之后再引入jquery.easyui-->
    <script src="jquery-easyui-1.9.10/jquery.min.js"></script>
    <script src="jquery-easyui-1.9.10/jquery.easyui.min.js"></script>
    <link rel="stylesheet" href="jquery-easyui-1.9.10/themes/default/easyui.css">
    <link rel="stylesheet" href="jquery-easyui-1.9.10/themes/icon.css">
    <!--支持中文-->
    <script src="jquery-easyui-1.9.10/locale/easyui-lang-zh_CN.js"></script>
    <script>
        // 属性较少的时候使用class="easyui-dialog"方式去创建,否则使用下面的js方式去创建
        $(function () {
          	$("#dd").dialog();
            console.info("$测试")
        });
    </script>
</head>
<body>
<!--dialog-方式1-->
<div id="dd2" class="easyui-dialog" title="My Dialog" style="width:400px;height:200px;"
     data-options="iconCls:'icon-save',resizable:true,modal:true">Dialog Content.
</div>

<div id="dd">Dialog Content.</div>
</body>
</html>
```

1、`class="easyui-dialog"`表示使用easyui方式来初始化样式，也可以使用JavaScript的方式来使用easyui的样式。

```
$("#dd").dialog();
```

2、属性书写

```html
<!--标签中的属性也可以写到data-options中-->
1、resizable="true"
2、data-options="resizable:true"
3、$("#dd").dialog({
		resizable:true
		});
```



