# 1、查找算法

在java中，我们常用的查找有四种：

（1）顺序(线性)查找

 （2）二分查找/折半查找

  （3）插值查找

  （4）斐波那契查找

## 1、顺序（线性）查找

1、需求

有一个数列：{1,8, 10, 89, 1000, 1234} ，判断数列中是否包含此名称。

2、代码

```java
package com.xiaolun.search;

//线性查找
public class SeqSearch {
   public static void main(String[] args) {
      int arr[] = {1, 9, 11, -1, 34, 89};// 没有顺序的数组
      int index = seqSearch(arr, -11);
      if (index == -1) {
         System.out.println("没有找到");
      } else {
         System.out.println("找到，下标为=" + index);
      }
   }

   /**
    * 找到一个满足条件的值，就返回
    *
    * @param arr
    * @param value
    * @return
    */
   public static int seqSearch(int[] arr, int value) {
      // 线性查找是逐一比对，发现有相同值，就返回下标。
      for (int i = 0; i < arr.length; i++) {
         if (arr[i] == value) {
            return i;
         }
      }
      return -1;
   }
}
```

## 2、二分查找

1、思路

```
1. 首先确定该数组的中间的下标
mid = (left + right) / 2
2. 然后让需要查找的数 findVal 和 arr[mid] 比较
    2. 1 findVal > arr[mid] ,  说明你要查找的数在mid 的右边, 因此需要递归的向右查找
    2.2 findVal < arr[mid], 说明你要查找的数在mid 的左边, 因此需要递归的向左查找
    2.3  findVal == arr[mid] 说明找到，就返回

//什么时候我们需要结束递归?
1) 找到就结束递归 
2) 递归完整个数组，仍然没有找到findVal ，也需要结束递归  当 left > right 就需要退出
```

2、需求

 （1） 请对一个有序数组进行二分查找 {1,8, 10, 89, 1000, 1234} ，输入一个数看看该数组是否存在此数，并且求出下标，如果没有就提示"没有这个数"。

 （2）{1,8, 10, 89, 1000, 1000，1234} 当一个有序数组中，有多个相同的数值时，如何将所有的数值都查找到，比如这里的 1000。

3、代码

```java
package com.xiaolun.search;

import java.util.ArrayList;
import java.util.List;

//二分查找算法，数组必须有序
public class BinarySearch {
   public static void main(String[] args) {
//    int arr[] = { 1,2,3};
//    int resIndex = binarySearch(arr, 0, arr.length - 1, 4);
//    System.out.println("resIndex=" + resIndex);

      int arr[] = {1, 1,2, 3};
      List<Integer> resIndexList = binarySearch2(arr, 0, arr.length - 1, 1);
      System.out.println("resIndexList=" + resIndexList);
   }

   /**
    * @param arr     数组
    * @param left    左边的索引
    * @param right   右边的索引
    * @param findVal 要查找的值
    * @return 如果找到就返回下标，反之，返回-1
    */
   public static int binarySearch(int[] arr, int left, int right, int findVal) {
      // 当left > right时，说明递归整个数组，但是没有找到
      if (left > right) {
         return -1;
      }

      int mid = (left + right) / 2;
      int midVal = arr[mid];

      if (findVal > midVal) { // 向右递归
         //当数组是 arr[] = { 1,2,3} 时，mid=2会返回
         return binarySearch(arr, mid + 1, right, findVal);
      } else if (findVal < midVal) { // 向左递归
         return binarySearch(arr, left, mid - 1, findVal);
      } else {
         return mid;
      }
   }

   /**
    * 当有多个相同的数值时，如何将所有的数值都查找到
    * 思路
    * 1、在找到mid索引值时，不要马上返回
    * 2、向mid索引值的左边扫描，将所有满足1000的元素找到，加入到集合ArrayList
    * 3、向mid索引值的右边扫描，将所有满足1000的元素找到，加入到集合ArrayList
    * 4、将ArrayList返回
    */
   public static List<Integer> binarySearch2(int[] arr, int left, int right, int findVal) {
      if (left > right) {
         return new ArrayList<Integer>();
      }
      int mid = (left + right) / 2;
      int midVal = arr[mid];

      if (findVal > midVal) {
         return binarySearch2(arr, mid + 1, right, findVal);
      } else if (findVal < midVal) {
         return binarySearch2(arr, left, mid - 1, findVal);
      } else {
         List<Integer> resIndexlist = new ArrayList<Integer>();
         //向mid索引值的左边扫描，将所有满足1000的元素找到，加入到集合ArrayList
         int temp = mid - 1;
         while (true) {
            if (temp < 0 || arr[temp] != findVal) {//退出
               break;
            }
            //否则，将temp放入到resIndexlist
            resIndexlist.add(temp);
            temp -= 1; //temp左移
         }
         resIndexlist.add(mid); //将周阿金啊这个放进去
         //向mid索引值的右边扫描，将所有满足1000的元素找到，加入到集合ArrayList
         temp = mid + 1;
         while (true) {
            if (temp > arr.length - 1 || arr[temp] != findVal) {//退出
               break;
            }
            //否则，将temp放入到resIndexlist
            resIndexlist.add(temp);
            temp += 1; //temp右移
         }
         return resIndexlist;
      }
   }
}
```

## 3、插值查找

1、介绍

（1）插值查找算法类似于二分查找，不同的是插值查找每次从自适应mid处开始查找。

（2）将折半查找中的求mid 索引的公式 , low 表示左边索引left, high表示右边索引right。key 就是前面我们讲的 findVal：

![image-20200928170647650](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200928170647650.png)

```
int mid = left + (right – left) * (findVal – arr[left]) / (arr[right] – arr[left])
```

（3）举例

举例说明插值查找算法 1-100 的数组。

```
数组  arr = [1, 2, 3, ......., 100]

假如我们需要查找的值  1 

使用二分查找的话，我们需要多次递归，才能找到 1

使用插值查找算法
int mid = left + (right – left) * (findVal – arr[left]) / (arr[right] – arr[left])

int mid = 0 + (99 - 0) * (1 - 1)/ (100 - 1) = 0 + 99 * 0 / 99 = 0 

比如我们查找的值 100

int mid = 0 + (99 - 0) * (100 - 1) / (100 - 1) = 0 + 99 * 99 / 99 = 0 + 99 = 99 
```

2、注意事项

（1）对于数据量较大，关键字分布比较均匀的查找表来说，采用插值查找，速度较快。

（2）关键字分布不均匀的情况下，该方法不一定比折半查找要好。

3、代码

```java
package com.xiaolun.search;


public class InsertValueSearch {
   public static void main(String[] args) {
      int arr[] = {1, 8, 10, 89, 1000, 1000, 1234};
      int index = insertValueSearch(arr, 0, arr.length - 1, 1000);
      System.out.println("index = " + index);
   }

   /**
    * @param arr     数组
    * @param left    左边的索引
    * @param right   右边的索引
    * @param findVal 要查找的值
    * @return 如果找到就返回下标，反之，返回-1
    */
   public static int insertValueSearch(int[] arr, int left, int right, int findVal) {
      /**
       * findVal < arr[0] 和 findVal > arr[arr.length - 1]都需要
       * 否则我们得到的mid可能越界
       */
      if (left > right || findVal < arr[0] || findVal > arr[arr.length - 1]) {
         return -1;
      }

      // 求mid,自适应
      int mid = left + (right - left) * (findVal - arr[left]) / (arr[right] - arr[left]);
      int midVal = arr[mid];
      if (findVal > midVal) { // 说明应该向右边递归查找
         return insertValueSearch(arr, mid + 1, right, findVal);
      } else if (findVal < midVal) { // 说明应该向左边递归查找
         return insertValueSearch(arr, left, mid - 1, findVal);
      } else {
         return mid;
      }
   }
}
```

## 4、斐波那契(黄金分割法)查找算法

1、介绍

（1）概述

斐波那契查找原理与前两种相似，仅仅改变了中间结点（mid）的位置，mid不再是中间或插值得到，而是位于黄金分割点附近，即

```
mid=low+F(k-1)-1
```

（F代表斐波那契数列，k代表斐波那契数列的第k个元素），如下图所示

![image-20200928202210429](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200928202210429.png)

（2）对于F（k-1）-1的理解

由斐波那契数列 F[k]=F[k-1]+F[k-2] 的性质，可以得到 

```
（F[k]-1）=（F[k-1]-1）+（F[k-2]-1）+1
```

该式说明：只要顺序表的长度为F[k]-1，则可以将该表分成长度为F[k-1]-1和F[k-2]-1的两段，即如上图所示。从而中间位置为：

```
mid=low+F(k-1)-1
```

类似的，每一子段也可以用相同的方式分割

但顺序表长度n不一定刚好等于F[k]-1，所以需要将原来的顺序表长度n增加至F[k]-1。这里的k值只要能使得F[k]-1恰好大于或等于n即可，由以下代码得到,顺序表长度增加后，新增的位置（从n+1到F[k]-1位置），都赋为n位置的值即可。

```java
while(n>fib(k)-1)
    k++;
```

2、代码

```java
package com.xiaolun.search;

import java.util.Arrays;

//斐波那契算法
public class FibonacciSearch {
   public static int maxSize = 20;

   public static void main(String[] args) {
      int[] arr = {1, 8, 10,12};
      System.out.println("index=" + fibSearch(arr, 8));// 0
   }

   /**
    * 因为后面我们mid=low+F(k-1)-1,需要使用到斐波那契数列，所以我们需要获取一个斐波那契数列
    * 非递归方法获取一个斐波那契数列
    */
   public static int[] fib() {
      int[] f = new int[maxSize];
      f[0] = 1;
      f[1] = 1;
      for (int i = 2; i < maxSize; i++) {
         f[i] = f[i - 1] + f[i - 2];
      }
      return f;
   }

   /**
    * 非递归方式的斐波那契查找算法
    *
    * @param a   数组
    * @param key 我们需要查找的关键字（码）
    * @return 返回对应的下标，如果没有返回-1
    */
   public static int fibSearch(int[] a, int key) {
      int low = 0;
      int high = a.length - 1;
      int k = 0; //表示斐波那契分割数值的下标
      int mid = 0; //存放mid值
      int f[] = fib(); //获取斐波那契数列
      //获取斐波那契数值的下标,让F[k]-1长度大于顺序表的长度，之后顺序表不够的进行补零
      while (high > f[k] - 1) {
         k++;
      }
      /**
       * 因为f[k]值可能大于a的长度，因此我们需要使用Arrays类，构造一个新的数组
       * 并指向temp[],长度为f[k]（斐波那契数列第k个元素）不足的部分使用0填充
       *
       */
      int[] temp = Arrays.copyOf(a, f[k]);
      /**
       * 实际上需要使用a数组的最后的数填充temp
       * temp = {1,8, 10, 89, 1000, 1234, 0, 0}  => {1,8, 10, 89, 1000, 1234, 1234, 1234}
       */
      for (int i = high + 1; i < temp.length; i++) {
         temp[i] = a[high];
      }

      // 使用while循环处理，找到我们的数key
      while (low <= high) { // 只要这个条件满足，就可以找
         mid = low + f[k - 1] - 1;
         if (key < temp[mid]) { //我们继续向数组的前面查找（左边）
            high = mid - 1;
            /**
             * 1、全部元素=前面的元素+后面的元素
             * 2、f[k] = f[k-1] + f[k-2]
             * 3、因为前面有 f[k-1]个元素,因此可以继续拆分 f[k-1] = f[k-2] + f[k-3]
             * 即在f[k-1]的前面继续查找 k--
             * 即下次循环 mid = f[k-1-1]-1
             */
            k--;
         } else if (key > temp[mid]) { // 我们应该继续在数组的后面查找（右边）
            low = mid + 1;
            /**
             * 1、全部元素=前面的元素+后面的元素
             * 2、f[k] = f[k-1] + f[k-2]
             * 3、因为后面有 f[k-2]个元素,因此可以继续拆分 f[k-2] = f[k-3] + f[k-4]
             * 即在f[k-1]的前面继续查找 k-=2
             * 即下次循环 mid = f[k-1-2]-1
             */
            k -= 2;
         } else { //找到
            //需要确定，返回的是那个下标
            if (mid <= high) {
               return mid;
            } else {
               return high;
            }
         }
      }
      return -1;
   }
}
```

# 2、哈希表（Hash table）

## 1、介绍

1、概述

散列表（Hash table，也叫哈希表），是根据关键码值(Key value)而直接进行访问的数据结构。也就是说，它通过把关键码值映射到表中一个位置来访问记录，以加快查找的速度。这个映射函数叫做散列函数，存放记录的数组叫做散列表。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200928211813956.png" alt="image-20200928211813956" style="zoom:50%;" />

2、需求

有一个公司,当有新的员工来报道时,要求将该员工的信息加入
 (id,性别,年龄,名字,住址..),当输入该员工的id时,要求查找到该员工的 
 所有信息。

要求：

（1）不使用数据库,,速度越快越好=>哈希表(散列)。

（2）添加时，保证按照id从低到高插入。

使用链表来实现哈希表, 该链表不带表头，即链表的第一个结点就存放雇员信息。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200928212049855.png" alt="image-20200928212049855" style="zoom:67%;" />

## 2、代码

1、员工表

```java
package com.xiaolun.hashtab;

//表示一个雇员
public class Emp {
   public int id;
   public String name;
   public Emp next; //next 默认为 null

   public Emp(int id, String name) {
      super();
      this.id = id;
      this.name = name;
   }
}
```

2、员工链表

```java
package com.xiaolun.hashtab;

//创建EmpLinkedList，表示链条。里面会存放很多数据
public class EmpLinkedList {
   //头指针，执行第一个雇员Emp,因此，我们这个链表的head是直接指向第一个Emp
   private Emp head; //默认为null

   /**
    * 添加雇员到链表
    * 1、假定，当添加雇员时，id是自增长，即id的分配总是从小到大
    * 因此，我们将该雇员直接加入到本链表的最后即可
    *
    * @param emp
    */
   public void add(Emp emp) {
      //如果是，添加第一个雇员
      if (head == null) {
         head = emp;
         return;
      }
      //如果不是第一个雇员，使用辅助指针，帮助定位到最后
      Emp curEmp = head;
      while (true) {
         if (curEmp.next == null) {//说明链表到最后
            break;
         }
         curEmp = curEmp.next; //后移
      }
      //退出时，直接将emp加入链表
      curEmp.next = emp;
   }

   //遍历链表中雇员的信息
   public void list(int no) {
      if (head == null) { //链表为null
         System.out.println("第" + (no + 1) + "链表为空");
         return;
      }
      System.out.print("当前"+(no + 1)+"链表的信息为：");
      Emp curEmp = head; //辅助指针
      while (true) {
         System.out.printf(" => id=%d name=%s\t \n", curEmp.id, curEmp.name);
         if (curEmp.next == null) {//说明curEmp已经到了最后节点
            break;
         }
         curEmp = curEmp.next; //后移，遍历
      }
   }

   /**
    * 根据id查雇员
    * 如果找到，就直接返回Emp,反之，返回null
    *
    * @param id
    * @return
    */
   public Emp findEmpById(int id) {
      //判断链表是否为null
      if (head == null) {
         System.out.println("链表为空");
         return null;
      }
      //辅助指针
      Emp curEmp = head;
      while (true) {
         if (curEmp.id == id) {//找到
            break;//curEmp指向要查找的雇员
         }
         if (curEmp.next == null) {//遍历当前链表中没有找到该雇员
            curEmp = null;
            break;
         }
         curEmp = curEmp.next;
      }
      return curEmp;
   }
}
```

3、哈希表

```java
package com.xiaolun.hashtab;

//创建HashTab 管理多条链表
public class HashTab {
   private EmpLinkedList[] empLinkedListArray;
   private int size; //表示有多少条链表

   //构造器
   public HashTab(int size) {
      this.size = size;
      //初始化empLinkedListArray
      empLinkedListArray = new EmpLinkedList[size];
      /**
       * !!!这里不要初始化每一个链表,不然会报错。空指针异常。
       * 1、原因是，上面一句的确是将链表数组创建，但是里面全部为null。
       * 即（All elements are null）
       * 2、需要进行下面的for循环的初始化操作
       * 3、在执行下面的add添加操作时，null中不能添加元素，故报错。
       */
      for (int i = 0; i < size; i++) {
         empLinkedListArray[i] = new EmpLinkedList();
      }
   }

   //添加雇员
   public void add(Emp emp) {
      //根据雇员的id，得到该雇员应当添加到那条链表
      int empLinkedListNO = hashFun(emp.id);
      //将emp添加到对应的链表中
      empLinkedListArray[empLinkedListNO].add(emp);
   }

   //遍历所有链表，遍历hashtab
   public void list() {
      for (int i = 0; i < size; i++) {
         empLinkedListArray[i].list(i);
      }
   }

   //根据输入的id,查找雇员
   public void findEmpById(int id) {
      //使用散列函数确定到那条链表上查找
      int empLinkedListNO = hashFun(id);
      Emp emp = empLinkedListArray[empLinkedListNO].findEmpById(id);
      if (emp != null) {//找到
         System.out.printf("在第%d条链表中找到雇员， id = %d\n", (empLinkedListNO + 1), id);
      } else {
         System.out.println("在哈希表中，没有找到雇员");
      }
   }

   //编写散列函数，使用一个简单取模法
   public int hashFun(int id) {
      return id % size;
   }
}
```

4、测试

```java
package com.xiaolun.hashtab;

import java.util.Scanner;

public class HashTabDemo {
   public static void main(String[] args) {
      //创建哈希表
      HashTab hashTab = new HashTab(7);

      //写一个简单的菜单
      String key = "";
      Scanner scanner = new Scanner(System.in);
      while (true) {
         System.out.println("add: 添加雇员");
         System.out.println("list: 显示雇员");
         System.out.println("find: 查找雇员");
         System.out.println("exit: 退出系统");

         key = scanner.next();
         switch (key) {
            case "add":
               System.out.println("输入id");
               int id = scanner.nextInt();
               System.out.println("输入名字");
               String name = scanner.next();
               //创建雇员
               Emp emp = new Emp(id, name);
               hashTab.add(emp);
               break;
            case "list":
               hashTab.list();
               break;
            case "find":
               System.out.println("请输入要查找的id");
               id = scanner.nextInt();
               hashTab.findEmpById(id);
               break;
            case "exit":
               scanner.close();
               System.exit(0);
            default:
               break;
         }
      }
   }
}
```

# 3、树结构

## 1、介绍

1、概述

（1）数组存储方式的分析

优点：通过下标方式访问元素，速度快。对于有序数组，还可使用二分查找提高检索速度。

缺点：如果要检索具体某个值，或者插入值(按一定顺序)会整体移动，效率较低。

（2）链式存储方式的分析

优点：在一定程度上对数组存储方式有优化(比如：插入一个数值节点，只需要将插入节点，链接到链表中即可， 删除效率也很好)。

缺点：在进行检索时，效率仍然较低，比如(检索某个值，需要从头节点开始遍历)。 

（3）树存储方式的分析

能提高数据存储，读取的效率, 比如利用 二叉排序树(Binary Sort Tree)，既可以保证数据的检索速度，同时也可以保证数据的插入，删除，修改的速度。

2、树的常用术语

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200929165147099.png" alt="image-20200929165147099" style="zoom:67%;" />

```
节点
根节点
父节点
子节点
叶子节点 (没有子节点的节点)
节点的权(节点值)
路径(从root节点找到该节点的路线)
层
子树
树的高度(最大层数)
森林 :多颗子树构成森林
```

3、二叉树

（1）概述

+ 树有很多种，每个节点最多只能有两个子节点的一种形式称为二叉树。其子节点分为左节点和右节点。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200929165343516.png" alt="image-20200929165343516" style="zoom:80%;" />

+ 如果该二叉树的所有叶子节点都在最后一层，并且结点总数= 2^n -1 , n 为层数，则我们称为满二叉树。
+ 如果该二叉树的所有叶子节点都在最后一层或者倒数第二层，而且最后一层的叶子节点在左边连续，倒数第二层的叶子节点在右边连续，我们称为完全二叉树。如果将（61）节点删除，就不是完全二叉树了，因为叶子节点不连续了。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200929165608056.png" alt="image-20200929165608056" style="zoom:67%;" />

（2）遍历

使用前序，中序和后序对下面的二叉树进行遍历。

前序遍历: 先输出父节点，再遍历左子树和右子树。

中序遍历: 先遍历左子树，再输出父节点，再遍历右子树。

后序遍历: 先遍历左子树，再遍历右子树，最后输出父节点。

小结: 看输出父节点的顺序，就确定是前序，中序还是后序。

（3）遍历步骤

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200929165801835.png" alt="image-20200929165801835" style="zoom:67%;" />

（2）查找节点

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200929185432763.png" alt="image-20200929185432763" style="zoom:67%;" />

（3）删除节点

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200929185516106.png" alt="image-20200929185516106" style="zoom:80%;" />

## 2、代码

1、英雄类

```java
package com.xiaolun.tree;


public class HeroNode {
   private int no;
   private String name;
   private HeroNode left; //默认为null
   private HeroNode right; //默认为null

   public HeroNode(int no, String name) {
      this.no = no;
      this.name = name;
   }

   public int getNo() {
      return no;
   }

   public void setNo(int no) {
      this.no = no;
   }

   public String getName() {
      return name;
   }

   public void setName(String name) {
      this.name = name;
   }

   public HeroNode getLeft() {
      return left;
   }

   public void setLeft(HeroNode left) {
      this.left = left;
   }

   public HeroNode getRight() {
      return right;
   }

   public void setRight(HeroNode right) {
      this.right = right;
   }

   @Override
   public String toString() {
      return "HeroNode [no=" + no + ", name=" + name + "]";
   }

   /**
    * 递归删除节点
    * 1、如果删除的节点是叶子节点，则删除该节点
    * 2、如果删除的节点是非叶子节点，则删除该子树
    */
   public void delNode(int no) {

      /**
       * 思路
       * 1、因为我们的二叉树是单向的，因此，我们判断当前节点的子节点是否是需要删除的节点，
       * 而不能去判断当前这个节点是不是需要删除的节点。
       * 2、如果当前节点的左子节点不为空，且删除的节点为左子节点，那么就将this.left=null;
       * 并且返回（删除后需要结束递归）
       * 3、右子节点同上。
       * 4、如果第2，3步没有删除节点，那么就需要向左子树进行递归删除
       * 5、如果第4步也没有删除节点，那么应该向右子树递归删除
       */
      if (this.left != null && this.left.no == no) {
         this.left = null;
         return;
      }
      if (this.right != null && this.right.no == no) {
         this.right = null;
         return;
      }
      if (this.left != null) {
         this.left.delNode(no);
      }
      if (this.right != null) {
         this.right.delNode(no);
      }
   }

   //前序遍历的方法
   public void preOrder() {
      System.out.println(this); //先输出父节点
      //递归向左子树前序遍历
      if (this.left != null) {
         this.left.preOrder();
      }
      //递归向右子树前序遍历
      if (this.right != null) {
         this.right.preOrder();
      }
   }

   //中序遍历
   public void infixOrder() {
      //递归向左子树前序遍历
      if (this.left != null) {
         this.left.infixOrder();
      }
      //输出父节点
      System.out.println(this);
      //递归向右子树前序遍历
      if (this.right != null) {
         this.right.infixOrder();
      }
   }

   //后序遍历
   public void postOrder() {
      if (this.left != null) {
         this.left.postOrder();
      }
      if (this.right != null) {
         this.right.postOrder();
      }
      System.out.println(this);
   }


   /**
    * 前序遍历查找
    *
    * @param no 查找no
    * @return 如果找到就返回该Node, 反之，返回 null
    */
   public HeroNode preOrderSearch(int no) {
      System.out.println("进入前序遍历");
      //比较当前节点
      if (this.no == no) {
         return this;
      }

      /**
       * 1、判断当前节点的左子节点是否为空，如果不为空，则递归前序查找
       * 2、如果左递归前序查找，找到节点，就返回
       */
      HeroNode resNode = null;
      if (this.left != null) {
         resNode = this.left.preOrderSearch(no);
      }
      if (resNode != null) {//说明我们的左子树找到
         return resNode;
      }
      if (this.right != null) {
         resNode = this.right.preOrderSearch(no);
      }
      return resNode;
   }

   //中序遍历查找
   public HeroNode infixOrderSearch(int no) {
      HeroNode resNode = null;
      if (this.left != null) {
         resNode = this.left.infixOrderSearch(no);
      }
      if (resNode != null) {
         return resNode;
      }
      System.out.println("进入中序查找");
      if (this.no == no) {
         return this;
      }
      //否则继续进行右递归的中序查找
      if (this.right != null) {
         resNode = this.right.infixOrderSearch(no);
      }
      return resNode;
   }

   //后序遍历查找
   public HeroNode postOrderSearch(int no) {

      HeroNode resNode = null;
      if (this.left != null) {
         resNode = this.left.postOrderSearch(no);
      }
      if (resNode != null) {
         return resNode;
      }

      if (this.right != null) {
         resNode = this.right.postOrderSearch(no);
      }
      if (resNode != null) {
         return resNode;
      }
      System.out.println("进入后序查找");
      if (this.no == no) {
         return this;
      }
      return resNode;
   }
}
```

2、二叉树

```java
package com.xiaolun.tree;

//定义二叉树
public class BinaryTree {
   private HeroNode root;

   public void setRoot(HeroNode root) {
      this.root = root;
   }

   //删除节点
   public void delNode(int no) {
      if (root != null) {
         //如果只有一个root节点，那么立即判断root是不是就是要删除的节点
         if (root.getNo() == no) {
            root = null;
         } else {
            //递归删除
            root.delNode(no);
         }
      } else {
         System.out.println("空树，不能删除~");
      }
   }

   //前序遍历
   public void preOrder() {
      if (this.root != null) {
         this.root.preOrder();
      } else {
         System.out.println("二叉树为空，无法遍历");
      }
   }

   //中序遍历
   public void infixOrder() {
      if (this.root != null) {
         this.root.infixOrder();
      } else {
         System.out.println("二叉树为空，无法遍历");
      }
   }

   //后序遍历
   public void postOrder() {
      if (this.root != null) {
         this.root.postOrder();
      } else {
         System.out.println("二叉树为空，无法遍历");
      }
   }

   //前序遍历
   public HeroNode preOrderSearch(int no) {
      if (root != null) {
         return root.preOrderSearch(no);
      } else {
         return null;
      }
   }

   //中序遍历
   public HeroNode infixOrderSearch(int no) {
      if (root != null) {
         return root.infixOrderSearch(no);
      } else {
         return null;
      }
   }

   //后续遍历
   public HeroNode postOrderSearch(int no) {
      if (root != null) {
         return this.root.postOrderSearch(no);
      } else {
         return null;
      }
   }
}
```

3、测试

```java
package com.xiaolun.tree;

public class BinaryTreeDemo {
   public static void main(String[] args) {
      //创建一颗二叉树
      BinaryTree binaryTree = new BinaryTree();
      //创建需要的节点
      HeroNode root = new HeroNode(1, "宋江");
      HeroNode node2 = new HeroNode(2, "吴用");
      HeroNode node3 = new HeroNode(3, "卢俊义");
      HeroNode node4 = new HeroNode(4, "林冲");
      HeroNode node5 = new HeroNode(5, "关胜");

      //手动创建二叉树
      root.setLeft(node2);
      root.setRight(node3);
      node3.setRight(node4);
      node3.setLeft(node5);
      binaryTree.setRoot(root);

      //测试
//    System.out.println("前序遍历"); // 1,2,3,5,4
//    binaryTree.preOrder();

//    System.out.println("中序遍历");
//    binaryTree.infixOrder(); // 2,1,5,3,4
//    
//    System.out.println("后序遍历");
//    binaryTree.postOrder(); // 2,5,4,3,1

      //前序遍历查找
      //前序遍历的次数：4
//    System.out.println("前序遍历方式~~~");
//    HeroNode resNode = binaryTree.preOrderSearch(5);
//    if (resNode != null) {
//       System.out.printf("找到了，信息为 no=%d name=%s", resNode.getNo(), resNode.getName());
//    } else {
//       System.out.printf("没有找到 no = %d 的英雄", 5);
//    }

      //中序遍历查找
      //中序遍历的次数：3
//    System.out.println("中序遍历方式~~~");
//    HeroNode resNode = binaryTree.infixOrderSearch(5);
//    if (resNode != null) {
//       System.out.printf("找到了，信息为 no=%d name=%s", resNode.getNo(), resNode.getName());
//    } else {
//       System.out.printf("没有找到 no = %d 的英雄", 5);
//    }

      //后序遍历查找
      //后序遍历的次数：2
//    System.out.println("后序遍历方式~~~");
//    HeroNode resNode = binaryTree.postOrderSearch(5);
//    if (resNode != null) {
//       System.out.printf("找到了，信息为 no=%d name=%s", resNode.getNo(), resNode.getName());
//    } else {
//       System.out.printf("没有找到 no = %d 的英雄", 5);
//    }

      System.out.println("删除前，前序遍历");
      binaryTree.preOrder(); //  1,2,3,5,4
      binaryTree.delNode(5);
      System.out.println("删除后，前序遍历：");
      binaryTree.preOrder(); // 1,2,3,4
   }
}
```

# 4、顺序存储二叉树

## 1、介绍

1、概述

从数据存储来看，数组存储方式和树的存储方式可以相互转换，即数组可以转换成树，树也可以转换成数组，如下图所示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200929185846960.png" alt="image-20200929185846960" style="zoom:67%;" />

2、特点

（1）顺序二叉树通常只考虑完全二叉树

（2）第n个元素的左子节点为 2 * n + 1 

（3）第n个元素的右子节点为 2 * n + 2

（4）第n个元素的父节点为 (n-1) / 2

n : 表示二叉树中的第几个元素(从0开始编号）

## 2、代码

1、要求

给你一个数组 *{*1,2,3,4,5,6,7}，要求以二叉树前序遍历的方式进行遍历。 前序遍历的结果应当为 1,2,4,5,3,6,7。

2、代码

（1）顺序二叉树

```java
package com.xiaolun.tree;

//创建一个 ArrBinaryTree ，实现顺序二叉树的遍历
public class ArrBinaryTree {
   private int[] arr;  //存储数据结点的数组

   public ArrBinaryTree(int[] arr) {
      this.arr = arr;
   }

   public void preOrder() {
      this.preOrder(0);
   }

   /**
    * 编写一个方法，完成顺序存储二叉树的前序遍历
    *
    * @param index 数组的下标
    */
   public void preOrder(int index) {
      //如果数组为空，或者 arr.length = 0
      if (arr == null || arr.length == 0) {
         System.out.println("数组为空，不能按照二叉树的前序遍历");
      }
      //输出当前这个元素
      System.out.println(arr[index]);
      //向左递归遍历
      if ((index * 2 + 1) < arr.length) {
         preOrder(2 * index + 1);
      }
      //向右递归遍历
      if ((index * 2 + 2) < arr.length) {
         preOrder(2 * index + 2);
      }
   }
}
```

（2）测试

```java
package com.xiaolun.tree;

public class ArrBinaryTreeDemo {
   public static void main(String[] args) {
      int[] arr = { 1, 2, 3, 4, 5, 6, 7 };
      //创建一个ArrBinaryTree
      ArrBinaryTree arrBinaryTree = new ArrBinaryTree(arr);
      arrBinaryTree.preOrder(); // 1,2,4,5,3,6,7
   }
}
```

# 5、线索化二叉树

## 1、介绍

1、背景

将数列 {1, 3, 6, 8, 10, 14 } 构建成一颗二叉树：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200929191246448.png" alt="image-20200929191246448" style="zoom:67%;" />

当我们对上面的二叉树进行中序遍历时，数列为 {8, 3, 10, 1, 6, 14 }，但是 6, 8, 10, 14 这几个节点的 左右指针，并没有完全的利用上。如果我们希望充分的利用 各个节点的左右指针， 让各个节点可以指向自己的前后节点，我们可以使用线索二叉树。

2、概述

（1）n个结点的二叉链表中含有n+1  【公式 2n-(n-1)=n+1】 个空指针域。利用二叉链表中的空指针域，存放指向该结点在某种遍历次序下的前驱和后继结点的指针（这种附加的指针称为"线索"）。

（2）这种加上了线索的二叉链表称为线索链表，相应的二叉树称为线索二叉树(Threaded BinaryTree)。根据线索性质的不同，线索二叉树可分为前序线索二叉树、中序线索二叉树和后序线索二叉树三种。

（3）一个结点的前一个结点，称为前驱结点。

（4）一个结点的后一个结点，称为后继结点。

3、案例

将上面的二叉树，转换成中序线索二叉树。中序遍历的数列为 {8, 3, 10, 1, 14, 6}

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200929191558196.png" alt="image-20200929191558196" style="zoom:67%;" />

当线索化二叉树后，Node节点的 属性 left 和 right ，有如下情况：

（1）left 指向的是左子树，也可能是指向的前驱节点. 比如 ① 节点 left 指向的左子树, 而 ⑩ 节点的 left 指向的就是前驱节点。

（2）right指向的是右子树，也可能是指向后继节点，比如 ① 节点right 指向的是右子树，而⑩ 节点的right 指向的是后继节点。

4、遍历线索二叉树

因为线索化后，各个结点指向有变化，因此原来的遍历方式不能使用，这时需要使用新的方式遍历线索化二叉树，各个节点可以通过线型方式遍历，因此无需使用递归方式，这样也提高了遍历的效率。 遍历的次序应当和中序遍历保持一致。

## 2、代码

1、英雄类

```java
package com.xiaolun.tree.threadedbinarytree;

//先创建 HeroNode 结点
class HeroNode {
   private int no;
   private String name;
   private HeroNode left;
   private HeroNode right;
   /**
    * 说明
    * 1. 如果 leftType == 0 表示指向的是左子树, 如果 1 则表示指向前驱结点
    * 2. 如果 rightType == 0 表示指向是右子树, 如果 1 表示指向后继结点
    */
   private int leftType;
   private int rightType;


   public int getLeftType() {
      return leftType;
   }

   public void setLeftType(int leftType) {
      this.leftType = leftType;
   }

   public int getRightType() {
      return rightType;
   }

   public void setRightType(int rightType) {
      this.rightType = rightType;
   }

   public HeroNode(int no, String name) {
      this.no = no;
      this.name = name;
   }

   public int getNo() {
      return no;
   }

   public void setNo(int no) {
      this.no = no;
   }

   public String getName() {
      return name;
   }

   public void setName(String name) {
      this.name = name;
   }

   public HeroNode getLeft() {
      return left;
   }

   public void setLeft(HeroNode left) {
      this.left = left;
   }

   public HeroNode getRight() {
      return right;
   }

   public void setRight(HeroNode right) {
      this.right = right;
   }

   @Override
   public String toString() {
      return "HeroNode [no=" + no + ", name=" + name + "]";
   }
}
```

2、线索二叉树

```java
package com.xiaolun.tree.threadedbinarytree;

//定义 ThreadedBinaryTree 实现了线索化功能的二叉树
class ThreadedBinaryTree {
   private HeroNode root;

   /**
    * 为了实现线索化，需要创建要给指向当前结点的前驱结点的指针
    * 在递归进行线索化时，pre 总是保留前一个结点
    */
   private HeroNode pre = null;

   public void setRoot(HeroNode root) {
      this.root = root;
   }

   //重载threadedNodes方法
   public void threadedNodes() {
      this.threadedNodes(root);
   }


   /**
    * 二叉树进行中序线索化的方法
    *
    * @param node 就是当前需要线索化的结点
    */
   public void threadedNodes(HeroNode node) {
      //如果 node==null, 不能线索化
      if (node == null) {
         return;
      }

      //(一)先线索化左子树
      threadedNodes(node.getLeft());

      /**
       * (二)线索化当前结点[有难度]
       * 处理当前结点的前驱结点
       * 以 8 结点来理解
       * 8 结点的.left = null , 8 结点的.leftType = 1
       */
      if (node.getLeft() == null) {
         //让当前结点的左指针指向前驱结点
         node.setLeft(pre);
         //修改当前结点的左指针的类型,指向前驱结点
         node.setLeftType(1);
      }

      //处理后继节点
      if (pre != null && pre.getRight() == null) {
         //让前驱结点的右指针指向当前结点
         pre.setRight(node);
         //修改前驱结点的右指针类型
         pre.setRightType(1);
      }
      //!!! 每处理一个结点后，让当前结点是下一个结点的前驱结点
      pre = node;

      //(三)在线索化右子树
      threadedNodes(node.getRight());
   }

   //遍历线索化二叉树
   public void threadedList() {
      //定义一个变量，存储当前遍历的结点，从 root 开始
      HeroNode node = root;
      while (node != null) {
         /**
          * 循环的找到 leftType == 1 的结点，第一个找到就是 8 结点
          * 后面随着遍历而变化,因为当 leftType==1 时，说明该结点是按照线索化
          * 处理后的有效结点
          */
         while (node.getLeftType() == 0) {
            node = node.getLeft();
         }

         //打印当前节点
         System.out.println(node);
         //如果当前结点的右指针指向的是后继结点,就一直输出
         while (node.getRightType() == 1) {
            //获取到当前结点的后继结点
            node = node.getRight();
            System.out.println(node);
         }
         //替换这个遍历的结点（准备下一次操作）
         node = node.getRight();
      }
   }
}
```

3、测试

```java
package com.xiaolun.tree.threadedbinarytree;

//测试中序线索二叉树
public class ThreadedBinaryTreeDemo {
   public static void main(String[] args) {
      HeroNode root = new HeroNode(1, "tom");
      HeroNode node2 = new HeroNode(3, "jack");
      HeroNode node3 = new HeroNode(6, "smith");
      HeroNode node4 = new HeroNode(8, "mary");
      HeroNode node5 = new HeroNode(10, "king");
      HeroNode node6 = new HeroNode(14, "dim");

      root.setLeft(node2);
      root.setRight(node3);
      node2.setLeft(node4);
      node2.setRight(node5);
      node3.setLeft(node6);

      //中序线索化
      ThreadedBinaryTree threadedBinaryTree = new ThreadedBinaryTree();
      threadedBinaryTree.setRoot(root);
      threadedBinaryTree.threadedNodes();

      //测试: 以 10 号节点测试
      HeroNode leftNode = node5.getLeft();
      HeroNode rightNode = node5.getRight();
      System.out.println("10 号结点的前驱结点是 =" + leftNode); //3
      System.out.println("10 号结点的后继结点是=" + rightNode); //1

      //当线索化二叉树后，能在使用原来的遍历方法
      System.out.println("使用线索化的方式遍历 线索化二叉树");
      threadedBinaryTree.threadedList(); // 8, 3, 10, 1, 14, 6
   }
}
```

# 6、树结构的实际应用

## 1、堆排序

### 1、介绍

1、概述

（1）堆排序是利用堆这种数据结构而设计的一种排序算法，堆排序是一种选择排序，它的最坏，最好，平均时间复杂度均为O(nlogn)，它也是不稳定排序。

（2）堆是具有以下性质的完全二叉树：每个结点的值都大于或等于其左右孩子结点的值，称为大顶堆, 注意 : 没有要求结点的左孩子的值和右孩子的值的大小关系。

（3）每个结点的值都小于或等于其左右孩子结点的值，称为小顶堆。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200929200631969.png" alt="image-20200929200631969" style="zoom:67%;" />

（4）大顶堆特点

```java
// i 对应第几个节点，i从0开始编号（左右子节点）
arr[i] >= arr[2*i+1] && arr[i] >= arr[2*i+2]  
```

（5）小顶堆特点

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200929200741452.png" alt="image-20200929200741452" style="zoom:67%;" />



```java
 // i 对应第几个节点，i从0开始编号
arr[i] <= arr[2*i+1] && arr[i] <= arr[2*i+2]
```

（6）一般升序采用大顶堆，降序采用小顶堆。

2、堆排序的基本思想

```
1、将待排序序列构造成一个大顶堆（数是以数组的形式来存储）
2、此时，整个序列的最大值就是堆顶的根节点。
3、将其与末尾元素进行交换，此时末尾就为最大值。
4、然后将剩余n-1个元素重新构造成一个堆，这样会得到n个元素的次小值。如此反复执行，便能得到一个有序序列了。
```

可以看到在构建大顶堆的过程中，元素的个数逐渐减少，最后就得到一个有序序列了。

3、图解

步骤一：构造初始堆。将给定无序序列构造成一个大顶堆。

（1）假定原始的数组 [4, 6, 8, 5, 9]，且假设给定无序序列结构如下：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200929211940041.png" alt="image-20200929211940041" style="zoom:67%;" />

（2）我们从最后一个非叶子结点开始（叶结点自然不用调整，第一个非叶子结点arr.length/2-1=5/2-1=1，也就是下面的 6 结点），从左至右，从下至上进行调整。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200929212005656.png" alt="image-20200929212005656" style="zoom:67%;" />

（3）找到第二个非叶节点 4，由于[4,9,8]中 9 元素最大，4 和 9 交换。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200929212027869.png" alt="image-20200929212027869" style="zoom:67%;" />

这时，交换导致了子根[4,5,6]结构混乱，继续调整，[4,5,6]中 6 最大，交换 4 和 6。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200929212051049.png" alt="image-20200929212051049" style="zoom:67%;" />

此时，我们就将一个无序序列构造成了一个大顶堆。

步骤二：将堆顶元素与末尾元素进行交换，使末尾元素最大。然后继续调整堆，再将堆顶元素与末尾元素交换，得到第二大元素。如此反复进行交换、重建、交换。

（1）将堆顶元素 9 和末尾元素 4 进行交换

![image-20200929212218908](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200929212218908.png)

重新调整结构，使其继续满足堆定义：

![image-20200929212242320](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200929212242320.png)

（2）再将堆顶元素 8 与末尾元素 5 进行交换，得到第二大元素 8。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200929212301350.png" alt="image-20200929212301350" style="zoom:67%;" />

（3）后续过程，继续进行调整，交换，如此反复进行，最终使得整个序列有序

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200929212324675.png" alt="image-20200929212324675" style="zoom:67%;" />



总结：

```
1、将无序序列构建成一个堆，根据升序降序需求选择大顶堆或小顶堆;
2、将堆顶元素与末尾元素交换，将最大元素"沉"到数组末端;
3、重新调整结构，使其满足堆定义，然后继续交换堆顶元素与当前末尾元素，反复执行调整+交换步骤，直到整个序列有序。
```

### 2、代码

```java
package com.xiaolun.tree;

import java.util.Arrays;

//堆排序
public class HeapSort {
   public static void main(String[] args) {
      int arr[] = {4, 6, 8, 5, 9}; //将数组进行升序排列（大顶堆）
      heapSort(arr);
      System.out.println("排序后输出=" + Arrays.toString(arr));
   }

   //堆排序的方法
   public static void heapSort(int arr[]) {
      int temp = 0;
      System.out.println("堆排序");

      //分步完成
//    adjustHeap(arr, 1, arr.length);
//    System.out.println("第一次调整：" + Arrays.toString(arr)); // 4, 9, 8, 5, 6
//    
//    adjustHeap(arr, 0, arr.length);
//    System.out.println("第二次调整：" + Arrays.toString(arr)); // 9,6,8,5,4

      /**
       * 1、将无序序列构建成一个堆，根据升序需求选择大顶堆，下面是一个大鼎退
       * 其中，arr.length / 2 - 1 表示非叶子节点
       * 只考虑完全二叉树
       */
      for (int i = arr.length / 2 - 1; i >= 0; i--) {
         adjustHeap(arr, i, arr.length);
      }

      /**
       * 2、将堆顶元素与末尾元素交换，将最大元素"沉"到数组末端;
       * 3、重新调整结构，使其满足堆定义，然后继续交换堆顶元素与当前末尾元素，反复执行调整+交换步骤，
       * 直到整个序列有序。
       */
      for (int j = arr.length - 1; j > 0; j--) {
         //交换
         temp = arr[j];
         arr[j] = arr[0];
         arr[0] = temp;
         adjustHeap(arr, 0, j);
      }
   }

   /**
    * 将一个数组(二叉树), 调整成一个大顶堆
    * 功能： 完成将以 i 对应的非叶子结点的树调整成大顶堆
    * 举例int arr[] = {4, 6, 8, 5, 9}; => i = 1 => adjustHeap => 得到 {4, 9, 8, 5, 6}
    * 如果我们再次调用adjustHeap 传入的是 i = 0 => 得到 {4, 9, 8, 5, 6} => {9,6,8,5, 4}
    *
    * @param arr    待调整的数组
    * @param i      表示非叶子结点在数组中索引
    * @param length 表示对多少个元素继续调整， length 是在逐渐的减少
    */
   public static void adjustHeap(int arr[], int i, int length) {
      int temp = arr[i];//先取出当前元素的值，保存在临时变量

      /**
       * 开始调整
       * 1、k = i * 2 + 1 k 是 i 结点的左子结点
       * k = k * 2 + 1 表示当前节点的左子节点
       */
      //找到最大值，并放到适当的位置
      for (int k = i * 2 + 1; k < length; k = k * 2 + 1) {
         //k + 1 < length 保证 右子节点存在
         if (k + 1 < length && arr[k] < arr[k + 1]) { //说明左子结点的值小于右子结点的值
            k++; // k 指向右子结点
         }
         if (arr[k] > temp) { //假设此时为arr[k]对应的是右子节点， 并且如果子结点大于父结点
            arr[i] = arr[k]; //把较大的值赋给当前结点
            i = k; //!!! i 指向 k,继续循环比较(可能还有左/右子树)
         } else {
            break; //从左至右，从下至上。此时下面的已经调整好了。
         }
      }
      //当 for 循环结束后，我们已经将以 i 为父结点的树的最大值，放在了 最顶(局部)
      arr[i] = temp;//将 temp 值放到调整后的位置
   }
}
```

## 2、赫夫曼树

### 1、介绍

1、概述

（1）给定 n 个权值作为 n 个叶子结点，构造一棵二叉树，若该树的带权路径长度(wpl)达到最小，称这样的二叉树为最优二叉树，也称为哈夫曼树(Huffman Tree), 还有的书翻译为霍夫曼树。

（2）赫夫曼树是带权路径长度最短的树，权值较大的结点离根较近。

2、几个重要概念和举例说明

（1）路径和路径长度：在一棵树中，从一个结点往下可以达到的孩子或孙子结点之间的通路，称为路径。通路中分支的数目称为路径长度。若规定根结点的层数为 1，则从根结点到第 L 层结点的路径长度为 L-1。

（2）结点的权及带权路径长度：若将树中结点赋给一个有着某种含义的数值，则这个数值称为该结点的权。结点的带权路径长度为：从根结点到该结点之间的路径长度与该结点的权的乘积。

（3）树的带权路径长度：树的带权路径长度规定为所有叶子结点的带权路径长度之和，记为 WPL(weighted path length) ,权值越大的结点离根结点越近的二叉树才是最优二叉树。

（4）WPL 最小的就是赫夫曼树。

2、图解

（1）需求

给你一个数列 {13, 7, 8, 3, 29, 6, 1}，要求转成一颗赫夫曼树。

（2）步骤

```
（1）从小到大进行排序, 将每一个数据，每个数据都是一个节点，每个节点可以看成是一颗最简单的二叉树。
（2）取出根节点权值最小的两颗二叉树。 
（3）组成一颗新的二叉树, 该新的二叉树的根节点的权值是前面两颗二叉树根节点权值的和。  
（4）再将这颗新的二叉树，以根节点的权值大小 再次排序， 不断重复  1-2-3-4 的步骤，直到数列中，所有的数据都被处理，就得到一颗赫夫曼树。
```







