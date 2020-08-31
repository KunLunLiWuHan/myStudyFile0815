# 1 介绍

前端三要素：HTML+CSS+JavaScript（结构+表现+交互）。

CSS中重点是CSS选择器，之后我们就可以美化网页（文字，颜色，边距，超链接等）。

CSS(Cascading Style Sheet)层叠级联样式表。

**发展史**

CSS2.0 Div + CSS ,HTML与CSS结构分离的思想，网页变得简单。

CSS2.1 浮动，定位。

CSS3.0 圆角，阴影，动画等。

## 1.1 快速入门

**项目结构**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125446.png" alt="image-20200706221652094" style="zoom:80%;" />

1. **html界面**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <!--
        将style.css中的样式和index.html相关联
        href:引用的地址。
		外部样式。
    -->
    <link rel="stylesheet" href="CSS/style.css">

</head>
<body>
    <h1>标题1</h1>
</body>
</html>
```

2. CSS样式

```css
/*
规范：要在html中的<style>里面可以编写CSS代码（针对CSS在同一个html中的情况）
   语法：
   选择器 {
     声明1,
     声明2,
     ....
   }
   举例：h1{}表示选中< body>中所有的h1标签
   ,以大括号结尾。将文字变成红色。
    每一个声明，以分号结尾。
 */
h1{
    color: pink;
}
```

这样书写代码，可以实现html界面和CSS样式相分离。

控制台输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125447.png" alt="image-20200706221823386" style="zoom:80%;" />

审查代码：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125448.png" alt="image-20200706221856796" style="zoom:80%;" />

## 1.2 CSS三种导入方式

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>

    <!--2.内部样式-->
    <style>
        h1{
            color: yellow;
        }
    </style>
    <!--3.外部样式(链接式（常用），还有个导入式，这里不做介绍)-->
    <link rel="stylesheet" href="CSS/style.css">
</head>
<body>
    <!--1.行内样式
    优先级：就近原则，上面外部样式比内部样式离的近，所以外部样式（优先级）>内部样式。
    -->
    <h1 >标题1</h1>
</body>
```

# 2 选择器

作用是选择页面上某一个或者某一类元素。

## 2.1 基本选择器

1. 标签选择器

在快速入门中，就是使用的该选择器。该选择器会选择这个页面中所有这个标签的元素，然后进行样式渲染。

2. 类选择器 class

```html
<style>
        /*语法：.class的名称{}
        可以多个标签归类及复用*/
       .test1{
           color: red;
       }
       .test2{
           color: green;
       }
    </style>
</head>
<body>
    <h1 class="test1"> 标题1</h1>
    <h2 class="test2"> 标题2</h2>
</body>
```

3. id选择器

```html
    <style>
        /*语法：#id名称{}
        id必须保证全局唯一     
        */
       #test01{
           color: red;
       }
       #test02{
           color: yellow;
       }
    </style>
</head>
<body>
    <h1 id="test01"> 标题1</h1>
    <h2 id="test02"> 标题2</h2>
</body>
```

优先级：id选择器>class选择器>标签选择器。

## 2.2 层次选择器

1. 后代选择器：某个元素的后面。

2. 子选择器

3. 相邻兄弟选择器

4. 通用选择器

```html
 <style>
    /*1.后代选择器*/
    /*    body p{*/
    /*        background: red;*/
    /*    }*/

    /*2.子选择器*/
    /*    body>p{*/
    /*        background: red;*/
    /*    }*/
    /*3.相邻兄弟选择器,只有一个，向下相邻*/
    /*.active + p{*/
    /*    background: red;*/
    /*}*/

    /*4.通用选择器,当前选中元素的向下所有兄弟*/
        .active~p{
            background: red;
        }

    </style>
</head>
<body>
  <p>p0</p>
  <p class="active">p1</p>
  <p>p2</p>
  <p>p3</p>
    <ul>
        <li>
            <p>p4</p>
        </li>
        <li>
            <p>p5</p>
        </li>
        <li>
            <p>p6</p>
        </li>
    </ul>
</body>
```

## 2.3 结构伪类选择器

```html
 <style>
        /*ul的第一个子元素*/
        ul li:first-child{
            background: red;
        }
        /*ul的最后一个子元素*/
        ul li:last-child{
            background: green;
        }
        /*选中p1:定位到父元素，选择当前的第一个元素
           选择当前p元素的父类元素，然后选中父类元素的第一个（按顺序）。
           会被父元素里面的内容干扰
        */
        p:nth-child(1){
            background: yellow;
        }
        /*选中父元素下的第1个p元素（按类型，推荐使用这一种）。
        */
        p:nth-of-type(1){
            background: pink;
        }

    </style>
</head>
<body>
  <p>p1</p>
  <p>p2</p>
  <p>p3</p>
    <ul>
        <li>li1</li>
        <li>li2</li>
        <li>li3</li>
    </ul>
</body>
```

## 2.4 属性选择器（常用）

```html
 <style>
        /*存在id属性的元素,语法：a[]{}*/
        /*a[id]{*/
        /*    background: red;*/
        /*}*/

        /*id=first属性的元素*/
        /*a[id=first]{*/
        /*    background: green;*/
        /*}*/

        /*class中存在links属性的元素*/
        /*a[class *= links]{*/
        /*    background: red;*/
        /*}*/

        /*选中href中以http元素开头的元素*/
        /*a[href^=http]{*/
        /*    background: green;*/
        /*}*/

        /*选中href中以http元素结尾的元素*/
        a[href$=pdf]{
            background: green;
        }
    </style>
</head>
<body>
    <a href="http://www.baidu.com" class="links item first" id="first">1</a>
    <a href="http://test" class="links item">2</a>
    <a href="abc.pdf">3</a>
    <a href="123s.pdf">4</a>
</body>
```

# 3 美化网页元素

美化网页可以有效地传递页面信息，吸引用户。

1. 对于要重点突出的字，使用`span`标签套起来。

## 3.1 盒子模型

外边距：margin。

内边距：padding。

边框：border，有三个属性：粗细，样式，颜色。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125449.png" alt="image-20200707165747974" style="zoom:80%;" />

计算公式：外边距+内边距+边框+内容。

1. 测试

```html
 <style>
        /*body总有一个默认的外边距，在这里我们进行初始化*/
        body{
            margin: 0;
        }

        #box{
            width: 300px;
            height: 300px;
            border: 1px solid red;
        }

        div:nth-of-type(1){
            border: 2px solid green;
        }
    </style>
</head>
<body>
    <!--使用div进行包裹-->
   <div id="box">
       <h2>会员登录</h2>
       <form action="#">
           <!--form中的属性值使用div进行包裹-->
           <div>
               <span>用户名：</span>
               <input type="text">
           </div>
           <div>
               <span>密码：</span>
               <input type="text">
           </div>
       </form>
   </div>
</body>
```

## 3.2 display

​		`block`和`inline`这两个概念是简略的说法，完整确切的说应该是 `block-level elements` (块级元素) 和 `inline elements` (内联元素)。

​	其中，`block`元素通常被现实为独立的一块，会单独换一行；`inline`元素则前后不会产生换行，一系列inline元素都在一行内显示，直到该行排满。

​	`inline-block`简单来说就是将对象呈现为`inline`对象，但是对象的内容作为`block`对象呈现。之后的内联对象会被排列在同一行内。比如我们可以给一个link（a元素）inline-block属性值，使其既具有block的宽度高度特性又具有inline的同行特性。

​	常见的块级元素有：

```ini
 DIV, FORM, TABLE, P, PRE, H1~H6, DL, OL, UL 等。
```

​	常见的内联元素有:

```ini
 SPAN, A, STRONG, EM, LABEL, INPUT, SELECT, TEXTAREA, IMG, BR 等。
```

1. 测试

```html
   <style>
        div{
            width: 100px;
            height: 100px;
            border: 1px solid red;
            /*display: inline-block;*/
        }
        span{
            width: 100px;
            height: 100px;
            border: 1px solid red;
            display: inline-block;
        }
    </style>
</head>
<body>
  <div>div块元素</div>
  <span>span行内元素</span>
</body>
```

控制台输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125450.png" alt="image-20200707201552144" style="zoom:80%;" />

# 4 定位

## 4.1 相对定位

相对于原来的位置，进行指定的偏移。

```css
position: relative; 
top: 20px; //相对于上面距20px的举例。
bottom:;
left:;
right:;
```

## 4.2 绝对定位

基于xxx定位，上下左右。

1. 没有父级元素定位的前提下，相对于浏览器进行定位。
2. 假设父级元素存在的情况下，我们通常会相对于父级元素进行定位，然后进行位置的偏移。此时，该元素只能在父级元素的范围中进行移动。

```html
     #father{
            width: 100px;
            height: 100px;
            border: 1px solid red;
            position: relative; //父级元素相对定位
        }
        #first{
          background: green;
            border: 1px solid red;
        }
        #second{
            background: red;
            border: 1px solid red;
            position: absolute; //相对于父级元素进行绝对定位
        }
    </style>
</head>
<body>
  <div id="father">
      <div id="first">第一个盒子</div>
      <div id="second">第二个盒子</div>
  </div>
```

经过测试，对于定位问题还有许多不理解的地方，希望有时间的话继续了解和学习吧。



