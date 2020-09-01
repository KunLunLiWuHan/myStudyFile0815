# 1 介绍

**名词解释**

JDK:Java Development Kit 开发工具包。

JRE:Java Runtime Enviroment 运行时环境。

JVM:Java Virtual  Machine 虚拟机。

IDE： Integrated Development Environment  集成开发环境。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901084600.png" alt="image-20200523200103119" style="zoom:150%;" />

**Java程序运行机制**

![image-20200523220712182](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901084601.png)

## 1.1 安装开发环境

**卸载JDK**

1. 删除Java的安装目录。
2. 删除JAVA_HOME。
3. 删除path下关于Java的目录。
4. 进行测试 java -version。

**安装Jdk**

1. 下载官网https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html，找到[jdk-8u251-windows-x64.exe](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html#license-lightbox)，同意协议后下载即可。
2. 双击安装JDK，要记住安装的路径。
3. 配置环境变量。

配置系统变量-----> JAVA_HOME:

配置环境变量------->path变量：

4. 进行检查安装，下面的为成功成功界面：

# 2 数据类型

1. **强类型语言**

要求变量的使用要严格符合规定，所有变量都必须先定义才能够使用。

2. **数据类型**

Java的数据类型分为两大类，基本类型（primitive type）和引用类型（reference type）。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901084602.png" alt="image-20200524111311667" style="zoom:80%;" />

引用数据类型：把参数作为对象的属性，然后把参数包装成对象来传递；java中的对象默认都是引用传递的，区别于指针。

```java
//整数
long num1 = 30L; //long类型要在数字后面加上L。

//小数
float num2 = 30.1F; //加上F。

//字符
char name = 'A'; //没问题，一个字符
char name2 = '小伦';//报错，只能是一个字符。

//字符串String不是关键字，类。
String name2 = "小伦";
```

3. **字节**

位（bit）：是计算机内部存储的最小单位，11001000是一个八位二进制数。

字节（byte）：是计算机中数据处理的最小单位，习惯上使用B来表示。

1B(byte , 字节) = 8 bit（位）。

字符：指计算中使用的字母、数字、字和符号。

**代码测试**

```java
public class demo1 {
    public static void main(String[] args) {
        //整数拓展：进制 二进制0b 八进制0 十进制 十六进制
        int i = 10; //十进制
        int i2 = 010; //八进制
        int i3 = 0x10; //十六进制

        System.out.println(i);
        System.out.println(i2);
        System.out.println(i3);

        //浮点数拓展 ，银行业务怎么表示
        //使用BigDemical（数学工具类），而不是使用float和double
        float f = 0.1f;
        double f2 = 0.1;
        System.out.println(f == f2); //false

        float d1 = 23333333333333f;
        float d2 =d1 + 1;
        System.out.println(d1 == d2); //true（存在舍人误差）

        //字符扩展(所有的字符本质还是数字，Unicode编码)
        char c1 = 'a';
        System.out.println((int)c1); //将字符强制转化位数字

        //转义字符
        // \t 制表符
        // \n 换行
        System.out.println("hello\tworld");
    }
}
```

## 2.1 类型转换

由于Java是强类型语言，所以要进行有些运算的时候，需要用到类型转换。

```java
低------------------------>高
    byte,short,char->int ->long->float->double //小数优先级大于整数
```

运算中，不同类型的数据先转化为同一个类型数据，然后再进行计算。

```java
public class demo2 {
    public static void main(String[] args) {
        /**
         * 1.不能对布尔值进行转换，转换的时候可能发生内存溢出，或者精度问题。
         * 2.强制类型转换：将高容量的数据转换为低容量的数据。
         * 3.自动转换：由低容量数据转换为高容量数据。
         */
        int i = 128;
        //强制类型转换
        byte b = (byte)i; //内存溢出，导致数据出现问题

        //自动转换
        double b2 = i; //内存溢出，导致数据出现问题
        System.out.println(b);
        System.out.println(b2);
    }
}
```

## 2.2 变量、常量和作用域

1. **变量的命名规范**

+ 所有变量、方法和类名需要见名知意。
+ 类成员变量：首字母小写和驼峰原则。
+ 局部变量：首字母小写和驼峰原则。
+ 常量：大写字母和下划线，MAX_VALUE。
+ 类名：首字母大写和驼峰原则。
+ 方法名：首字母小写和驼峰原则：run()。

2. **常量**

常量名一般使用大写字母。

```java
//final 常量名 = 常量值；
static final double PI = 3.14; // static final位置互换无影响。
```



### 2.2.1 变量作用域

```java 
public class Dem03 {
    /**
     * 实例变量：从属于对象，通过类来使用它.如果不自行初始化，这个类型的默认值为 0，null。
     * 布尔值默认为false，除了基本类型，其余的默认值为null。
     */
    String name; //不用初始化

    /**
     * 类变量：static
     */
    static double salary = 2500;

    public static void main(String[] args) {
        /**
         * 局部变量：必须声明和初始化值
         */
        int i = 10; //在声明时要初始化
        System.out.println(i);

        /**
         *  操作实例变量
         *  变量类型 变量名字 = new Dem03()（将自己给拿到了）
         */
        Dem03  dem03 = new Dem03();
        System.out.println(dem03.name); //输出为：null

        /**
         * 操作类变量
         */
        System.out.println(salary); //可以直接输出
    }
}
```

# 3 包机制

+ 为了更好的组织类，Java提供了包机制，用于区别类名的命名空间。包的本质就是文件夹的意思。

+ 包机制的语法格式为：

```java
package pkg1[]
```

+ 一般公司域名倒置作为包名。

```html
www.baidu.com -> com.baidu.www
```

+ 为了能够使用某一个包中的成员，我们需要在Java程序中明确导入该包，使用`import`语句即可完成此功能。

```java
import package;
```

# 4 Javadoc生成文档

Javadoc命令是用来生成自己API文档的，其中API文档是一个技术内容交付文件，包含如何有效地使用和集成api的说明。它是一个简明的参考手册，包含了使用API所需的所有信息，详细介绍了函数、类、返回类型、参数等，并有教程是示例支撑。

API（Application Programming Interface,应用程序编程接口）是一些预先定义的函数，目的是提供应用程序与开发人员基于某软件或硬件得以访问一组例程的能力，而又无需访问源码，或理解内部工作机制的细节。

参数信息如下。

+ @author：作者名
+ @version：版本号
+ @since：指明需要最早使用的jdk版本
+ @para：参数名
+ @return：返回值情况
+ @throws：异常抛出情况

程序代码：

```java
package com.xiaolun;

/**
 * 加在类上的文档
 * @author xiaolun
 * @version 1.0
 * @since 1.8
 */
public class Doc {

    /**
     * 加在方法上的文档
     * @param name
     * @return
     * @throws Exception
     */
    public String test(String name) throws Exception{
        return name;
    }
}
```

点击鼠标右键，弹出命令，选择`show in explorer`:

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901084603.png" alt="image-20200530102411821" style="zoom:67%;" />

此时该文件下输入`cmd`进入命令行，输入：

```java
javadoc -encoding UTF-8 -charset UTF-8 Doc.java
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901084604.png" alt="image-20200530102231704" style="zoom:80%;" />

在这个文件夹下就会生成对应的文档，点击`index.html`就可以查阅。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901084605.png" alt="image-20200530102302315" style="zoom:67%;" />

# 5 Java流程控制

## 5.1 用户交互Scanner

Java给我们提供了这样一个可以实现人机交互的工具类`java.util.Scanner`，使用它我们可以获取用户的输入。`Scanner`是一个扫描器，我们录取到键盘的数据，先存到缓存区等待读取。

基本语法：

```java
Scanner s = new Scanner(System.in);
```

+ 通过`Scanner`类的`next()`与`nextLine()`方法获取输入的字符串，在读取前我们一般需要使用`hasNext()`与`hasNextLine()`判断是否还有输入的数据。

1. **next()方法**

+ 一定要读取到有效字符后才可以结束输入。
+ 对输入的有效字符之前遇到的空白，`next()`方法会自动将其去掉。
+ `next`()方法读取到空白符（比如空格，回车，tab 等）就结束，`next`()不能得到带有空格的字符串。

2. **nextLine()方法**

+ 以`Enter`作为结束符，也就是说该方法返回的是输入回车之前的所有字符。
+ 可以获得空白。

总结：

- hasNext（） 是检测 还有没有**下一个**输入
- next（）是指针移动到当前下标，并**取出下一个**输入
- nextLine（） 把指针移动到**下一行** 让然后**取出当前这一行**的输入
- hasNextLine（） 是检测**下一行**有没有输入

```java
public class Demo04 {
    public static void main(String[] args) {
        //创建一个扫描器对象，用于接收键盘数据
        Scanner scanner = new Scanner(System.in);
        System.out.println("使用nextline()方式接收");

        //判断用户有没有输入字符串
        if (scanner.hasNext()){
            String str = scanner.nextLine();
            System.out.println("输入内容为："+str);
        }
        //凡是属于IO流的类如果不关闭，会一直占用资源，要养成好习惯用完就关闭。
        scanner.close();
    }
}
```

## 5.2 Scanner接收数字

```java
public class Demo05 {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        //从键盘接收数据
        int i = 0;
        float f =0.0f;

        System.out.println("请输入整数:");
        if (scanner.hasNextInt()){
            i = scanner.nextInt();
            System.out.println("整数数据："+ i);
        }else {
            System.out.println("输入的不是整数数据");
        }

        System.out.println("请输入小数:");
        if (scanner.hasNextFloat()){
            f = scanner.nextFloat();
            System.out.println("小数数据："+ f);
        }else {
            System.out.println("输入的不是小数数据");
        }
    }
}
```

## 5.3 增强for循环

主要是针对数组或集合而引入的。

循环语法格式如下：

```java
for (声明语句：表达式){
	//代码句子
}
```

+ 声明语句：声明新的局部变量，该变量的类型必须和数组元素的类型匹配。其作用域限定在循环语句块，其值与此时数组元素的值相等。
+ 表达式：要访问的数组名，或者是返回值为数组的方法。

```java
public class Demo06 {
    public static void main(String[] args) {
        int[] numbers = {10,20,30,40}; //定义一个数组

        //遍历数组的元素（将numbers中的每一项遍历出来后赋值给x）
        for (int x:numbers) {
            System.out.println(x);
        }
    }
}
```

# 6 Java方法详解

## 6.1 方法介绍

Java方法是语句的集合，它们放在一起去执行一个功能。

+ 方法是解决一类问题的步骤的有序组合。
+ 包含于类和对象中。
+ 在程序中被创建，在其他地方被引用。

**设计方法的原则：**

尽量保持方法的原子性，即一个方法只完成一个功能，这样有利于后期的扩展。

## 6.2 方法的定义和调用

方法包含一个方法头和一个方法体。

```java
修饰符 返回值类型 方法名（参数类型 参数名）{
  // 方法体
  return 返回值；
}
```

+ 修饰符：告诉编译器如何调用该方法，定义了该方法的访问类型。
+ 返回值类型：有些方法执行了所需的操作，但是没有返回值。使用`returnValueType`的关键字`void`。

+ 参数类型：参数就像一个占位符。当方法被调用时，传递值给参数。这个值被称为实参或者变量。

  形式参数：在方法调用时用于接收外界输入的数据。

  实参：调用方法时实际传递给方法的数据。

```java
public class Demo05 {
    public static void main(String[] args) {

        //实参：实际调用时传递给它的参数
  int sum = add(1,2);
        System.out.println(sum);
    }
    //形式参数：其定义作用
    public static int add(int a,int b){
        return a+b;
    }
}
```

**静态/非静态方法**

```java
public class Demo09 {
    public static void main(String[] args) {
    }

    //静态方法，和类一起加载，时间片比较早
    public static void a(){
//        b(); //静态方法调用非静态方法会报错
    }

    //非静态方法，类实例化后才存在
    public void b(){

    }
}
```

**值传递**

```java
public class Demo10 {
    public static void main(String[] args) {
        int a = 1;
        System.out.println(a);

        Demo10.change(a);
        System.out.println(a);//输出仍然是1，不会改变
    }

    //返回值为null
    public static void change(int a){
        a = 10;
    }
}
```

**引用传递**

```java
public class Demo11 {
    public static void main(String[] args) {
        Person person = new Person();
        System.out.println(person.name); //null

        Demo11.change(person);
        System.out.println(person.name); //输出为:xiaolun
    }

    public static void change(Person person){
        /**
         * 修改的是 Person 类中属性name的值
         * person是一个对象;指向的--> Person person = new Person();
         */
        person.name = "xiaolun";
    }
}

//定义一个Person类
class Person{
    String name; //null
}
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901084606.png" alt="image-20200530171933103" style="zoom:80%;" />

### 6.2.1 方法的重载

重载就是在一个类中，有相同的函数名称，但是形参不同的函数。

**重载规则：**

+ 方法名称必须相同。
+ 仅仅返回值不同不能构成方法的重载。

### 6.2.2 命令行传递参数

```java
public class Demo06 {
    public static void main(String[] args) {

        //args.length:数组长度
        for (int i = 0; i < args.length; i++) {
            System.out.println("args["+ i +"]："+args[i]);
        }
    }
}
```

点击类的`show in explorer`选项，进入到类的文件夹，输入`cmd`，进入控制面板，输入以下 命令，就可以使用命令行执行了。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901084607.png" alt="image-20200530150122579" style="zoom:80%;" />

### 6.2.3  可变参数

从Java1.5开始，Java支持传递同类型的可变参数。在方法声明中，在指定参数类型后加一个省略号（...）。

一个方法中只能指定一个可变参数，它必须是方法的最后一个参数。任何普通参数必须在它之前声明。

+ 使用可变参数来比较一组数据中最大值：

```java
public class Demo07 {
    public static void main(String[] args) {
        printMax(35,8,9,56,9);
    }

    public static void printMax(double...numbers){ //可变参数
        if (numbers.length==0){
            System.out.println("No argument passed");
        }
        double result = numbers[0];

        //排序
        for (int i = 1; i < numbers.length; i++) {
            if (numbers[i] > result){
                result = numbers[i];
            }
        }
        System.out.println("The Max value is:"+ esult);
    }
}
```

### 6.2.4 递归

递归就是自己调用自己。

利用递归可以用简单的程序解决一些复杂的问题。它通常把一个大型复杂的问题层层转化为一个与原问题相似的规模较小的问题来求解，递归策略只需少量的程序即可描述出结题过程所需要的多次重复计算，减少了代码量。

主要分为两个部分：

+ 递归头：什么时候不调用自身方法。如果没有头，将陷入死循环。
+ 递归体：什么时候需要调用自身方法。

```java
public class Demo08 {
    public static void main(String[] args) {
        System.out.println(f(5));
    }

    //计算阶乘
    public static int f(int n){
       if(n==1){
           return 1;
       } else {
           return n*f(n-1);
       }
    }
}
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901084608.png" alt="image-20200530153141280" style="zoom:80%;" />

主要分为三个阶段：前阶段（上图向右进行），边界条件（本文为f(1),此时f(1)存在值），返回阶段（n*(n-1)）。

# 7 数组

## 7.1 数组的声明和创建

首先必须声明数组变量，才能在程序中使用数组。语法为：

```java
dataType[] arrayVar; //首选的方法
dataType arrayVar[];  //效果相同，但不是首选方法
```

**创建数组**：

```java
dataType[] arrayVar = new dataType[arraySize]; //使用new 操作符来创建数组
```

**三种初始化**:

+ 静态初始化

```java
int[] arrayVar = {1,2,3,4};
```

+ 动态初始化

```java
int[] a = new int[2]; //包含默认初始化
a[0]=0;
a[1]=1;
```

+ 默认初始化

数组是引用类型，它的元素相当于类的实例变量，因此数组一经分配空间，其中的每个元素也被按照实例变量同样的方式被隐式初始化。

**数组的四个基本特点：**

+ 其长度是固定的，一经创建，大小不能改变。
+ 数组元素必须是相同类型，不允许出现混合类型。
+ 数组的元素可以是任何数据类型，包含基本类型和引用类型。
+ 数组变量属于引用类型，数组可以看成对象，每个元素可以相当于该对象的成员变量。数组对象本身在堆中。

## 7.2 二维数组

多维数组可以看成是数组的数组，比如二维数组就是一个特殊的一维数组，其每一个元素都是一个一维数组。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901084609.png" alt="image-20200530162324766" style="zoom:80%;" />

**二维数组的创建：**

```java
int a[][] = new int[2][5];
int array[][] = {{1,2},{3,4},{5,6}}; //静态初始化
```

可以将以上二维数组a看成一个两行五列的数组。

# 8 面向对象

## 8.1 介绍

**面向对象思想：**

+ 步骤清晰，第一步做什么，第二步做什么...
+ 适合处理一些较为简单的问题。

**面向对象思想：**

+ 物以类聚，分类的思维模式，思考要问题解决的话需要那些分类，然后对分类进行单独思考。
+ 适合处理需要多人协作的问题。

对于描述复杂的事物，为了从宏观上把握，从整体上合理分析，我们需要使用面向对象的思路进行分析整个系统。但是，具体到微观操作，仍然需要面向过程的思路去处理。

**面向对象编程：**

英文为：Object-Oriented Programming,OOP。本质是：已类的方式组织代码，以对象的形式组织（封装）数据。

从认识论的角度考虑，先有对象后有类。对象，是具体的事物，类，是抽象的，是对对象的抽象；从代码运行的角度考虑，先有类后有对象，类是对象的模板。

## 8.2 类和对象的关系

类是一种抽象的数据类型，它是对某一类事物的整体描述，但是并不能代表某一个具体的事物。

对象是抽象概念的具体实例。

### 8.2.1 创建和初始化对象

+ 使用new关键字创建对象。

在创建的时候，除了分配内存空间外，还会给创建好的对象进行默认的初始化以及对类中构造器的调用。

类实例化后会返回一个自己的对象。

```java
public class Student{ //实体类
	//属性：字段
	String name; //是一个模板，没有具体的值。
}
```

### 8.2.2 构造器

类中的构造器也称为构造方法，是在进行创建对象时必须要调用的，构造器有以下两个特点：

+ 必须和类的名字相同。
+ 没有返回类型，不能写`void`。

```java
public class Person2 {
    String name;

    /**
     *  实例化初始值
     *  使用new关键字，本质是在调用构造器，进而完成初始化
     */
    //无参构造
    public Person2(){
        this.name = "xiaolun";
    }

    //有参构造,一旦定义了有参构造，无参构造就必须定义
    public Person2(String name){
        this.name = name;
    }
}
```

```java
public class Application { //测试类
    public static void main(String[] args) {

        Person2 person2 = new Person2("xiaolun");
        System.out.println(person2.name);
    }
}
```

+ 控制台输出：xiaolun。

**显示项目结构的方法**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901084610.png" alt="image-20200530210107390" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901084611.png" alt="image-20200530210158097" style="zoom:80%;" />

然后点击确定即可看到java编译的class文件。

## 8.3 三大特点

### 8.3.1 封装

封装就是数据的隐藏，应该禁止直接访问一个对象中的实际表示，而应通过操作接口来访问，称为信息隐藏。

即：属性应该写成私有，通过`get/set`（提供一些可以操作属性的方法）来间接操作。

**优点**

+ 提高程序的安全性，保护数据。
+ 隐藏代码的实现细节。
+ 统一接口。

### 8.3.2 继承

子类继承父类，就会拥有父类中除了私有的的全部方法。在Java中，所有的类，都默认直接或者间接继承Object类。

如果父类被`final`关键字修饰，子类就不能继承它了。

**测试1**

```java
public class Person2 {//父类
    protected String name = "kunlun";
}
```

```java
public class Student extends Person2{ //子类
    private String name = "xiaolun";

    public void test(String name){
        System.out.println(name); //形式参数
        System.out.println(this.name); //当前成员变量
        System.out.println(super.name); //父类成员变量
    }
}
```

```java
public class Application { //测试类
    public static void main(String[] args) {
        Student student = new Student();
        student.test("昆伦");
    }
}
```

+ 控制台输出：

```java
昆伦
xiaolun
kunlun
```

**测试2**

```java
public class Person2 {
    protected String name = "kunlun";

    public void print(){ //添加了该方法
        System.out.println("Person");
    }
}
```

```java
public class Student extends Person2{
    private String name = "xiaolun";

    public void print(){//添加了该方法
        System.out.println("Student");
    }

    public void test(){
        print(); //代表当前类方法中的 print()
        this.print();  //和上面的print()方法一样。
        super.print(); // 父类的print()方法
    }
}
```

```java
public class Application {  //测试类
    public static void main(String[] args) {
        Student student = new Student();
        student.test();
    }
}
```

+ 控制台输出：

```java
Student
Student
Person
```

+ 当父类中的`print()`方法使用`private`修饰时，在子类中是使用`super.print();`会报错，主要是因为，私有的方法无法被继承。

**测试3**

```java
public class Person2 {
    public Person2() {
        System.out.println("Person无参构造执行了");
    }
}
```

```java
public class Student extends Person2{

    public Student() {
        //隐藏代码，默认调用了父类的无参构造方法
        //super(); //调用父类的构造器，必须放到子类构造器的第一行。
        //this(); //调用自己构造器会报错，它也需要放到第一行，所以this()和super()只能调用一个。
        System.out.println("Student无参构造执行了");
    }
}
```

```java
public class Application {
    public static void main(String[] args) {
        Student student = new Student();
    }
}
```

+ 控制台输出

```java
Person无参构造执行了
Student无参构造执行了
```

表明先执行父类构造器，然后执行子类构造器。

+ 当父类写有参构造方法时，无参构造也需要加上，不然在子类中`super();`会报错，同时，子类的无参构造也无法书写，也报错。但是调用有参的`super("name");`不会报错。

```java
//public Person2(String name) { //父类中书写了参构造方法，但是没有写无参构造，在子类中被调用。
//        System.out.println("Person无参构造执行了");
//    }

public class Student extends Person2{

    public Student() {
	super("name"); //不会报错，如果空着就会报错
        System.out.println("Student无参构造执行了");
    }
}
```

**总结**

注意点：

+ `super`调用父类的构造方法，必须在子类构造方法中的第一行。
+ `super`只能出现在子类的方法或者构造方法中。
+ `super`和`this`不能同时调用构造方法。

`super`和`this`的比较：

1. 代表的对象不同：

+ `this`代表本身调用者这个对象。
+ `super`代表父类对象的引用。

2. 前提条件不同

`this`在没有继承的时候也可以使用，`super`只能在继承条件时使用。

3. 构造方法不同

+ `this()`表示本类的构造。
+ `super()`表示父类的构造。

#### 8.3.2.1 方法重写

**要求**

重写需要有继承关系，是子类重写父类的方法。

+ 方法名、参数列表需要相同。
+ 修饰符：范围可以扩大但不能缩小。
+ 抛出的异常：范围可以缩小，但不能被扩大。

即：子类和父类的方法一致，方法体不同。

**静态方法测试**

```java
public class B { //父类
    public static void test(){ //静态方法
        System.out.println("B->test()");
    }
}
```

```java
public class A extends B{ //子类
    public static void test(){  //静态方法
        System.out.println("A->test()");
    }
}
```

```java
public class Application { //测试类
    public static void main(String[] args) {
        A a = new A(); //产生A这个对象
        A.test();

        //父类对引用指向了子类
        B b = new A();//产生A这个对象（子类赋值给父类）
        b.test();
    }
}
```

+ `new`的对象都是`A`但是输出结果确实不同的，控制台输出为：

```java
A->test()
B->test()
```

+ 使用该静态方法，方法的调用之和左边定义的数据类型有关。

**非静态方法**

```java
public class B {
    public void test(){
        System.out.println("B->test()");
    }
}
```

```java
public class A extends B{
    @Override  //方法重写
    public void test() {
//        super.test();
        System.out.println("A->test()");
    }
}
```

+ 控制台输出

```ava
A->test()
A->test(
```

### 8.3.3 多态

同一个方法可以根据发送对象的不同而采取多种不同的行为方式。

**多态存在的条件**

+ 有继承关系。
+ 子类重写父类方法。
+ 父类引用指向子类对象，如果反过来，可能会发生类型转换异常（`ClassCastException`）！

其中，多态是方法的多态，属性没有多态。

**不能被重写的方法**

+ `static`方法，属于类，不属于实例。
+ `final`常量修饰的方法。
+ `private`方法。

**测试**

```java
public class Person2 {
    public void run(){
        System.out.println("Person2->run");
    }
}
```

```java
public class Student extends Person2{

    @Override
    public void run() {
        System.out.println("Student->run");
    }

    public void eat(){
        System.out.println("eat");
    }
}
```

```java
public class Application {
    public static void main(String[] args) {
        /**
         * 1、一个对象的实际类型是确定的。
         * new Person2(); new Student();
         */
        //可以指向的引用类型是不确定的。父类的引用指向子类。
        //Student能调用的方法都是自己的或继承父类的。
        Student s1 = new Student();
        //父类引用可以指向子类，但是不能调用子类独有的方法。
        Person2 s2 = new Student();

        s1.run();
        s2.run(); //子类重写父类的方法，会执行子类的方法。
        /**
         *  对象能够执行那些方法，主要看对象左边的类型，与右边关系不大。
         *  因为eat()是Student类独有的方法，父类不能调用。
         */

//        s2.eat(); //报错
    }
}
```

+ 控制台输出结果：

```java
Student->run
Student->run
```

#### 8.3.3.1 instanceof和类型转换

`instanceof`判断一个对象是什么类型。i

nstanceof 运算符是用来在运行时指出对象是否是特定类的一个实例。instanceof通过返回一个布尔值来指出，这个对象是否是这个特定类或者是它的子类的一个实例。

**instanceof测试**

```java
public class Person2 { //父类
}
```

```java
public class Student extends Person2{//子类
}
```

```java
public class Teacher extends Person2{
}
```

```java
public class Application {
    public static void main(String[] args) {

        Object object = new Student();
        System.out.println(object instanceof Student);
        System.out.println(object instanceof Person2);
        System.out.println(object instanceof Object);
        System.out.println(object instanceof Teacher);
        System.out.println(object instanceof String);
        System.out.println("--------------------------");

        Person2 person = new Student();
        System.out.println(person instanceof Student);
        System.out.println(person instanceof Person2);
        System.out.println(person instanceof Object);
        System.out.println(person instanceof Teacher);
//        System.out.println(person instanceof String); //编译报错
        System.out.println("--------------------------");

        Student student = new Student();
        System.out.println(student instanceof Student);
        System.out.println(student instanceof Person2);
        System.out.println(student instanceof Object);
//        System.out.println(student instanceof Teacher); //编译报错
//        System.out.println(student instanceof String);
    }
}
```

+ 控制台输出结果

```java
true
true
true
false
false
--------------------------
true
true
true
false
--------------------------
true
true
true
```

**类型转换**

```java
public class Student extends Person2{ //该类中添加新方法go()
    public void go(){
        System.out.println("go");
    }
}
```

```java
public class Application { //测试类
    public static void main(String[] args) {
        //类型之间的转化
        // 高              低（子类转化为父类，可能丢失一些信息）
        Person2 obj = new Student();

        //Student将这个对象转化为Student类型，我们就可以使用Student类型的方法了。
        ((Student)obj).go(); //等价于下面两行代码
        // Student student = (Student) obj;
        // student.go();
    }
}
```

+ 
+ 把子类转化为父类，向上转型。
+ 把父类转化为子类，向下转型。
+ 类型转换的目的主要是方便方法的调用，减少重复的代码。

## 8.4 static关键字

**测试1**

```java
public class Student {
    private static int age; // 静态变量
    private double score; //非静态变量

    public static void go(){ //静态方法可以调用静态方法。
//        run(); //静态方法不能访问非静态方法
    }

    public void run(){
        go(); //非静态方法可以访问静态方法
    }

    public static void main(String[] args) {
        Student.go(); //可以直接这样书写
//        go();   //Student类和main方法在同一个类下，所以可以省去Student。
//        run(); //报错，静态方法不能访问非静态方法
    }
}
```

**静态代码块**

```java
public class Student {
    //1、在创建对象时会首先执行,且只执行一次
    static {
        System.out.println("静态代码块");
    }

    //2、其次执行，赋初值
    {
        System.out.println("匿名代码块");
    }
    //2、最后执行
    public Student() {
        System.out.println("构造方法");
    }

    public static void main(String[] args) {
        Student s1 = new Student();
        System.out.println("--------------");
        Student s2 = new Student();
    }
}
```

**静态导入包**

```java
//静态导入包
import static java.lang.Math.random;
public class Test {
    public static void main(String[] args) {
//        System.out.println(Math.random());
        System.out.println(random()); //直接使用random()
    }
}
```

## 8.5 抽象类

`abstract`修饰符可以修饰方法也可以修饰类，前者叫抽象方法，后者叫抽象类。抽象类中可以没有抽象方法，但是抽象方法的类一定要声明抽象类。子类继承抽象类，就必须要实现抽象类中没有实现的抽象方法，否则子类也要声明为抽象类。

抽象类不能直接使用`new`关键字来创建对象，它是用来子类继承的；抽象方法只有方法的声明，没有方法的实现，它是可以通过子类来实现的。

**测试**

```java
public abstract class Action{ //抽象类

    //约束，让其他人帮助我们来实现
    //抽象方法，只有方法名字，没有具体实现
    public abstract void doSomething();
    
    //普通方法
    public void test(){
        System.out.println("抽象类你好");
    }
}
```

```java
public class A extends Action{ //继承抽象类

    @Override //需要实现继承的抽象类的方法
    public void doSomething() {
        System.out.println("doSomething");
    }

    public static void main(String[] args) {
        A a = new A();
        a.test();
        a.doSomething(); //可以调用普通方法
    }
}
```

+ 控制台输出

```java
抽象类你好
doSomething
```

## 8.6 接口定义和实现

普通类只有具体实现，抽象类包含具体实现和规范（抽象方法），但接口只有规范，我们无法写方法（比较专业的约束，实现约束和实现分离）。在大公司中，都是面向接口编程，也就是公司架构师将所有的接口都定义好，我们只需要写里面的方法就好了。

接口就是规范，定义了一组规则，体现了现实世界的“如果你是...则必须能...”的思想，比如，如果你是汽车，则必须能跑。接口的本质是契约，就像人间法律一样，制定好后大家都遵守。

OO的精髓，是对对象的抽象，最能体现的这一点就是接口。同时，设计模式所研究的就是如何合理的去抽象。

声明接口关键字为`interface`。

```java
public interface UserService { //定义的接口

    //前面省略了static final修饰符，但一般不推荐写常量
    int AGE = 99;

    //前面省略了public abstract，我们只需要写返回类型和方面名就可以了。
    void add(); //方法无法具体实现。
    void delete();
    void update();
    void query();
}
```

```java
public class UserServiceImpl implements UserService { //接口的实现类
    @Override
    public void add() {

    }

    @Override
    public void delete() {

    }

    @Override
    public void update() {

    }

    @Override
    public void query() {

    }
}
```

## 8.7 内部类

内部类就是在一个类的内部定义了一个类，比如，A类中定义了一个类B，那么B类相对于A类来说就是内部类，而A类相对于B类来说就是外部类。

内部类包括成员内部类，静态内部类，局部内部类和匿名内部类。

**测试**

```java
public class Outer {

    private int id;
    public void out(){
        System.out.println("这是外部类的方法");
    }

    class Inner{  //内部类
        public void In(){
            System.out.println("这是内部类的方法");
        }

        //通过内部类获得外部类的私有属性
        public void getID(){
            System.out.println(id);
        }
    }
}
```

```java
public class Application {
    public static void main(String[] args) {
        Outer outer = new Outer();
        //通过外部类来实例化内部类
        Outer.Inner inner = outer.new Inner();
        inner.In();
        inner.getID();
    }
}
```

+ 控制台输出

```java
这是内部类的方法
0
```

# 9 异常

## 9.1 Error和Exception

异常发生在程序运行期间，影响了正常的程序执行流程。

**三种类型的异常**

**1、检查性异常**

最具代表的检查性异常是用户错误或问题引起的异常，这是程序员无法预见的。例如打开一个不存在的文件时，一个异常就发生了。在编译时不能被简单地忽略。

**2、运行时异常**

运行时异常是可能被程序员避免的异常。可以在编译时忽略。

**3、错误Error**

错误不是异常，而是脱离程序员控制的问题。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901084612.png" alt="image-20200531145205604" style="zoom: 67%;" />

Java把异常作为对象来处理，并定义一个基类java.lang.Throwable作为所有异常的超类。

Error类对象由Java虚拟机生成并抛出，大多数错误与代码编写者所执行的操作有关。

Exception分支中有一个重要的子类RuntimeException（运行时异常）

+ ArrayIndexOutOfBoundsException(数组下标越界)
+ NullPointerException(空指针异常)
+ ArithmeticException(算术异常)
+ MissingResourceException(丢失资源)
+ ClassNotFoundException(找不到类)异常

## 9.2 捕获和抛出异常

```java
public class Test {
    public static void main(String[] args) {
        int a = 1;
        int b = 0;
        try{  //try 监控区域
            System.out.println(a / b);
        }catch (ArithmeticException e){  //catch（想要捕获的异常类型） 捕获异常
            System.out.println("程序出现异常，变量b不能为0");
            e.printStackTrace(); //打印错误的栈信息
        }finally {  //处理善后工作
            System.out.println("finally");
        }
    }
}
```

**throw和throws的区别**

**1、throw**

+ 用在方法体内，跟的是异常对象名。只能抛出一个异常对象名。

**2、throws**

+ 用在方法声明的后面，跟的是异常类名，可以有多个异常类名，用逗号隔开。
+ 抛出异常，由方法的调用者来处理。

**3、处理异常的原则**

如果在函数体内用`throw`抛出了某种异常，最好要在函数名中加`throws`抛异常声明，然后交给调用它的上层函数进行处理，我们要将上层语句可能会出现异常的部分使用`try-catch`语句来包裹，这样的话，才能够对异常进行处理。

所以，我们在写程序时，对可能会出现异常的部分通常要用`try-catch`语句去捕捉它并对它进行处理；如果是捕捉IO输入输出流中的异常，一定要在`try-catch-finally`语句把输入输出流关闭；

```java
public class Test {
    public static void main(String[] args) {

        try {
            new Test().test(1, 0); //调用者拿到这个异常进行处理
        } catch (ArithmeticException e) {
            e.printStackTrace();
        }
        System.out.println("程序继续执行"); //添加try后可以继续执行
    }

    //假设该方法中处理不了这个异常，需要在方法上抛出
    public void test(int a,int b) throws ArithmeticException{
        if (b == 0){
            throw new ArithmeticException(); //主动抛出异常
        }
        System.out.println("你好");// 没有执行到这里
    }
}
```

+ 控制台输出

```java
java.lang.ArithmeticException
	at com.xiaolun.Test.test(Test.java:17)
	at com.xiaolun.Test.main(Test.java:7)
程序继续执行
```

## 9.3 自定义异常

使用Java内置的异常类可以描述在编程时出现的大部分异常情况，除此之外，用户还可以自定义异常。用户自定义异常类，只需要继承Exception类即可。

**步骤**

+ 创建自定义异常类。
+ 在方法中通过throw关键字抛出异常对象。
+ 如果在当前抛出异常的方法中处理异常，可以使用`try-catch`语句捕获异常并处理，否则在方法的声明处通过throws关键字指明要抛出的异常类型，传递给方法调用者。
+ 在出现异常方法的调用者中捕获并处理异常。

```java
public class MyException extends Exception{ //自定义异常

    //传递数字如果大于10,就抛出异常
    private int detail;

    public MyException(int a) {
        this.detail = a;
    }

    @Override //异常打印的信息
    public String toString() {
        return "MyException出现"+ detail;
    }
}
```

```java
public class Test { //测试类

    static  void test(int a) throws MyException {
        System.out.println("传递的参数为："+ a);

        if (a > 10){
            throw new MyException(a); //抛出
        }
        System.out.println("没有异常，正常执行。");
    }

    public static void main(String[] args) {
        try {
            test(12);
        } catch (MyException e) { //e=自定义异常中的输出信息："MyException出现"+ detail
            System.out.println("测试类中异常"+e);
        }
    }
}
```

+ 控制台输出：

```java
传递的参数为：12
测试类中异常 MyException出现12
```









