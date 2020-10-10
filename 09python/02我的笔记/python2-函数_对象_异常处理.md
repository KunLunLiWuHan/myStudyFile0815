# 1、函数

## 1、介绍

1、概述

函数也是一个对象，函数可以用来保存一些可执行的代码，并且可以在需要时，对这些语句进行多次的调用。

创建函数语法：

```python
def 函数名([形参1,形参2,...形参n]) :
  代码块
```

（1）函数名必须要符号标识符的规范，可以包含字母、数字、下划线、但是不能以数字开头。

（2）函数中保存的代码不会立即执行，需要调用函数代码才会执行。

（3）调用函数如下：

```
函数对象()
```

2、函数的参数

（1）- 在定义函数时，可以在函数名后的()中定义数量不等的形参，多个形参之间使用,隔开。

（2）形参（形式参数），定义形参就相当于在函数内部声明了变量，但是并不赋值。

（3）实参（实际参数），如果函数定义时，指定了形参，那么在调用函数时也必须传递实参，实参将会赋值给对应的形参，简单来说，有几个形参就得传几个实参。

3、函数式编程

（1）高阶函数

高阶函数至少要符合以下两个特点中的一个：

+ 接收一个或多个函数作为参数。
+ 将函数作为返回值返回 。

（2）在Python中，函数是一等对象，对象一般都会具有如下特点：

+ 对象是在运行时创建的。
+ 能赋值给变量或作为数据结构中的元素。

+ 能作为参数传递。
+ 能作为返回值返回。

## 2、代码

1、函数介绍

```python
"""
1、定义一个函数
    fn是函数对象  fn()调用函数。
2、定义函数时指定形参a,b。
"""


def fn(a, b):
    print(a, "+", b, "=", a + b)


#
#
"""
1、调用函数时，来传递实参
2、实参的传递方式
        位置参数就是将对应位置的实参复制给对应位置的形参，
        第一个实参赋值给第一个形参，第二个实参赋值给第二个形参。
3、输出：输出：10 + 20 = 30
4、函数在调用时，解析器不会检查实参的类型。
"""
fn(10, 20)

"""
1、定义函数时，可以为形参指定默认值。
指定了默认值以后，如果用户传递了参数则默认值没有任何作用；
如果用户没有传递，则默认值就会生效。
"""


def fn2(a, b=20):
    print('a =', a)
    print('b =', b)


"""
输出：
a = 1
b = 20
"""
fn2(1)
```

2、函数的形参

```python
"""
1、不定长参数
    在定义函数时，可以在形参前边加上一个*，这样这个形参将会获取到所有的实参。
2、定义一个函数，可以求任意个数字的和
    *nums会接受所有的位置实参，并且会将这些实参统一保存到一个元组中（装包）。
"""


def sum1(*nums):
    # 定义一个变量，来保存结果
    result = 0
    # 遍历元组，并将元组中的数进行累加
    for n in nums:
        result += n
    print(result)

"""
1、带星号的形参只能有一个，可以和其他参数配合使用。
"""
# 第一个参数给a，第二个参数给b，剩下的都保存到c的元组中
def fn1(a, b, *c):
    print('a =', a)
    print('b =', b)
    print('c =', c)


"""
输出结果如下：
a = 1
b = 2
c = (3, 4)
"""
fn1(1, 2, 3, 4)

"""
1、可变参数不是必须写在最后，但是注意，带*的参数后的所有参数
，必须以关键字参数的形式传递。
"""


# 第一个参数给a，剩下的位置参数给b的元组，c必须使用关键字参数。
def fn2(a, *b, c):
    print('a =', a)
    print('b =', b)
    print('c =', c)


"""
输出结果如下：
a = 1
b = (2, 3)
c = 4
"""
fn2(1, 2, 3, c=4)

"""
1、**形参可以接收其他的关键字参数，它会将这些参数统一保存到一个字典中。
2、**形参只能有一个，并且必须写在所有参数的最后。
3、字典的key就是参数的名字，字典的value就是参数的值。
"""


def fn3(b, c, **a):
    print('a =', a, type(a))
    print('b =', b)
    print('c =', c)


"""
输出结果如下：
a = {'d': 2, 'e': 10, 'f': 20} <class 'dict'>
b = 1
c = 3
"""
fn3(b=1, d=2, c=3, e=10, f=20)

"""
1、参数的解包
    传递实参时，也可以在序列类型的参数前添加星号，
    这样他会自动将序列中的元素依次作为参数传递。
    这里要求序列中元素的个数必须和形参的个数的一致。
"""

def fn4(a, b, c):
    print('a =', a)
    print('b =', b)
    print('c =', c)


# 创建一个元组
t = (10, 20, 30)
"""
输出结果：
a = 10
b = 20
c = 30
"""

fn4(*t)

# 创建一个字典
d = {'a': 100, 'b': 200, 'c': 300}
"""
1、通过 **来对一个字典进行解包操作
2、结果如下：
a = 100
b = 200
c = 300
"""

fn4(**d)
```

3、函数的返回值

```python
"""
1、可以通过 return 来指定函数的返回值，也可以通过一个变量来接收函数的返回值。
2、return 后边跟什么值，函数就会返回什么值，
return 后边可以跟任意的对象，返回值甚至可以是一个函数。
3、return后的代码都不会执行，return 一旦执行函数自动结束。
"""


# 如果仅仅写一个return 或者 不写return，则相当于return None
def fn():
    a = 10
    return


# 这个函数的执行结果就是它的返回值
result = fn()
# 输出：None
print(result)
```

4、函数帮助和文档字符串

```python
"""
1、help()是Python中的内置函数
2、通过help()函数可以查询python中的函数的用法
3、语法：
    help(函数对象)
"""
help(print) # 获取print()函数的使用说明


"""
1、文档字符串（doc str）
2、在定义函数时，可以在函数内部编写文档字符串，文档字符串就是函数的说明。
    当我们编写了文档字符串时，就可以通过help()函数来查看函数的说明。
    文档字符串非常简单，其实直接在函数的第一行写一个字符串就是文档字符串。
"""


def fn(a: int, b: bool, c: str = 'hello') -> int:
    """
    这是一个文档字符串的示例
    函数的作用；...
    函数的参数：...
    """
    return 10


"""
控制台输出：
    fn(a:int, b:bool, c:str='hello') -> int
        这是一个文档字符串的示例
        函数的作用；...
        函数的参数：...
"""
help(fn)
```

## 3、命名空间

1、介绍

命名空间指的是变量存储的位置，每一个变量都需要存储到指定的命名空间当中。每一个作用域都会有一个它对应的命名空间。全局命名空间，用来保存全局变量。函数命名空间用来保存函数中的变量。

命名空间实际上就是一个字典，是一个专门用来存储变量的字典。

2、代码

```python
"""
1、locals()用来获取当前作用域的命名空间。
2、如果在全局作用域中调用locals()则获取全局命名空间，如果在函数作用域中调用locals()则获取函数命名空间
返回的是一个字典。
"""
scope = locals()  # 当前命名空间，也就是全局命名空间
# 输出：{'__name__': '__main__', '__doc__':
print(scope)


def fn():
    a = 10
    scope = locals()  # 在函数内部调用locals()会获取到函数的命名空间
    # 输出：{'a': 10}
    print(scope)
    # scope['b'] = 20 # 可以通过scope来操作函数的命名空间，但是不建议这么做

    # globals() 函数可以用来在任意位置获取全局命名空间
    global_scope = globals()
    # 输出：{'__name__': '__main__', '__doc__':
    print(global_scope)
    # print(global_scope['a']) # 报错：KeyError: 'a'
    # global_scope['a'] = 30
    # print(global_scope['a'])# 可以执行


fn()
```

## 4、递归

1、介绍

递归是解决问题的一种方式，它和循环很像。它的整体思想是，将一个大问题分解为一个个的小问题，直到问题无法分解时，再去解决问题。

 递归式函数的两个要件：

（1）基线条件

 问题可以被分解为的最小问题，当满足基线条件时，递归就不在执行了。

（2）递归条件

将问题继续分解的条件。

2、代码

```python
# 求任意数的阶乘
def factorial(n):
    # 基线条件 判断n是否为1，如果为1则此时不能再继续递归
    if n == 1:
        # 1的阶乘就是1，直接返回1
        return 1

    # 递归条件
    return n * factorial(n - 1)


print(factorial(3))
```

## 5、高阶函数

1、代码

```python
# 创建一个列表
list_test = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]


# 定义一个函数，用来检查一个任意的数字是否是偶数
def fn2(i):
    if i % 2 == 0:
        return True
    return False


# 该函数用来检查指定的数字是否大于5
def fn3(i):
    if i > 5:
        return True
    return False


#  lst：要进行筛选的列表
def fn4(func, lst):
    # 创建一个新列表
    new_list = []

    # 对列表进行筛选
    for n in lst:
        # 判断n的奇偶
        if func(n):
            new_list.append(n)
    # 返回新列表
    return new_list


# 输出：[2, 4, 6, 8, 10]
print(fn4(fn2, list_test))

"""
1、filter()
filter()可以从序列中过滤出符合条件的元素，保存到一个新的序列中
    参数：
        1.函数，根据该函数来过滤序列（可迭代的结构）
        2.需要过滤的序列（可迭代的结构）
    返回值：
      过滤后的新序列（可迭代的结构）
2、fn2是作为参数传递进filter()函数中，而fn2实际上只有一个作用，
就是作为filter()的参数，filter()调用完毕以后，fn2就已经没用。
"""
# 输出：<filter object at 0x00000221B178BA90>
print(filter(fn2, list_test))
# 输出：[2, 4, 6, 8, 10]
print(list(filter(fn2, list_test)))
```

6、Lambda表达式

```python
# 第一种方式：定义函数的方式
def fn(a, b):
    return a + b


"""
1、语法
    lambda 参数列表 : 返回值
2、 匿名函数一般都是作为参数使用，其他地方一般不会使用
"""
# 输出：30
print((lambda a, b: a + b)(10, 20))

lst = [4, 5, 6]
r = filter(lambda i: i > 5, lst)
# 输出：[6]
print(list(r))

"""
1、map()
2、该函数可以对可跌倒对象中的所有元素做指定的操作，然后将其添加到一个新的对象中返回
"""
lst = [1, 2, 3, 4, 5]

r = map(lambda i: i * 2, lst)
# 输出:[2, 4, 6, 8, 10]
print(list(r))
```

## 6、sort

```python
"""
1、sort()该方法用来对列表中的元素进行排序。
2、sort()方法默认是直接比较列表中的元素的大小。
3、在sort()可以接收一个关键字参数 ， key
    key需要一个函数作为参数，当设置了函数作为参数，
    每次都会以列表中的一个元素作为参数来调用函数，
    并且使用函数的返回值来比较元素的大小。
4、会对原来的函数进行影响，在原来的基础上产生一个排序后的对象。
"""

lst = [2, 5, '1', 3, '6', '4']
# 按照int类型进行排序，首先将字符串转化为int类型
lst.sort(key=int)
# 输出：['1', 2, 3, '4', 5, '6']
print(lst)

"""
1、sorted()
2、该函数和sort()的用法基本一致，但是sorted()可以对任意的序列进行排序。
3、 并且使用sorted()排序不会影响原来的对象，而是返回一个新对象
"""
lst = [2, 5, '1', 3, '6', '4']

print('排序前:', lst)
# 输出：['1', 2, 3, '4', 5, '6']
print(sorted(lst, key=int))
print('排序后:', lst)
```

## 7、闭包

1、介绍

（1）概述

将函数作为返回值返回，也是一种高阶函数，这种函数我们称之为闭包，通过闭包可以创建一些只有当前函数能访问的变量，或者也可以将一些私有的数据藏到闭包中。

（2）形成闭包的要件

+ 函数嵌套。

+ 将内部函数作为返回值返回。

+ 内部函数必须要使用到外部函数的变量。

2、代码

```python
# 闭包，求一个列表中的平均数
def make_averager():
    # 创建一个列表，用来保存数值，嵌套的函数可以访问该列表。
    nums = []

    # 创建一个函数，用来计算平均值
    def averager(n):
        # 将n添加到列表中
        nums.append(n)
        # 求平均值
        return sum(nums) / len(nums)

    return averager


# 获取闭包中的内部函数
average_fun = make_averager()
# 输出：10.0
print(average_fun(10))
# 输出：15.0
print(average_fun(20))
```

## 8、装饰器

1、介绍

通过装饰器，可以在不修改原来函数的情况下来对函数进行扩展，在开发中，我们都是通过装饰器来扩展函数的功能的。

在定义函数时，可以通过@装饰器，来使用指定的装饰器，来装饰当前的函数，我们也可以同时为一个函数指定多个装饰器，这样函数将会安装从内向外的顺序被装饰 。

2、代码

```python
# 我们希望在不修改原函数的情况下，来对函数进行扩展
"""
1、 old 要扩展的函数对象。begin_end 装饰器
2、作用：用来对其他函数进行扩展，使其他函数可以在执行前打印开始执行，
执行后打印执行结束。
"""


def begin_end(old):
    # 创建一个新函数
    def new_function(*args, **kwargs):
        print('开始执行~~~~')
        # 调用被扩展的函数
        result = old(*args, **kwargs)
        print('执行结束~~~~')
        # 返回函数的执行结果
        return result

    # 返回新函数
    return new_function

@begin_end
def say_hello():
    print('使用say_hello~方法')


"""
输出： 
    开始执行~~~~
    使用say_hello~方法
    执行结束~~~~
"""
say_hello()
```

# 2、对象

## 1、介绍

1、面向对象（oop）

所谓的面向对象的语言，简单理解就是语言中的所有操作都是通过对象来进行的。该语言关注的是对象，而不关注过程。它的编程思想，将所有的功能统一保存到对应的对象中，要使用某个功能，直接找到对应的对象即可。

这种方式编写的代码，比较容易阅读，并且比较易于维护，容易复用。但是这种方式编写，不太符合常规的思维，编写起来稍微麻烦一点

面向过程的编程的语言，将我们的程序的逻辑分解为一个一个的步骤，通过对每个步骤的抽象，来完成程序。 这种编程方式，符合我们人类的思维，编写起来相对比较简单。

但是这种方式编写代码的往往只适用于一个功能，如果要在实现别的功能，即使功能相差极小，也往往要重新编写代码，因此，它可复用性比较低，并且难于维护。

2、类

（1）概述

类，简单理解它就相当于一个图纸。在程序中我们需要根据类来创建对象。如果多个对象是通过一个类创建的，我们称这些对象是一类对象。

类就是一个用来创建对象的对象，类是type类型的对象，定义类实际上就是定义了一个type类型的对象。

像 int() float() bool() str() list() dict() .... 这些都是类。

```python
# 创建一个int类的实例 等价于 a = 10
 a = int(10)
```

我们也称对象是类的实例（instance）。

（2）类的定义和使用

```python
a = int(10)  # 创建一个int类的实例
b = str('hello')  # 创建一个str类的实例


class Person:
    # 在类的代码块中，我们可以定义变量和函数
    # 在类中我们所定义的变量，将会成为所有的实例的公共属性
    # 所有实例都可以访问这些变量
    name = 'swk'  # 公共属性，所有实例都可以访问

    # 在类中也可以定义函数，类中的定义的函数，我们称为方法
    # 这些方法可以通过该类的所有实例来访问
    def say_hello(self):
        # 方法每次被调用时，解析器都会自动传递第一个实参
        # 第一个参数，就是调用方法的对象本身，
        #   如果是p1调的，则第一个参数就是p1对象
        #   如果是p2调的，则第一个参数就是p2对象
        # 一般我们都会将这个参数命名为self

        # say_hello()这个方法，可以显示如下格式的数据：
        #   在方法中不能直接访问类中的属性
        print('你好！我是 %s' % self.name)


# 创建类的实例
p1 = Person()

# 修改属性前，输出：你好！我是 swk
p1.say_hello()
p1.name = '猪八戒'
# 修改属性前，输出：你好！我是 猪八戒
p1.say_hello()
```

（3）对象的初始化

```python
class Person:

    # init会在对象创建以后离开执行
    # init可以用来向新创建的对象中初始化属性
    # 调用类创建对象时，类后边的所有参数都会依次传递到init()中
    def __init__(self, name):
        # 通过self向新建的对象中初始化属性
        self.name = name

    def say_hello(self):
        print('大家好，我是%s' % self.name)


"""
1、目前来讲，对于Person类来说name是必须的，并且每一个对象中的name属性基本上都是不同，
而我们现在是将name属性在定义为对象以后，手动添加到对象中，这种方式很容易出现错误。
2、我们希望，在创建对象时，必须设置name属性，如果不设置对象将无法创建，
并且属性的创建应该是自动完成的，而不是在创建对象以后手动完成。
"""
# 创建对象时为对象赋属性
p1 = Person('孙悟空')
p1.say_hello()
```

3、类的定义

（1）概述

在类的代码块中，我们可以定义变量和函数。

变量会成为该类实例的公共属性，所有的该类实例都可以通过 对象.属性名 的形式访问。

函数会成为该类实例的公共方法，所有该类实例都可以通过 对象.方法名() 的形式调用方法。方法调用时，第一个参数由解析器自动传递，所以定义方法时，至少要定义一个形参！ 

（2）属性和方法查找的流程

 当我们调用一个对象的属性时，解析器会先在当前对象中寻找是否含有该属性，如果有，则直接返回当前的对象的属性值， 如果没有，则去当前对象的类对象中去寻找，如果有则返回类对象的属性值，如果类对象中依然没有，则报错！

（3）一般情况下，属性保存到实例对象中，而方法需要保存到类对象中 。

4、创建对象的流程

```python
p1 = Person()
```

（1）创建一个变量。

（2）在内存中创建一个新对象。

（3）init__(self)方法执行。

（4）将对象的id赋值给变量。

## 2、封装

1、介绍

 封装指的是隐藏对象中一些不希望被外部所访问到的属性或方法。将对象的属性名，修改为一个外部不知道的名字，这样的话我们就可以隐藏一个对象中的属性。我们需要在类中提供一个getter（获取对象中的指定属性（get _属性名））和 setter（用来设置对象的指定属性（set _属性名））方法使外部可以访问到属性。

2、代码

（1）基本方式

```python
"""
1、使用__开头的属性，实际上依然可以在外部访问，所以这种方式我们一般不用，
一般我们会将一些私有属性（不希望被外部访问的属性）以_开头，
一般情况下，使用_开头的属性都是私有属性，没有特殊需要不要修改私有属性。
"""


class Person:
    def __init__(self, name):
        self._name = name

    def get_name(self):
        return self._name

    def set_name(self, name):
        self._name = name


p = Person('孙悟空')
# 输出：孙悟空
print(p.get_name())
p.set_name("猪八戒")
# 输出：猪八戒
print(p.get_name())
```

（2）注解方式

```python
class Person:
    def __init__(self, name):
        self._name = name

    # property装饰器，用来将一个get方法，转换为对象的属性
    # 添加为property装饰器以后，我们就可以像调用属性一样使用get方法
    # 使用property装饰的方法，必须和属性名是一样的
    @property
    def name(self):
        print('get方法执行了~~~')
        return self._name

    # setter方法的装饰器：@属性名.setter
    @name.setter
    def name(self, name):
        print('setter方法调用了')
        self._name = name


p = Person('猪八戒')
# 使用的是setter方法
p.name = '孙悟空'
# 使用的是注解方法
print(p.name)
```

## 3、继承

1、j入门代码

```python
# 定义一个类 Animal（动物）
#   这个类中需要两个方法：run() sleep()
class Animal:
    def run(self):
        print('动物会跑~~~')

    def sleep(self):
        print('动物睡觉~~~')


# 子类继承父类
class Dog(Animal):
    def bark(self):
        print('汪汪汪~~~')
    """
    1、override
    如果在子类中如果有和父类同名的方法，则通过子类实例去调用方法时，
    会调用子类的方法而不是父类的方法，这个特点我们成为叫做方法的重写。
    """
    def run(self):
        print('狗跑~~~~')


d = Dog()
# 使用子类自己的方式，输出：狗跑~~~~
d.run()
# 输出：汪汪汪~~~
d.bark()

"""
1、issubclass() 
该函数用来检查一个类是否是另一个类的子类。
"""
# 输出：True
print(issubclass(Dog, Animal))

"""
1、isinstance()
该函数用来检查一个对象是否是一个类的实例。
如果这个类是这个对象的父类，也会返回True
"""
# 输出：True
print(isinstance(d, Dog))
```

2、super介绍

```python
class Animal:
    def __init__(self, name):
        self._name = name

    @property
    def name(self):
        return self._name

    @name.setter
    def name(self, name):
        self._name = name


# 父类中的所有方法都会被子类继承，包括特殊方法（__init__），也可以重写特殊方法
class Dog(Animal):
    def __init__(self, name, age):
        """
        1、super() 可以用来获取当前类的父类，然后直接调用父类的__init__方法
            来初始化父类中定义的属性。
            并且通过super()返回对象调用父类方法时，不需要传递self
        """
        super().__init__(name)
        self._age = age

    @property
    def age(self):
        return self._age

    @age.setter
    def age(self, age):
        self._age = age


d = Dog('旺财', 18)
print(d.name)  # 旺财
print(d.age)  # 18
```

3、多重继承

（1）介绍

在Python中是支持多重继承的，也就是我们可以为一个类同时指定多个父类。此时，我们 可以在类名的()后边添加多个类，来实现多重继承。

在开发中没有特殊的情况，应该尽量避免使用多重继承，因为多重继承会让我们的代码过于复杂。

（2）代码

```python
class A(object):
    def test(self):
        print('A中的test()方法..........')


class B(object):
    def test(self):
        print('B中的test()方法.........')


# C类继承A，B两个类
class C(A, B):
    pass


# 类名.__bases__ 这个属性可以用来获取当前类的所有父类
# 输出：(<class '__main__.A'>, <class '__main__.B'>)
print(C.__bases__)
# 输出：(<class 'object'>,)
print(B.__bases__)
```

## 4、多态

（1）介绍

多态从字面上理解是多种形态，比如狗具有多种形态，狼狗、藏獒、哈士奇、古牧等。同理， 一个对象可以以不同的形态去呈现。

```python
class A:
    def __init__(self, name):
        self._name = name

    @property
    def name(self):
        return self._name

    @name.setter
    def name(self, name):
        self._name = name


class B:
    def __init__(self, name):
        self._name = name

    def __len__(self):
        return 10

    @property
    def name(self):
        return self._name

    @name.setter
    def name(self, name):
        self._name = name


a = A('孙悟空')
b = B('猪八戒')

#
# 对于say_hello()这个函数来说，只要对象中含有name属性，它就可以作为参数传递
#   这个函数并不会考虑对象的类型，只要有name属性即可
"""
1、定义一个函数,对于该函数来说，只要对象中含有name属性，它就可以作为参数传递。
    这个函数并不会考虑对象的类型，只要有name属性即可。
"""


def say_hello(obj):
    print('你好 %s' % obj.name)


# 执行
# 输出：你好 孙悟空
say_hello(a)
# 输出：你好 猪八戒
say_hello(b)

print("---------------------------------------------")
# len()
# 之所以一个对象能通过len()来获取长度，是因为对象中具有一个特殊方法__len__
# 换句话说，只要对象中具有__len__特殊方法，就可以通过len()来获取它的长度
"""
1、之所以一个对象能通过len()来获取长度，是因为对象中具有一个特殊方法__len__。
   换句话说，只要对象中具有__len__特殊方法，就可以通过len()来获取它的长度。
"""
l = [1, 2, 3]
s = 'hello'

print(len(l))
print(len(s))
```

## 5、类的属性和方法

1、介绍

类中包含属性和方法主要有五种：类属性，实例属性，类方法，实例方法和静态方法。

（1）类属性

直接在类中定义的属性是类属性，类属性可以通过类或类的实例访问到，但是类属性只能通过类对象来修改，无法通过实例对象修改。

（2）实例属性

通过实例对象添加的属性属于实例属性，实例属性只能通过实例对象来访问和修改，类对象无法访问修改。

（3）类方法  

在类内部使用 @classmethod 来修饰的方法属于类方法，类方法的第一个参数是cls，也会被自动传递，cls就是当前的类对象。

类方法和实例方法的区别：

实例方法的第一个参数是self，而类方法的第一个参数是cls。

类方法可以通过类去调用，也可以通过实例调用，没有区别。

（4）实例方法 

在类中定义，以self为第一个参数的方法都是实例方法，实例方法在调用时，Python会将调用对象作为self传入。

实例方法可以通过实例和类去调用， 当通过实例调用时，会自动将当前调用对象作为self传入， 当通过类调用时，不会自动传递self，此时我们必须手动传递self。

（5）静态方法

在类中使用 @staticmethod 来修饰的方法属于静态方法， 静态方法不需要指定任何的默认参数，静态方法可以通过类和实例去调用。

静态方法，基本上是一个和当前类无关的方法，它只是一个保存到当前类中的函数。静态方法一般都是一些工具方法，和当前类无关。

```python
class A(object):

    # 类属性
    count = 0

    def __init__(self):
        # 实例属性
        self.name = '孙悟空'

    # 实例方法
    def test(self):
        print('这是test方法~~~ ', self)

    # 类方法
    @classmethod
    def test_2(cls):
        print('这是test_2方法，他是一个类方法~~~ ', cls)
        print(cls.count)

    # 静态方法
    @staticmethod
    def test_3():
        print('test_3执行了~~~')


a = A()
# 实例属性，通过实例对象添加的属性属于实例属性
a.count = 10
# 输出：A , 0
print('A ,', A.count)
# 输出：A , 10
print('a ,', a.count)

# 等价于 A.test(a)
a.test()
# 等价于 a.test_2()
A.test_2()

# 静态方法的调用
A.test_3()
a.test_3()
```

## 6、垃圾回收

1、介绍

程序运行过程中产生的垃圾会影响到程序的运行的运行性能，所以这些垃圾必须被及时清理。

没用的东西就是垃圾。在程序中没有被引用的对象就是垃圾，这种垃圾对象过多以后会影响到程序的运行的性能，所谓的垃圾回收就是讲垃圾对象从内存中删除。

在Python中有自动的垃圾回收机制，它会自动将这些没有被引用的对象删除，所以我们不用手动处理垃圾回收。

2、代码

```python
class A:
    def __init__(self):
        self.name = 'A类'

    # del是一个特殊方法，它会在对象被垃圾回收前调用
    def __del__(self):
        print('A()对象被删除了，', self)


a = A()
"""
1、将a设置为了None，此时没有任何的变量对A()对象进行引用，它就是变成了垃圾。
2、此时在程序运行过程中将会输出：A()对象被删除了， <__main__.A object at 0x000001A8A291B3C8>
"""
a = None
"""
1、在上面的基础上，我们又使用一个变量b，来引用a对应的对象（此时注释：a = None）。
    此时，a对象不会被gc删除
"""
b = a
input('回车键退出...')
```

## 7、特殊方法

1、介绍

特殊方法，也称为魔术方法，该方法是使用__（双下划线）开头和结尾的。该方法一般不需要我们手动调用，需要在一些特殊情况下自动执行。

2、代码

```python
# 定义一个Person类
class Person():

    def __init__(self, name, age):
        self.name = name
        self.age = age

    # __str__（）这个特殊方法会在尝试将对象转换为字符串的时候调用
    # 它的作用可以用来指定对象转换为字符串的结果  （print函数）
    def __str__(self):
        return '__str__输出：Person [name=%s , age=%d]' % (self.name, self.age)

    # __repr__()这个特殊方法会在对当前对象使用repr()函数时调用
    # 它的作用是指定对象在 ‘交互模式’中直接输出的效果
    def __repr__(self):
        return 'Hello'


# __str__（）测试
# 创建一个Person类的实例
p1 = Person('孙悟空', 18)
"""
1、当我们打印一个对象时，实际上打印的是对象的中特殊方法 __str__()的返回值。
2、控制台输出：__str__输出：Person [name=孙悟空 , age=18]
"""
print(p1)

# __repr__()测试
print(repr(p1))
```

## 8、模块化（module）

1、介绍

（1）概述

模块化，模块化指将一个完整的程序分解为一个一个小的模块，通过将模块组合，来搭建出一个完整的程序，采用模块化，将程序分别编写到多个文件中。

 在Python中一个py文件就是一个模块，要想创建模块，实际上就是创建一个python文件。

（2）引入外部模块

```python
#方法1：（模块名，就是python文件的名字，注意不要py）
import 模块名 
# 方法2
import 模块名 as 模块别名
```

+ 可以引入同一个模块多次，但是模块的实例只会创建一个。
+ import可以在程序的任意位置调用，但是一般情况下，import语句都会统一写在程序的开头。
+ 在每一个模块内部都有一个__name__属性，通过这个属性可以获取到模块的名字，__name__属性值为 __main__的模块是主模块，一个程序中只会有一个主模块。主模块就是我们直接通过 python 执行的模块。

2、代码

文件结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201009145722208.png" alt="image-20201009145722208" style="zoom:67%;" />

好像没有__init_.py文件也可以执行代码。

（1）辅助模块

```python
# 预引入的模块

# 定义的变量
a = 10
b = 2


# 定义的函数
def fn():
    print('Hello fn')


def fn2():
    print('Hello fn2')


def fn3():
    print('Hello fn3')
    5 / 0


# 编写测试代码，这部分代码，只要当当前文件作为主模块的时候才需要执行；
# 而当模块被其他模块引入时，不需要执行的，此时我们就必须要检查当前模块是否是主模块。
if __name__ == '__main__':
    fn()
```

（2）主模块

```python
import module_assistant
# 为模块指定别名
import module_assistant as m

"""
输出结果如下：
__main__
10 2
Hello fn2
"""
print(__name__)
print(m.a, m.b)
m.fn2()

# 输出：10 2
print(module_assistant.a, module_assistant.b)
```

## 9、包

1、介绍

（1）概述

当我们模块中代码过多时，或者一个模块需要被分解为多个模块时，这时就需要使用到包。

包也是一个模块。普通的模块就是一个py文件，而包是一个文件夹。包中必须要一个一个 __init__.py 这个文件，这个文件中可以包含有包中的主要内容。

（2）__pycache__

__pycache__ 是模块的缓存文件。py代码在执行前，需要被解析器先转换为机器码，然后再执行，所以我们在使用模块（包）时，也需要将模块的代码先转换为机器码然后再交由计算机执行。而为了提高程序运行的性能，python会在编译过一次以后，将代码保存到一个缓存文件中。这样在下次加载这个模块（包）时，就可以不再重新编译而是直接加载缓存中编译好的代码即可。

2、代码

项目结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201009154524010.png" alt="image-20201009154524010" style="zoom:80%;" />

（1）package_module_assistan.py文件

```python
a = 10
b = 2
```

（2）测试文件

```python
from test_package import package_module_assistant as m

print(m.a)
```

# 3、python标准库

1、介绍

为了实现开箱即用的思想，Python中为我们提供了一个模块的标准库，在这个标准库中，有很多很强大的模块我们可以直接使用，并且标准库会随Python的安装一同安装。

（1）sys

它里面提供了一些变量和函数，使我们可以获取到Python解析器的信息或者通过函数来操作Python解析器。

（2）sys.platform

表示当前Python运行的平台。

（3）os

该模块让我们可以对操作系统进行访问。

（4）pprint

该模块它给我们提供了一个方法 pprint() 该方法可以用来对打印的数据做简单的格式化。

2、代码

```python
import os
import pprint
import sys

# sys.argv
# 获取执行代码时，命令行中所包含的参数
# 该属性是一个列表，列表中保存了当前命令的所有参数
# print(sys.argv)

# sys.modules
# 获取当前程序中引入的所有模块
# modules是一个字典，字典的key是模块的名字，字典的value是模块对象
# pprint.pprint(sys.modules)

# sys.path
# 他是一个列表，列表中保存的是模块的搜索路径
pprint.pprint(sys.path)

# 控制台输出:win32
print(sys.platform)

# os.environ
# 通过这个属性可以获取到系统的环境变量
pprint.pprint(os.environ['path'])

# 可以用来执行操作系统的名字
print("-----------------------")
os.system('dir')
```

# 4、异常处理

## 1、介绍

1、概述

程序在运行过程当中，不可避免的会出现一些错误，比如：   除0，这些错误在程序中，我们称其为异常。程序运行过程中，一旦出现异常将会导致程序立即终止，异常以后的代码全部都不会执行。

2、处理异常

（1）try语句

```python
try:
	代码块（可能出现错误的语句）
except 异常类型 as 异常名:
	代码块（出现错误以后的处理方式）
except 异常类型 as 异常名:
	代码块（出现错误以后的处理方式）
except 异常类型 as 异常名:
	代码块（出现错误以后的处理方式）
else：
	代码块（没出错时要执行的语句）    
finally:
	代码块（该代码块总会执行）   
```

try是必须的，else语句有没有都行，except和finally至少有一个。   

将可能出错的代码放入到try语句，这样如果代码没有错误，则会正常执行，如果出现错误，则会执行expect子句中的代码，这样我们就可以通过代码来处理异常。

3、异常传播

当在函数中出现异常时，如果在函数中对异常进行了处理，则异常不会再继续传播；如果函数中没有对异常进行处理，则异常会继续向函数调用处传播； 如果函数调用处处理了异常，则不再传播，如果没有处理则继续向调用处传播；直到传递到全局作用域（主模块）如果依然没有处理，则程序终止，并且显示异常信息。

当程序运行过程中出现异常以后，所有的异常信息会被保存一个专门的异常对象中，而异常传播时，实际上就是异常对象抛给了调用处。比如 ： ZeroDivisionError类的对象专门用来表示除0的异常。

4、抛出异常

可以使用 raise 语句来抛出异常，raise语句后需要跟一个异常类 或 异常的实例。

## 2、代码

1、异常对象

```python
try:
    print(10 / 0)
except NameError:
    # 如果except后不跟任何的内容，则此时它会捕获到所有的异常
    # 如果在except后跟着一个异常的类型，那么此时它只会捕获该类型的异常
    print('出现 NameError 异常')
except IndexError:
    print('出现 IndexError 异常')
# Exception 是所有异常类的父类，所以如果except后跟的是Exception，他也会捕获到所有的异常
# 可以在异常类后边跟着一个 as xx 此时xx就是异常对象
except Exception as e:
    print('未知异常', e, type(e))
finally:
    print('无论是否出现异常，该子句都会执行')
```

2、抛出异常

```python
# 自定义异常类，只需要创建一个类继承Exception即可
class MyError(Exception):
    pass


def add(a, b):
    # 如果a和b中有负数，就向调用处抛出异常
    if a < 0 or b < 0:
        # raise用于向外部抛出异常，后边可以跟一个异常类，或异常类的实例
        # raise Exception
        # 抛出异常的目的，告诉调用者这里调用时出现问题，希望你自己处理一下
        raise MyError('自定义的异常')
    r = a + b
    return r


print(add(1, 2))
# 抛出异常
print(add(-1, 2))
```

























































































































































































































































