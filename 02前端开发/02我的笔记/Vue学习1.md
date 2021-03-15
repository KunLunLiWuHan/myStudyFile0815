# 1、介绍

1、概述

Vue是一种渐进式JavaScript框架，由尤雨溪创建，可以动态的构建用户界面。

2、特点

（1）遵循MVVM模式

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210220121508366.png" alt="image-20210220121508366" style="zoom:67%;" />

Model : 模型层主要负责处理交互请求并返回响应的数据。

View : 视图层主要负责展示视图。

ViewModel : 视图-模型层起到桥梁的作用,一方面响应用户事件并向模型层发送请求,另一方面将模型层返回的数据通过数据数据绑定在视图层中展示。

该模式解耦了视图和模型 : 每一个视图都对应一个ViewModel,同时ViewModel与模型建立联系,当接收到用户请求时,ViewModel获取模型响应数据,并通过数据绑定将相应的视图页面重新渲染。视图层的数据只需要传入ViewModel即可实现视图层的同步更新,从而实现了视图和模型之间的松散耦合 。

（2）编程简洁、体积小，运行效率高。

# 2、简单使用

## 1、基本使用

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="vue.js"></script>
    <title>first program for Vue</title>
</head>

<body>
    <!-- View视图层 -->
    <div id="app">
        <input type="text" v-model="message">
        <p>显示信息：{{ message }}</p>
    </div>
    <script>
        // （ViewModel层）创建Vue实例，Vue类由vue.js文件引入
        const vm = new Vue({ //配置对象 option
            el: '#app', //element:选择器
            data: { //数据（model层）
                message: 'Hello Vue!'
            }
        });
    </script>
</body>

</html>
```

浏览器界面显示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210220120107790.png" alt="image-20210220120107790" style="zoom:67%;" />

在这个实例中,初始化了带有`message`数据的`vm`对象,并将其绑定到`id`为`app`的`DOM`节点上。

初始化时，在实例上绑定的常规数据对象会被Vue转换为被观察的拥有可响应行为的对象，即当数据发生改变时,会同步更新其数据链和作用域中所有的相关状态。

下面简单地解析一下其语法 ：

1、双向绑定

View层中下面的代码实现双向绑定的功能，进而是input输入时显示信息的自动更新。

```html
<input type="text" v-model="message">
<p>显示信息：{{ message }}</p>
```

2、vm对象

通过构造函数Vue就可以创建一个Vue的根实例，并启动Vue应用，上文中的变量vm就代表这个Vue实例。

3、el选择器

用于指定一个页面中已存在的`DOM`元素，然后用来挂载Vue实例。

4、data-model层

通过Vue实例的data选项,配合`v-model`指令可以声明应用内需要双向绑定的数据。

5、双括号{{ }}

是最基本的文本插值方法,它可以自动将我们双向绑定的数据实时显示出来。可以调用对象的方法。

编码过程总结：

（1）引入vue.js

（2）创建Vue对象

​	el：初始化element（选择器）；

​	data：初始化数据（页面可以访问）。

（3）双向数据绑定：v-model

（4）显示数据：{{ xx }}

## 2、模板语法

1、介绍

模板就是动态的html界面，包含一些JS的语法代码：双大括号表达式、指令（v-开头的标签属性）。

（1）指令1：强制数据绑定

该指令用于指定变化的属性值。

```vue
v-bind:xxx="yyy" //yyy会作为表达式解析执行
//简洁写法
:xxx="yyy"
```

（2）指令2：绑定事件监听

该指令用于绑定事件名的回调函数。

```vue
v-on:click="xxx"
//简洁写法
@click="xxx"
```

## 3、计算属性和监视

1、computed属性

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="vue.js"></script>
    <title>计算属性和监视的理解</title>
</head>

<body>
    <div id="demo">
        姓: <input type="text" placeholder="First Name" v-model="firstName"><br>
        名: <input type="text" placeholder="Last Name" v-model="lastName"><br>
        姓名1(单向): <input type="text" placeholder="Full Name" v-model="fullName1"><br>
    </div>

    <script type="text/javascript">
        var vm = new Vue({
            el: '#demo',
            data: {
                firstName: 'li',
                lastName: 'kunlun',
            },
            computed: {
                // 执行时间：初始化显示/相关data属性发生变化
                //" 姓名1(单向)"的v-model="fullName1"现在是计算属性的一个方法，方法的返回值作为一个属性值
                fullName1: function () {
                    return this.firstName + " " + this.lastName
                }
            },
    </script>
</body>

</html>
```

控制台输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210220161349576.png" alt="image-20210220161349576" style="zoom:67%;" />

2、get/set方法

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="vue.js"></script>
    <title>计算属性和监视的理解</title>
</head>

<body>
    <div id="demo">
        姓: <input type="text" placeholder="First Name" v-model="firstName"><br>
        名: <input type="text" placeholder="Last Name" v-model="lastName"><br>
        姓名1(单向): <input type="text" placeholder="Full Name" v-model="fullName1"><br>
        姓名3(双向): <input type="text" placeholder="Full Name2" v-model="fullName3"><br>
    </div>

    <script type="text/javascript">
        var vm = new Vue({
            el: '#demo',
            data: {
                firstName: 'li',
                lastName: 'kunlun',
            },
            computed: {
                // 执行时间：初始化显示/相关data属性发生变化
                //" 姓名1(单向)"的v-model="fullName1"现在是计算属性的一个方法，方法的返回值作为一个属性值
                // fullName1: function () {
                //     return this.firstName + " " + this.lastName
                // },
                fullName3: {
                    // 回调函数特点：你定义的；你没有调用；最终会执行。
                    // 需要明白：回调函数什么时候调用；用来做什么。
                    // get方法中，当需要读取当前属性值时回调，计算并返回当前属性的值
                    get: function () {
                        return this.firstName + " " + this.lastName
                    },
                    //当属性值发生改变时回调，更相关的属性数据。
                    set: function (value) { //value就是fullName3的最新属性值
                        var names = value.split(' ')
                        this.firstName = names[0]
                        this.lastName = names[1]
                    }
                }
            },
    </script>
</body>

</html>
```

浏览器输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210220161629461.png" alt="image-20210220161629461" style="zoom:67%;" />

3、watch属性

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="vue.js"></script>
    <title>计算属性和监视的理解</title>
</head>

<body>
    <div id="demo">
        姓: <input type="text" placeholder="First Name" v-model="firstName"><br>
        名: <input type="text" placeholder="Last Name" v-model="lastName"><br>
        姓名2(单向): <input type="text" placeholder="Full Name" v-model="fullName2"><br>
    </div>

    <script type="text/javascript">
        var vm = new Vue({
            el: '#demo',
            data: {
                firstName: 'li',
                lastName: 'kunlun',
                fullName2: 'li kunlun'
            },
            watch: { //配置监视-第一种方式
                // lastName发生改变
                lastName: function (newVal, oldVal) {
                    this.fullName2 = this.firstName + ' ' + newVal
                }
            }
        })
        // 第二种监视方式：使用vm对象
        vm.$watch('firstName', function (val) {
            this.fullName2 = val + ' ' + this.lastName
        })
    </script>
</body>

</html>
```

浏览器输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210220163114561.png" alt="image-20210220163114561" style="zoom:50%;" />

## 4、Style和class绑定

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="vue.js"></script>
    <title>Document</title>
    <!-- 样式 -->
    <style>
        .classA {
            color: red;
        }

        .classB {
            background: blue;
        }

        .classC {
            font-size: 20px;
        }
    </style>
</head>

<body>
    <div id="demo">
        <h2>1. class 绑定: :class='xxx'</h2>
        <p class="classB" :class="a">表达式是字符串: 'classA'</p>
        <p :class="{classA: isA, classB: isB}">表达式是对象: {classA:isA, classB: isB}</p>
        <h2>2. style 绑定</h2>
        <p :style="{color, fontSize}">style="{ color: activeColor, fontSize: fontSize + 'px' }"</p>
        <button @click="update">更新</button>
    </div>
    <script type="text/javascript">
        new Vue({
            el: '#demo',
            data: {
                a: 'classA',
                isA: true,
                isB: false,
                color: 'red',
                fontSize: '20px'
            },
            methods: {
                update() {
                    this.a = 'classC'
                    this.isA = false
                    this.isB = true
                    this.color = 'blue'
                    this.fontSize = '30px'
                }
            }
        })
    </script>
</body>

</html>
```

浏览器输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210220171318888.png" alt="image-20210220171318888" style="zoom:50%;" />

## 5、列表渲染

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="vue.js"></script>
    <title>列表渲染</title>
</head>

<body>
    <div id="demo">
        <h2>测试: v-for 遍历数组</h2>
        <ul>
            <!--(p, index)=（对象，下标）使用for循环进行遍历  -->
            <!-- "":key="含义表示当v-for所绑定的数据发生变化时只重新渲染变化的项，而不是重新渲染整个列表 -->
            <li v-for="(p, index) in persons" :key="index">
                {{index}}--{{p.name}}--{{p.age}}
                --
                <button @click="deleteItem(index)">删除</button>
                --
                <button @click="updateItem(index, {name:'Jok',age:15})">更新</button>
            </li>
        </ul>
        <h2>测试: v-for 遍历对象</h2>
        <ul>
            <li v-for="(value, key) in persons[0]">
                {{ key }} : {{ value }}
            </li>
        </ul>
    </div>
    <script type="text/javascript">
        new Vue({
            el: '#demo',
            data: {
                persons: [ //对象数组
                    // 下面每一个大括号{}都是一个对象
                    { id: 1, name: 'Tom', age: 12 },
                    { id: 2, name: 'Jack', age: 13 },
                    { id: 3, name: 'Bob', age: 14 }
                ]
            },
            methods: {
                //列表删除-删除persons中指定index的数据项
                //splice() 方法可用于添加或删除数组中的元素
                deleteItem(index) {
                    this.persons.splice(index, 1)
                },
                //列表更新
                updateItem(index, p) {
                    // this.persons[index] = p // 页面不会更新
                    // 移除数组的第index个元素，并在数组第index个位置添加新元素p替代删除元素
                    this.persons.splice(index, 1, p)
                }
            }
        })
    </script>
</body>
</html>
```

浏览器输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210220220126149.png" alt="image-20210220220126149" style="zoom:50%;" />

列表排序

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="vue.js"></script>
    <title>列表搜索和排序</title>
</head>

<body>
    <div id="demo">
        <input type="text" name="searchName" placeholder="搜索指定用户名" v-model="searchName">
        <ul>
            <!-- filterPerson进行过滤计算 -->
            <li v-for="(p, index) in filterPerson" :key="index">
                {{index}}--{{p.name}}--{{p.age}}
            </li>
        </ul>
        <!--设置orderType排序方法 -->
        <button @click="setOrderType(1)">年龄升序</button>
        <button @click="setOrderType(2)">年龄降序</button>
        <button @click="setOrderType(0)">原本顺序</button>
    </div>
    <script type="text/javascript">
        new Vue({
            el: '#demo',
            data: {
                orderType: 0, //0 代表不排序, 1 为升序, 2 为降序
                searchName: '',
                persons: [
                    { id: 1, name: 'Tom', age: 13 },
                    { id: 2, name: 'Jack', age: 12 },
                    { id: 3, name: 'Bob', age: 17 },
                    { id: 4, name: 'Cat', age: 14 },
                    { id: 4, name: 'Mike', age: 14 },
                    { id: 4, name: 'Monica', age: 16 }
                ]
            },
            methods: {
                setOrderType(orderType) {
                    this.orderType = orderType
                }
            },
            computed: {
                filterPerson() { //过滤计算
                    // 解构，取出相关的数据
                    let { orderType, searchName, persons } = this
                    // 对persons进行过滤
                    // 从头到尾地检索字符串 p.name，看它是否含有子串 searchName。
                    //如果找到一个 searchName。，则返回 searchName。 的第一次出现的位置；反之，返回-1。
                    persons = persons.filter(p => p.name.indexOf(searchName) != -1)
                    // 排序
                    if (orderType !== 0) {
                        persons = persons.sort(function (p1, p2) {
                            // 1-升序；2-降序。
                            if (orderType === 1) {
                                return p1.age - p2.age
                            } else {
                                return p2.age - p1.age
                            }
                        })
                    }
                    return persons
                }
            }
        })
    </script>
</body>

</html>
```

浏览器输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210220222644106.png" alt="image-20210220222644106" style="zoom:67%;" />

## 6、事件处理

1、介绍

（1）绑定监听

```html
v-on:xxx="fun"
@xxx="fun"
@xxx="fun(参数)"
默认事件形参: event
隐含属性对象: $event
```

（2）事件修饰符

```html
.prevent : 阻止事件的默认行为event.preventDefault()
.stop : 停止事件冒泡event.stopPropagation()
```

（3）按键修饰符

```html
.keycode : 操作的是某个keycode 值的键
```

2、测试代码

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="vue.js"></script>
    <title>事件处理</title>
</head>

<body>
    <div id="example">
        <h2>1. 绑定监听</h2>
        <!-- 原始写法 -->
        <button v-on:click="test1">Greet</button>
        <button @click="test1">Greet2</button>
        <!-- 事件传递函数，$event代表事件对象 -->
        <button @click="test2($event, 'hello')">Greet3</button>
        <h2>2. 事件修饰符</h2>
        <!-- 阻止事件默认行为-->
        <a href="http://www.baidu.com" @click.prevent="test3">百度一下</a>
        <br />
        <br />
        <!-- 停止事件冒泡-->
        <div style="width: 200px;height: 200px;background: red" @click="test4">
            <!-- @click.stop阻止事件冒泡 -->
            <div style="width: 100px;height: 100px;background: green" @click.stop="test5"></div>
        </div>
        <h2>3. 按键修饰符</h2>
        <!-- @keyup.8表示当event.keyCode=8的时候进行触发 -->
        <input @keyup.8="test6">
        <input @keyup.enter="test6">
    </div>
    <script type="text/javascript">
        new Vue({
            el: '#example',
            data: {
                name: 'Vue.js'
            },
            methods: {
                test1(event) {
                    // `event` 是原生DOM 事件
                    alert(event.target.innerHTML)
                },
                test2(event, msg) {
                    //输出：Greet3---Hello
                    alert(event.target.innerHTML + '---' + msg)
                },
                test3() {
                    alert('阻止事件的默认行为')
                },
                test4() {
                    alert('out')
                },
                test5() {
                    alert('inner')
                },
                test6(event) {
                    // event.target.value显示输入框中的内容
                    alert(event.keyCode + '---' + event.target.value)
                }
            }
        })
    </script>
</body>

</html>
```

浏览器输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210220224749886.png" alt="image-20210220224749886" style="zoom:67%;" />

## 7、表单数据的自动收集

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="vue.js"></script>
    <title>表单数据的自动收集</title>
</head>

<body>
    <div id="demo">
        <!-- submit点击默认行为是提交表单，这里并不需要它提交,只需要执行handleSubmit方法 -->
        <form @submit.prevent="handleSubmit">
            <span>用户名: </span>
            <input type="text" v-model="user.username"><br>
            <span>密码: </span>
            <input type="password" v-model="user.pwd"><br>
            <span>性别: </span>
            <input type="radio" id="female" value="female" v-model="user.sex">
            <!-- for 属性规定 label 与哪个表单元素绑定。 -->
            <label for="female">女</label>
            <input type="radio" id="male" value="male" v-model="user.sex">
            <label for="male">男</label><br>
            <span>爱好: </span>
            <input type="checkbox" id="basket" value="basketball" v-model="user.likes">
            <label for="basket">篮球</label>
            <input type="checkbox" id="foot" value="football" v-model="user.likes">
            <label for="foot">足球</label>
            <input type="checkbox" id="pingpang" value="pingpang" v-model="user.likes">
            <label for="pingpang">乒乓</label><br>
            <span>城市: </span>
            <select v-model="user.cityId">
                <option value="">未选择</option>
                <option v-for="city in allCitys" :value="city.id">
                    {{ city.name }}
                </option>
            </select><br>
            <span>介绍: </span>
            <textarea v-model="user.desc" rows="10"></textarea><br><br>
            <input type="submit" value="注册">
        </form>
    </div>
    <script type="text/javascript">
        var vm = new Vue({
            el: '#demo',
            data: {
                user: {
                    username: '',
                    pwd: '',
                    sex: 'female',
                    likes: [],
                    cityId: '',
                    desc: '',
                },
                allCitys: [{ id: 1, name: 'BJ' }, { id: 2, name: 'SZ' }, { id: 4, name: 'SH' }],
            },
            methods: {
                handleSubmit(event) {
                    alert(JSON.stringify(this.user))
                }
            }
        })
    </script>
</body>

</html>
```

浏览器输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210220230654495.png" alt="image-20210220230654495" style="zoom: 50%;" />

## 8、Vue实例的生命周期

1、介绍

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210221082954963.png" alt="image-20210221082954963" style="zoom:50%;" />

Vue实例大体上有三个生命周期

（1）初始化显示

beforeCreate()；created()；beforeMount()；mounted()。

（2）更新状态

beforeUpdate()；updated()。

（3）销毁Vue实例：vm.$destory()

beforeDestory()；destory()。

常用的生命周期方法：

（1）created()/mounted()：发送Ajax请求，启动定时器等异步任务。

（2）beforeDestory()：做收尾工作，比如：清除定时器。

2、代码

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="vue.js"></script>
    <title>Document</title>
</head>

<body>
    <div>
        <button @click="destoryVue">destory vue</button>
        <p v-show="isShowing">{{msg}}</p>
    </div>
    <script type="text/javascript">
        var vue = new Vue({
            el: 'div',
            data: {
                msg: '尚硅谷IT 教育',
                isShowing: true,
                persons: []
            },
            beforeCreate() {
                console.log('beforeCreate() msg=' + this.msg)

            },
            created() {
                console.log('created() msg=' + this.msg)
                // 设置定时任务
                this.intervalId = setInterval(() => {
                    console.log('-----')
                    this.isShowing = !this.isShowing
                }, 1000)
            },
            beforeMount() {
                console.log('beforeMount() msg=' + this.msg)
            },
            mounted() {
                console.log('mounted() msg=' + this.msg)
            },
            beforeUpdate() {
                console.log('beforeUpdate isShowing=' + this.isShowing)
            },
            updated() {
                console.log('updated isShowing=' + this.isShowing)
            },
            beforeDestroy() {
                console.log('beforeDestroy() msg=' + this.msg)
                // 清除定时任务
                clearInterval(this.intervalId)
            },
            destroyed() {
                console.log('destroyed() msg=' + this.msg)
            },
            methods: {
                // 销毁Vues实例
                destoryVue() {
                    vue.$destroy()
                }
            }
        })
    </script>
</body>

</html>
```

浏览器输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210221082814107.png" alt="image-20210221082814107" style="zoom: 50%;" />

## 9、过渡和动画

1、介绍

操作css 的trasition 或animation，vue 会给目标元素添加/移除特定的class。

（1）过渡的相关类名

```
xxx-enter-active: 指定显示的transition
xxx-leave-active: 指定隐藏的transition
xxx-enter/xxx-leave-to: 指定隐藏时的样式
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210221085300354.png" alt="image-20210221085300354" style="zoom:67%;" />

（2）基本过渡动画的编码

```
(1)在目标元素外包裹<transition name="xxx">
(2)定义class 样式
指定过渡样式: transition
指定隐藏时的样式: opacity/其它
```

2、代码

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="vue.js"></script>
    <title>动画操作</title>

    <style>
        /* 进入时样式 */
        .fade-enter-active,
        .fade-leave-active {
            transition: opacity .5s
        }
        /* 离开时样式 */
        .fade-enter,
        .fade-leave-to {
            opacity: 0
        }

        /* 可以设置不同的进入和离开动画*/
        .slide-fade-enter-active {
            transition: all .3s ease;
        }

        .slide-fade-leave-active {
            transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
        }

        .slide-fade-enter,
        .slide-fade-leave-to {
            transform: translateX(10px);
            opacity: 0;
        }
    </style>
</head>

<body>
    <div id="demo1">
        <button @click="show = !show">
            Toggle1
        </button>
        <!-- name="fade"和 -->
        <transition name="fade">
            <p v-if="show">hello</p>
        </transition>
    </div>
    <div id="demo2">
        <button @click="show = !show">
            Toggle2
        </button>
        <transition name="slide-fade">
            <p v-if="show">hello</p>
        </transition>
    </div>
    <script type="text/javascript">
        new Vue({
            el: '#demo1',
            data: {
                show: true
            }
        })
        new Vue({
            el: '#demo2',
            data: {
                show: true
            }
        })
    </script>
</body>

</html>
```

浏览器输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210221085148108.png" alt="image-20210221085148108" style="zoom:67%;" />

## 10、过滤器

1、介绍

过滤就是对要显示的数据进行特定格式化后再显示，并没有改变原来的数据，但是会产生新的数据。

（1）定义过滤器

```vue
Vue.filter(filterName, function(value[,arg1,arg2,...]){
// 进行一定的数据处理
return newValue
})
```

（2）使用过滤器

```vue
<div>{{myData | filterName}}</div>
<div>{{myData | filterName(arg)}}</div>
```

2、代码

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="vue.js"></script>
    <title>过滤器</title>
</head>

<body>
    <div id="test">
        <p>当前时间为: {{currentTime}}</p>
        <p>当前时间1 为: {{currentTime | dateStr}}</p>
        <p>当前时间2 为: {{currentTime | dateStr('YYYY-MM-DD')}}</p>
        <p>当前时间3 为: {{currentTime | dateStr('HH:mm:ss')}}</p>
    </div>
    <script type="text/javascript" src="https://cdn.bootcss.com/moment.js/2.19.0/moment.js"></script>
    <script>
        // 注册过滤器
        Vue.filter('dateStr', function (value, format) {
            // 日期格式化-使用moment插件
            return moment(value).format(format || 'YYYY-MM-DD HH:mm:ss')
        })
        new Vue({
            el: '#test',
            data: {
                currentTime: new Date()
            }
        })
    </script>
</body>

</html>
```

浏览器输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210221090535795.png" alt="image-20210221090535795" style="zoom:67%;" />

## 11、内置指令和自定义指令

1、介绍

（1）常用内置指令

```
1) v:text : 更新元素的textContent
2) v-html : 更新元素的innerHTML
3) v-if : 如果为true, 当前标签才会输出到页面
4) v-else: 如果为false, 当前标签才会输出到页面
5) v-show : 通过控制display 样式来控制显示/隐藏
6) v-for : 遍历数组/对象
7) v-on : 绑定事件监听, 一般简写为@
8) v-bind : 强制绑定解析表达式, 可以省略v-bind
9) v-model : 双向数据绑定
10) ref : 指定唯一标识, vue 对象通过$els 属性访问这个元素对象
11) v-cloak : 防止闪现, 与css 配合: [v-cloak] { display: none }
```

（2）自定义指令

```
1) 注册全局指令
Vue.directive('my-directive', function(el, binding){
el.innerHTML = binding.value.toupperCase()
})

2) 注册局部指令
directives : {
	'my-directive' : {
		bind (el, binding) {
			el.innerHTML = binding.value.toupperCase()
		}
	}
}

3) 使用指令
v-my-directive='xxx'
```

2、代码1-v-cloak使用

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="vue.js"></script>
    <title>指令</title>
    <style>
        /* 属性过滤器 */
        [v-cloak] {
            display: none
        }
    </style>
</head>

<body>
    <div id="example">
        <p>
            <span ref="message">xiaolun</span>
            <button @click="showMsg">显示左侧文本</button>
        </p>
        <!-- 防止闪现, 与css 配合: [v-cloak] { display: none } -->
        <p v-cloak>{{ msg }}</p>
    </div>
    <script type="text/javascript">
        alert('模拟加载慢')
        new Vue({
            el: '#example',
            data: {
                msg: "阳光明媚"
            },
            methods: {
                showMsg() {
                    console.log("进入showMsg方法")
                    alert(this.$refs.message.textContent)
                }
            }
        })
    </script>
</body>

</html>
```

浏览器输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210221094927661.png" alt="image-20210221094927661" style="zoom:67%;" />

3、代码2-自定义指令

功能类型于v-text, 但转换为全大写，其中一个转换为全小写。

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="vue.js"></script>
    <title>自定义指令</title>
</head>

<body>
    <div id="demo1">
        <p v-upper-text="msg"></p>
        <p v-lower-text="msg"></p>
    </div>
    <div id="demo2">
        <p v-upper-text="msg2"></p>
         <!--局部指令, 此处不能使用-->
        <!-- <p v-lower-text="msg2"></p> -->
    </div>
    <script type="text/javascript">
        //注册全局指令
        Vue.directive('upper-text', function (el, binding) {
            el.innerHTML = binding.value.toUpperCase()
        })
        new Vue({
            el: '#demo1',
            data: {
                msg: 'NBA love this game!'
            },
            directives: { // 注册局部指令
                'lower-text': {
                    bind(el, binding) {
                        el.innerHTML = binding.value.toLowerCase()
                    }
                }
            }
        })
        new Vue({
            el: '#demo2',
            data: {
                msg2: 'I Like You'
            }
        })
    </script>
</body>

</html>
```

浏览器输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210221095904733.png" alt="image-20210221095904733" style="zoom:67%;" />

## 12、自定义插件

1、介绍

Vue 插件是一个包含install 方法的对象，通过install 方法给Vue 或Vue 实例添加方法, 定义全局指令等。

2、测试

（1）插件JS

```js
/**
* 自定义Vue 插件
*/
(function () {
    const MyPlugin = {}
    MyPlugin.install = function (Vue, options) {
        // 1. 添加全局方法或属性
        Vue.myGlobalMethod = function () {
            alert('Vue 函数对象方法执行')
        }
        // 2. 添加全局资源
        Vue.directive('my-directive', function (el, binding) {
            el.innerHTML = "全部资源 my-directive： " + binding.value
        })
        // 3. 添加实例方法
        Vue.prototype.$myMethod = function () {
            alert('vue 实例对象方法执行')
        }
    }
    // 向外暴露
    window.MyPlugin = MyPlugin
})()
```

（2）页面使用插件

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="vue.js"></script>
    <script type="text/javascript" src="13-vue-myPlugin.js"></script>
    <title>自定义插件</title>
</head>

<body>
    <div id="demo">
        <!--使用自定义指令-->
        <p v-my-directive="msg"></p>
    </div>
    
    <script type="text/javascript">
        //声明使用自定义插件
        Vue.use(MyPlugin) //内部会执行MyPlugin.install(Vue)
        var vm = new Vue({
            el: '#demo',
            data: {
                msg: 'xiaolun'
            }
        })
        //调用自定义的静态方法-使用插件定义的功能
        Vue.myGlobalMethod()
        //调用自定义的对象方法
        vm.$myMethod()
    </script>
</body>

</html>
```

控制台输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210221101327375.png" alt="image-20210221101327375" style="zoom:67%;" />

# 3、Vue组件化编码

## 1、使用vue-cli创建项目

1、介绍

vue-cli 是vue 官方提供的脚手架工具，主要从下面网址中用于下载模板项目。

```http
https://github.com/vuejs-templates 
```

下载完成后的模板项目的解构

```
|-- build : webpack 相关的配置文件夹(基本不需要修改)
	|-- dev-server.js : 通过express 启动后台服务器
|-- config: webpack 相关的配置文件夹(基本不需要修改)
	|-- index.js: 指定的后台服务的端口号和静态资源文件夹
|-- node_modules
|-- src : 源码文件夹
  |-- components: vue 组件及其相关资源文件夹
  |-- App.vue: 应用根主组件
  |-- main.js: 应用入口js
|-- static: 静态资源文件夹
|-- .babelrc: babel 的配置文件(rc-runtime control)
|-- .eslintignore: eslint 检查忽略的配置
|-- .eslintrc.js: eslint 检查的配置
|-- .gitignore: git 版本管制忽略的配置
|-- index.html: 主页面文件
|-- package.json: 应用包配置文件
|-- README.md: 应用描述说明的readme 文件
```

使用下面网址访问界面：

```http
http://localhost:8080
```

对组件的理解：

组件是一个局部功能界面，包含了所有实现该功能界面所有的相关资源（html/css/js/img等）。

2、

（1）编写组件`vue-demo\src\components\Hello.vue`

```vue
<template>
  <div>
    <h1>hello,你好,{{ name }}</h1>
    <el-radio v-model="radio" label="1">备选项1</el-radio>
    <el-radio v-model="radio" label="2">备选项2</el-radio>
  </div>
</template>

<script>
export default { //配置对象（与Vue一致）
  data() { //必须写函数
    return {
      name: "张三",
      radio: "2"
    };
  },
};
</script>

<style >
</style>
```

（2）`vue-demo\src\App.vue`引入组件

```vue
<template>
  <!-- 模板里面必须有一个根标签 div-->
  <div id="app">
    <img src="./assets/logo.png" />
    <!--路由视图，根据访问路径动态决定-->
    <!-- <router-view /> -->
    <!-- 3、使用主键标签 -->
    <Hello></Hello>
  </div>
</template>

<script>
// 1、引入组件 @代表着到src文件夹的路径
import Hello from "./components/Hello.vue"
// 向外默认暴露一个配置对象(与Vue一致)
export default {
  name: "App",
  components:{
    //2、映射主键标签
    Hello
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

（3）入口JS`vue-demo\src\main.js`编写

```js
// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
import Vue from 'vue'
import App from './App'
import router from './router'
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';

Vue.use(ElementUI);
Vue.config.productionTip = false

// 
/*
入口js:创建Vue实例
 1、main.js是一个主实例new Vue，用来挂载 app 的元素
 2、router表示一个页面跳转的路由，全写为： router:router,
 3、components是一个组件名，全写为：components: { App:App }
 4、template（用来表示渲染效果）是一个模板，使用了app组件。
*/
new Vue({
  el: '#app',
  router,
  components: { App }, //将App的组件渲染到页面中，将其映射成标签
  template: '<App/>' //使用组件标签
})
```

（4）`vue-demo\src\router\index.js`路由配置

```js
import Vue from 'vue'
import Router from 'vue-router'
import Hello from '@/components/Hello.vue'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/hello',
      name: 'Hello',
      component: Hello
    }
  ]
})
```

浏览器输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210221151849750.png" alt="image-20210221151849750" style="zoom:50%;" />

## 2、项目的打包和发布

1、打包

```
npm run build
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210221154209354.png" alt="image-20210221154209354" style="zoom:67%;" />

2、使用静态服务器工具包

```
npm install -g serve
serve dist
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210221154111014.png" alt="image-20210221154111014" style="zoom:67%;" />

访问地址：

```http
http://localhost:5000
```

不知道为什么，会遇到下面的404请求：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210221154146742.png" alt="image-20210221154146742" style="zoom:50%;" />

## 3、组件间通信

### 1、props方式

1、介绍

父组件通过`import`的方式导入子组件，并在`components`属性中注册，然后子组件就可以用标签`<child>`的形式嵌进父组件了。

2、代码

（1）子组件

```vue
<template>
  <h2>{{ message }}</h2>
</template>
<script>
export default {
  props: ["message"], //声明一个自定义的属性
};
</script>
```

（2）父组件

```vue
<template>
  <div id="app">
    <img src="./assets/logo.png" />
    <h1>我是父组件！</h1>
    <!-- 方式1：静态传递
  通过子代的自定义属性传递数据,<child>和<Child>效果相同
    -->
    <child message="静态传递：父 -> 子"></child>
    <!--
      方式2：通过v-bind进行动态传递
    -->
    <child v-bind:message=str></child>
  </div>
</template>

<script>
import Child from "./components/Child.vue";
export default {
  name: "App",
  components: {
    Child,
  },
  data() {
    return {
      str: "动态传递：" + Math.random(),
    };
  },
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

浏览器输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210221162843293.png" alt="image-20210221162843293" style="zoom:50%;" />

# 4、vue-ajax使用

## 1、介绍

1、概述

axios是官方推荐的通用的ajax 请求库。

下载：

```
npm install axios --save
```

## 2、编码

1、测试github中最受欢迎的项目

（1）测试接口

```http
https://api.github.com/search/repositories?q=v&sort=stars
```

界面返回：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210221172107585.png" alt="image-20210221172107585" style="zoom: 50%;" />

（2）测试代码

```vue
<template>
  <div id="app">
    <div v-if="!repoUrl">loading...</div>
    <div v-else>
      most star repo is <a :href="repoUrl">{{ repoName }}</a>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "App",
  data() {
    return {
      repoUrl: " ",
      repoName: "",
    };
  },
  mounted() {
    //发送Ajax请求获取数据
    const url = "https://api.github.com/search/repositories?q=v&sort=stars";
    axios
      .get(url)
      .then((response) => {
        const result = response.data;
        const mostRepo = result.items[0];
        this.repoUrl = mostRepo.html_url;
        this.repoName = mostRepo.name;
        console.log(mostRepo.html_url); // 得到返回结果数据
      })
      .catch((error) => {
        console.log(error.message);
      });
  },
};
</script>
```

浏览器显示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20210221172208529.png" alt="image-20210221172208529" style="zoom:67%;" />

# 5、vue-router

## 1、介绍

1、概述

参考网址：

```http
http://router.vuejs.org/zh-cn/
```

（1）`vue-demo\src\router\About.vue`定义路由器组件

（2）`vue-demo\src\router\index.js`路由配置

```html
routes: [
  { // 一般路由
    path: '/about',
    component: About
	},
  { // 自动跳转路由
    path: '/',
    redirect: '/about'
  }
]
```

（3）`vue-demo\src\main.js`注册路由器

```html
import router from './router'
new Vue({
	router
})
```

（4）`App.vue`组件中使用路由组件标签

```html
1. <router-link>: 用来生成路由链接
<router-link to="/xxx">Go to XXX</router-link>
2. <router-view>: 用来显示当前路由组件界面
<router-view></router-view>
```

