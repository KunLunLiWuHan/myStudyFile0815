# 1、介绍

1、分类

 数据库主要分成两种：

（1）关系型数据库

包括MySQL、Oracle、DB2、SQL Server等，其中关系数据库中全都是表。

（2）非关系型数据库

包括MongoDB、Redis等，其中其为键值对数据库。

MongoDB数据库又是文档数据库。

2、概述

MongoDB是为快速开发互联网Web应用而设计的数据库系统。

MongoDB的数据模型是面向文档的，所谓文档是一种类似于JSON的结构，简单理解MongoDB这个数据库中存的是各种各样的JSON。（BSON）

3、基本概念

MongoDB包含三个比较重要的元素：

+ 数据库（database）

数据库是一个仓库，在仓库中可以存放集合。

+ 集合（collection）

 集合类似于数组，在集合中可以存放文档。

+ 文档（document）

文档数据库中的最小单位，我们存储和操作的内容都是文档。

在MongoDB中，数据库和集合都不需要手动创建，当我们创建文档时，如果文档所在的集合或数据库不存在，MongoDB会自动创建数据库和集合。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201014155111724.png" alt="image-20201014155111724" style="zoom:67%;" />

# 2、安装和部署

## 1、下载

1、下载地址

```http
https://www.mongodb.com/download-center/community/releases
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201014140358948.png" alt="image-20201014140358948" style="zoom:67%;" />

2、下载版本

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201014140238345.png" alt="image-20201014140238345" style="zoom: 50%;" />

## 2、安装

1、下载注意事项

（1）安装过程中选择自定义安装，将安装包安装到指定路径下。

（2）取消Install MongDB Compass，不然安装过程会很慢。

2、将MongoDB的bin目录添加到path下

```
D:\03Enviroment\25Mongodb\install\bin
```

3、创建MongoDB的数据库数据位置

```
D:\03Enviroment\25Mongodb\data\db
```

4、在CMD面板启动服务端MongDB

```shell
//端口默认为：27017
mongod --dbpath D:\03Enviroment\25Mongodb\data --port xx
```

5、启动客户端

```
mongo
```

需要重新开启一个窗口。此外，我们可以在mongo中进行简单的操作，比如

```sql
show databases; # 查看数据库
```

我们在浏览器端输入下面的网址，来查看MongoDB的运行状态。

```http
http://localhost:27017/
```

结果显示：

```
It looks like you are trying to access MongoDB over HTTP on the native driver port.
```

6、 将MongoDB设置为系统服务，自动在后台启动

（1）关闭MongoDB服务器和客户端

（2）创建数据和日志文件夹

```
D:\03Enviroment\25Mongodb\data\db
D:\03Enviroment\25Mongodb\data\log\mongod.log
```

（3）在bin的同级目录下创建配置文件mongod.cfg，并添加如下内容：

```
systemLog:
	destination: file
	path: D:\03Enviroment\25Mongodb\data\log\mongod.log
storage:
	dbPath: D:\03Enviroment\25Mongodb\data\db
```

（4）以管理员身份打开CMD窗口，执行下面的指令

```shell
mongod --bind_ip 0.0.0.0 --logpath D:\03Enviroment\25Mongodb\data\log\mongod.log --logappend --dbpath D:\03Enviroment\25Mongodb\data\db --port 27017 --serviceName "MongoDB" --serviceDisplayName "MongoDB" --install
```

如果在执行命令的过程中，遇到问题，可以执行下面的指令，将MongoDB删除，进行重新安装。

```shell
sc delete MongoDB
```

（5）在服务中启动MongoDB服务。

7、连接图形化工具

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201014154553237.png" alt="image-20201014154553237" style="zoom: 50%;" />

# 3、基本操作

1、基本指令

```sql
-- 显示当前的数据库
show databases

-- 进入指定的数据库中
use 数据库名

-- 表示当前所处的数据库位置
db

-- 显示数据库中的所有集合
show collections

-- 向集合中插入一个文档
db.<collection>.insert(doc)

--查询当前集合中的所有的文档
db.<collection>.find()
```

2、测试

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201014154949912.png" alt="image-20201014154949912" style="zoom: 50%;" />

# 4、插入文档

1、代码

```java
/**
* 1、当我们向集合中插入文档时，如果没有给文档指定_id属性，数据库
* 会自动为文档添加_id。该属性作为文档的唯一标识。
* 2、_id属性可以自己指定，如果我们指定了，数据库就不会添加了。
* 如果指定_id属性，要确保_id属性的唯一性。
* 3、db.stus.insertOne() 向文档中插入一个对象
* 4、db.stus.insertMany() 向文档中插入多个对象
*/
//向集合中插入一条数据
db.stus.insert({
    name:"猪八戒",
    age:"17"
})

//向集合中插入多条数据,应该外面放个数组。不然会失效
db.stus.insert([
    {name:"猪八戒",age:"20"},
    {name:"沙和尚",age:"40"},
    {name:"唐僧",age:"60"}
]);

db.stus.insertOne()
db.stus.insertMany([
    {name:"猪八戒",age:"20"},
    {name:"沙和尚",age:"40"},
    {name:"唐僧",age:"60"}
])


db.stus.find()
```

# 5、查询文档

1、基本代码

```java
/**
* 1、查询
* db.stus（collection）.find()
* 2、find()用来查询集合中所有符合条件的文档，
*  可以接收一个对象作为参数。
*  {} 表示插叙集合中的所有文档
*  {属性:值} 查询指定属性的文档
* 3、db.stus（collection）.findOne()
*  返回查询集合中符合条件的第一个文档
*  findOne（）返回的是一个文档对象
* 4、db.stus（collection）.findMany()
*  返回查询集合中符合条件的多个文档
*/
db.stus.find({name:"猪八戒"})
db.stus.find({name:"猪八戒",age:"17"})
db.stus.findOne({name:"猪八戒"})
```

2、升序

```java
/*
    1、查找文档时，默认情况是按照_id的值进行排列（升序）
    2、sort（）可以指定文档的排序规则，需要传递一个对象参数。
    其中，1表示升序，-1表示降序。
*/
db.stus.insert([
    {name:"猪八戒",age:12},
    {name:"孙悟空",age:30},
    {name:"沙和尚",age:40},
    {name:"唐僧",age:24},
])

//升序排列
db.stus.find().sort({age:1})
```

# 6、更新操作

1、代码

```java
/**
* 1、更新操作
* $set 可以用来修改文档中指定属性
* $unset 用来删除文档中的指定属性
*/
db.stus.updateOne({name:"沙和尚"},{$set:{age:"28"} })
db.stus.updateOne({_id:ObjectId("5f86af3d54e6e9770ab502ca")},{$unset:{age:"17"} })
```

# 7、修改操作

1、代码

```java
/**
* 1、更新操作 db.stus.update
* $set 可以用来修改文档中指定属性
* $unset 用来删除文档中的指定属性
* 2、update() 默认只更改表格中的第一条数据，
*/
db.stus.update({name:"沙和尚"},{$set:{age:"28"}})
db.stus.updateOne({_id:ObjectId("5f86af3d54e6e9770ab502ca")},{$unset:{age:"17"} })
//更新多条数据
db.stus.updateMany(
    {name:"猪八戒"},
    {$set:{age:"100"}},
)
db.stus.find()
```

# 8、删除操作

1、代码

```java
/**
* 1、删除文档
* db.stus.remove()会根据条件删除所有符合条件的数据。
*/
db.stus.remove({name:"猪八戒"})
//删除所有文档（性能略差）
db.stus.remove({})
db.stus.insert([
    {name:"沙和尚"},
    {name:"沙和尚"},
    {name:"沙和尚"}
])
db.stus.find()
//删除集合
db.stus.drop();
show collections;
```

# 9、文档间的关系

1、代码

```java
/*
1、文档之间的关系
(1)一对一（one to one）
    比如：丈夫和妻子。
    在MongoDB中可以通过内嵌文档的形式来体现一对一的关系
(2)一对多（one to many/或者many to one）
    用户和订单
(3)多对多
    分类和商品
*/
//一对一
db.wifeAndHusband.insert([
    {
        name:"黄蓉",
        husband:{
            name:"郭靖"
        }
    },{
        name:"潘金莲",
        husband:{
            name:"西门庆"
        }
    }
])
db.wifeAndHusband.find()

//一对多
db.user.insert([
    {username:"swk"},
    {username:"zbj"}
])
db.user.find()

//order 中通过 user_id关联 user集合
db.order.insert({
    list:["西瓜","桃子"],
    user_id:ObjectId("5f86e7d5ca7c073fd8c14946")
})
db.order.find().limit(1)

//多对多
db.teachers.insert([
    {name:"洪七公"},
    {name:"黄药师"},
    {name:"龟仙人"}
])
db.teachers.find()

db.students.insert([
    {
        name:"郭靖",
        tech_id:[
            ObjectId("5f86eaf6ca7c073fd8c14949"),
            ObjectId("5f86eaf6ca7c073fd8c1494a")
        ]
    },
       {
        name:"swk",
        tech_id:[
            ObjectId("5f86eaf6ca7c073fd8c14949"),
            ObjectId("5f86eaf6ca7c073fd8c1494a"),
            ObjectId("5f86eaf6ca7c073fd8c1494b")
        ]
    },
])

db.students.find()
```



















































