# 1 介绍

UML(Unified Modeling Language)统一建模语言，UML式在开发阶段，说明、可视化、构建和书写一个面向对象系统的一种开放方法。模型大多以图表的方式表现出来，一份典型的建模图表通常包含几个块或框，连接线和作为模型附加信息的只用的文本。它可以帮助软件开发人员进行思考和记录思路的结果。

UML是一种建模语言，而不是一个开发过程。

1. UML包括：事物、关系、图和扩展机制。

+ 事物：
  + 结构：类、接口、构件（软件的一个文件，比如一个jarb包）和节点等。
  + 行为：交互（消息）和状态等。
  + 分组：包和子系统等。
  + 注释

+ 关系：
  + 依赖
  + 关联（聚合、组合）、泛化、实现

+ 图：
  + 用例图、交互图（顺序图、协作图）、类图、活动图和状态图等。

+ 扩展机制：
  + Stereotype、Tagged Value 、Constaint。

2. 图的分类：

+ 静态建模 
  + 类图
+ 动态建模
  + 顺序图（协作图）
  + 用例图
  + 活动图
  + 状态图

+ 图形描述
  + 用例图：从用户角度描述系统功能。
  + 类图：描述系统中类的静态结构（重点关注）。
  + 对象图：系统中的多个对象在某一个时刻的状态。
  + 状态图：是描述状态到状态控制流，用于动态特性建模。
  + 顺序图：对象之间的动态合作关系，强调对象发送消息的顺序，同时显示对象之间的交互。
  + 协作图：描述对象之间的协作关系。
  + 构件图：一种特殊的UML图来描述系统的静态实现视图。
  + 部署图：定义系统中软硬件的物理体系结构。
  + 包图：对构成系统的模型元素进行分组整理的图。
  + 组合结构图：表示类或者构建内部结构的图。
  + 活动图：描述业务实现用例的工作流程。
  + 交互概览图：用活动图来表示多个用户之间的控制关系的图。

+ 学习顺序为：
  + 类图 -> 顺序图 -> 用例图 -> 活动图 -> 状态图。

# 2 类图

## 2.1 类与类之间的关系

### 2.1.1 依赖（Dependency）

使用虚线+箭头，箭头指向被依赖的类的方式进行表示。如果A依赖于B,B可以通过构造函数参数，方法参数，方法返回值，方法内局部变量的形式存在于A类中。也就是在类中用到了对方。

这种使用关系是具有偶然性、临时性、非常弱的特点，但是类B会影响到类A 。

```java
//Car通过构造参数的形式存在于Driver中。
public class Car {
   public void startup() {
      System.out.print(“car startup”);
   }
}
public class Driver {
   public Driver(Car car) {
      car.startup();
   }
}
```

+ 驾驶员类Driver需要依赖汽车类Car才能行驶,其中Car可以通过构造函数参数，方法参数等形式存在于Driver类中。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200503105835704.png" alt="image-20200503105835704" style="zoom:150%;" />

```java
// Car通过方法参数的形式存在于Driver中
public class Driver2 {
   public void dirver(Car car) {
      car.startup();
   }
}
```

### 2.1.2 关联(Association)

使用实线+箭头，箭头指向被关联的类的方式进行表示。表示了一个类A与另一个类B之间的联系，使类A知道类B的属性和方法。通常类B会以私有成员变量的形式存在于类A中。可以通过get/set函数进行赋值。

这种关系具有必然的，长期的，强烈的特点。

关联形式有多种，比如分为一对一（员工和工牌）、一对多（部门和员工）和多对对关联（商店和商品）。

1. 类型：对一（员工和工牌）、一对多（部门和员工）。

```java
//工牌类代码:
public class WorkCard {
}
//员工类代码:
public class Employee {
   private WorkCard workCard;
}
//部门类代码：
public class Department {
   private List<Employee> employees;
}
```

+ 带普通箭头的实心线，指向被拥有者

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200503110057073.png" alt="image-20200503110057073" style="zoom:80%;" />

2. 类型;多对对关联（商店和商品）。

```java
public class Shop {
   private List<Goods> goodsList;
}
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200503110430318.png" alt="image-20200503110430318" style="zoom:150%;" />

### 2.1.3 聚合(Aggregation)

使用空心菱形+实线+箭头，箭头指向个体方式进行表示。比一般关联具有更强的关联关系，是关联关系的一种特例。一般关联关系的两个类处于同一个层次上，聚合关系的两个类处于不同的层次上，强调整体和个体之间的关系。

```java
//书页类代码：
public class BookPage {
}
//书本类代码：
public class Book {
   private List<BoolPage> bookPageList;
}
```

+ 书本和书页类型，这个类型对于组合页同样适用，因为当书本消失的时候，书页页跟着消失了。
+ 带空心菱形的实心线，菱形指向整体。
+ Multiplicity:多重性，0…1(0到1，可选)



<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200503110633273.png" alt="image-20200503110533385" style="zoom:150%;" />



<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200503110533385.png" alt="image-20200503110633273" style="zoom:80%;" />

+ 聚合图形多重性的表示

### 2.1.4 组合Composition)

使用实心菱形+实线+箭头，箭头指向个体方式进行表示。比聚合关系更强的关联关系，是关联关系的一种特例。要求代表整体的对象也代表个体的对象的生命周期。当删除整体类时，要级联删除其下的个体。比如人体和四肢之间的关系。

带实心菱形的实线,菱形指向整体

### 2.1.5 继承（泛化关系Generalization）

使用实线+空心三角形，三角形指向父类的方式进行表示。

子类继承父类,或者是接口和子接口之间的关系。语法为: `extends`。

```java
//父类代码：
public class Parent {
     private String name;
}
//子类代码：
public class Child extends Parent {
}
```

+ 带三角形箭头的实线，箭头指向父类。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200503110740448.png" alt="image-20200503110740448" style="zoom:150%;" />

### 2.1.6 实现（Realization）

使用虚线+空心三角形，三角形指向接口的方式进行表示。

表示类和接口之间的关系，一个类可以实现多个接口，语法为：`implements`。

```java
//接口类代码：
public interface IParent {
    String name();
}

//实现类代码：
public class IChild implements IParent {
   @Override
   public String name() {
      return null;
   }
}
```

+ 带三角形箭头的虚线，箭头指向接口。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200503111052329.png" alt="image-20200503110923364" style="zoom:150%;" />

## 2.2 外国小哥类图

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200503110923364.png" alt="image-20200503111020713" style="zoom:150%;" />

+ 动物园系统图

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200503111020713.png" alt="image-20200503111052329" style="zoom:150%;" />

+ 在线购物图