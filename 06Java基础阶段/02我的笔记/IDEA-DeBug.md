#### 1、Debug简介和意义

1、什么是程序DeBug？

Debug，是程序开发人员必会的一项调试程序的技能。

企业中程序开发和程序调试的比例为1:1.5，可以说如果你不会调试程序，你就没有办法从事编程工作。

2、Debug能帮助我们做什么？

（1）追踪代码的运行流程。

（2）程序运行异常定位。

（3）线上问题追踪。

3、Debug对于程序学习者的意义

（1）通过调试能够更好的查看程序的执行流程。

（2）复杂的程序逻辑，通过老师的口述讲解，很难理解清楚，这个时候借助调试能够很好的帮助同学们理解程序。

（3）定位问题，提高自我解决问题的能力。


#### 2、IDEA中的Debug步骤

1、设置断点（F9）

2、调试程序（8个按钮）

![](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/2019-09-28_162216.jpg)

| 按钮                                                         | 说明                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/2019-09-28_163744.jpg) | (Alt + F10)：如果你的光标在其它行或其它页面，点击这个按钮可跳转到当前代码执行的行 |
| ![](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/2019-09-28_164022.jpg) | (F8)：步过，一行一行地往下走，如果这一行上有方法不会进入方法。 |
| ![](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/2019-09-28_162345.jpg) | (F7)：步入。如果当前行有方法，可以进入方法内部，一般用于进入自定义方法内，不会进入官方类库的方法。 |
| ![](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/2019-09-28_164200.jpg) | (Alt + Shift + F7)：强制步入，能进入任何方法，查看底层源码的时候可以用这个进入官方类库的方法。 |
| ![](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/2019-09-28_164215.jpg) | (Shift + F8)：步出，从步入的方法内退出到方法调用处，此时方法已执行完毕，只是还没有完成赋值。 |
| ![](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/2019-09-28_164428.jpg) | 回退断点。                                                   |
| ![](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/2019-09-28_164418.jpg) | (Alt + F9)：运行到光标处，你可以将光标定位到你需要查看的那一行，然后使用这个功能，代码会运行至光标行，而不需要打断点。 |
| ![](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/2019-09-28_164437.jpg) | (Alt + F8)：计算表达式。                                     |

3、观察变量

（1）查看变量有三种方式：
+ 程序区查看变量
+ Debugger的Variables中查看变量
+ 鼠标悬停到变量名上会弹出当前变量的值

![](images\2019-09-28_165050.jpg)

（2）查看输出

#### 3、跳转到当前代码执行的行

跳转到当前代码执行的行（Alt + F10）![](IDEA-DeBug - 副本.assets/2019-09-28_163744.jpg)

作用：使程序窗口切换到当前正在运行的程序处。

#### 4、步过调试的使用

1、步过调试

步过调试按钮（F8）![](IDEA-DeBug - 副本.assets/2019-09-28_164022-1601005452664.jpg)

2、作用

步过，一行一行地往下走，如果这一行上有方法不会进入方法。

常用于调试过程中不想进入调用的方法体的情况。

#### 5、步入调试的使用

1、步入调试

步过调试按钮（F7）![](IDEA-DeBug - 副本.assets/2019-09-28_162345-1601005519372.jpg)

2、作用

步入，一行一行地往下走，如果这一行上有方法，则进入方法内部。

一般用于进入自定义方法内，不会进入官方类库的方法。

#### 6、强制步入调试的使用

1、强制步入调试

强制步入调试按钮（Alt + Shift + F7）![](IDEA-DeBug - 副本.assets/2019-09-28_164200.jpg)

2、作用

进入官方类库方法。

帮助我们学习和查看JDK源码。

#### 7、步出调试的使用

1、步出调试

步出调试按钮（Shift + F8）![](IDEA-DeBug - 副本.assets/2019-09-28_164215.jpg)

2、作用

从方法内退出到方法调用处。

调试的时候，有时候会跳入到自己不想查看的方法体，这个时候使用步出。

#### 8、回退断点调试的使用

1、回退断点

回退断点按钮 ![](IDEA-DeBug - 副本.assets/2019-09-28_164428.jpg)

2、作用

回退到当前方法的调用处。

当想重新查看该方法体的执行过程时，不用重新启动Debug，可以使用回退断点方式。

#### 9、运行到光标处

1、运行到光标处

运行光标处按钮 （F9）![](IDEA-DeBug - 副本.assets/2019-09-28_164418.jpg)

2、作用

使程序运行到光标处，而无需设置断点。

#### 10、计算表达式

1、计算表达式

计算表达式按钮（Alt + F8）![](IDEA-DeBug - 副本.assets/2019-09-28_164437.jpg)

![](images\2019-10-03_182159.jpg)

2、作用

设置变量，在计算表达式的框里，可以改变变量的值，这样有时候就能很方便我们去调试各种值的情况了。 

#### 11、条件断点

1、条件断点

右键单击断点处，可以设置进入断点的条件

![](images\2019-10-03_182101.jpg)

2、作用

通过设置断点条件，在满足条件时，才停在断点处，否则直接运行。

#### 12、多线程调试

1、步骤

（1）多线程调试，需要调整断点挂起级别为Thread

![](images\2019-10-05_164127.jpg)

（2）Frame中选择线程进行调试

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200925114949296.png" alt="image-20200925114949296" style="zoom:80%;" />

同时，在表达式中，我们也可以进行如下的设置：

```java
Thread.currentThread().getName()
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200925135141912.png" alt="image-20200925135141912" style="zoom:67%;" />