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















































