# 1 初探JavaScript

## 1.1 第一个JS特效-鼠标提示框

```javascript
<body>
    <input type="checkbox" onmouseover="document.getElementById('div1').style.display='block'" onmouseout="document.getElementById('div1').style.display='none'">
    <div id="div1">
        为了你的信息安全。。。。
    </div>
</body>
```

+ `document.getElementById('div1').style.display`：通过`document.getElementById('div1')`，可以获取`"<div>"`标签坐标以便进行操作，同时也考虑到兼容性的影响。 

+ “`onmouseover`”表示鼠标移入时的事件，“'`block`'”表示：此元素将显示为块级元素，此元素前后会带有换行符，“`none`”表示：此元素不会被显示。  

+ 改的是`"<div>"`标签的样式。 

+  特效基础：响应用户操作，对页面元素（标签）进行修改。  

    元素属性操作：`obj.style.[….]`

+   编写JS流程流程：  

  + 布局：HTML+CSS；
  + 属性：确定要修改的那些属性；
  + 事件：确定用户做那些操作；  
  + 编写JS：在事件中，用JS来修改页面元素的样式。

### 1.1.1 函数

直接在事件内写代码会很乱，我们引入function()，可以把JS从标签里放到函数里，类似CSS里的class，变量我们可以使用“别名”来代替。

1. 定义和调用：

函数定义：只是告诉系统又这个函数，不会立即执行。

函数调用：真正执行函数里的代码。

```javascript
<head>
    <meta charset="UTF-8">
    <title>JS文件学习1</title>
    <script>
        function toRed() {
            var oDiv = document.getElementById('div1');
            oDiv.style.width = '300px';
            oDiv.style.background = 'red';
        }
    </script>
</head>
<body>
<div id="div1" onmouseover="toGreen()" onmouseout="toRed()">
    你好。。。
</div>
</body>
```

+ `var oDiv = ***document\***.getElementById('div1')`;通过使用变量的方式，可以减少大量的代码复用。此时，只需要将函数名“`toRed()`”传到标签汇中引用的位置即可。

+ 可以看到`HTML`文件中共怎么写代码，在函数里中的变量也就怎么写代即可。

+ 注：

  假如我们要修改`div`中的`class`属性，我们这样写oDiv.class不正确，需要改成`oDiv.className,`因为`class`是JS中的一个关键字（这是一个例外）。

  `class`和`id`一样是个选择器而已，通过定义样式后，被`html`的标签所调用来实现页面的排版、布局和样式。

2. 函数传参

```javascript
<head> 
<script>
        function setColor(color){
            var oDiv = document.getElementById("div1");
            oDiv.style.background = color;
        }
    </script>
</head>
<body>
    <input type="button" value="变绿" onclick="setColor('green')">
    <input type="button" value="变黄" onclick="setColor('yellow')">
    <input type="button" value="变黑" onclick="setColor('blue')">
</body>
<div id = "div1">
    为了你的信息安全
</div>
</html>
```

+ `setColor('green')`在这里要使用单引号；**`*document\***.getElementById("div1")`;单引号双引号都可以。
+ 函数传参进行不同颜色的改变。

+ 更改属性的第二种方式：

```javascript
<input type="button" value="变绿" onclick="setStyle('width','400px')">
函数变为：
function setStyle(name,value){
    var oDiv = document.getElementById("div1");
    oDiv.style[name] = value;
}
```

+  `oDiv.style[‘width’] = oDiv.style.width`,该方法在修改的样式固定的时候。即将属性名作为参数传递。

### 1.1.2 If判断

```javascript
<head>
    <meta charset="UTF-8">
    <title>JS文件学习1</title>
    <script>
        function showHide() {
            var oDiv = document.getElementById('div1');
            if (oDiv.style.display == 'block'){
                oDiv.style.display ='none'
            }else{
                oDiv.style.display = 'block'
            }
        }
    </script>
</head>
<body>
    <input type="button" value="显示/隐藏" onclick="showHide()">
    <div id="div1">
        为了你的信息安全。。。。
    </div>
```

+ `oDiv.style.display == 'block'`：使用if判断语句，进行选择操作。

1. 为a链接添加JS

```javascript
<a href="javascript:;">链接</a>
```

+ <a> 标签定义超链接，用于从一张页面链接到另一张页面。<a> 元素最重要的属性是 href 属性，它指示链接的目标。

+ `href="javascript:;"`：放置一个空的JS语句，不会发生页面跳转。

+ `style`与`className`

  元素.style.属性 = xxx，修改的是行间样式（行间样式就是写在标签体内的样式：如，`<div style="color:red"></div>`，在这里`color`样式就是行间样式。），之后再修改`className`不会有效果。因为style优先级比`className`高。

2. 提取行间事件

为元素添加事件，事件和其他属性一样，可以用JS添加

```javascript
<body>
    <input id="but1" type="button" value="按钮" >
    
    <script>
        var oBut = document.getElementById("but1");
        function onClickTest(){
            alert('test')
        }
        oBut.onclick = onClickTest;
    </script>
</body>
```

+ `oBut.onclick = onClickTest`;在函数里面也可以写事件“`onClick`”,此时该事件进行赋值的应该是函数的名字。

+ 也可以不为函数`function`写名字“`onClickTest`()”，而使用匿名函数即：

  `o`But.onclick = function (){`
     `alert('test')`
    }`;

  JS里面大部分函数都是没有名字的。

+ ” `<script>….</script>”`此时应该写在`"<input></input>"`后面不然会报错,” `<script>….</script>”`方法<head>标签里,`"<input></input>"`放到<body>标签里时，会报错，因为`document.getElementById("but1");`里面的“but1”没有加载出来。

+ 可以这样写：

```javascript
<script>
    window.onload = function () {
        var oBut = document.getElementById("but1");
        //方法2：匿名函数
        oBut.onclick = function (){
            alert('test')
        };
    };
</script>
```

+ `window.onload=function(){}`表示当页面加载完成后才执行函数里面的代码。

### 1.1.3 获取一组元素

```javascript
<head>
    <meta charset="UTF-8">
    <title>提取事件-复选框</title>
    <script>
        window.onload = function () {
            var oBut = document.getElementById("but1");//获取标签
            var oBut2 = document.getElementById("but2");//获取标签
            var oBut3 = document.getElementById("but3");//获取标签
            var div1 = document.getElementById("div1")
            var oCh = div1.getElementsByTagName("input");

            oBut.onclick = function () {
                for (var i = 0;i < oCh.length;i ++){
                    oCh[i].checked = true;
                }
            };
            oBut2.onclick = function () {
                for (var i = 0;i < oCh.length;i ++){
                    oCh[i].checked = false;
                }
            };
            oBut3.onclick = function () {
                for (var i = 0;i < oCh.length;i ++){
                    if ( oCh[i].checked == true){
                        oCh[i].checked = false;
                    }else{
                        oCh[i].checked = true;
                    }
                }
            };
        };
    </script>
</head>
<body>
    <input id="but1" type="button" value="全选"><br>
    <input id="but2" type="button" value="全不选"><br>
    <input id="but3" type="button" value="反选"><br>
    <div id="div1">
        <input type="checkbox" ><br>
        <input type="checkbox"><br>
        <input type="checkbox"><br>
        <input type="checkbox"><br>
        <input type="checkbox"><br>
        <input type="checkbox"><br>
        <input type="checkbox"><br>
    </div>
</body>
```

+ `getElementsByTagName()`：该标签可以获取一组相同标签的元素，进而构成一个数组，使用for循环遍历的方式可以为数组进行赋值。
+  `<div id="div1">`该标签的含义是将类型“checkbox” 的元素进行嵌套到里面，然后通过“`var oCh = div1.getElementsByTagName("input")`;”获取“`checkbox`”，不然的话，“全选”框也会被选上。 

### 1.1.4 选项卡

```javascript
<head>
    <meta charset="UTF-8">
    <title>提取事件</title>
    <style>
        #div1 .active{background: yellow;} <!--定义一个active类，在这个类中设置一些样式：-->
        #div1 div {width: 200px;height: 200px;background: #CCC;border:1px solid #999;display：none;}
    </style>
    <script>
        window.onload = function () {
            var oDiv = document.getElementById("div1");
            var oBut = oDiv.getElementsByTagName("input");
            var aDiv = oDiv.getElementsByTagName("div");

            //先让所有的都隐藏，然后将当前的显示出来
            for (var i = 0;i < oBut.length;i ++){
                oBut[i].index = i;  //让按钮和div对应上，而添加的索引
                oBut[i].onclick = function () {
                    //this表示当前发生事件的元素
                    // alert(this.value);
                    for (var i = 0;i < oBut.length;i ++){
                        oBut[i].className ='';
                        aDiv[i].style.display = 'none'; //让当前div显示出来
                    }
                    this.className = 'active';
                    aDiv[this.index].style.display = 'block';
                };
            }
        };
    </script>
</head>
<body>
    <div id="div1">
        <input class="active" type="button" value="教育">
        <input type="button" value="培训">
        <input type="button" value="招生">
        <input type="button" value="出国">
        <div style="display: block;">1111</div>
        <div style="display: none;">2222</div>
        <div style="display: none;">3333</div>
        <div style="display: none;">4444</div>
    </div>
</body>
```

+ `style`中的“`display：none`;”在浏览器上加载失效，原因未知。
+ `oBut[i].index = i`; 为了让按钮和”div”对应上，而添加的索引,这样当点击按钮的时候，相应的`“div”“aDiv[this.index].style.display = 'block'`;”会显示对应的内容。
+ “`this`” 表示当前发生事件的元素,比如当点击按钮时，按键就是当前事件的元素。
+ 为了让当前`div`显示出来，我们需要将当前显示的所有元素进行隐藏。
+ “`active`”对应上面的`“#div1 .active`”，当点击按钮时``(this.className = 'active';`
   )`按钮背景颜色会变黄。

### 1.1.5 简易年历

```javascript
<head>
    <meta charset="UTF-8">
    <title>提取事件</title>
    <script>
        window.onload = function () {
            //将要显示的数据放到数组中
            var arr= ['快过年了，大家商量去哪去玩啊',
                        '二月来了',
                        '三月来了',
                        '四月来了'];
            var oDiv = document.getElementById("tab");
            var aLi = oDiv.getElementsByTagName("li");
            var oTxt = oDiv.getElementsByTagName("div")[0]; //要加上[0],不然没有显示
            for (var i = 0;i < aLi.length;i ++){
                aLi[i].index = (i + 1); //加上编号
                aLi[i].onmouseover = function () {
                    for (var i = 0;i < aLi.length;i ++){
                        aLi[i].className = '';
                    }
                    this.className = 'active';
                    oTxt.innerHTML = '<h2>' + this.index + '月活动</h2><p>'+arr[this.index-1]+'</p>';
 };
            }
        };
    </script>
</head>
<body>
    <div id="tab" class="calendar-grid">
        <ul>
            <li class = "active"><h2>1</h2><p>JAN</p></li>
            <li ><h2>2</h2><p>FER</p></li>
            <li ><h2>3</h2><p>MAR</p></li>
            <li ><h2>4</h2><p>APR</p></li>
            <li ><h2>5</h2><p>MAY</p></li>
            <li ><h2>6</h2><p>JUN</p></li>
            <li ><h2>7</h2><p>JUL</p></li>
            <li ><h2>8</h2><p>AUG</p></li>
            <li ><h2>9</h2><p>SEP</p></li>
            <li ><h2>10</h2><p>OCT</p></li>
            <li ><h2>11</h2><p>MOV</p></li>
            <li ><h2>12</h2><p>DEC</p></li>
        </ul>
        <div class="text">
            <h2>1月活动</h2>
            <p>快过年了，大家商量去哪去玩啊</p>
        </div>
    </div>
</body>
```

+ 制造简易年历，当点击月份的时候，可以将相应的月份及事件显示出来。
+ 定义一个数组，以便将相应的事件显示出来。
+ `aLi[i].index = (i + 1`); //加上编号时，要带上括号，不然会变成一个字符串。
+ `oDiv.getElementsByTagName("div")[0];` 最后的“[0]”要加上，不然在“div”中显示数据，不会奏效。

1. InnerHTML的使用

```javascript
<script>
        window.onload = function () {
            var oTxt = document.getElementById('txt1');
            var oBtn = document.getElementById('btn1');
            var oDiv = document.getElementById('div1');
            oBtn.onclick = function () {
                oDiv.innerHTML = oTxt.value; //赋值给div
                // alert(oDiv.innerHTML) //将oDiv数据读出
            }
        }
    </script>
</head>
<body>

<input id="txt1" type="text">
<input id="btn1" type="button" value="设置文字">
<div id="div1">
    abct
</div>
</body>
```

+ `oDiv.innerHTML = oTxt.value;` 将文本框中的数据赋值给div，当然也可以读取出来。此外，该属性也可以将HTML语句读出并展示。

# 2 JavaScript基础

ECMAScript：解释器，是JavaScript的核心。     

DOM Document Object Model 文档对象模型 ,在JS中就是“document”，赋予了JS操作HTML的能力。

BOM Browser Object Model 浏览器对象模型。 window(弹个窗口等)赋予了JS浏览器的能力。

1. 兼容性问题

| ECMA | 几乎没有兼容性问题 |
| ---- | :----------------: |
| DOM  |  有一些操作不兼容  |
| BOM  |    没有兼容问题    |

2. 变量类型：

一个变量应该只存一种类型数据。

类型：typeof 运行符，通过(typeof a)可以看出类型。

常用类型：number、string、boolean、undefined、object、function

+ 类型转换

```javascript
<script>
        window.onload = function () {
            var oTxt1 = document.getElementById('txt1');
            var oTxt2 = document.getElementById('txt2');
            var oBut = document.getElementById('but1');

            oBut.onclick = function () {
                var n1 = parseInt(oTxt1.value);  
                var n2 = parseInt(oTxt2.value); /
                if (isNaN(n1)){
                    alert('你输入的第一个数字有误');
                }else if (isNaN(n2)){
                    alert('你输入的第二个数字有误');
                }else {
                    alert(n1 + n2);
                }
            }
        };
    </script>
</head>
<body>
<input id="txt1" type="text">
<input id="txt2" type="text">
<input id="but1" type="button" value="求和">
</body>
```

+ 强制类型转换（显式类型）`var n1 = parseInt(oTxt1.value);  parseInt:`对整数进行转化，parseFlaot:对小数进行转化。同时`parseInt`还可以取小数中的整数部分。
+ 判断是不是“`NAN`”值。
+ 闭包：子函数可以使用父函数的局部变量，子函数在父函数中嵌套。
+ 隐式转换

```javascript
<script>
    var a = 5;
    var b = '5';
    // alert(a == b);//先转换类型然后比较
    alert(a === b); //不转换类型进行比较
</script>
```

3. 命名规范

+ 首字母大写：Var aDivUserLogin

+ 类型前缀：给变量取名字时

![image-20200414103607756](JavaScript基础.assets/image-20200414103607756.png)

4. switch用法

```javascript
<script>
    var name = 'abc';
    var sex = '男';
    switch (sex){
        case "男":
            alert(name + '先生，您好');
            break;
        case "女":
            alert(name + '女士，您好');
            break;
        default:
            alert(name + '您好');
            break;
    }
</script>
```

5. 三目运算符

```javascript
条件？语句1：语句2
等价于：
if(条件){
    语句1；
}else{
    语句2；
}
```

6. 真假判断：

真：true、非零数字、非空字符串、非空对象（document）

假：false、数字零、空字符串、空对象、undefined

## 2.1 Json

[JSON](https://baike.baidu.com/item/JSON)([JavaScript](https://baike.baidu.com/item/JavaScript) Object Notation, JS 对象简谱)，它基于 [ECMAScript](https://baike.baidu.com/item/ECMAScript)的一个子集。

```javascript
<script>
    // var JsonTest = {a:12,b:14,c:'abc'}; //json数据
    // // alert(JsonTest.a)    
// alert(JsonTest.c)
   </script>
//json和数组
    var JsonTest = {a:12,b:14,c:7};
    var arr = [12,5,7];
    // alert(JsonTest['a'])
    // alert(arr[0])

//JsonTest.length不对
    //数组循环第二种方式
    // for(var i in arr){
    //     alert(i);
    // }

    for(var i in JsonTest){ /
        alert(JsonTest[i])
    }
```

+ 数组和J都可以使用for in循环，解决json循环的问题。
+ JsonTest['a']和数组有点类似。

# 3 深入JS

1. return使用

```javascript
<script>
    function show() {
        return 1;
    }
    var a = show();
    alert(a);
</script>
```

+ return在哪里调用哪里返回。

2. 不定参使用

```javascript
<script>
    // arguments是一个参数数组
    function sum() { //不定参数的求和
        var result = 0;
        for (var i = 0;i < arguments.length;i++){
            result += arguments[i];
        }
        return  result;
    }
    alert(sum(1.2,2,3));
```

+ `arguments`表示不定参的意思，使用该参数可以用来求和。
+ arguments.length使用

```javascript
<body>代码：
<!--style表示行间样式-->
<div id="div1" style="width: 200px;height: 200px;background: red"></div>
 </body>
<script>代码：
<script>
    function css(obj,name,value) {
        if (arguments.length == 2){
            return obj.style[name];
        }else {
            obj.style[name] = value;
        }
    }
    window.onload = function () {
        var oDiv = document.getElementById("div1");
        // alert(css(oDiv,'width'));
        css(oDiv,'background','green');
    }
```

+ 判断arguments.length,执行不同的情况。
+ css(obj,name,value)给参数取名，增强可读性。其中obj = arguments[0]，其中“css(oDiv,'width');”中的“width”要写成字符串的形式进行调用。

```javascript
<body>
<div id="div1"></div>
<!--非行间样式代码：-->
<style>
    #div1 {width: 200px;height: 200px;background: red}
</style>
<script>代码：
    function getStyle (obj,name){ //获取非行间样式
        if (obj.currentStyle){ //undefined 为false 兼容问题
            return obj.currentStyle[name];   //IE浏览器
        }else{
            return getComputedStyle(obj,false)[name];//chrome支持
        }
    }
    window.onload = function () {
        var oDiv = document.getElementById("div1");
                  alert(getStyle(oDiv,'width'));
    }
</script>
```

+ `alert(getComputedStyle(oDiv,false).width)` 获取非行间样式，对于chrome支持的代码，其中第一个参数代表要获取那个元素的样式，第二个是解决FF较低版本的写法，而对于高版本的可以不用写。
+ `alert(oDiv.style.width)`;只能获取行间样式，非行间样式值取不到。
+ `function getStyle (obj,name`)表示获取非行间样式封装的方法，使用“if”判断进行保证浏览器的兼容性。

## 3.1 数组操作

1. 数组中单个数据的按顺序的添加和删除

```javascript
a.push(4);//数组尾部添加内容
alert(a);
a.unshift(5); //头部添加元素
alert(a);
    a.pop()  //数组尾部删除
alert(a);
    a.shift(); //头部删除元素
alert(a);
```

2. splice的用法：splice(起点，长度)

```javascript
a.splice(1,2);
alert(a);

//插入元素
a.splice(2,0,'a','b');
alert(a);

替换元素
a.splice(2,2,'a','b');
alert(a);
```

3. 数组的连接

```javascript
// var a = [1,2,3,4];
// var b = [1,2,3,4];
// alert(a.concat(b));

数组的"连接符"拼接:
var a = [1,2,3,4];
alert(a.join('-'))
```

4. 字符串排序

```javascript
var arr = ['float','width','alpha','left']; 
arr.sort();
alert(arr);
```

5. 数字排序

```javascript
var arr = [112,17,9,4,8]; 
arr.sort(function (n1,n2) {    
return n1 - n2;
   });
alert(arr);
```

+ 数字排序，数字当成字符串排序。sort只认识字符串。

## 3.2 定时器的使用

1. 开启定时器

+ setInterval  间隔型
+ setTimeout 延时型

2. 关闭定时器

+ clearInterval
+ clearTimeout

```html
<script>
        window.onload = function () {
            var oBut1 = document.getElementById('btn1');
            var oBut2 = document.getElementById('btn2');
            var timer = null;
			<!--开启时钟-->
            oBut1.onclick= function () { 
                timer = setInterval(function (){
                            alert("a");
                },3000); //间隔1S
            };
			<!--关闭时钟-->
            oBut2.onclick= function () {
                clearInterval(timer); //关闭定时器
            };
        };
    </script>
</head>
<body>
<input id="btn1" type="button" value="开启"/>
<input id="btn2" type="button" value="关闭"/>
```

### 3.2.1 数码时钟

1. 获取系统时间

+ Date对象
+ getHours,getMinutes,getSeconds

2. 显示系统时间

+ 字符串连接
+ 空位补零

3. 设置图片路径

+ charAt()方法(解决浏览器不兼容问题)

```html
<script>
    function toDou(n){ //字符串连接和补零
        if (n<10){
            return '0'+n;
        }else{
            return ''+n;
        }
    }
    window.onload = function () {
        var aImg =document.getElementsByTagName('img');
        function tick() {
            var oDate = new Date();
            var str = toDou(oDate.getHours())+toDou(oDate.getMinutes())+toDou(oDate.getSeconds());

            for (var i = 0;i < aImg.length;i++){
                aImg[i].src = 'img/'+str.charAt(i)+'.png';
            }
        }
        setInterval(tick,1000); //每秒钟显示一次
        tick(); // 解决刚开始的时候，时钟显示为0的问题
    };
</script>
</head>
<body style="background: black;color: white;font-size: 50px">
    <img src="img/0.png">
    <img src="img/0.png">
    <img src="img/0.png">
    <img src="img/0.png">
    <img src="img/0.png">
    <img src="img/0.png">
</body>
```

### 3.2.2 延时提示框

1. 效果：移入显示，移除延时隐藏。

```html
<style>
    div {float: left;margin: 10px}
    #div1 {width: 50px;height: 50px;background: red}
    #div2 {width: 250px;height: 250px;background: #3fff1a;display: none}
</style>
<script>
    window.onload = function () {
        var oDiv1 = document.getElementById('div1');
        var oDiv2 = document.getElementById('div2');
        var timer = null;

        oDiv1.onmouseover=function () {//移入div1开始显示
            clearTimeout(timer);  //解决从div2移入div1消失的问题
            oDiv2.style.display='block';
        };
        oDiv1.onmouseout = function () { //移出div1开始缓慢消失
            timer = setInterval(function () {
                oDiv2.style.display='none';
            },1000);
        };
        oDiv2.onmouseover = function () { //
            clearTimeout(timer);//进入div2将定时器关闭，不然进入div2时随着延时时间消失div也消失。
        };
        oDiv2.onmouseout = function () {
            timer = setTimeout(function () {
                oDiv2.style.display = "none"; //延时1秒后消失,不然移除div2再进入div1的时候会有闪现
            },1000);
        };
    };
</script>

</head>
<body>
<div id="div1"></div>
<div id="div2"></div>
```

2. 简化代码

```html
 oDiv2.onmouseover = oDiv1.onmouseover=function () {
            clearTimeout(timer);
            oDiv2.style.display='block';
        };
        oDiv2.onmouseout = oDiv1.onmouseout = function () {
            timer = setInterval(function () {
                oDiv2.style.display='none';
            },1000);
        };
```



+ 合并两个相同的mouseover和mouseout

# 4 DOM

CSS标签，JS元素，DOM节点

子节点：只算第一层。

1. children使用

```javascript
<script>
    window.onload = function () {
    var oUi = document.getElementById("ul1");
    for (var i = 0;i < oUi.children.length;i++){
        if (oUi.children[i].nodeType == 1){
            oUi.children[i].style.background = 'red';
        }
    }
};
</script>
</head>
<body>
<ul id="ul1">
    <li></li>
    <li></li>
</ul>
</body>
```

+ children只包含元素，不包含节点，在浏览器中兼容。

2. 父节点：parentNode的使用

```javascript
<script>
    window.onload = function () {
    var aLi = document.getElementsByTagName('a');
    for (var i = 0;i < aLi.length;i++){
        aLi[i].onclick = function () {
        //将<a>标签的父节点li给隐藏（this指代<a>标签）
     	this.parentNode.style.display = 'none';
        };
    }
};
</script>
</head>
<body>

<ul id="ul1">
    <li>123 <a href="javascript:;">隐藏</a></li>
    <li>abc <a href="javascript:;">隐藏</a></li>
</ul>
```

+ 将<a>标签的父节点<li>给隐藏（this指代<a>标签）。

3. offsetParent的使用

```javascript
<style>
#div1 {width: 200px;height: 200px;background: #CCC;position: relative}
#div2 {width: 100px;height: 100px;background: red;position: absolute}
</style>
    <script>
        //div2相对于div1进行定位
        window.onload = function () {
            var oDiv = document.getElementById("div2");
            alert(oDiv.offsetParent);
        };
    </script>
</head>
<body>
<div id="div1">
    <div id="div2"></div>
</div>
</body>
```

+ div2相对于div1进行定位，其中div2的（;position: absolute），div1的（;position: relative，此时div1会相对div2进行移动。





# 5 面向对象

## 5.1 this使用

封装：不考虑内部实现，只考虑功能使用。

1. this归属

```javascript
<script>
    var arr = [1,2,3];
    arr.a = 12;
    arr.show = function () {
        alert(this.a); //this表示为arr
    }
    arr.show();
</script>
```

+ 事件处理中`this`本质(属于谁)：即为当前发生时间的对象，即当前方法属于谁，this即为谁。
+ `arr.show`表示给`arr`添加的show()方法。
+ `arr.a`表示给`arr`添加的a属性。
+ 数组为系统对象，不要在系统对象上随意添加方法和属性，不然会覆盖原来的方法和属性。

2. window对象

```javascript
<script>
show = function () {
            alert(this);//这里指代window
        }
        show();
</script>
```

+ 这里的`show`方法表示全局方法，但是并没有对象使用，其实隐含对象是`window`,使用它在窗口输出`[object window]`。

## 5.2 创建对象

### 5.1.1 第一个对象

```javascript
<script>
var obj = new Object();//创建一个对象
obj.name = "blue"; //为对象添加属性
obj.qq = "3493826518@qq.com";

obj.showName = function () { //为对象添加方法
    alert('我的名字叫'+this.name);
}

obj.showQQ = function () { //为对象添加方法
    alert('我的QQ号'+this.qq);
}
obj.showName(); //方法调用
obj.showQQ();
</script>
```

+ `new Object()`中的`object`对象是一个没有功能，空白的对象，我们可以对它进行许多操作。

1. 构造函数

```javascript
<script>
function createPerson(name,qq) { //构造函数就是普通函数（构建一个对象出来）
    //原料（空白的对象）工厂方式
    var obj = new Object();//创建一个对象

    //加工
    obj.name = name; //为对象添加属性
    obj.qq = qq;

    obj.showName = function () { //为对象添加方法
        alert('我的名字叫'+this.name);
    }

    obj.showQQ = function () { //为对象添加方法
        alert('我的QQ号'+this.qq);
    }
    //出厂
    return obj;
}
var obj1 = createPerson('blue','3493826518@qq.com');
obj1.showName(); //方法调用
obj1.showQQ();
var obj2 = createPerson('blue','3493826518@qq.com');
obj2.showName(); //方法调用
obj2.showQQ();
</script>
```

+ 构造函数本身是一个普通函数，主要作用是构建一个对象出来。
+ 上面构造函数的流程可以看作一种工厂方式：即原料，加工和出厂。
+ (obj1.showName() ==obj2.showQQ())返回的结果是false。

### 5.1.2 工厂方式

上面构造方法遇到的问题有两个：没有new,函数重复（导致资源浪费问题）。

1.原型的理解

|    区域    | 描述                                                         |
| :--------: | ------------------------------------------------------------ |
|    CSS     | class（一次给一组元素加样式）                          行间样式（一次给一个元素加样式）              class   .box {background:red}                        <div class = "box" style ="background:green">                              <div class = "box" style ="background:green"> |
| JavaScript | 原型和class原理类似。  给对象添加东西和行间样式类似。        |

解决问题1：没有new

```javascript
<script>
function CreatePerson(name,qq) { //构造函数就是普通函数（构建一个对象出来）

            //系统偷偷地为我们做
            //var this = new Object();

            this.name = name; //为对象添加属性
            this.qq = qq;

            obj.showName = function () { //为对象添加方法
                alert('我的名字叫'+this.name);
            }

            obj.showQQ = function () { //为对象添加方法
                alert('我的QQ号'+this.qq);
            }
            //偷偷做一些
            // return this;
        }
        var obj1 = new CreatePerson('blue','3493826518@qq.com');
        obj1.showName(); //方法调用
        obj1.showQQ();
        var obj2 = new CreatePerson('blue','3493826518@qq.com');
        obj2.showName(); //方法调用
        obj2.showQQ();
    </script>
```

+   `var obj1 = new CreatePerson();`添加`new`时，系统为我们做两件事：
  + 为我们添加：var this = new Object();
  + 将对象返回：return this;

2. 类和对象的理解

类就是模子，对象就是产品（成品）

```javascript
var arr = new Array(1,2,3);
```

+ Array就是类，arr就是对象。

解决问题2：函数重复。

```javascript
<script>
function CreatePerson(name,qq) { //构造函数就是普通函数（构建一个对象出来）
    this.name = name; //为对象添加属性
    this.qq = qq;
}
CreatePerson.prototype.showName = function () { //原型
    alert('我的名字叫'+this.name);
};
CreatePerson.prototype.showQQ = function () {
    alert('我的QQ号'+this.qq);
};

var obj1 = new CreatePerson('blue','3493826518@qq.com');
obj1.showName(); //方法调用
obj1.showQQ();
var obj2 = new CreatePerson('blue','3493826518@qq.com');
obj2.showName(); //方法调用
obj2.showQQ();
alert(obj1.showName() == obj2.showName());
</script>
```

+ `alert(obj1.showName() == obj2.showName())`会返回true。
+ `CreatePerson.prototype.showName（）`表示使用原型创建对象。

3. 对象编程原则

+ 构造函数中加属性。
+ 原型中加方法。

## 5.3 面向对象的选项卡

1. 面向过程的方式

```html
<style>
    #div1 input {background: white}
    #div1 input.active {background: yellow}
    #div1 div {width:200px;height: 200px; background: #CCC;display: none}
</style>
<script>
 	window.onload=function () {
        var oDiv = document.getElementById('div1');
        var aBtn = oDiv.getElementsByTagName('input');
        var aDiv = oDiv.getElementsByTagName('div');

        for (var i = 0;i < aBtn.length;i ++){
            aBtn[i].index = i;
            aBtn[i].onclick = function () {
                for (var i = 0;i < aBtn.length;i ++){
                    aBtn[i].className='';
                    aDiv[i].style.display='none';
                }
                this.className='active';
                aDiv[this.index].style.display='block';
            }
        }
    }
</script>
</head>
<body>
<div1 id="div1">
    <input class="active" type="button" value="aaa">
    <input type="button" value="bbb">
    <input type="button" value="ccc">
    <div style="display: block">aaa</div>
    <div>bbb</div>
    <div>ccc</div>
</div1>
```

+ 过程比较简洁

2. 面向过程的方式

```javascript
<script>
    window.onload = function () {
    new TabSwitch('div1');
};

function TabSwitch(id) {
    var _this = this;
    var oDiv = document.getElementById(id);
    this.aBtn = oDiv.getElementsByTagName('input');
    this.aDiv = oDiv.getElementsByTagName('div');

    for (var i = 0; i < this.aBtn.length; i++) {
        this.aBtn[i].index = i;
        this.aBtn[i].onclick = function () {
            _this.fnClick(this); //将选项卡的this传过来,将this.aBtn[i]的this传过去
        };
    }
}

TabSwitch.prototype.fnClick = function (oBtn) {
    for (var i = 0; i < this.aBtn.length; i++) {
        this.aBtn[i].className = '';
        this.aDiv[i].style.display = 'none';
    }
    oBtn.className = 'active';
    this.aDiv[oBtn.index].style.display = 'block';
}
</script>
```

+ `fnClick`主要解决的是面向对象不能有函数嵌套的问题。
+ `function () { _this.fnClick(this);}`闭包传递this,才不至于将选项卡的“this”传过来。
+ 过程中的转换
  + onload  变成构造函数
  + 全局变量  变成属性
  + 函数  变成方法

## 5.4 JSON面向对象编程

```javascript
<script>
    var json = {
        name:'blue',
        qq:'3493826518',
        showName: function () {
            alert('我的名字叫:'+this.name);
        },
        showQQ:function () {
            alert('我的QQ为:'+this.qq);
        }
    };
json.showName();
json.showQQ();
</script>
```

+ (单体)整个程序中，只有一个，不适合多个对象，写起来比较简单。

## 5.5 继承

1. call()函数

```javascript
<script>
function show(a,b) {
    alert('this是：'+this+'\na是'+a+'\nb是'+b)
}
	show.call('abc',12,5); 
 </script>
```

+ 可以看到，`call（）`函数中的第一个参数作为`this`,后面依此传给函数作为参数。

2. 子类继承父级属性

```javascript
<script>
function A() {
    this.abc = 12;
}

A.prototype.show = function () {
    alert(this.abc);
};

//继承A
function B() {
    //this->new B();继承父级的属性
    //原本给A加属性，变成给B加的属性。
    A.call(this);
}

var obj = new B();
alert(obj.abc);
</script>
```

+  `A.call(this)`，原本给A的属性`this.abc = 12;`但是现在的`this`变成了`new B()`所以变成了给B赋值，即B成功的继承了A属性。

3. 子类继承父类方法

```javascript
for(var i in A.prototype){ //B继承A的方法
    B.prototype[i] = A.prototype[i];
}
B.prototype.fn=function () { //B独有的方法
    alert('fnabc')
}

var obj = new B();
var objA = new A();
obj.fn();
obj.show();
// objA.fn();//报错
objA.show();
```

+ objA.fn();会报错，找不到对应的函数名。
+ `for(var i in A.prototype)`为B继承A的方法，二者这样写的话，A和B就分属于不同的空间，A变化时就不会影响B。
+ `B.prototype= A.prototype;`这样表示A,B二者共属于统一空间。

# 6 Ajax

服务器获取的数据的字符集编码要统一。缓存要阻止掉。

```javascript
ajax(‘aaa.txt?t=’+new Date().getTime(),function(){},function(){})
```

+ `new Date().getTime()`表示增加一个时间戳来阻止浏览器缓存。后台不会来接收这个“t”,仅仅只是针对浏览器来看的。

动态数据：请求JS（或json）文件,ajax读取到的数据都是以字符串数据存在。

1. 假设服务器端文本数据为： [1,2,3,4],前端代码书写为：

```javascript
function(str){

var arr = eval(str) // eval()将文本文件中的数据(也就是服务端的数据data)读过来，然后进行解析。

arr[0]；//即为读取到的数组数据的第一个元素“1”。

}
```

2. 假设服务器端数据为：[{a:5,b:7},{a:8,b:12}],前端代码为：

```javascript


function(str){

var arr = eval(str);

 arr[0].a；//读取到数据为“5”。

}               
```

3. 假设服务器端数据为：[{user: ‘blue’,pass:’123’},{user:’张三,pass:’456’}],即存的是一个用户列表，（我们要在前端生成一个用户列表），前端代码：

```javascript
<script>代码：
var oBut = document.getElementById(“but1”);
var oUl = document.getElementById(“ul1”);
function(str){
var arr = eval(str);

   for(var i =0;I < arr.length;i++){

       var oLi = document.createElement(‘li’);

       oLi.innerHTML = ‘用户名：<strong>’ + arr[i].user+’</strong>密码：<span>’+arr[i].pass +’</span>’;

      oUl.appendChild(oLi);// 放到<ul>中
}
}

<body>代码：

<input id = “but1" type = “button" value = “读取">

<ul id = “ul1" >

</ul>
```

4. HTT请求

|            GET方式             |                   POST方式                   |
| :----------------------------: | :------------------------------------------: |
|    Get方式通过网址方式传递     |       Post方式通过http Content方式传递       |
|    容量小，不适合传递大数据    |                                              |
|          安全性非常差          |                                              |
|             有缓存             | 没有缓存，每一次post都会向服务器提取新的数据 |
| 更适合用于获取数据（浏览帖子） |        更适合用于上传数据（用户注册）        |

# 7 cookie基础

## 7.1 介绍

cookie主要时页面进行保存信息，比如自动登录，记住用户名等等。

特性

+ 同一个网站中所有页面共享一套cookie。
+ 大小数量有限（4K-10K）。
+ 过期时间。

## 7.2 封装函数

1. 添加Cookie

```javascript
document.cookie='user=blue';
document.cookie='password=123';
```

+ 其中等号“=”等同于添加，使用“名字=值”的方式进行添加。

2. 封装添加Cookie

```javascript
function setCookie(name,value,iDay) {
    var oDate = new Date();
    oDate.setDate(oDate.getDate()+iDay);
    document.cookie = name +'='+ value+';expires ='+oDate;
}
setCookie('username','xiaolun',14);
setCookie('password','123',14);
```

+ `expires`表示有效时间。

3. 封装获取Cookie

```javascript
function getCookie(name) { //获取某一个名字的Cookie
    var arr = document.cookie.split(';'); //将Cookie数据进行分割，并以字符串的形式存在arr数组中。
    for (var i = 0;i < arr.length;i ++){
        var arr2 = arr[i].split("=");//将字符串继续以“=”进行分割
        if (arr2[0] == name){
            return arr2[1];
        }else {
            return '';
        }
    }
}
alert(getCookie('username'));
```

4. 删除Cookie

```javascript
//删除Cookie。
function removeCookie(name) {
    setCookie(name,1,-1); //-1天过期
}

removeCookie('username');  //删除对应的Cookie
alert(getCookie('username'));
alert(getCookie('password'));
```

+ 过期时间设置为“-1”天，即昨天，浏览器就会删除Cookie。

# 8 正则表达式 

## 8.1 基础

### 8.1.1 字符串操作

```javascript
var str = 'abcd';
alert(str.search('b')); //返回字符串的位置索引
alert(str.substring(1,3));//获取字符串不包含结束位置
alert(str.charAt(0)); //获取某个字符串
```

1. 使用普通操作将字符串中数据取出

```javascript
 var str = 'abcd1s2df3';
var arr = [];
var temp = '';
for (var i = 0;i < str.length;i ++){
    if (str.charAt(i) >= '0' && str.charAt(i) <= '9'){
        temp += str.charAt(i);
    }else{
        if (temp){
            arr.push(temp);
            temp='';
        }
    }
    console.log("temp = "+ i +'次'+temp);
}
if (temp){ //将字符串最后一个数字给加入
    arr.push(temp);
}
alert(arr);
```

2. 使用正则表达式取出数据

```javascript
alert(str.match(/\d+/g));
```

###  8.1.2 介绍

正则表达式就是规则，模式，一种字符匹配工具。

1. 搜索字符

```javascript
// var re = new RegExp('a','i');//js风格('i':ignore)
var re = /a/i; //perl风格
var str = 'abc';
alert(str.search(re));
```

+ 有两种风格的书写方式。

2. 匹配数字

```javascript
var str = 'shijian 1111 tianqi';
var re = /\d/g;//g，global，找到全部（\d只要一位数字，\d\d;两位数字）
var re = /\d+/g;//+ 若干，将数据按照顺序找出来
alert(str.match(re));
```

3. replace替换使用

```javascript
var str = 'shijian 1111 tianqi';
// alert(str.replace('1','0')); //只将第一个‘1’替换成0
var re =/1/g;
alert(str.replace(re,'0'));
```

4. 过滤敏感信息

```javascript
<script>
    window.onload = function () {
    var oTxt1 = document.getElementById('txt1');
    var oTxt2 = document.getElementById('txt2');
    var oBtn = document.getElementById('btn1');
    oBtn.onclick = function () {
        var re =/北京|百度|淘宝/g;// "|"横线表示或的意思。
        oTxt2.value = oTxt1.value.replace(re,'***'); //将敏感信息过滤
    }
}
</script>
</head>
<body>
<textarea id="txt1" rows="10" cols="10"></textarea><br/>
<input id="btn1" type="button" value="过滤"><br/>
<textarea id="txt2" rows="10" cols="10"></textarea>
</body>
```

5. 过滤HTML标签

```javascript
var re = /<[^<>]+>/g;// 过滤HTML标签
oTxt2.value = oTxt1.value.replace(re,''); //将敏感信息过滤
```

6. 转义字符

|         描述         |    字符     |
| :------------------: | :---------: |
|         数字         |   \d[0-9]   |
|  英文、数字、下划线  | \w[a-z0-9_] |
|       空白字符       |     \s      |
|        非数字        |     \D      |
| 非英文、数字、下划线 |     \W      |
|      非空白字符      |     \S      |

7. 量词（个数）

| 语法  |        描述         |
| :---: | :-----------------: |
|  {n}  |    正好出现n次。    |
| {n,m} | 最少n次，最多m次。  |
| {n,}  | 最少n次，最多不限。 |
|   +   |        {1,}         |
|   ?   |   {0,1}，可有可无   |
|   *   |   {0，}，不推荐用   |





















