# 1 JVM体系结构概述

## 1、JVM位置

<img src="JVM学习.assets/image-20200815201646263.png" alt="image-20200815201646263" style="zoom:80%;" />

JVM是运行在操作系统之上的，它与硬件没有直接的交互。

## 2、JVM体系结构概览

<img src="JVM学习.assets/image-20200815201839725.png" alt="image-20200815201839725" style="zoom:80%;" />

在运行数据区，有亮色和暗色的区别：对于亮色所有线程共享，灰色表示线程私有；同时亮色存在垃圾回收机制。

## 3、类装载器-ClassLoader

1、介绍

（1）负责加载class文件，class文件在文件开头有特定的文件标示，将class文件字节码内容加载到内存中，并将这些内容转换成方法区中的运行时数据结构并且ClassLoader只负责class文件的加载，至于它是否可以运行，则由Execution Engine决定 。

<img src="JVM学习.assets/image-20200815202112264.png" alt="image-20200815202112264" style="zoom:67%;" />

2、加载器

<img src="JVM学习.assets/image-20200815202806028.png" alt="image-20200815202806028" style="zoom:80%;" />

（1）虚拟机自带的加载器

+ 启动类加载器（Bootstrap）C++

+ 扩展类加载器（Extension）Java

+ 应用程序类加载器（AppClassLoader）Java也叫系统类加载器，加载当前应用的classpath的所有类

（2）用户自定义加载器

Java.lang.ClassLoader的子类，用户可以定制类的加载方式。

```java
public class MyObject {
   public static void main(String[] args) {
//    Object object = new Object();
      /**
       * Object是系统自带的
       * 1、object.getClass() 找到一瓶水的模板 （由手里的这瓶水得到水的模板）
       * 由小class得到大Class。
       * 2、object.getClass().getClassLoader() 这瓶水的模板是由那个快递员端过来的，
       * 通过这个订单编号，找这家快递公司
       * 3、Bootstrap加载器 返回null
       */
//    System.out.println(object.getClass()); //class java.lang.Object
//    System.out.println(object.getClass().getClassLoader()); //null

      /**
       * MyObject 是我们自己书写的类
       * AppClassLoader加载器
       */
      MyObject myObject = new MyObject();
      System.out.println(myObject.getClass()); //class com.xiaolun.MyObject
      //sun.misc.Launcher$AppClassLoader@18b4aac2
      System.out.println(myObject.getClass().getClassLoader());
      //sun.misc.Launcher$ExtClassLoader@1b6d3586
      System.out.println(myObject.getClass().getClassLoader().getParent());
      //null
      System.out.println(myObject.getClass().getClassLoader().getParent().getParent());
   }
}
```

3、双亲委派

​		当一个类收到了类加载请求，他首先不会尝试自己去加载这个类，而是把这个请求委派给父类去完成，每一个层次类加载器都是如此，因此所有的加载请求都应该传送到启动类加载其中，只有当父类加载器反馈自己无法完成这个请求的时候（在它的加载路径下没有找到所需加载的`Class`），子类加载器才会尝试自己去加载。

​		采用双亲委派的一个好处是：比如加载位于 `rt.jar` 包中的类 `java.lang.Objec`t，不管是哪个加载器加载这个类，最终都是委托给顶层的启动类加载器进行加载，这样就保证了使用不同的类加载器最终得到的都是同样一个 `Object`对象。 

<img src="JVM学习.assets/image-20200815210242369.png" alt="image-20200815210242369" style="zoom:80%;" />

4、Execution Engine

Execution Engine执行引擎负责解释命令，提交操作系统执行。

## 4、本地接口

1、Native Interface本地接口

​		本地接口的作用是融合不同的编程语言为 `Java` 所用，它的初衷是融合 `C/C++`程序，Java 诞生的时候是 `C/C++`横行的时候，要想立足，必须有调用 `C/C++`程序，于是就在内存中专门开辟了一块区域处理标记为`native`的代码，它的具体做法是 `Native Method Stack`中登记 `native`方法，在`Execution Engine` 执行时加载`native libraies`。

 		目前该方法使用的越来越少了，除非是与硬件有关的应用，比如通过Java程序驱动打印机或者Java系统管理生产设备，在企业级应用中已经比较少见。因为现在的异构领域间的通信很发达，比如可以使用 `Socket`通信，也可以使用`Web Service`等等，不多做介绍。

2、Native Method Stack

​		它的具体做法是Native Method Stack中登记native方法，在Execution Engine 执行时加载本地方法库。

3、代码讲解

```java
public class NativeInterfaceDemo {
   public static void main(String[] args) {
      Thread thread = new Thread();
      thread.start();
      /**
       *  1、thread.start()两次会报错：java.lang.IllegalThreadStateException
       *   at java.lang.Thread.start(Thread.java:708)
       *   2、
       */
//    thread.start();
   }
}
```

<img src="JVM学习.assets/image-20200815213922325.png" alt="image-20200815213922325" style="zoom: 50%;" />

<img src="JVM学习.assets/image-20200815214019200.png" alt="image-20200815214019200" style="zoom:50%;" />

可以发现，native是一个关键字，有声明，但是没有方法的实现。

## 5、PC寄存器

​		每个线程都有一个程序计数器（记录了方法之间的调用和执行情况，类似于排班值日表），是线程私有的，就是一个指针，指向方法区中的方法字节码（用来存储指向下一条指令的地址,也即将要执行的指令代码），由执行引擎读取下一条指令，是一个非常小的内存空间，几乎可以忽略不记。

​		这块内存区域很小，它是**当前线程所执行的字节码的行号指示器**，字节码解释器通过改变这个计数器的值来选取下一条需要执行的字节码指令。

​		如果执行的是一个Native方法，那这个计数器是空的。

​		用以完成分支、循环、跳转、异常处理、线程恢复等基础功能。不会发生内存溢出(OutOfMemory=OOM)错误

## 6、方法区-Method Area

​		供各线程共享的运行时内存区域。它存储了每一个类的结构信息，例如运行时常量池（`Runtime Constant Pool`）、字段和方法数据、构造函数和普通方法的字节码内容。上面讲的是规范，在不同虚拟机里头实现是不一样的，最典型的就是永久代(`PermGen space`)和元空间(`Metaspace`)。

注意：实例变量存在堆内存中,和方法区无关。

<img src="JVM学习.assets/image-20200815215620567.png" alt="image-20200815215620567" style="zoom:80%;" />

## 7、栈

1、介绍

（1）栈管运行，堆管存储。

（2）

















