# 1、计算机基础

1、计算机的使用方式

我们必须要通过软件来对计算机完成各种操作， 但是软件中并不是所有的功能都会对用户开放，用户需要调用软件提供的接口（Interface 交互界面）来操作计算机。

2、环境变量

环境变量界面分成了两个部分，上边是用户环境变量，下边是系统环境变量，建议只修改用户的环境变量，不要修改系统的环境变量。

3、path环境变量

（1）path环境变量中保存的是一个一个的路径。

（2）当我们在命令行中输入一个命令（或访问一个文件时），系统会首先在当前目录下寻找，如果找到了则直接执行或打开；如果没有找到，则会依次去path环境变量的路径中去寻找，直到找到为止；如果path环境变量中的路径都没有找到，则报错。

因此，我们可以将一些经常需要访问到的文件会程序的路径，添加到path环境变量中，这样我们就可以在任意的位置访问到这些文件了。

4、计算机语言

计算机语言发展经历了三个阶段，分别如下：

（1）机器语言

机器语言通过二进制编码来编写程序，执行效率好，编写起来太麻烦。

（2）符号语言（汇编）

使用符号来代替机器码，编写程序时，不需要使用二进制，而是直接编写符号。

（3）高级语言

高级语言的语法基本和现在英语语法类似，并且和硬件的关系没有那么紧密了，也就是说我们通过高级语言开发程序可以在不同的硬件系统中执行。

5、编译型语言和解释型语言

根据转换时机的不同，语言分为两大类：

（1） 编译型语言

该语言会在代码执行前将代码编译为机器码，然后将机器码交由计算机执行，比如C语言。过程如下：

a(源码) --编译--> b(编译后的机器码)。

（2）解释性语言

该语言不会在执行前对代码进行编译，而是在执行的同时一边执行一边编译。过程如下：

a（源码）--解释器--> 解释执行 。

6、基本概念

（1）表达式

表达式就是一个类似于数学公式的东西，比如：10 + 5，8 - 4。表达式一般仅仅用了计算一些结果，不会对程序产生实质性的影响。

（2）函数

 函数就是一种语句，函数专门用来完成特定的功能。形如：xxx()。

函数一般分为两种形式：

+ 内置函数

由Python解释器提供的函数，可以在Python中直接使用。

+ 自定义函数

由程序员为了完成某个功能而自主的创建的函数。

函数一般具有两个要素，一个就是参数，即()中的内容就是函数的参数，函数中可以没有参数，也可以有多个参数，多个参数之间使用,隔开；另一个就是返回值，返回值是函数的返回结果，不是所有的函数都有返回值。

# 2、安装

1、介绍

开发环境搭建就是安装Python的解释器。

2、步骤

下载网址

```http
https://www.python.org/downloads/windows/
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201006171107479.png" alt="image-20201006171107479" style="zoom:50%;" />

（1）点击自定义安装

（2）将安装路径添加到环境变量中

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201006170337704.png" alt="image-20201006170337704" style="zoom:67%;" />

（3）CMD命令测试

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201006170533029.png" alt="image-20201006170533029" style="zoom: 50%;" />

当我们通过命令行来输入Python，所进入到的界面就是Python的交互界面。其中，`>>>`表示命令提示符，    在命令提示符后可以直接输入Python的指令！输入完的指令将会被Python的解释器立即执行。

# 3、入门介绍

## 1、基本语法

1、介绍

（1）在Python中严格区分大小写。

（2）Python中的每一行就是一条语句，每条语句以换行结束。

（3）Python中每一行语句不要过长（规范中建议每行不要超过80个字符）。

（4）一条语句可以分多行编写，多行编写时语句后边以\结尾。

（5）Python是缩进严格的语言，所以在Python中不要随便写缩进 。

（6）在Python中使用#来表示注释，#后的内容都属于注释，注释的内容将会被解释器所忽略。

2、对象

（1）概述

程序运行当中，所有的数据都是存储到内存当中然后再运行的，对象就是内存中专门用来存储指定数据的一块区域。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/图1.png" alt="图1" style="zoom: 33%;" />

（2）对象的结构

+ id（标识）

id用来标识对象的唯一性，每一个对象都有唯一的id，id是由解析器生成的，在CPython中，id就是对象的内存地址，对象一旦创建，则它的id永远不能再改变。我们可以通过下面的函数来查看对象的id：

```python
id()
```

+ type（类型）

类型用来标识当前对象所属的类型,比如：int str......，类型决定了对象有哪些功能，Python是一门强类型的语言，对象一旦创建类型便不能修改，可以通过下面的函数来查看对象的类型：

```python
type()
```

+ value（值）

值就是对象中存储的具体的数据，根据对象值是否可以改变，我们可以将对象分为两大类：可变对象和不可变对象。其中， 可变对象的值可以改变，不可变对象的值不能改变。

（3）变量和对象

 对象并没有直接存储到变量中，在Python中变量更像是给对象起了一个别名。变量中存储的不是对象的值，而是对象的id（内存地址）， 当我们使用变量时，实际上就是在通过对象id在查找对象。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201006203057253.png" alt="image-20201006203057253" style="zoom:67%;" />

3、字面量和变量

字面量就是一个一个的值，比如：1，2，3，4，5，6，‘HELLO’，字面量所表示的意思就是它的字面的值，在程序中可以直接使用字面量。

变量（variable）变量可以用来保存字面量，并且变量中保存的字面量是不定的，变量本身没有任何意思，它会根据不同的字面量表示不同的意思。

4、数据类型

（1）介绍

数据类型指的就是变量的值得类型，也就是可以为变量赋哪些值。

数值：整型，浮点型，复数和布尔型；字符串和空值。

（2）代码

```python
# 数值

# 在Python中所有的整数都是int类型
a = 10
print(a)

# 如果数字的长度过大，可以使用下划线作为分隔符
c = 123_456_789
print(c)

# d = 0123 10进制的数字不能以0开头
# 其他进制的整数，只要是数字打印时一定是以十进制的形式显示的
# 二进制 0b开头，输出为2
c = 0b10  # 二进制的10
print(c)
# 八进制 0o开头,输出为8
c = 0o10
print(c)
# 十六进制 0x开头，输出为16
c = 0x10
print(c)

# 浮点数（小数），在Python中所有的小数都是float类型
c = 1.23
print(c)

# 对浮点数进行运算时，可能会得到一个不精确的结果 0.30000000000000004
c = 0.1 + 0.2
print(c)
```

（3）变量和标识符

需要遵循的规范如下：

+ 标识符中可以含有字母、数字、_，但是不能使用数字开头。
+ 标识符不能是Python中的关键字和保留字

代码如下：

```python
a = 'hello'

"""
1、不能使用没有进行过赋值的变量
2、如果使用没有赋值过的变量，会报错 NameError: name 'b' is not defined
print(b)
"""
print(a)
```

5、字符串

（1）介绍

运算符可以对一个值或多个值进行运算或各种操作，比如 + 、-、= 都属于运算符，运算符的分类：

```
1.算术运算符
2.赋值运算符
3.比较运算符（关系运算符）
4.逻辑运算符
5.条件运算符（三元运算符）    
```

（2）代码

```python
# 格式化字符串
# 如果将两个字符串进行相加，则会自动将两个字符串拼接为一个
a = 'abc' + 'haha'
print(a)

"""
1、字符串只能不能和其他的类型进行加法运算,不然会报错
    TypeError: must be str, not int
2、代码如下
    a = 'abc' + 123
    print(a)
"""

"""
1、在创建字符串时，可以在字符串中指定占位符
    %s 在字符串中表示任意字符
    %f 浮点数占位符
    %d 整数占位符
"""
b = 'Hello %s' % '孙悟空'
print(b)
# 需要传入对应的两个占位符
b = 'hello %s 你好 %s' % ('tom', '孙悟空')
print(b)
# %3.5s字符串的长度限制在3-5之间，输出：hello abcde
b = 'hello %3.5s' % 'abcdefg'
print(b)
# 保留两位小数 输出：hello 123.46
b = 'hello %.2f' % 123.456
print(b)
# 输出整型 hello 123
b = 'hello %d' % 123.95
print(b)

# 下面是四种方式来输出，欢迎 xx 光临
name = "孙悟空"
# 方式1：拼串
print("欢迎 " + name + " 光临")
# 方式2：多个参数
print("欢迎", name, "光临")
# 方式3：占位符
print("欢迎 %s 光临" % name)
#
"""
方式4：格式化字符串
    格式化字符串，可以通过在字符串前添加一个f来创建一个格式化字符串
    在格式化字符串中可以直接嵌入变量
"""
print(f"欢迎 {name} 光临")

"""
1、字符串的复制（将字符串和数字相乘）
* 在语言中表示乘法，
如果将字符串和数字相乘，则解释器会将字符串重复指定的次数并返回。
"""
a = 'abc'
a = a * 2
# 控制台输出：abcabc
print(a)
```

6、布尔类型和空值

```python
# 布尔值和空值

"""
1、布尔值（bool）
    布尔值一共有两个 True 和 False。
    True表示真 False表示假。
2、布尔值实际上也属于整型，True就相当于1，False就相当于0。
"""
a = True
a = False
# 输出：a = False
print('a =', a)

# 输出：1
print(1 + False)

#
# None专门用来表示不存在
"""
1、None（空值）
    None专门用来表示不存在
"""
b = None
# 输出：None
print(b)
```

7、类型检查

```python
# 类型检查
"""
1、通过类型检查，可以检查值（变量）的类型
2、type()用来检查值的类型
3、该函数会将检查的结果作为返回值返回，可以通过变量来接收函数的返回值
"""
print(type(1))  # <class 'int'>
print(type(1.5))  # <class 'float'>
print(type(True))  # <class 'bool'>
print(type('hello'))  # <class 'str'>
print(type(None))  # <class 'NoneType'>
```

8、类型转换

（1）介绍

所谓的类型转换，将一个类型的对象转换为其他对象。类型转换不是改变对象本身的类型，而是根据当前对象的值创建一个新对象。

```python
# 类型转换

"""
1、类型转换包含四个函数 int() float() str() bool()
2、int()
    （1）布尔值：True -> 1   False -> 0
    （2）浮点数：直接取整，省略小数点后的内容
    （3）字符串：合法的整数字符串，直接转换为对应的数字
    （4）如果不是一个合法的整数字符串，则报错；对于其他不可转换为整型的对象，直接抛出异常 ValueError
    （5）float() 和 int()基本一致，不同的是它会将对象转换为浮点数
3、str()
    可以将对象转换为字符串。
4、bool()
    可以将对象转换为布尔值，任何对象都可以转换为布尔值。
    即：对于所有表示空性（0、None）的对象都会转换为False，其余的转换为True。
"""
a = '123'
a = int(a)
# 输出：123
print(a)

a = None
a = bool(a)
# 输出：False
print(a)
```

类型转换不是改变对象本身的类型，而是根据当前对象的值创建一个新对象9、算数运算符

```python
"""
算术运算符
1、+ 加法运算符（如果是两个字符串之间进行加法运算，则会进行拼串操作）
2、- 减法运算符
3、* 乘法运算符（如果将字符串和数字相乘，则会对字符串进行复制操作，将字符串重复指定次数）
4、/ 除法运算符，运算时结果总会返回一个浮点类型
5、// 整除，只会保留计算后的整数位，总会返回一个整型
6、** 幂运算，求一个值的几次幂
7、% 取模，求两个数相除的余数
"""
a = 10 / 3
# 输出：3.3333333333333335
print(a)

# 输出：3
a = 10 // 3
print(a)

# 求16的平方根（1/2次幂），输出：4.0
a = 16 ** 0.5
print(a)
```

10、逻辑运算符

```python
"""
逻辑运算符
逻辑运算符主要用来做一些逻辑判断。
1、not 逻辑非
    not可以对符号右侧的值进行非运算；
    对于布尔值，非运算会对其进行取反操作，True变False，False变True。
    对于非布尔值，非运算会先将其转换为布尔值，然后再取反。
2、and 逻辑与
    and可以对符号两侧的值进行与运算，与运算是找False的。
3、or 逻辑或
    or 可以对符号两侧的值进行或运算，或运算是找True的。
"""
a = 2
a = not a
# 输出：False
print(a)

result = 1 and 2  # 2
result = 1 and 0  # 0
result = 0 and 1  # 0
result = 0 and None  # 0

result = 1 or 2  # 1
result = 1 or 0  # 1
result = 0 or 1  # 1
result = 0 or None  # None
```

11、条件运算符

```python
"""
条件运算符（三元运算符）
1、语法
语句1 if 条件表达式 else 语句2
2、执行流程
    条件运算符在执行时，会先对条件表达式进行求值判断；
    如果判断结果为True，则执行语句1，并返回执行结果；
    如果判断结果为False，则执行语句2，并返回执行结果。
"""
a = 30
b = 50
max1 = a if a > b else b
# 输出：50
print(max1)
```

## 2、流程控制语句

1、if语句

（1）语法

```python
if 条件表达式 : 
	代码块
	
if 条件表达式 :
	代码块1
else :
	代码块2	
  
if 条件表达式 :
	代码块
elif 条件表达式 :
	代码块
else :
	代码块
```

（2）代码

```python
age = 68
if 18 <= age < 30:
    print('你已经成年了！')
elif age >= 30 and age < 60:
    print('你已经中年了！')
else:
    print('你已经退休了！')
```

2、while

（1）语法

```python
while 条件表达式 :
	代码块
else :
	代码块
  
# 死循环，慎用。
while True :
	print('hello')
```

执行流程如下：

while语句在执行时，会先对while后的条件表达式进行求值判断，如果判断结果为True，则执行循环体（代码块），循环体执行完毕，继续对条件表达式进行求值判断，以此类推，直到判断结果为False，则循环终止，如果循环有对应的else，则执行else后的代码块。

（2）代码

```python
i = 0
while i < 3:
    i += 1
    print(i, 'hello')
else:
    print('else中的代码块')
```

3、break和continue

（1）介绍

 break可以用来立即退出循环语句（包括else）,continue可以用来跳过当次循环，break和continue都是只对离他最近的循环起作用，pass是用来在判断或循环语句中占位的。

（2）代码

```python
# break和continue语句

"""
控制台输出：
1
2
"""
i = 1
while i <= 5:
    if i == 3:
        break
    print(i)
    i += 1
else:
    print('循环结束')

"""
输出：
2
4
5
6
循环结束
"""
i = 1
while i <= 5:
    # i自增放到这里，才可以在i==3成立的时候，继续增加，进而跳出循环
    i += 1
    if i == 3:
        continue
    print(i)
else:
    print('循环结束')

i = 0
if i < 5:
    # 这里的功能还没想好，只是简单的在这里保证程序的正常执行而已。
    pass
```

4、input输入函数

（1）介绍

该函数用来获取用户的输入，input()调用后，程序会立即暂停，等待用户输入，用户输入完内容以后，点击回车程序才会继续向下执行，用户输入完成以后，其所输入的的内容会以返回值得形式返回。

input()的返回值是一个字符串，input()函数中可以设置一个字符串作为参数，这个字符串将会作为提示文字显示。

（2）代码

```python
# input输出函数
# 获取用户输入的用户名
username = input('请输入你的用户名:')
# 判断用户名是否是admin
if username == 'admin':
    print('欢迎管理员光临！')
```

## 3、序列

1、介绍

数据结构指计算机中数据存储的方式。

序列的分类：

（1）可变序列（序列中的元素可以改变），比如列表（list）。

（2）不可变序列（序列中的元素不能改变），比如字符串（str）和元组（tuple）。

2、可变对象

列表就是一个可变对象，比如对于列表a = [1,2,3]，我们可以通过a[0] = 10 来改变对象，这个操作是在通过变量去修改对象的值，但是不会改变变量所指向的对象。当我们去修改对象时，如果有其他变量也指向了该对象，则修改也会在其他的变量中体现。

我们也可以通过a = [4,5,6] 来改变变量， 这个操作是在给变量重新赋值，会改变变量所指向的对象，为一个变量重新赋值时，不会影响其他的变量。

### 1、列表（list）

1、列表的创建

列表中可以保存多个有序的数据，是用来存储对象的对象。列表的使用包括：列表的创建和操作列表中的数据。

创建列表时需要注意下面几个问题：

（1）列表存储的数据，我们称为元素，一个列表中可以存储多个元素，也可以在创建列表时，来指定列表中的元素。

（2）当向列表中添加多个元素时，多个元素之间使用,隔开。

（3）列表中可以保存任意的对象，但是我们一般不这样做。

（4）列表中的对象都会按照插入的顺序存储到列表中，第一个插入的对象、
保存到第一个位置，第二个保存到第二个位置。我们可以通过索引（index）
来获取列表中的元素， 索引是元素在列表中的位置，列表中的每一个元素都有一个索引，索引是从0开始的整数。

代码

```python
# 列表的创建
my_list = []
# 输出：[]
print(my_list)

my_list = [10, 20, 30, 40, 50]
# 输出：[10, 20, 30, 40, 50]
print(my_list)
# 通过该函数获取列表的长度，输出：5
print(len(my_list))
```

2、切片

（1）介绍

 切片指从现有列表中，获取一个子列表。

语法1：列表[起始:结束] 

 通过切片获取元素时，会包括起始位置的元素，不会包括结束位置的元素。做切片操作时，总会返回一个新的列表，不会影响原来的列表，起始和结束位置的索引都可以省略不写。如果省略结束位置，则会一直截取到最后；如果省略起始位置，则会从第一个元素开始截取；如果起始位置和结束位置全部省略，则相当于创建了一个列表的副本。

语法2：列表[起始:结束:步长] 

步长表示，每次获取元素的间隔，默认值是1。

（2）代码

```python
stus = ['孙悟空', '猪八戒', '沙和尚', '唐僧']

# 输出：['猪八戒', '沙和尚', '唐僧']
print(stus[1:])
# 输出：['孙悟空', '猪八戒', '沙和尚']
print(stus[:3])
# 输出：['孙悟空', '猪八戒', '沙和尚', '唐僧']
print(stus[:])
# 输出：['孙悟空', '猪八戒', '沙和尚', '唐僧']
print(stus)
# 输出：['孙悟空', '沙和尚']
print(stus[::2])
```

3、列表通用操作

（1）介绍

index表示获取指定元素在列表中的第一次出现时索引。index()的第二个参数，表示查找的起始位置， 第三个参数，表示查找的结束位置。

如果要获取列表中没有的元素，会抛出异常。

s.count() 统计指定元素在列表中出现的次数。方法和函数基本上是一样，只不过方法必须通过 对象.方法() 的形式调用。

（2）代码

```python
# 列表的拼接和乘法
my_list = [1, 2, 3] + [4, 5, 6]
# 输出：[1, 2, 3, 4, 5, 6]
print(my_list)
my_list = [1, 2, 3] * 2
# 输出：[1, 2, 3, 1, 2, 3]
print(my_list)

# in的用法
stus = ['孙悟空', '猪八戒', '沙和尚', '唐僧']
# 输出：True
print('牛魔王' not in stus)
print('牛魔王' in stus)

arr = [10, 1, 2, 5, 100, 77]
# 输出：1 100
print(min(arr), max(arr))

# 输出:2
print(stus.index('沙和尚'))
# 抛出异常：ValueError: '牛魔王' is not in list
# print(stus.index('牛魔王'))

# s.count() 统计指定元素在列表中出现的次数
# 输出：1
print(stus.count('沙和尚'))
```

4、列表的修改

```python
# 列表的修改
stus = ['孙悟空', '猪八戒', '沙和尚', '唐僧']

"""
1、修改列表中的元素
2、直接通过索引来修改元素
"""
stus[0] = 'sunwukong'
# 输出：['sunwukong', '猪八戒', '沙和尚', '唐僧']
print(stus)

# 通过del来删除元素
del stus[2]
# 删除索引为2的元素，输出：['sunwukong', '猪八戒', '唐僧']
print(stus)
# 通过切片来删除元素
# del stus[0:2]

stus = ['孙悟空', '猪八戒', '沙和尚', '唐僧']

# 通过切片来修改列表
# 在给切片进行赋值时，只能使用序列
"""
使用新的元素替换旧元素
"""
stus[0:2] = ['牛魔王', '红孩儿']
print(stus)
stus[0:2] = ['牛魔王', '红孩儿', '二郎神']
print(stus)
# 当设置了步长时，序列中元素的个数必须和切片中元素的个数一致,
# 输出：['牛魔王', '红孩儿', '二郎神', '沙和尚', '唐僧']
stus[::2] = ['牛魔王', '红孩儿', '二郎神']

# 以上操作，只适用于可变序列
s = 'hello'
# s[1] = 'a' 不可变序列，无法通过索引来修改
# 可以通过 list() 函数将其他的序列转换为list
s = list(s)
s[0] = '5'
# 输出：['5', 'e', 'l', 'l', 'o']
print(s)
```

5、列表的方法

```python
# 列表的方法
stus = ['孙悟空', '猪八戒', '沙和尚']

# append()
# 向列表的最后添加一个元素
stus.append('唐僧')
# 输出：['孙悟空', '猪八戒', '沙和尚', '唐僧']
print(stus)

# insert()
# 向列表的指定位置插入一个元素
# 参数：
#   1.要插入的位置
#   2.要插入的元素
"""
1、insert()
向列表的指定位置插入一个元素。
    参数：
      1.要插入的位置
      2.要插入的元素
"""
stus = ['孙悟空', '猪八戒', '沙和尚']
stus.insert(2, '唐僧')
# 输出：['孙悟空', '猪八戒', '唐僧', '沙和尚']
print(stus)

# 使用新的序列来扩展当前序列
# 需要一个序列作为参数，它会将该序列中的元素添加到当前列表中
"""
1、extend()
    使用新的序列来扩展当前序列
    需要一个序列作为参数，它会将该序列中的元素添加到当前列表中
"""
stus = ['孙悟空', '猪八戒']
stus.extend(['唐僧', '白骨精'])
# 输出：['孙悟空', '猪八戒', '唐僧', '白骨精']
print(stus)

# 清空序列
# stus.clear()
# 输出：[]
# print(stus)

"""
1、pop()
    根据索引删除并返回被删除的元素
"""
result = stus.pop(2)  # 删除索引为2的元素
print('result =', result)
result = stus.pop()  # 删除最后一个
print('result =', result)

"""
remove()
删除指定值得元素，如果相同值得元素有多个，只会删除第一个
"""
stus.remove('猪八戒')

"""
reverse()反转列表.
"""
stus = ['孙悟空', '猪八戒', '沙和尚']
stus.reverse()
# 输出：['沙和尚', '猪八戒', '孙悟空']
print(stus)

"""
1、sort()
    用来对列表中的元素进行排序，默认是升序排列。
    如果需要降序排列，则需要传递一个reverse=True作为参数。
"""
my_list = [10, 1, 20, 3, 0, -2]
# my_list.sort()表示降序排序
my_list.sort(reverse=True)
# 输出：[20, 10, 3, 1, 0, -2]
print(my_list)
```

6、列表遍历

```python
# 遍历列表
stus = ['孙悟空', '猪八戒', '沙和尚', '唐僧']

# 通过while循环来遍历列表
i = 0
while i < len(stus):
    print(stus[i])
    i += 1

"""
1、通过for循环来遍历列表
2、语法
    for 变量 in 序列 :
        代码块
3、说明
for循环的代码块会执行多次，序列中有几个元素就会执行几次，
每执行一次就会将序列中的一个元素赋值给变量，所以我们可以
通过变量，来获取列表中的元素。
"""
print("-----------------------------------")
for s in stus:
    print(s)
```

### 2、range

1、代码

```python
# range()函数,生成一个自然数的序列

r = range(5)  # 生成一个这样的序列[0,1,2,3,4]
# 输出：range(0, 5)
print(r)
# 输出： [0, 1, 2, 3, 4]
print(list(r))

"""
该函数需要三个参数
    1.起始位置（可以省略，默认是0）
    2.结束位置
    3.步长（可以省略，默认是1）
"""
r = range(0, 10, 2)
# 输出：[0, 2, 4, 6, 8]
print(list(r))
r = range(4, 0, -1)
# 输出：[4, 3, 2, 1]
print(list(r))

print("----------------------------------")
"""
1、通过range()可以创建一个执行指定次数的for循环
2、输出：
0
1
2
3
"""
for i in range(4):
    print(i)
```

### 3、元组

1、介绍

元组是一个不可变的序列，它的操作的方式基本上和列表是一致的，因此，我们在操作元组时，把元组当成是一个不可变的列表就可以了。

一般当我们希望数据不改变时，就使用元组，其余情况都使用列表。

2、代码

```python
"""
1、创建元组
"""
my_tuple = ()  # 创建了一个空元组
# 输出：()
print(my_tuple)

# 创建了一个5个元素的元组
my_tuple = (1, 2, 3, 4, 5)
# 输出：(1, 2, 3, 4, 5)
print(my_tuple)
#  当元组不是空元组时，括号可以省略
my_tuple = 1, 2, 3, 4, 5
# 输出：(1, 2, 3, 4, 5)
print(my_tuple)

"""
1、元组的解包
解包指就是将元组当中每一个元素都赋值给一个变量。
"""
my_tuple = 10, 20, 30, 40
a, b, c, d = my_tuple
# 输出：10 20 30 40
print(a, b, c, d)

# a，b元素的交互
a = 100
b = 300
a, b = b, a
# 输出：300 100
print(a, b)

"""
在对一个元组进行解包时，变量的数量必须和元组中的元素的数量一致；
也可以在变量前边添加一个*，这样变量将会获取元组中所有剩余的元素。
"""
my_tuple = 10, 20, 30, 40
a, b, *c = my_tuple
# 输出：10 20 [30, 40]
print(a, b, c)
```

### 4、字典（dict）

1、介绍

字典属于一种新的数据结构，称为映射（mapping）,键值对（key-value）结构，字典的作用和列表类似，都是用来存储对象的容器。列表存储数据的性能很好，但是查询数据的性能的很差，这是因为在字典中每一个元素都有一个唯一的名字，通过这个唯一的名字可以快速的查找到指定的元素。

在字典中可以保存多个对象，每个对象都会有一个唯一的名字，这个唯一的名字，我们称其为键（key），通过key可以快速的查询value， 这个对象，我们称其为值（value）。 每个字典中都可以有多个键值对，而每一个键值对我们称其为一项（item）。

2、列表的创建

（1）语法

语法：

```python
{key1:value1,key2:value2......}
```

字典的值可以是任意对象，字典的键可以是任意的不可变对象（int、str、bool、tuple ...），但是一般我们都会使用str。

  字典的键是不能重复的，如果出现重复的后边的会替换到前边的。

（2）代码

```python
# 创建了一个空字典
d = {}
# 输出：{}
print(d)

d = {
    'name': '孙悟空',
    'age': 18,
    'gender': '男'
}
# 输出：{'name': '孙悟空', 'age': 18, 'gender': '男'}
print(d)

"""
需要根据键来获取值
"""
# 输出：孙悟空 18 男
print(d['name'], d['age'], d['gender'])
# 如果使用了字典中不存在的键，会报错:KeyError: 'hello'
# print(d['hello'])
```

3、字典的使用

```python
# 字典的使用
d = dict(name='孙悟空', age=18, gender='男')

# len() 获取字典中键值对的个数
# 输出：3
print(len(d))

"""
in 检查字典中是否包含指定的键
not in 检查字典中是否不包含指定的键
"""
# 输出：False
print('hello' in d)

print("----------------------------------------")
"""
1、需要根据键来获取值
2、语法
    d[key]
"""
d = dict(name='孙悟空', age=18, gender='男')
# 输出：孙悟空 18 男
print(d['name'], d['age'], d['gender'])
# 如果使用了字典中不存在的键，会报错:KeyError: 'hello'
# print(d['hello'])

"""
1、get(key[, default]) 该方法用来根据键来获取字典中的值。
    如果获取的键在字典中不存在，会返回None；
    也可以指定一个默认值，来作为第二个参数，这样获取不到值时将会返回默认值。
"""
# 输出：孙悟空
print(d.get('name'))
# 输出：默认值
print(d.get('hello', '默认值'))

"""
1、修改字典
    d[key] = value 
    如果key存在则覆盖，不存在则添加。
"""
d['name'] = 'sunwukong'
# 输出：{'name': 'sunwukong', 'age': 18, 'gender': '男'}
print(d)

"""
1、setdefault(key[, default]) 
    向字典中添加key-value，
    如果key已经存在于字典中，则返回key的值，不会对字典做任何操作，
    如果key不存在，则向字典中添加这个key，并设置value。
"""
result = d.setdefault('name', '猪八戒')
# 输出：sunwukong
print(result)

"""
1、update([other])
    将其他的字典中的key-value添加到当前字典中，
    如果有重复的key，则后边的会替换到当前的。
"""
d = {'a': 1, 'b': 2}
d2 = {'d': 4, 'e': 5}
d.update(d2)
# 输出：{'a': 1, 'b': 2, 'd': 4, 'e': 5}
print(d)

# 删除，可以使用 del 来删除字典中的 key-value
del d['a']

"""
1、pop(key[, default])
    根据key删除字典中的key-value，会将被删除的value返回。
    如果删除不存在的key，会抛出异常；如果指定了默认值，
    再删除不存在的key时，不会报错，而是直接返回默认值
"""
d = {'a': 1, 'b': 2}
result = d.pop('z', '这是默认值')
# 输出：这是默认值
print(result)

# clear()用来清空字典
d.clear()

"""
1、copy()
用于对字典进行浅复制，复制以后的对象，和原对象是独立，修改一个不会影响另一个；
浅复制会简单复制对象内部的值。
"""
d = {'a': 1, 'b': 2}
d2 = d.copy()
# 输出：{'a': 1, 'b': 2}
print(d2)

# 报错：AttributeError: 'str' object has no attribute 'copy'
d = "str"
d2 = d.copy()
print(d2)
```

4、字典的遍历

```python
# 字典的遍历

"""
1、keys()
    该方法会返回字典的所有的key，
    会返回一个序列，序列中保存有字典的所有的键。
"""
d = {'name': '孙悟空', 'age': 18, 'gender': '男'}

# 通过遍历keys()来获取所有的键
for k in d.keys():
    print(k, d[k])

"""
1、values()
该方法会返回一个序列，序列中保存有字典的左右的值
"""
print("---------------------------")
for v in d.values():
    print(v)

"""
1、items()
    该方法会返回字典中所有的项，它会返回一个序列，序列中包含有双值子序列。
    双值分别是，字典中的key和value。
2、输出
    name = 孙悟空
    age = 18
    gender = 男
"""
print("---------------------------")
for k, v in d.items():
    print(k, '=', v)
```

### 5、集合（set）

1、介绍

集合和列表非常相似，不同点如下：

（1）集合中只能存储不可变对象。

（2）集合中存储的对象是无序（不是按照元素的插入顺序保存）。

（3）集合中不能出现重复的元素。

2、集合的创建和操作

```python
# 集合的创建
s = set()
# 输出：set()
print(s)
s = set('hello')
# 输出：{'o', 'l', 'e', 'h'}
print(s)
s = set([1, 2, 3, 4, 5])
print(s)
# 使用set()将字典转换为集合时，只会包含字典中的键
# 输出：{1, 2, 3, 4, 5}
s = set({'a': 1, 'b': 2, 'c': 3})
# 输出：{'a', 'b', 'c'}
print(s)

# 使用in和not in来检查集合中的元素
print('c' in s)

# 使用len()来获取集合中元素的数量
print(len(s))

# add() 向集合中添加元素
s.add(10)
# 输出：{'a', 'c', 10, 'b'}
print(s)


"""
1、update() 
    将一个集合中的元素添加到当前集合中，
    update()可以传递序列或字典作为参数，字典只会使用键。
"""
s2 = set('hello')
s.update(s2)
# 输出：{10, 'e', 'c', 'b', 'l', 'h', 'o', 'a'}
# 将 'hello' 字符串分成字符后插入
print(s)

s = set([1, 2, 3, 4, 5])
# remove()删除集合中的指定元素
s.remove(1)
print(s)

# clear()清空集合
s.clear()
```

3、集合的运算

```python
# 集合的运算

# 在对集合做运算时，不会影响原来的集合，而是返回一个运算结果
s = {1, 2, 3, 4, 5}
s2 = {3, 4, 5, 6, 7}

# & 交集运算
# 输出：{3, 4, 5}
result = s & s2
print(result)

# | 并集运算
result = s | s2  # {1,2,3,4,5,6,7}
# 输出：{1,2,3,4,5,6,7}
print(result)

# - 差集
result = s - s2
# 输出：{1, 2}
print(result)

# ^ 异或集 获取只在一个集合中出现的元素
result = s ^ s2
# 输出：{1, 2, 6, 7}
print(result)

"""
<= 检查一个集合是否是另一个集合的子集
如果a集合中的元素全部都在b集合中出现，那么a集合就是b集合的子集，b集合是a集合超集
"""
a = {1, 2, 3}
b = {1, 2, 3, 4, 5}

result = a <= b
# 输出：True
print(result)
result = {1, 2, 3, 4, 5} <= {1, 2, 3}
# 输出：False
print(result)

"""
< 检查一个集合是否是另一个集合的真子集
如果超集b中含有子集a中所有元素，并且b中还有a中没有的元素，则b就是a的真超集，a是b的真子集
"""
result = {1, 2, 3} < {1, 2, 3}  # False

"""
>= 检查一个集合是否是另一个的超集
> 检查一个集合是否是另一个的真超集
"""
result = {1, 2, 3, 4, 5} >= {1, 2, 3}
# 输出：True
print(result)
```



































































































