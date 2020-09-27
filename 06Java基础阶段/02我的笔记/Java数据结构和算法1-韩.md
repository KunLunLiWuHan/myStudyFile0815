# 1、数据结构和算法概述

1、数据结构和算法的关系

数据data结构(structure)是一门研究组织数据方式的学科，有了编程语言也就有了数据结构.学好数据结构可以编写出更加漂亮,更加有效率的代码。

要学习好数据结构就要多多考虑如何将生活中遇到的问题,用程序去实现解决。

程序 = 数据结构+ 算法。

2、线性结构和非线性结构

数据结构包括：线性结构和非线性结构。

（1）线性结构

线性结构作为最常用的数据结构，其特点是数据元素之间存在一对一的线性关系。

线性结构有两种不同的存储结构，即顺序存储结构和链式存储结构。顺序存储的线性表称为顺序表，顺序表中的存储元素是连续的。

链式存储的线性表称为链表，链表中的存储元素不一定是连续的，元素节点中存放数据元素以及相邻元素的地址信息。

线性结构常见的有：数组、队列、链表和栈。

（2）非线性结构

非线性结构包括：二维数组，多维数组，广义表，树结构，图结构。

# 2、稀疏数组和对列

## 1、稀疏数组

### 1、介绍

1、概述

当一个数组中大部分元素为０，或者为同一个值的数组时，可以使用稀疏数组来保存该数组。

2、处理方法

（1）记录数组一共有几行几列，有多少个不同的值。

（2）把具有不同值的元素的行列及值记录在一个小规模的数组中，从而缩小程序的规模。

3、举例说明

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200925152441506.png" alt="image-20200925152441506" style="zoom:67%;" />

### 2、案例

1、需求

（1）使用稀疏数组，来保留类似前面的二维数组(棋盘、地图等等)。

（2）把稀疏数组存盘，并且可以从新恢复原来的二维数组。

2、思路

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200925152611510.png" alt="image-20200925152611510" style="zoom:67%;" />

（1）二维数组 转 稀疏数组的思路

+ 遍历 原始的二维数组，得到有效数据的个数 sum

+ 根据sum 就可以创建 稀疏数组 sparseArr  int[sum + 1]  [3]

+ 将二维数组的有效数据数据存入到 稀疏数组

（2）稀疏数组转原始的二维数组的思路

+ 先读取稀疏数组的第一行，根据第一行的数据，创建原始的二维数组，比如上面的 chessArr2 = int [11] [11]

+ 在读取稀疏数组后几行的数据，并赋给原始的二维数组 即可。

3、代码

```java
package com.atguigu.sparsearray;

/**
 * 稀释数组
 */
public class SparseArray {
   public static void main(String[] args) {
      // 创建一个原始的二维数组 11 * 11
      // 0: 表示没有棋子， 1 表示 黑子 2 表蓝子
      int chessArr1[][] = new int[11][11];
      chessArr1[1][2] = 1;
      chessArr1[2][3] = 2;
      // 输出原始的二维数组
      System.out.println("原始的二维数组~~");
      for (int[] row : chessArr1) { //遍历每一行
         for (int data : row) { //遍历每一行中的数据
            System.out.printf("%d\t", data);
         }
         System.out.println();
      }

      // 将二维数组 转 稀疏数组的思
      // 1. 先遍历二维数组 得到非0数据的个数
      int sum = 0;
      for (int i = 0; i < 11; i++) {
         for (int j = 0; j < 11; j++) {
            if (chessArr1[i][j] != 0) {
               sum++;
            }
         }
      }

      // 2. 创建对应的稀疏数组
      int sparseArr[][] = new int[sum + 1][3];
      // 给稀疏数组的第一行赋值
      sparseArr[0][0] = 11;
      sparseArr[0][1] = 11;
      sparseArr[0][2] = sum;

      // 遍历二维数组，将非0的值存放到 sparseArr中
      int count = 0; //count 用于记录是第几个非0数据(递增)
      for (int i = 0; i < 11; i++) {
         for (int j = 0; j < 11; j++) {
            if (chessArr1[i][j] != 0) {  //chessArr1[1][2]=1
               count++;
               //[0]表示第1列，i表示遍历第几行
               sparseArr[count][0] = i;
               sparseArr[count][1] = j; //第2列
               sparseArr[count][2] = chessArr1[i][j]; //第3列
            }
         }
      }

      // 输出稀疏数组的形式
      System.out.println();
      System.out.println("得到稀疏数组为~~~~");
      for (int i = 0; i < sparseArr.length; i++) {
         System.out.printf("%d\t%d\t%d\t\n", sparseArr[i][0], sparseArr[i][1], sparseArr[i][2]);
      }

      //将稀疏数组 --》 恢复成 原始的二维数组
      /*
       *  1. 先读取稀疏数组的第一行，根据第一行的数据，创建原始的二维数组，比如上面的  chessArr2 = int [11][11]
         2. 在读取稀疏数组后几行的数据，并赋给 原始的二维数组 即可.
       */
      //1. 先读取稀疏数组的第一行，根据第一行的数据，创建原始的二维数组
      int chessArr2[][] = new int[sparseArr[0][0]][sparseArr[0][1]];

      //2. 在读取稀疏数组后几行的数据(从第二行开始)，并赋给 原始的二维数组 即可
      for (int i = 1; i < sparseArr.length; i++) {
         chessArr2[sparseArr[i][0]][sparseArr[i][1]] = sparseArr[i][2];
      }

      // 输出恢复后的二维数组
      System.out.println();
      System.out.println("恢复后的二维数组");

      for (int[] row : chessArr2) {
         for (int data : row) {
            System.out.printf("%d\t", data);
         }
         System.out.println();
      }
   }
}
```

## 2、队列

### 1、介绍

1、概述

队列是一个有序列表，可以用**数组**或是**链表**来实现。

遵循**先入先出**的原则。即：先存入队列的数据，要先取出。后存入的要后取出。

2、示意图

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200925155914383.png" alt="image-20200925155914383" style="zoom:80%;" />

队列本身是有序列表，若使用数组的结构来存储队列的数据，则队列数组的声明如下图, 其中 maxSize 是该队列的最大容量。

因为队列的输出、输入是分别从前后端来处理，因此需要两个变量 front及 rear分别记录队列前后端的下标，front 会随着数据输出而改变，而 rear则是随着数据输入而改变。

3、数组模拟对列步骤

（1）将尾指针往后移：rear+1 , 当front == rear （空）。

（2）若尾指针 rear 小于队列的最大下标 maxSize-1，则将数据存入 rear所指的数组元素中，否则无法存入数据。 rear == maxSize - 1（队列满）。

### 2、案例

1、代码

（1）对列类

```java
package com.xiaolun.queue;

public class ArrayQueue {
   private int maxSize; // 表示数组的最大容量
   private int front; // 队列头
   private int rear; // 队列尾
   private int[] arr; // 该数据用于存放数据, 模拟队列

   // 创建队列的构造器
   public ArrayQueue(int arrMaxSize) {
      maxSize = arrMaxSize;
      arr = new int[maxSize];
      front = -1; // 指向队列头部，分析出front是指向队列头的前一个位置.
      rear = -1; // 指向队列尾，指向队列尾的数据(即就是队列最后一个数据)
   }

   // 判断队列是否满
   public boolean isFull() {
      return rear == maxSize - 1;
   }

   // 判断队列是否为空
   public boolean isEmpty() {
      return rear == front;
   }

   // 添加数据到队列
   public void addQueue(int n) {
      // 判断队列是否满
      if (isFull()) {
         System.out.println("queue is full,can't add Queue Data");
         return;
      }
      rear++; // 让rear 后移
      arr[rear] = n;
   }

   // 获取队列的数据, 出队列
   public int getQueue() {
      // 判断队列是否空
      if (isEmpty()) {
         // 通过抛出异常来处理
         throw new RuntimeException("queue is empty,can't get Queue Data");
      }
      front++; // front后移
      return arr[front];
   }

   // 显示队列的所有数据
   public void showQueue() {
      // 遍历
      if (isEmpty()) {
         System.out.println("队列空的，没有数据~~");
         return;
      }
      for (int i = 0; i < arr.length; i++) {
         System.out.printf("arr[%d]=%d\n", i, arr[i]);
      }
   }

   // 显示队列的头数据， 注意不是取出数据
   public int headQueue() {
      // 判断
      if (isEmpty()) {
         throw new RuntimeException("queue is empty,can't get head Queue data");
      }
      return arr[front + 1];
   }
}
```

（2）测试类

```java
package com.xiaolun.queue;

import java.util.Scanner;

public class ArrayQueueDemo {
   public static void main(String[] args) {
      //测试一把
      //创建一个队列
      ArrayQueue queue = new ArrayQueue(3);
      char key = ' '; //接收用户输入
      Scanner scanner = new Scanner(System.in);//
      boolean loop = true;
      //输出一个菜单
      while (loop) {
         System.out.println("s(show): 显示队列");
         System.out.println("e(exit): 退出程序");
         System.out.println("a(add): 添加数据到队列");
         System.out.println("g(get): 从队列取出数据");
         System.out.println("h(head): 查看队列头的数据");
         key = scanner.next().charAt(0);//接收一个字符
         switch (key) {
            case 's':
               queue.showQueue();
               break;
            case 'a':
               System.out.println("输出一个数");
               int value = scanner.nextInt();
               queue.addQueue(value);
               break;
            case 'g': //取出数据
               try {
                  int res = queue.getQueue();
                  System.out.printf("取出的数据是%d\n", res);
               } catch (Exception e) {
                  System.out.println(e.getMessage());
               }
               break;
            case 'h': //查看队列头的数据
               try {
                  int res = queue.headQueue();
                  System.out.printf("队列头的数据是%d\n", res);
               } catch (Exception e) {
                  System.out.println(e.getMessage());
               }
               break;
            case 'e': //退出
               scanner.close();
               loop = false;
               break;
            default:
               break;
         }
      }
      System.out.println("程序退出~~");
   }
}
```

存在的问题：

（1）目前数组使用一次就不能再用，没有达到复用的目的。

（2）将该数组改进成一个环形的对列（取模 %）。

## 3、数组模拟环形队列

### 1、介绍

1、分析

对前面的数组模拟对列进行优化，充分利用数组。

（1）对列为满的判断

尾索引的下一个为头索引时表示队列满，即将队 列容量空出一个作为约定,这个在做判断队列满的时候需要注意：

```
 (rear + 1) % maxSize == front
```

（2）对列为空的判断

```
rear == front
```

2、示意图

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200925164804320.png" alt="image-20200925164804320" style="zoom:80%;" />

思路如下:

 （1）front 变量的含义做一个调整： front 就指向队列的第一个元素, 也就是说 arr[front] 就是队列的第一个元素front 的初始值 = 0

 （2）rear 变量的含义做一个调整：rear 指向队列的最后一个元素的后一个位置. 因为希望空出一个空间做为约定。rear 的初始值 = 0。

（3） 队列中有效的数据的个数：

```java
// rear = 1 front = 0 表示只有一个元素 
(rear + maxSize - front) % maxSize
```

（4）我们可以在原来的队列上修改得到，一个环形队列。

### 2、案例

1、环形对列代码

```java
package com.xiaolun.queue;

public class CircleArray {
   private int maxSize; // 表示数组的最大容量
   private int front;
   private int rear;
   private int[] arr;

   public CircleArray(int arrMaxSize) {
      maxSize = arrMaxSize;
      arr = new int[maxSize];
   }

   // 判断对列是否满
   public boolean isFull() {
      return (rear + 1) % maxSize == front;
   }

   //判断对列是否空
   public boolean isEmpty() {
      return rear == front;
   }

   // 添加数据到对列中
   public void addQueue(int n) {
      if (isFull()) {
         System.out.println("队列满，不能添加数据");
         return;
      }
      //直接将数据加入
      arr[rear] = n;
      //将rear后移，这里必须考虑取模
      rear = (rear + 1) % maxSize;
   }

   // 获取对列的数据，出队列
   public int getQueue() {
      // 判断对列是否为空
      if (isEmpty()) {
         // 抛出异常
         throw new RuntimeException("对列空，不能取数据");
      }
      // 这里需要分析出front是指向对列的第一个元素
      // 1. 先把front对应的值保留到一个临时变量中，这样的话，front才有机会后移
      // 2. 将front后移，考虑取模（保证数组下标不越界）
      // 3. 将临时保存的变量返回
      int value = arr[front];
      front = (front + 1) % maxSize;
      return value;
   }

   // 显示对列的所有数据
   public void showQueue() {
      // 遍历
      if (isEmpty()) {
         System.out.println("对列为空，没有数据");
         return;
      }
      // 思路：从front遍历，遍历多少个元素
      // 动脑筋
      for (int i = front; i < front + size(); i++) {
         System.out.printf("arr[%d]=%d\n", i % maxSize, arr[i % maxSize]);
      }
   }

   // 求出当前对列中有效数据的个数
   public int size() {
      // rear = 2
      // front = 1
      // maxSize = 3 
      return (rear + maxSize - front) % maxSize;
   }

   // 显示对列的头数据
   public int headQueue() {
      if (isEmpty()) {
         throw new RuntimeException("对列为空，没有数据");
      }
      return arr[front];
   }
}
```

（2）测试类

```java
//创建一个环形对列
CircleArray queue = new CircleArray(4); //设置4,说明对列中的有效数据最大为3
```

# 3、单链表

## 1、介绍

1、概述

链表（Linked List）是有序的列表，但是它在内存中是存储如下：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200925195046667.png" alt="image-20200925195046667" style="zoom:67%;" />

（1）链表是以节点的方式来存储，是链式存储，链表的各个节点不一定是连续存储。

（2）每个节点包含 data 域和 next 域，其中next域指向下一个节点。

（3）链表分带头节点的链表和没有头节点的链表，根据实际的需求来确定。

2、逻辑结构存储

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200925195258835.png" alt="image-20200925195258835" style="zoom:80%;" />

## 2、案例

1、需求

使用带head头的单向链表实现 –水浒英雄排行榜管理：

主要完成对英雄人物的增删改查操。

（1）第一种方法在添加英雄时，直接添加到链表的尾部

（2）第二种方式在添加英雄时，根据排名将英雄插入到指定位置 (如果在链表中这个排名已存在，则添加失败，并给出提示)。

（3）修改节点

（4）删除节点

2、分析

（1）插入链表末尾

![image-20200925195659214](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200925195659214.png)

创建操作：

+ 先创建一个head 头节点， 作用就是表示单链表的头。

+ 后面我们每添加一个节点，就直接加入到 链表的最后。

遍历操作：

+ 通过一个辅助变量遍历，帮助遍历整个链表。

（2）顺序插入节点

![image-20200925195806823](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200925195806823.png)

需要按照编号的顺序添加

+ 首先找到新添加的节点的位置, 是通过辅助变量(指针), 通过遍历来搞定。

+ 新的节点.next = temp.next。

+ 将temp.next = 新的节点。

（3）修改节点

+ 通过遍历，先找到该节点（编号不进行修改，只修改下面两个内容）。
+ temp.name=newHeroNode.name。
+ temp.nickname=newHeroNode.nickname。

（4）删除节点

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200925200231784.png" alt="image-20200925200231784" style="zoom:80%;" />

+ 首先找到需要删除的这个节点的前一个节点 temp。

+ temp.next = temp.next.next。

+ 被删除的节点，将不会有其它引用指向，会被垃圾回收机制回收。

## 3、代码

1、节点类

```java
package com.xiaolun.linkedlist;

//定义HeroNode，每个HeroNode 对象就是一个节点
public class HeroNode {
   public int no;
   public String name;
   public String nickname; //昵称
   public HeroNode next; //指向下一个节点

   //构造器
   public HeroNode(int no, String name, String nickname) {
      this.no = no;
      this.name = name;
      this.nickname = nickname;
   }

   @Override
   public String toString() {
      return "HeroNode [no=" + no + ", name=" + name + ", nickname=" + nickname + "]";
   }
}
```

2、链表类

```java
package com.xiaolun.linkedlist;

//定义SingleLinkedList，管理我们的英雄
public class SingleLinkedList {
   //先初始化一个头节点，头节点不要动。不存放具体的数据
   private HeroNode head = new HeroNode(0, "", "");

   //第一种方式：添加节点到单向链表
   //思路：当不考虑编号顺序时
   //1. 找到当前链表的最后节点
   //2. 将最后这个节点的next指向新节点
   public void add(HeroNode heroNode) {
      //因为head节点不能动，所以我们需要一个辅助遍历 temp
      HeroNode temp = head;

      /**
       * 遍历链表，找到最后
       * 因为head一直是不变的，因此第一次的temp.next是固定的，第二次的temp.next.next
       * 也是固定的
       */
      while (true) {
         //找到链表的最后
         if (temp.next == null) {
            break;
         }
         //如果没有找到链表最后，将temp后移
         temp = temp.next;
      }
      //当退出while循环时，temp就指向了链表的最后
      //将最后这个节点的next指向新节点
      temp.next = heroNode;
   }

   //第二种方式添加英雄，根据排名将英雄插入到指定位置
   //(如果有这个排名，则添加失败，并给出提示)
   public void addByOrder(HeroNode heroNode) {
      //因为头节点不能动，因此，我们仍然通过一个辅助指针（变量）来帮助我们找到添加的位置
      //因为是单链表，因此我们找到temp是位于添加位置的前一个节点，否则插入不了
      HeroNode temp = head;
      boolean flag = false; // flag标志添加的标号是否存在，默认为false
      while (true) {
         if (temp.next == null) {//说明temp已经在链表的最后
            break;
         }
         /**
          * (1)hero1->hreo4
          * (2)添加hero
          * (3)遍历到hero1时，temp.next.no（hreo.no=4）>hero.no(2)
          * 说明hreo2在hreo1的后面
          */
         if (temp.next.no > heroNode.no) { //位置找到，就在temp的后面插入
            break;
         } else if (temp.next.no == heroNode.no) {//说明希望添加的heroNode的编号已经存在
            flag = true; //说明编号存在
            break;
         }
         temp = temp.next; //后移，遍历当前链表
      }
      //判断flag的值
      if (flag) { //不能添加，说明编号存在
         System.out.printf("准备输入的英雄编号 %d 已经存在了, 不能加入\n", heroNode.no);
      } else {
         //插入到链表中，temp的后面
         heroNode.next = temp.next;
         temp.next = heroNode;
      }
   }

   //修改节点的信息，根据no编号来修改，即no编号不能改
   //1. 根据 newHeroNode 的 no 来修改即可
   public void update(HeroNode newHeroNode) {
      //判断是否为空
      if (head.next == null) {
         System.out.println("链表为空~");
         return;
      }
      //找到需要修改的节点，根据no编号
      //定义一个辅助变量
      HeroNode temp = head.next;
      boolean flag = false; //表示是否找到该节点
      while (true) {
         if (temp == null) {
            break; //已经遍历完链表
         }
         if (temp.no == newHeroNode.no) {
            //找到该节点
            flag = true;
            break;
         }
         temp = temp.next;
      }
      //根据flag判断是否找到要修改的节点
      if (flag) {
         temp.name = newHeroNode.name;
         temp.nickname = newHeroNode.nickname;
      } else { //没有找到
         System.out.printf("没有找到 编号 %d 的节点，不能修改\n", newHeroNode.no);
      }
   }

   //删除节点
   //思路
   //1. head 不能动，因此，我们需要一个temp辅助节点找到待删除节点的前一个节点
   //2. 说明我们在比较时，是temp.next.no和需要删除的节点的no比较
   public void del(int no) {
      HeroNode temp = head;
      boolean flag = false; // 标志是否找到待删除节点的
      while (true) {
         if (temp.next == null) { //已经到链表的最后
            break;
         }
         if (temp.next.no == no) {
            //找到待删除节点的前一个节点temp
            flag = true;
            break;
         }
         temp = temp.next; //temp后移，遍历
      }
      //?ж?flag
      if (flag) { //找到
         //可以删除
         temp.next = temp.next.next;
      } else {
         System.out.printf("要删除的 %d 节点不存在\n", no);
      }
   }

   //显示链表（遍历）
   public void list() {
      //判断链表是否为空
      if (head.next == null) {
         System.out.println("链表为空");
         return;
      }
      //因为头节点不能动，我们需要一个辅助变量来遍历
      HeroNode temp = head.next;
      while (true) {
         //判断是否到链表最后
         if (temp == null) {
            break;
         }
         //输出节点信息
         System.out.println(temp);
         //将temp后移，一定小心。不然是一个死循环
         temp = temp.next;
      }
   }
}
```

3、测试类

```java
package com.xiaolun.linkedlist;

public class SingleLinkedListDemo {
   public static void main(String[] args) {
      HeroNode hero1 = new HeroNode(1, "宋江", "及时雨");
      HeroNode hero2 = new HeroNode(2, "卢俊义", "玉麒麟");
      HeroNode hero3 = new HeroNode(3, "吴用", "智多星");
      HeroNode hero4 = new HeroNode(4, "林冲", "豹子头");

      //创建一个单向链表
      SingleLinkedList singleLinkedList = new SingleLinkedList();

      //第一种方式加入
//    singleLinkedList.add(hero1);
//    singleLinkedList.add(hero4);
//    singleLinkedList.add(hero2);
//    singleLinkedList.add(hero3);
//
//    // 遍历
//    singleLinkedList.list();

      //第二种方式添加
      singleLinkedList.addByOrder(hero1);
      singleLinkedList.addByOrder(hero4);
      singleLinkedList.addByOrder(hero2);
      singleLinkedList.addByOrder(hero3);

      // 遍历
      singleLinkedList.list();

      //修改新节点
      System.out.println("-----------------------------");
      HeroNode newHeroNode = new HeroNode(2, "小龙", "小尾巴");
      singleLinkedList.update(newHeroNode);
      // 遍历
      singleLinkedList.list();

      //删除节点
      System.out.println("-----------------------------");
      singleLinkedList.del(2);
      singleLinkedList.list();
   }
}
```

## 4、面试题

1、求单链表中有效节点的个数

（1）main函数中添加方法

```java
/**
 * 求单链表中有效节点的个数
 *
 * @param head 链表的头节点
 * @return 返回有效节点的个数
 */
public static int getLength(HeroNode head) {
   if (head.next == null) { //空链表
      return 0;
   }
   int length = 0;
   //定义一个辅助变量，这里没有统计头节点
   HeroNode cur = head.next;
   while (cur != null) {
      length++;
      cur = cur.next; //遍历
   }
   return length;
}
```

（2）SingleLinkedList中添加获取头节点的方法

```java
public HeroNode getHead() {
   return head;
}
```

（3）测试

```java
getLength(singleLinkedList.getHead());
```

2、查找单链表中的倒数第k个结点 

（1）main中添加方法

```java
/**
 * 查找单链表中的倒数第k个结点
 * 1. 编写方法，接收head节点和参数index
 * 2. 先将链表从头到尾遍历，得到链表长度 getLength（size）
 * 3. 得到尺寸后，从链表的第一个开始，遍历(size-index)个，就可以得到
 * 4. 如果找到了，则返回该节点，反之返回null
 *
 * @param head  头节点
 * @param index 表示倒数第index个节点
 * @return
 */
public static HeroNode findLastIndexNode(HeroNode head, int index) {
   //判断链表是否为null
   if (head.next == null) {
      return null; //没有找到
   }
   //第一个遍历得到的链表长度（节点个数）
   int size = getLength(head);
   //第二次遍历  size-index 位置，就是我们倒数的第K个节点
   //先做一个index的校验
   if (index <= 0 || index > size) {
      return null;
   }
   //定义给辅助变量，for循环定位到倒数的index
   HeroNode cur = head.next;
   for (int i = 0; i < size - index; i++) {
      cur = cur.next;
   }
   return cur;
}
```

（2）测试

```java
System.out.println("---------查找倒数第一个数据---------");
HeroNode res = findLastIndexNode(singleLinkedList.getHead(), 1);
System.out.println("res=" + res);
```

3、单链表的反转

思想：

+ 先定义一个节点 reverseHead = new HeroNode();

+ 从头到尾遍历原来的链表，每遍历一个节点，就将其取出，并放在新的链表reverseHead 的最前端.

+ 原来的链表的head.next = reverseHead.next

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200925213224930.png" alt="image-20200925213224930" style="zoom:80%;" />

（1）main函数中添加方法

```java
	//将链表反转
	public static void reversetList(HeroNode head) {
		//如果当前链表为空，或者只有一个节点，无需反转，直接返回。
		if (head.next == null || head.next.next == null) {
			return;
		}

		//定义一个辅助的指针（变量），帮助我们遍历原来的链表
		HeroNode cur = head.next;
		HeroNode next = null;// 指向当前节点[cur]的下一个节点
		HeroNode reverseHead = new HeroNode(0, "", "");

		//遍历原来的链表，每遍历一个节点，就将其取出，并放到一个新链表reverseHead的前端
		while (cur != null) {
			next = cur.next;//先暂时保存当前节点的下一个节点，因为后面需要使用
			cur.next = reverseHead.next;//将cur的下一个节点指向新的链表的最前端（第二次指向的时候，变成cur.next -> no.1）
			reverseHead.next = cur; //（reverseHead.next 指向 cur）将cur连接到新的链表上
			cur = next;//让cur后移
		}

		//将head.next 指向 reverseHead.next，实现单链表的反转
		head.next = reverseHead.next;
	}
```

（2）测试

```java
reversetList(singleLinkedList.getHead());
singleLinkedList.list();
```

4、从尾到头打印单链表



（1）main中添加方法

```java
//可以利用栈这个数据结构，将各个节点压入到栈中，然后利用栈的先进后出的特点，就实现了逆序打印的效果
public static void reversePrint(HeroNode head) {
   if(head.next == null) {
      return;//空链表，不能打印
   }
   //创建要给一个栈，将各个节点压入栈
   Stack<HeroNode> stack = new Stack<HeroNode>();
   HeroNode cur = head.next;
   //将链表的所有节点压入栈
   while(cur != null) {
      stack.push(cur);
      cur = cur.next; //cur后移，这样就可以压入下一个节点
   }
   //将栈中的节点进行打印,pop 出栈
   while (stack.size() > 0) {
      System.out.println(stack.pop()); //stack的特点是先进后出
   }
}
```

（2）测试

```java
//没有改变表结构
reversePrint(singleLinkedList.getHead());
```

# 4、双向链表

## 1、介绍

1、概述

（1）单向链表，查找的方向只能是一个方向，而双向链表可以向前或者向后查找。

（2）单向链表不能自我删除，需要靠辅助节点 ，而双向链表，则可以自我删除，所以前面我们单链表删除时节点，总是找到temp,temp是待删除节点的前一个节点。

2、示意图

![image-20200926100725741](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200926100725741.png)

（1）遍历操作

遍历方法和单链表一样，只是可以向前，也可以向后查找。

（2）添加操作

默认添加到双向链表的最后。

+ 先找到双向链表的最后这个节点。

+ temp.next = newHeroNode。

+ newHeroNode.pre = temp。

（3）修改操作

思路和原来的单向链表一样。

（4）删除操作

因为是双向链表，因此，我们可以实现自我删除某个节点。

+ 直接找到要删除的这个节点，比如temp。
+ temp.pre.next = temp.next。
+ temp.next.pre = temp.pre。

## 2、案例

1、需求

使用带head头的双向链表实现 –水浒英雄排行榜。

2、代码

（1）英雄类

```java
// 定义HeroNode2 ， 每个HeroNode 对象就是一个节点
class HeroNode2 {
   public int no;
   public String name;
   public String nickname;
   public HeroNode2 next; // 指向下一个节点, 默认为null
   public HeroNode2 pre; // 指向前一个节点, 默认为null

   // 构造器
   public HeroNode2(int no, String name, String nickname) {
      this.no = no;
      this.name = name;
      this.nickname = nickname;
   }

   // 为了显示方法，我们重新toString
   @Override
   public String toString() {
      return "HeroNode [no=" + no + ", name=" + name + ", nickname=" + nickname + "]";
   }
}
```

（2）双向链表类

```java
package com.xiaolun.linkedlist;

// 创建一个双向链表的类
class DoubleLinkedList {
	// 先初始化一个头节点, 头节点不要动, 不存放具体的数据
	private HeroNode2 head = new HeroNode2(0, "", "");

	// 返回头节点
	public HeroNode2 getHead() {
		return head;
	}

	// 遍历双向链表的方法
	// 显示链表[遍历]
	public void list() {
		// 判断链表是否为空
		if (head.next == null) {
			System.out.println("链表为空");
			return;
		}
		// 因为头节点，不能动，因此我们需要一个辅助变量来遍历
		HeroNode2 temp = head.next;
		while (true) {
			// 判断是否到链表最后
			if (temp == null) {
				break;
			}
			// 输出节点的信息
			System.out.println(temp);
			// 将temp后移， 一定小心
			temp = temp.next;
		}
	}

	// 添加一个节点到双向链表的最后.
	public void add(HeroNode2 heroNode) {

		// 因为head节点不能动，因此我们需要一个辅助遍历 temp
		HeroNode2 temp = head;
		// 遍历链表，找到最后
		while (true) {
			// 找到链表的最后
			if (temp.next == null) {//
				break;
			}
			// 如果没有找到最后, 将将temp后移
			temp = temp.next;
		}
		// 当退出while循环时，temp就指向了链表的最后
		// 形成一个双向链表
		temp.next = heroNode;
		heroNode.pre = temp;
	}

	// 修改一个节点的内容, 可以看到双向链表的节点内容修改和单向链表一样
	// 只是 节点类型改成 HeroNode2
	public void update(HeroNode2 newHeroNode) {
		// 判断是否空
		if (head.next == null) {
			System.out.println("链表为空~");
			return;
		}
		// 找到需要修改的节点, 根据no编号
		// 定义一个辅助变量
		HeroNode2 temp = head.next;
		boolean flag = false; // 表示是否找到该节点
		while (true) {
			if (temp == null) {
				break; // 已经遍历完链表
			}
			if (temp.no == newHeroNode.no) {
				// 找到
				flag = true;
				break;
			}
			temp = temp.next;
		}
		// 根据flag 判断是否找到要修改的节点
		if (flag) {
			temp.name = newHeroNode.name;
			temp.nickname = newHeroNode.nickname;
		} else { // 没有找到
			System.out.printf("没有找到 编号 %d 的节点，不能修改\n", newHeroNode.no);
		}
	}

	// 从双向链表中删除一个节点
	// 1 对于双向链表，我们可以直接找到要删除的这个节点
	// 2 找到后，自我删除即可
	public void del(int no) {
		// 判断当前链表是否为空
		if (head.next == null) {// 空链表
			System.out.println("链表为空，无法删除");
			return;
		}

		HeroNode2 temp = head.next; // 辅助变量(指针)
		boolean flag = false; // 标志是否找到待删除节点的
		while (true) {
			if (temp == null) { // 已经到链表的最后
				break;
			}
			if (temp.no == no) {
				// 找到的待删除节点的前一个节点temp
				flag = true;
				break;
			}
			temp = temp.next; // temp后移，遍历
		}
		// 判断flag
		if (flag) { // 找到
			// 可以删除
			// temp.next = temp.next.next;[单向链表]
			temp.pre.next = temp.next;
			// 这里我们的代码有问题?
			// 如果是最后一个节点，就不需要执行下面这句话，否则出现空指针
			if (temp.next != null) {
				temp.next.pre = temp.pre;
			}
		} else {
			System.out.printf("要删除的 %d 节点不存在\n", no);
		}
	}
}
```

（3）测试

```java
HeroNode hero1 = new HeroNode(1, "宋江", "及时雨");
HeroNode hero2 = new HeroNode(2, "卢俊义", "玉麒麟");
HeroNode hero3 = new HeroNode(3, "吴用", "智多星");
HeroNode hero4 = new HeroNode(4, "林冲", "豹子头");

//创建要给链表
SingleLinkedList singleLinkedList = new SingleLinkedList();

//加入
singleLinkedList.add(hero1);
singleLinkedList.add(hero2);
singleLinkedList.add(hero3);
singleLinkedList.add(hero4);
```

# 5、约瑟夫问题

## 1、介绍

1、概述

设编号为1，2，… n的n个人围坐一圈，约定编号为k（1<=k<=n）的人从1开始报数，数到m 的那个人出列，它的下一位又从1开始报数，数到m的那个人又出列，依次类推，直到所有人出列为止，由此产生一个出队编号的序列。

用一个不带头结点的循环链表来处理Josephu 问题：先构成一个有n个结点的单循环链表，然后由k结点起从1开始计数，计到m时，对应结点从链表中删除，然后再从被删除结点的下一个结点又从1开始计数，直到最后一个结点从链表中删除算法结束。

2、原理实现图

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200926110343114.png" alt="image-20200926110343114" style="zoom:80%;" />

（1）需要创建一个辅助指针(变量) helper , 事先应该指向环形链表的最后这个节点.

补充： 小孩报数前，先让 first 和 helper 移动 k - 1次

（2）当小孩报数时，让first 和 helper 指针同时 的移动 m - 1 次

（3）这时就可以将first 指向的小孩节点出圈：

```
first = first .next 

helper.next = first 
```

原来first 指向的节点就没有任何引用，就会被回收。

举例：

```
n = 5 , 即有5个人 
k = 1, 从第一个人开始报数
m = 2, 数2下

出圈的顺序：
2->4->1->5->3
```

## 2、代码

1、孩子类

```java
package com.xiaolun.linkedlist;

// 创建一个Boy类，表示一个节点
class Boy {
   private int no;// 编号
   private Boy next; // 指向下一个节点，默认为null

   public Boy(int no) {
      this.no = no;
   }

   public int getNo() {
      return no;
   }

   public void setNo(int no) {
      this.no = no;
   }

   public Boy getNext() {
      return next;
   }

   public void setNext(Boy next) {
      this.next = next;
   }
}
```

2、不带头结点的循环链表

```java
package com.xiaolun.linkedlist;

// 创建一个环形的单向链表
class CircleSingleLinkedList {
   // 创建一个first节点，当前没有编号
   private Boy first = null;

   // 添加小孩节点，构建成一个环形的链表
   public void addBoy(int nums) {
      // nums 构建一个数据校验
      if (nums < 1) {
         System.out.println("nums的值不正确");
         return;
      }
      Boy curBoy = null; //辅助指针，帮助构建环形链表
      // 使用for来创建我们的话逆行链表
      for (int i = 1; i <= nums; i++) {
         // 根据编号，创建小孩节点
         Boy boy = new Boy(i);
         // 如果是第一个小孩
         if (i == 1) {
            first = boy;
            first.setNext(first); // 构建环
            curBoy = first; // 让curBoy指向第一个小孩
         } else {
            curBoy.setNext(boy);
            boy.setNext(first);
            curBoy = boy;
         }
      }
   }

   // 遍历当前的环形链表
   public void showBoy() {
      // 判断链表是否为null
      if (first == null) {
         System.out.println("no children");
         return;
      }
      // 因为first不能动，因此我们仍然使用一个辅助指针来完成遍历
      Boy curBoy = first;
      while (true) {
         System.out.printf("children number is %d \n", curBoy.getNo());
         if (curBoy.getNext() == first) {// 说明已经遍历啊完毕
            break;
         }
         curBoy = curBoy.getNext(); // curBoy后移
      }
   }

   /**
    * 根据用户输入，计算出小孩出圈的顺序
    *
    * @param startNo  表示从第几个小孩开始数数
    * @param countNum 表示数几下
    * @param nums     表示最初有多少个小孩在圈中
    */
   public void countBoy(int startNo, int countNum, int nums) {
      // 先对数据进行校验
      if (first == null || startNo < 1 || startNo > nums) {
         System.out.println("args is wrong,please input in begin...");
         return;
      }
      // 创建一个辅助指针，帮助完成小孩出圈
      Boy helper = first;
      // 需要创建一个辅助指针（变量）helper，事先应该指向环形链表的最后这个节点
      while (true) {
         if (helper.getNext() == first) { // 说明helper指向最后小孩节点
            break;
         }
         helper = helper.getNext();
      }
      //小孩报数前，先让first和helper移动k-1次，移动到k这个小孩这里来
      for (int j = 0; j < startNo - 1; j++) {
         first = first.getNext();
         helper = helper.getNext();
      }
      //当小孩报数时，让first和helper指针同时移动m-1次(本身也要数一下)，然后出圈
      //这里是一个循环操作，直到圈中只有一个节点
      while (true) {
         if (helper == first) { //说明圈中只有一个节点
            break;
         }
         //让first和helper指针同时移动 countNum - 1
         for (int j = 0; j < countNum - 1; j++) {
            first = first.getNext();
            helper = helper.getNext();
         }
         //这时first指向的节点，就是要出圈的小孩节点
         System.out.printf("小孩%d出圈\n", first.getNo());
         //这里将first指向的小孩节点出圈
         first = first.getNext();
         helper.setNext(first);
      }
      System.out.printf("最后留在圈中的小孩编号 %d \n", first.getNo());
   }
}
```

3、测试

```java
public class Josepfu {
   public static void main(String[] args) {
      CircleSingleLinkedList circleSingleLinkedList = new CircleSingleLinkedList();
      circleSingleLinkedList.addBoy(5);// 加入5个小孩节点
      circleSingleLinkedList.showBoy();

      circleSingleLinkedList.countBoy(1, 2, 5); // 2->4->1->5->3
   }
}
```

# 6、栈

## 1、介绍

1、概述

（1）栈的英文为(stack)

（2）栈是一个先入后出(FILO-First In Last Out)的有序列表。

（3）栈(stack)是限制线性表中元素的插入和删除只能在线性表的同一端进行的一种特殊线性表。允许插入和删除的一端，为变化的一端，称为栈顶(Top)，另一端为固定的一端，称为栈底(Bottom)。

（4）根据栈的定义可知，最先放入栈中元素在栈底，最后放入的元素在栈顶，而删除元素刚好相反，最后放入的元素最先删除，最先放入的元素最后删除。

![image-20200926110907938](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200926110907938.png)

2、应用场景

（1）子程序的调用：在跳往子程序前，会先将下个指令的地址存到堆栈中，直到子程序执行完后再将地址取出，以回到原来的程序中。  

（2）处理递归调用：和子程序的调用类似，只是除了储存下一个指令的地址外，也将参数、区域变量等数据存入堆栈中。

（3）表达式的转换[中缀表达式转后缀表达式]与求值(实际解决)。

（4）二叉树的遍历。

（5）图形的深度优先(depth一first)搜索法。

## 2、案例1

1、需求

使用数组模拟栈。

2、分析

（1）定义一个 top 来表示栈顶，初始化 为 -1。

（2）入栈的操作，当有数据加入到栈时，进行下面的操作：

```
top++; stack[top] = data;
```

（3）出栈的操作如下：

```
 int value = stack[top]; top--, return value
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200926150302056.png" alt="image-20200926150302056" style="zoom:67%;" />



3、代码

（1）栈类

```java
package com.xiaolun.stack;

//定义一个Arraystack栈
public class ArrayStack {
   private int maxSize; // 栈的大小
   private int[] stack; // 数组模拟栈，数据就放到数组中
   private int top = -1;// top表示栈顶，初始化为-1

   //构造器
   public ArrayStack(int maxSize) {
      this.maxSize = maxSize;
      //数组初始化后才能放数据
      stack = new int[this.maxSize];
   }

   //栈满
   public boolean isFull() {
      return top == maxSize - 1;
   }

   //栈空
   public boolean isEmpty() {
      return top == -1;
   }

   //入栈-push
   public void push(int value) {
      //先判断栈是否满
      if (isFull()) {
         System.out.println("栈满");
         return;
      }
      top++;
      stack[top] = value;
   }

   //出栈，将栈顶的数据返回
   public int pop() {
      //先判断栈是否空
      if (isEmpty()) {
         //抛出异常
         throw new RuntimeException("栈空，无数据~");
      }
      int value = stack[top];
      top--;
      return value;
   }

   //显示栈的情况（遍历），遍历时，从栈顶开始遍历数据
   public void list() {
      if (isEmpty()) {
         System.out.println("栈空，无数据~");
         return;
      }
      //需要从栈顶显示数据
      for (int i = top; i >= 0; i--) {
         System.out.printf("stack[%d]=%d\n", i, stack[i]);
      }
   }
}
```

（2）测试

```java
package com.xiaolun.stack;

import java.util.Scanner;

public class ArrayStackDemo {
   public static void main(String[] args) {
      //先创建一个Arraystack对象-》表示栈
      ArrayStack stack = new ArrayStack(4);
      String key = "";
      boolean loop = true; //判断是否退出菜单
      Scanner scanner = new Scanner(System.in);

      while (loop) {
         System.out.println("show: 显示栈");
         System.out.println("exit: 退出程序");
         System.out.println("push: 入栈");
         System.out.println("pop: 出栈");
         System.out.println("输入你的选择：");
         key = scanner.next();
         switch (key) {
            case "show":
               stack.list();
               break;
            case "push":
               System.out.println("请输入一个数：");
               int value = scanner.nextInt();
               stack.push(value);
               break;
            case "pop":
               try {
                  int res = stack.pop();
                  System.out.printf("出栈的数据为 %d\n", res);
               } catch (Exception e) {
                  System.out.println(e.getMessage());
               }
               break;
            case "exit":
               scanner.close();
               loop = false;
               break;
            default:
               break;
         }
      }
      System.out.println("程序退出~~~");
   }
}
```

## 3、案例2

1、需求

栈实现计算器，计算表达式“3+ 2*6-2”的值。

2、分析

（1）通过一个 index 值（索引），来遍历我们的表达式

（2）如果我们发现是一个数字，就直接入数栈

（3）如果发现扫描到是一个符号,  就分如下情况

+ 如果发现当前的符号栈为 空，就直接入栈

+ 如果符号栈有操作符，就进行比较,如果当前的操作符的优先级小于或者等于栈中的操作符， 就需要从数栈中pop出两个数,再从符号栈中pop出一个符号，进行运算，将得到结果，入数栈，然后将当前的操作符入符号栈。 

  如果当前的操作符的优先级大于栈中的操作符， 就直接入符号栈。

（4）当表达式扫描完毕，就顺序的从 数栈和符号栈中pop出相应的数和符号，并运行。

（5）在数栈只有一个数字，就是表达式的结果。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200926150051724.png" alt="image-20200926150051724" style="zoom:67%;" />

3、代码

（1）栈类

```java
package com.xiaolun.stack;

//先创建一个栈，使用前面创建好的
//定义一个ArrayStack2，需要扩展功能
public class ArrayStack2 {
   private int maxSize; // 栈的大小
   private int[] stack;
   private int top = -1;

   //构造器
   public ArrayStack2(int maxSize) {
      this.maxSize = maxSize;
      stack = new int[this.maxSize];
   }

   //增加一个方法，可以返回当前栈顶的值，但是不是真正的pop
   public int peek() {
      return stack[top];
   }

   //栈满
   public boolean isFull() {
      return top == maxSize - 1;
   }

   //栈空
   public boolean isEmpty() {
      return top == -1;
   }

   //push
   public void push(int value) {
      if (isFull()) {
         System.out.println("栈满");
         return;
      }
      top++;
      stack[top] = value;
   }

   //pop
   public int pop() {
      if (isEmpty()) {
         throw new RuntimeException("栈空，无数据");
      }
      int value = stack[top];
      top--;
      return value;
   }

   //遍历
   public void list() {
      if (isEmpty()) {
         System.out.println("栈空，无数据");
         return;
      }
      for (int i = top; i >= 0; i--) {
         System.out.printf("stack[%d]=%d\n", i, stack[i]);
      }
   }

   /**
    * 返回运算的优先级，优先级由程序员确定，优先级使用数字表示。
    * 数字越大，优先级越高
    *
    * @param oper 操作符
    * @return
    */
   public int priority(int oper) {
      if (oper == '*' || oper == '/') {
         return 1;
      } else if (oper == '+' || oper == '-') {
         return 0;
      } else {
         return -1; //假定目前只含有加减乘除，没有小括号。
      }
   }

   //判断是不是一个运算符
   public boolean isOper(char val) {
      return val == '+' || val == '-' || val == '*' || val == '/';
   }

   //计算方法
   public int cal(int num1, int num2, int oper) {
      return cal(num1, num2, oper, 0);
   }

   //计算方法
   public int cal(int num1, int num2, int oper, int temp) {
      int res = 0; // res 用于存放计算的结果
      switch (oper) {
         case '+':
            res = num1 + num2;
            break;
         case '-':
            if (temp == '-') {
               res = num2 + num1;// 注意顺序
            } else {
               res = num2 - num1;// 注意顺序
            }
            break;
         case '*':
            res = num1 * num2;
            break;
         case '/':
            res = num2 / num1;
            break;
         default:
            break;
      }
      return res;
   }
}
```

（2）测试

```java
package com.xiaolun.stack;

public class Calculator {
	public static void main(String[] args) {
		/**
		 * 1、表达式为 3-2*6-2 结果错误：-7（正确为-11）
		 */
		String expression = "3-2*6-2"; // 如何处理多位数的计算
		//创建两个栈，一个是数栈，*一个是符号栈
		ArrayStack2 numStack = new ArrayStack2(10);
		ArrayStack2 operStack = new ArrayStack2(10);
		//定义需要的相关变量
		int index = 0;//用于扫描
		int num1 = 0;
		int num2 = 0;
		int oper = 0;
		int res = 0;
		char ch = ' '; //将每次扫描得到的char保存到ch
		String keepNum = ""; //用于拼接多位数
		boolean flag = false; //为表达式中第一位为-号使用
		//while循环扫描expression
		while (true) {
			//依次得到expression的每一个字符
			ch = expression.substring(index, index + 1).charAt(0);
			//判断ch是什么，然后做相应的处理
			if (expression.substring(0, 0 + 1).charAt(0) == '-') {
				flag = true; //为表达式中第一位为-号使用
			}

			if (operStack.isOper(ch)) {//如果是运算符
				//判断当前的符号栈是否为null
				if (!operStack.isEmpty()) {
					/**
					 * 如果符号栈中有操作符，就进行比较，如果当前的操作符优先级小于或者等于栈中的操作符，
					 * 就需要从数栈中pop出两个数。
					 * 再从符号栈中pop出一个符号，进行运算，将得到的结果入数栈，然后将当前的操作符入符号栈
					 */
					if (operStack.priority(ch) <= operStack.priority(operStack.peek())) {
						num1 = numStack.pop();
						num2 = numStack.pop();
						oper = operStack.pop();
						res = numStack.cal(num1, num2, oper);
						//把运算的结果入数栈
						numStack.push(res);
						//然后将当前的操作符入符号符
						operStack.push(ch);
					} else {
						//如果当前的操作符的优先级大于栈中的操作符，就直接入符号栈
						operStack.push(ch);
					}
				} else {
					if (flag) {
						//开始时为负号，添加0
						numStack.push(0);
					}
					//如果为符号栈null,直接入符号栈
					operStack.push(ch); // 1 + 3
				}
			} else { //如果是数据，直接入数栈
				/**
				 * numStack.push(ch - 48);
				 * 扫描"1+3" 是字符'1'而不是数字1，字符1和数字1ASCII相差48.
				 *
				 * 1、当处理多位数时，不能发现是一个数就直接入栈，因为它可能是一个多位数
				 * 2、在处理数时，需要向expression的表达式的index后再看一位，如果是数就进行扫描，
				 * 如果是符号才入栈
				 * 3、我们需要定义一个字符串，用于拼接
				 */
				keepNum += ch; //处理多位数->拼接

				//如果ch是expression的最后一位，就直接入栈
				if (index == expression.length() - 1) {
					numStack.push(Integer.parseInt(keepNum));
				} else {

					/**
					 * 判断下一个字符是不是数字，如果是数字，继续扫描，如果是运算符，则入栈
					 */
					if (operStack.isOper(expression.substring(index + 1, index + 2).charAt(0))) {
						//如果后一位是运算符，则入栈 keepNum="1" 或者“123”
						numStack.push(Integer.parseInt(keepNum));
						//重要的，keepNum清空
						keepNum = "";
					}
				}
			}
			//让index + 1,并判断是不是扫描到expression最后
			index++;
			if (index >= expression.length()) {
				break;
			}
		}

		//当表达式扫描完毕，就顺序的从数栈和符号栈中pop出相应的数和符号，并运行
		while (true) {
			int temp = 0;
			//如果符号栈为null,则计算到最后的结果，数栈中只有一个数字（结果）
			if (operStack.isEmpty()) {
				break;
			}
			num1 = numStack.pop();
			num2 = numStack.pop();
			oper = operStack.pop(); //将符号出栈

			//针对 3-2*6-2 这种算法的异常情况 12-2，3-（12-2）
			if (!operStack.isEmpty()) {
				temp = operStack.pop(); //再出一个栈，放到了临时变量中
				res = numStack.cal(num1, num2, oper, temp);
				//然后再将符号重新压回到栈中
				operStack.push(temp);
			} else {
				res = numStack.cal(num1, num2, oper);
			}
			
			numStack.push(res);//入栈
		}
		//将数栈的最后数，pop出，就是结果
		int res2 = numStack.pop();
		System.out.printf("表达式%s = %d", expression, res2);
	}
}
```

## 4、后缀表达式

### 1、介绍

（1）前缀表达式、

从右至左扫描表达式，遇到数字时，将数字压入堆栈，遇到运算符时，弹出栈顶的两个数，用运算符对它们做相应的计算（栈顶元素 和 次顶元素），并将结果入栈；重复上述过程直到表达式最左端，最后运算得出的值即为表达式的结果。

例如: (3+4)×5-6 对应的前缀表达式就是 - × + 3 4 5 6 , 针对前缀表达式求值步骤如下:

+ 从右至左扫描，将6、5、4、3压入堆栈。

+ 遇到+运算符，因此弹出3和4（3为栈顶元素，4为次顶元素），计算出3+4的值，得7，再将7入栈。

+ 接下来是×运算符，因此弹出7和5，计算出7×5=35，将35入栈。

+ 最后是-运算符，计算出35-6的值，即29，由此得出最终结果。

（2）中缀表达式

中缀表达式就是常见的运算表达式，如(3+4)×5-6。

（3）后缀表达式

后缀表达式又称逆波兰表达式,与前缀表达式相似，只是运算符位于操作数之后。

比如： (3+4)×5-6 对应的后缀表达式就是 3 4 + 5 × 6 –

![image-20200926171138126](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200926171138126.png)

后缀表达式的计算机求值过程如下：

从左至右扫描表达式，遇到数字时，将数字压入堆栈，遇到运算符时，弹出栈顶的两个数，用运算符对它们做相应的计算（次顶元素 和 栈顶元素），并将结果入栈；重复上述过程直到表达式最右端，最后运算得出的值即为表达式的结果。

例如: (3+4)×5-6 对应的后缀表达式就是 **3 4 + 5 × 6 - ,** 针对后缀表达式求值步骤如下：

+ 从左至右扫描，将3和4压入堆栈；

+ 遇到+运算符，因此弹出4和3（4为栈顶元素，3为次顶元素），计算出3+4的值，得7，再将7入栈；

+ 将5入栈；

+ 接下来是×运算符，因此弹出5和7，计算出7×5=35，将35入栈；

+ 将6入栈；

+ 最后是-运算符，计算出35-6（后面出栈的-先出栈的数据）的值，即29，由此得出最终结果 。

### 2、案例1

1、需求

输入一个逆波兰表达式(后缀表达式)，使用栈(Stack), 计算其结果。

支持小括号和多位数整数，因为这里我们主要讲的是数据结构，因此计算器进行简化，只支持对整数的计算。

2、代码

```java
package com.xiaolun.stack;

import java.util.ArrayList;
import java.util.List;
import java.util.Stack;

public class PolandNotation {
   public static void main(String[] args) {
      /**
       * 后缀表达式测试
       * 1、先定义一个逆波兰表达式
       * 2、为了说明方便，逆波兰表达式的数字和符号使用空格隔开
       * (3+4)×5-6 =>就是 3 4 + 5 × 6 –
       * 3、思路
       * （1）先将 3 4 + 5 × 6 – 放到ArrayList中
       * （2）将ArrayList传递给一个方法，遍历ArrayList配合栈完成计算
       */
		/**
		 * 后缀表达式测试
		 * 1、先定义一个逆波兰表达式
		 * 2、为了说明方便，逆波兰表达式的数字和符号使用空格隔开
		 * (3+4)×5-6 =>就是 3 4 + 5 × 6 –
		 * 3、思路
		 * （1）先将 3 4 + 5 × 6 – 放到ArrayList中
		 * （2）将ArrayList传递给一个方法，遍历ArrayList配合栈完成计算
		 */
		String suffixExpression = "3 4 + 5 * 6 -";
		List<String> list = getListString(suffixExpression);
		System.out.println("List=" + list);

		int res = calculate(list);
		System.out.println("计算的结果是=" + res);
   }
  
  	//将一个逆波兰表达式，依次将数据和运算符放到ArrayList中（当成字符串处理）
	public static List<String> getListString(String suffixExpression) {
		//将suffixExpression分割
		String[] split = suffixExpression.split(" ");
		List<String> list = new ArrayList<String>();
		for (String ele : split) {
			list.add(ele);
		}
		return list;
	}
  
   /**
    * 完成对逆波兰表达式的运算：
    * （1）从左至右扫描，将3和4压入堆栈；
    * （2）遇到+运算符，因此弹出4和3（4为栈顶元素，3为次顶元素），计算出3+4的值，得7，再将7入栈；
    * （3）将5入栈；
    * （4）接下来是×运算符，因此弹出5和7，计算出7×5=35，将35入栈；
    * （5）将6入栈；
    * （6）最后是-运算符，计算出35-6的值，即29，由此得出最终结果
    */
   public static int calculate(List<String> ls) {
      // 创建一个栈，只需要一个栈即可
      Stack<String> stack = new Stack<String>();
      //遍历 ls
      for (String item : ls) {
         // 使用正则表达式取出数
         if (item.matches("\\d+")) { //匹配的是多位数
            // 入栈
            stack.push(item);
         } else {
            // pop出两个数，并运算，再入栈
            int num2 = Integer.parseInt(stack.pop());
            int num1 = Integer.parseInt(stack.pop());
            int res = 0;
            if (item.equals("+")) {
               res = num1 + num2;
            } else if (item.equals("-")) {
               res = num1 - num2;
            } else if (item.equals("*")) {
               res = num1 * num2;
            } else if (item.equals("/")) {
               res = num1 / num2;
            } else {
               throw new RuntimeException("运算符有误");
            }
            //将res入栈
            stack.push("" + res);
         }
      }
      //最后留在stack中的数据是运算结果
      return Integer.parseInt(stack.pop());
   }
}
```

### 3、案例2

1、需求

将中缀表达式转化为后缀表达式。

2、分析

（1）案例

```
中缀表达式 1 + ( ( 2 + 3 )× 4) - 5 =》 
后缀表达式 - 5 + * 4 + 3 2 1  =>  1 2 3 + 4 * + 5 -
```

（2）分析

```
1、初始化两个栈：运算符栈s1和储存中间结果的栈s2。
2、从左至右扫描中缀表达式。
3、遇到操作数时，将其压s2。
4、遇到运算符时，比较其与s1栈顶运算符的优先级：
	（1）如果s1为空，或栈顶运算符为左括号“(”，则直接将此运算符入栈；
	（2）否则，若优先级比栈顶运算符的高，也将运算符压入s1；
	（3）否则，将s1栈顶的运算符弹出并压入到s2中，再次转到(4.1)与s1中新的栈顶运算符相比较。
5、遇到括号时
	（1）如果是左括号“(”，则直接压入s1；
	（2）如果是右括号“)”，则依次弹出s1栈顶的运算符，并压入s2，直到遇到左括号为止，此时将这一对括号丢弃。
6、重复步骤2至5，直到表达式的最右边。
7、将s1中剩余的运算符依次弹出并压入s2。
8、依次弹出s2中的元素并输出，结果的逆序即为中缀表达式对应的后缀表达式。
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200926172012781.png" alt="image-20200926172012781" style="zoom:80%;" />

3、代码

（1）比较类

```java
package com.xiaolun.stack;

//可以返回一个运算符对应的优先级
public class Operation {
   private static int ADD = 1;
   private static int SUB = 1;
   private static int MUL = 2;
   private static int DIV = 2;

   //写一个方法，返回对应的优先级数字
   public static int getValue(String operation) {
      int result = 0;
      switch (operation) {
         case "+":
            result = ADD;
            break;
         case "-":
            result = SUB;
            break;
         case "*":
            result = MUL;
            break;
         case "/":
            result = DIV;
            break;
         default:
            System.out.println("不存在该运算符" + operation);
            break;
      }
      return result;
   }
}
```

（2）转换方法

```java
/**
	 * 将中缀表达式转成对应的list转换成后缀表达式的list
	 * ArrayList[1,+,(,(,2,+,3,),×,4,),-,5] -> ArrayList[1,2,3,+,4,×,+,5,–]
	 *
	 * @param ls
	 * @return
	 */
	public static List<String> parseSuffixExpreesionList(List<String> ls) {
		//定义两个栈
		Stack<String> s1 = new Stack<String>(); // 符号栈
		/**
		 * 说明
		 * 因为s2这个栈，在整个转换过程中，没有pop操作，而且后面我们还需要逆序操作，我们
		 * 不使用下面的Stack<String>，而是使用List<String> s2
		 * Stack<String> s2 = new Stack<String>(); // 储存中间结果的栈s2
		 */
		List<String> s2 = new ArrayList<String>(); // 储存中间结果的栈s2

		//遍历ls
		for (String item : ls) {
			//如果是一个数，加入s2
			if (item.matches("\\d+")) {
				s2.add(item);
			} else if (item.equals("(")) {
				s1.push(item);
			} else if (item.equals(")")) {
				/**
				 * 如果是右括号），则弹出s1栈顶的运算符，并压入s2，直到遇到左括号
				 * 为止，此时将该对括号丢弃
				 */
				while (!s1.peek().equals("(")) {
					s2.add(s1.pop());
				}
				s1.pop();//!!! 将 （弹出s1栈，消除小括号
			} else {
				/**
				 * item此时是中缀表达式的运算符
				 * 1、当item的运算符的优先级小于等于s1栈顶的运算符，将s1栈顶的运算符弹出
				 * 并放到s2中，再次转到（4.1）与s1中新的栈顶运算符比较
				 * 2、需要一个优先级比较的方法
				 */
				while (true) {
					if (s1.size() == 0 || s1.peek() == "(") {
						s1.push(item);
						break;
					} else if (Operation.getValue(item) > Operation.getValue(s1.peek())) {
						s1.push(item);
						break;
					} else {
						s2.add(s1.pop());
					}
				}
			}
		}
		//将s1中剩余的运算符依次弹出并加入s2
		while (s1.size() != 0) {
			s2.add(s1.pop());
		}

		//因为是存放到list中，所以按顺序输出就是后缀表达式的list
		return s2;
	}

	/**
	 * 将中缀表达式转换为对应的List
	 * s="1+((2+3)??4)-5";
	 *
	 * @param s
	 * @return
	 */
	public static List<String> toInfixExpressionList(String s) {
		List<String> ls = new ArrayList<String>();
		int i = 0; //指针，用于遍历中缀表达式对应的内容
		String str; // 对于多位数的拼接
		char c; // 每遍历一个字符，就放入到c
		do {
			//如果c是一个非数字，就加入到ls
			if ((c = s.charAt(i)) < 48 || (c = s.charAt(i)) > 57) {
				ls.add("" + c);
				i++; //i后移
			} else { //如果是一个数，需要考虑多位数
				str = ""; //先将str置成“” '0'[48]->'9'[57]
				while (i < s.length() && (c = s.charAt(i)) >= 48 && (c = s.charAt(i)) <= 57) {
					str += c;//拼接
					i++;
				}
				ls.add(str);
			}
		} while (i < s.length());
		return ls;//返回
	}
```

（3）测试

```java
/**
 * 中缀表达式转后缀表达式
 * 1、因为直接对表达式直接进行操作，不太方便，因此，先将1+((2+3)×4)-5中缀表达式
 * 转换成对应的list => ArrayList[1,+,(,(,2,+,3,),×,4,),-,5]
 * 2、将得到的中缀表达式的list转换成后缀表达式的list
 *  ArrayList[1,2,3,+,4,×,+,5,–]
 */
String expression = "1+((2+3)*4)-5";
List<String> infixExpressionList = toInfixExpressionList(expression);

System.out.println("中缀表达式List=" + infixExpressionList); // ArrayList [1,+,(,(,2,+,3,),*,4,),-,5]
List<String> suffixExpreesionList = parseSuffixExpreesionList(infixExpressionList);
System.out.println("后缀表达式List" + suffixExpreesionList); //ArrayList [1,2,3,+,4,*,+,5]
System.out.printf("expression=%d", calculate(suffixExpreesionList));
```



























































