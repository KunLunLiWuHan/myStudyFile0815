# 1、递归（Recursion）

## 1、介绍

1、概述

递归就是方法自己调用自己,每次调用时传入不同的变量。递归有助于编程者解决复杂的问题，同时可以让代码变得简洁。

2、使用场景

（1）各种数学问题如: 8皇后问题 , 汉诺塔, 阶乘问题, 迷宫问题, 球和篮子的问题(google编程大赛)。

（2）各种算法中也会使用到递归，比如快排，归并排序，二分查找，分治算法等。

（3）将用栈解决的问题-->递归代码比较简洁。

3、递归程序遵守的规则

（1）执行一个方法时，就创建一个新的受保护的独立空间(栈空间)。

（2）方法的局部变量是独立的，不会相互影响, 比如n变量。

（3）如果方法中使用的是引用类型变量(比如数组)，就会共享该引用类型的数据。

（4）递归必须向退出递归的条件逼近，否则就是无限递归,出现StackOverflowError，死龟了)。

（5）当一个方法执行完毕，或者遇到return，就会返回，遵守谁调用，就将结果返回给谁，同时当方法执行完毕或者返回时，该方法也就执行完毕。

## 2、迷宫问题

1、路径

![image-20200926193821999](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200926193821999.png)

2、代码

```java
package com.xiaolun.recursion;

public class MiGong {
   public static void main(String[] args) {
      // 先创建一个二维数组，模拟迷宫
      // 地图
      int[][] map = new int[8][7];
      // 使用1 表示墙
      // 上下全部置为1
      for (int i = 0; i < 7; i++) {
         map[0][i] = 1;
         map[7][i] = 1;
      }

      // 左右全部置为1
      for (int i = 0; i < 8; i++) {
         map[i][0] = 1;
         map[i][6] = 1;
      }

      //设置挡板, 1 表示
      map[3][1] = 1;
      map[3][2] = 1;

      // 输出地图
      System.out.println("地图的情况");
      for (int i = 0; i < 8; i++) {
         for (int j = 0; j < 7; j++) {
            System.out.print(map[i][j] + " ");
         }
         System.out.println();
      }

      //
      /**
       * 使用递归回溯给小球找路
       * 传入的是一个数组，说明该数组在递归程序中共享。
       */
      setWay(map, 1, 1);
//    setWay2(map, 1, 1);

      //输出新的地图, 小球走过，并标识过的递归
      System.out.println("小球走过，并标识过的 地图的情况");
      for (int i = 0; i < 8; i++) {
         for (int j = 0; j < 7; j++) {
            System.out.print(map[i][j] + " ");
         }
         System.out.println();
      }
   }

   /**
    * 使用递归回溯来给小球找路
    * 说明
    * 1. map 表示地图
    * 2. i,j 表示从地图的哪个位置开始出发 (1,1)
    * 3. 如果小球能到 map[6][5] 位置，则说明通路找到
    * 4. 约定： 当map[i][j] 为 0 表示该点没有走过，为 1 表示墙； 2 表示通路可以走 ； 3 表示该点已经走过，但是走不通
    * 5. 在走迷宫时，需要确定一个策略(方法) 下->右->上->左 , 如果该点走不通，再回溯
    *
    * @param map 表示地图
    * @param i   从哪个位置开始找(原始位置)
    * @param j
    * @return 如果找到通路，就返回true, 否则返回false
    */
   public static boolean setWay(int[][] map, int i, int j) { //2 2
      if (map[6][5] == 2) { // 通路已经找到ok
         return true;
      } else {
         if (map[i][j] == 0) { //如果当前这个点还没有走过
            //按照策略 下->右->上->左  走
            map[i][j] = 2; // 假定该点是可以走通.
            if (setWay(map, i + 1, j)) {//向下走
               return true;
               //上一个域中的数据（i=2,j=1）会被带到这里来,进而变成(2,1+1=2)
            } else if (setWay(map, i, j + 1)) { //向右走 3
               return true;
            } else if (setWay(map, i - 1, j)) { //向上
               return true;
            } else if (setWay(map, i, j - 1)) { // 向左走
               return true;
            } else {
               //说明该点是走不通，是死路
               map[i][j] = 3;
               return false;
            }
         } else { // 如果map[i][j] != 0 , 可能是 1， 2， 3
            return false;
         }
      }
   }

   //修改找路的策略，改成 上->右->下->左
   public static boolean setWay2(int[][] map, int i, int j) {
      if (map[6][5] == 2) { // 通路已经找到ok
         return true;
      } else {
         if (map[i][j] == 0) { //如果当前这个点还没有走过
            //按照策略 上->右->下->左
            map[i][j] = 2; // 假定该点是可以走通.
            if (setWay2(map, i - 1, j)) {//向上走
               return true;
            } else if (setWay2(map, i, j + 1)) { //向右走
               return true;
            } else if (setWay2(map, i + 1, j)) { //向下
               return true;
            } else if (setWay2(map, i, j - 1)) { // 向左走
               return true;
            } else {
               //说明该点是走不通，是死路
               map[i][j] = 3;
               return false;
            }
         } else { // 如果map[i][j] != 0 , 可能是 1， 2， 3
            return false;
         }
      }
   }
}
```

## 3、八皇后问题

1、介绍

（1）概述

在8×8格的国际象棋上摆放八个皇后，使其不能互相攻击，即：任意两个皇后都不能处于同一行、同一列或同一斜线上，问有多少种摆法。

（2）思路分析

+ 第一个皇后先放第一行第一列。

+ 第二个皇后放在第二行第一列、然后判断是否OK， 如果不OK，继续放在第二列、第三列、依次把所有列都放完，找到一个合适。

+ 继续第三个皇后，还是第一列、第二列……直到第8个皇后也能放在一个不冲突的位置，算是找到了一个正确解。

+ 当得到一个正确解时，在栈回退到上一个栈时，就会开始回溯，即将第一个皇后，放到第一列的所有正确解，全部得到。

+ 然后回头继续第一个皇后放第二列，后面继续循环执行 1,2,3,4的步骤。

说明：

理论上应该创建一个二维数组来表示棋盘，但是实际上可以通过算法，用一个一维数组即可解决问题。

```java
//对应arr 下标 表示第几行，即第几个皇后
arr[8] = {0 , 4, 7, 5, 2, 6, 1, 3} 

//val 表示第i+1个皇后，放在第i+1行的第val+1列
arr[i] = val
```

 2、代码

```java
package com.xiaolun.recursion;

public class Queue8 {
   //定义一共有多少皇后
   int max = 8;
   //定义数组array, 保存皇后放置位置的结果，比如：arr = {0 , 4, 7, 5, 2, 6, 1, 3}
   int[] array = new int[max];
   static int count = 0;
   static int judgeCount = 0;

   public static void main(String[] args) {
      Queue8 queue8 = new Queue8();
      queue8.check(0);
      System.out.printf("一共有%d中解法", count);
      System.out.printf("一共判断冲突次数%d次", judgeCount); // 1.5w
   }

   //放置第n个皇后
   //注：check是每一次递归时，进入check中都有 for(int i = 0; i < max; i++)，因此有回溯
   private void check(int n) {
      if (n == max) {  //n = 8，其实8个皇后已经放好了
         print();
         return;
      }

      //依次判断皇后，并判断是否冲突
      for (int i = 0; i < max; i++) {
         //先把当前这个皇后n,放到该行的第一列
         array[n] = i;
         //判断当前放置第n个皇后到i列时，是否冲突
         if (judge(n)) { //  不冲突
            //接着放n+1个皇后，开始递归
            check(n + 1);
         }
         //如果冲突，就继续执行array[n] = i;即将第n个皇后，放置在本行的后一个位置
      }
   }


   /**
    * 查看当我们放置第n个皇后，就去检测该皇后是否和前面已经摆放的皇后冲突
    *
    * @param n 表示第n个皇后
    * @return
    */
   private boolean judge(int n) {
      judgeCount++;
      for (int i = 0; i < n; i++) {
         /**
          * 说明
          * 1、array[i] == array[n] 表示判断第n个皇后是否和前面的n-1个皇后在同一列
          * 2、Math.abs(n-i) == Math.abs(array[n] - array[i])表示判断第n个皇后是否
          *   和第i个皇后在同一斜线（与数组array的设计有密切关系）
          * 3、判断是否在同一行，没有必要，n每次都在增加
          * 比如 判断，第二个皇后的位置 arr[1]=1,是否和arr[0]=0冲突（原则上是冲突的）
          */
         if (array[i] == array[n] || Math.abs(n - i) == Math.abs(array[n] - array[i])) {
            return false;
         }
      }
      return true;
   }

   //写一个方法，可以将皇后摆放的位置输出
   private void print() {
      count++;
      for (int i = 0; i < array.length; i++) {
         System.out.print(array[i] + " ");
      }
      System.out.println();
   }
}
```

# 2、排序算法(Sort Algorithm)

## 1、介绍

1、概述

排序也称排序算法，是将一组数据，依指定的顺序进行排列的过程。

2、分类

（1）内部排序:

指将需要处理的所有数据都加载到内部存储器中进行排序。

（2）外部排序法：

数据量过大，无法全部加载到内存中，需要借助外部存储进行排序。

（3）排序算法分类如下：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200926210455269.png" alt="image-20200926210455269" style="zoom:67%;" />



3、算法的时间复杂度

（1）度量一个程序(算法)执行时间的两种方法

+ 事后统计

 这种方式，要在同一台计算机的相同状态下运行，才能比较那个算法速度更快。

+ 事前估算

1)通过分析某个算法的时间复杂度来判断哪个算法更优。

4、时间频度

一个算法花费的时间与算法中语句的执行次数成正比例，哪个算法中语句执行次数多，它花费时间就多。

一个算法中的语句执行次数称为语句频度或时间频度。记为T(n)。

5、时间复杂度

（1）一般情况下，算法中的基本操作语句的重复执行次数是问题规模n的某个函数，用T(n)表示，若有某个辅助函数f(n)，使得当n趋近于无穷大时，T(n) / f(n) 的极限值为不等于零的常数，则称f(n)是T(n)的同数量级函数。记作 T(n)=Ｏ( f(n) )，称Ｏ( f(n) ) 为算法的渐进时间复杂度，简称时间复杂度。

（2）T(n) 不同，但时间复杂度可能相同。

 如：T(n)=n²+7n+6 与 T(n)=3n²+2n+2 它们的T(n) 不同，但时间复杂度相同，都为O(n²)。

（3）计算时间复杂度的方法

+ 用常数1代替运行时间中的所有加法常数

```
 T(n)=n²+7n+6 => T(n)=n²+7n+1
```

+ 修改后的运行次数函数中，只保留最高阶项 

```
T(n)=n²+7n+1 => T(n) = n²
```

+ 去除最高阶项的系数 

```
T(n) = n² => T(n) = n² => O(n²)
```

6、常见的时间复杂度

![image-20200926210852862](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200926210852862.png)

+ 常数阶O(1)

+ 对数阶O(log2n)

+ 线性阶O(n)

+ 线性对数阶O(nlog2n)

+ 平方阶O(n^2)

+ 立方阶O(n^3)

+ k次方阶O(n^k)

+ 指数阶O(2^n)

常见的算法时间复杂度由小到大依次为：

```
Ο(1)＜Ο(log2n)＜Ο(n)＜Ο(nlog2n)＜Ο(n2)＜Ο(n3)＜Ο(nk) ＜Ο(2n) 
```

随着问题规模n的不断增大，上述时间复杂度不断增大，算法的执行效率越低。

从图中可见，我们应该尽可能避免使用指数阶的算法。

（1）常数阶O(1)

无论代码执行了多少行，只要是没有循环等复杂结构，那这个代码的时间复杂度就都是O(1)。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200926211031522.png" alt="image-20200926211031522" style="zoom:80%;" />

上述代码在执行的时候，它消耗的时候并不随着某个变量的增长而增长，那么无论这类代码有多长，即使有几万几十万行，都可以用O(1)来表示它的时间复杂度。

（2）对数阶O(log2n)

![image-20200926211103437](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200926211103437.png)

在while循环里面，每次都将 i 乘以 2，乘完之后，i 距离 n 就越来越近了。假设循环x次之后，i 就大于 n了，此时这个循环就退出了，也就是说 2 的 x 次方等于 n，那么 x = log2n，也就是说当循环 log2n 次以后，这个代码就结束了。因此这个代码的时间复杂度为：O(log2n)  。 

O(log2n) 的这个2 时间上是根据代码变化的，i = i * 3 ，则是 O(log3n)

（3）线性阶O(n)

![image-20200926211207037](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200926211207037.png)

这段代码，for循环里面的代码会执行n遍，因此它消耗的时间是随着n的变化而变化的，因此这类代码都可以用O(n)来表示它的时间复杂度。

（4）线性对数阶O(nlogN)

![image-20200926211233838](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200926211233838.png)

线性对数阶O(nlogN) 其实非常容易理解，将时间复杂度为O(logn)的代码循环N遍的话，那么它的时间复杂度就是 n * O(logN)，也就是了O(nlogN)。

7、平均时间复杂度和最坏时间复杂度

平均时间复杂度和最坏时间复杂度是否一致，和算法有关，如下图所示：

![image-20200926211306460](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200926211306460.png)

（1）平均时间复杂度是指所有可能的输入实例均以等概率出现的情况下，该算法的运行时间。

（2）最坏情况下的时间复杂度称最坏时间复杂度。一般讨论的时间复杂度均是最坏情况下的时间复杂度。 这样做的原因是：最坏情况下的时间复杂度是算法在任何输入实例上运行时间的界限，这就保证了算法的运行时间不会比最坏情况更长。

8、空间复杂度

（1）类似于时间复杂度的讨论，一个算法的空间复杂度(Space Complexity)定义为该算法所耗费的存储空间，它也是问题规模n的函数。

（2）空间复杂度(Space Complexity)是对一个算法在运行过程中临时占用存储空间大小的量度。有的算法需要占用的临时工作单元数与解决问题的规模n有关，它随着n的增大而增大，当n较大时，将占用较多的存储单元，例如快速排序和归并排序算法就属于这种情况。

（3）在做算法分析时，主要讨论的是时间复杂度。从用户使用体验上看，更看重的程序执行的速度。

一些缓存产品(redis, memcache)和算法(基数排序)本质就是用空间换时间。

## 2、冒泡排序（Bubble Sorting）

### 1、介绍

1、概述

冒泡排序的基本思想是：通过对待排序序列从前向后（从下标较小的元素开始）,依次比较相邻元素的值，若发现逆序则交换，使值较大的元素逐渐从前移向后部（从小到大排序），就象水底下的气泡一样逐渐向上冒。

因为排序的过程中，各元素不断接近自己的位置，如果一趟比较下来没有进行过交换，就说明序列有序，因此要在排序过程中设置一个标志flag判断元素是否进行过交换，从而减少不必要的比较。

2、举例

```
原始数组：3（指针1）, 9（指针2）, -1, 10, 20

第一趟排序
(1)  3, 9（指针1）, -1（指针2）, 10, 20   // 如果相邻的元素逆序就交换
(2)  3, -1, 9, 10, 20
(3)  3, -1, 9, 10, 20
(4)  3, -1, 9, 10, 20
在上面的原始数组到（1）中，进行比较后的排序和指针的移动操作，第一次排序可以确定最大值20。

第二趟排序
(1) -1, 3, 9, 10, 20 //交换
(2) -1, 3, 9, 10, 20
(3) -1, 3, 9, 10, 20 //确定10，20

第三趟排序
(1) -1, 3, 9, 10, 20 
(2) -1, 3, 9, 10, 20 //确定9，10，20

第四趟排序
(1) -1, 3, 9, 10, 20 //确定3，9，10，20
```

规则：

（1）一共进行 （数组的大小-1）次 大的循环

（2）每一趟排序的次数在逐渐的减少

（3）如果我们发现在某趟排序中，没有发生一次交换， 可以提前结束冒泡排序。这个就是优化操作。

### 2、代码

```java
package com.xiaolun.sort;

import java.util.Arrays;

/**
 * 冒泡排序
 */
public class BubbleSort {
   public static void main(String[] args) {
      //测试1：简单数据排列
      int arr[] = {3, 9, -1, 20, 7}; //从小到大排列
      System.out.println("排序前");
      System.out.println(Arrays.toString(arr));
      //使用封装的方法
//    System.out.println("排序后");
//    bubbleSort(arr);
//    System.out.println(Arrays.toString(arr));

         /*
      // 第一趟排序，就是将最大的数排在最后
      for (int j = 0; j < arr.length - 1 - 0; j++) {
         // 如果前面的数比后面的大就交换
         if (arr[j] > arr[j + 1]) {
            temp = arr[j];
            arr[j] = arr[j + 1];
            arr[j + 1] = temp;
         }
      }
      System.out.println("第二趟排序后的数组");
      System.out.println(Arrays.toString(arr));

      // 第二趟排序，就是将第二大的数排在倒数第二位
      for (int j = 0; j < arr.length - 1 - 1 ; j++) {
         // 如果前面的数比后面的大就交换
         if (arr[j] > arr[j + 1]) {
            temp = arr[j];
            arr[j] = arr[j + 1];
            arr[j + 1] = temp;
         }
      }
      System.out.println("第二趟排序后的数组");
      System.out.println(Arrays.toString(arr));
      */

      //测试2：排序速度O(n^2), 创建一个80000个随机的数据
//    int[] arr = new int[80000];
//    for (int i = 0; i < 80000; i++) {
//       arr[i] = (int) (Math.random() * 8000000); //生成一个[0, 8000000)的数
//    }
//
//    Date data1 = new Date();
//    SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
//    String date1Str = simpleDateFormat.format(data1);
//    System.out.println("排序前的时间为=" + date1Str);
//
//    //测试冒泡排序
//    bubbleSort(arr);
//
//    Date data2 = new Date();
//    String date2Str = simpleDateFormat.format(data2);
//    System.out.println("排序后的时间=" + date2Str);
   }

   // 将前面的冒泡算法，封装成一个方法
   public static void bubbleSort(int[] arr) {
      // 时间复杂度O(n^2),
      int temp = 0; // 临时变量
      boolean flag = false; // 标识变量，自己是否进行过交换
      for (int i = 0; i < arr.length - 1; i++) {
         //数组进行(arr.length-1)趟排序
         for (int j = 0; j < arr.length - 1 - i; j++) {
            if (arr[j] > arr[j + 1]) {
               flag = true;
               temp = arr[j];
               arr[j] = arr[j + 1];
               arr[j + 1] = temp;
            }
         }
         //System.out.println("第" + (i + 1) + "趟排序后的数组");
         //System.out.println(Arrays.toString(arr));

         if (!flag) { // 在一趟排序中，一次交换都没有发生过
            break;
         } else {
            flag = false; // 重置flag,进行下次判断。
         }
      }
   }
}
```

## 3、选择排序（select sorting）

### 1、介绍

1、概述

选择式排序也属于内部排序法，是从欲排序的数据中，按指定的规则选出某一元素，再依规定交换位置后达到排序的目的。

2、思想

```
第一次从arr[0]~arr[n-1]中选取最小值，与arr[0]交换，
第二次从arr[1]~arr[n-1]中选取最小值，与arr[1]交换，
第三次从arr[2]~arr[n-1]中选取最小值，与arr[2]交换，…，
第i次从arr[i-1]~arr[n-1]中选取最小值，与arr[i-1]交换，…, 
第n-1次从arr[n-2]~arr[n-1]中选取最小值，与arr[n-2]交换，总共通过n-1次，得到一个按排序码从小到大排列的有序序列。
```

3、分析图

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200927141107147.png" alt="image-20200927141107147" style="zoom:67%;" />

```
原始的数组 ：101, 34, 119, 1
第一轮排序 :   1, 34, 119, 101
第二轮排序 :   1, 34, 119, 101
第三轮排序 :   1, 34, 101, 119

说明：
1. 选择排序一共有 数组大小 - 1 轮排序
2. 每1轮排序，又是一个循环, 循环的规则(代码)
  2.1先假定当前这个数是最小数
  2.2 然后和后面的每个数进行比较，如果发现有比当前数更小的数，就重新确定最小数，并得到下标
  2.3 当遍历到数组的最后时，就得到本轮最小数和下标
  2.4 交换 
```

### 2、代码

```java
package com.xiaolun.sort;

import java.text.SimpleDateFormat;
import java.util.Date;

//选择排序
public class SelectSort {
   public static void main(String[] args) {
      //测试1：从小到大排列
//    int[] arr = {101, 34, 119, 1};

         /*
      //逐步推导
      //第一轮
      //原始数据     101, 34, 119, 1
      //排序后 :   1, 34, 119, 101
      //算法 先简单，后复杂（把复杂算法拆解成简单的问题，逐步解决）
      int minIndex = 0;
      int min = arr[0];
      for (int j = 0 + 1; j < arr.length; j++) {
         if (min > arr[j]) {
            min = arr[j];
            minIndex = j;
         }
      }

      if (minIndex != 0) {
         arr[minIndex] = arr[0];
         arr[0] = min;
      }
      System.out.println("第1轮后~~");
      System.out.println(Arrays.toString(arr));// 1, 34, 119, 101


      //第2轮
      minIndex = 1;
      min = arr[1];
      for (int j = 1 + 1; j < arr.length; j++) {
         if (min > arr[j]) {
            min = arr[j];
            minIndex = j;
         }
      }
      if(minIndex != 1) {
         arr[minIndex] = arr[1];
         arr[1] = min;
      }

      System.out.println("第2轮后");
      System.out.println(Arrays.toString(arr));// 1, 34, 119, 101
   }
*/
      //测试2：选择排序速度，创建80000随机数，比冒泡快
      int[] arr = new int[80000];
      for (int i = 0; i < 80000; i++) {
         arr[i] = (int) (Math.random() * 8000000);
      }

      Date data1 = new Date();
      SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
      String date1Str = simpleDateFormat.format(data1);
      System.out.println("排序前时间=" + date1Str);

      selectSort(arr);

      Date data2 = new Date();
      String date2Str = simpleDateFormat.format(data2);
      System.out.println("排序前时间=" + date2Str);
   }

   public static void selectSort(int[] arr) {
      //时间复杂度O(n^2)，使用for循环解决
      for (int i = 0; i < arr.length - 1; i++) {
         int minIndex = i;
         int min = arr[i];
         for (int j = i + 1; j < arr.length; j++) {
            if (min > arr[j]) { // 说明假定的最小值，并不是最小
               min = arr[j]; // 重置min
               minIndex = j; // 重置minIndex（保存最小的值的索引值）
            }
         }

         /**
          * 交换的时候，做一个判断，进行优化
          * 因为，当经过上面的查询最小值后，发现最小值就是其本身，此时
          * 不需要进行交换
          */
         if (minIndex != i) {
            arr[minIndex] = arr[i];
            arr[i] = min;
         }

         //System.out.println("第"+(i+1)+"轮后~~");
         //System.out.println(Arrays.toString(arr));// 1, 34, 119, 101
      }
   }
}
```

## 4、插入排序（Insertion Sorting）

### 1、介绍

1、概述

是对于欲排序的元素以插入的方式找寻该元素的适当位置，以达到排序的目的。

2、思想

把n个待排序的元素看成为一个有序表和一个无序表，开始时有序表中只包含一个元素，无序表中包含有n-1个元素，排序过程中每次从无序表中取出第一个元素，把它的排序码依次与有序表元素的排序码进行比较，将它插入到有序表中的适当位置，使之成为新的有序表。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200927150610665.png" alt="image-20200927150610665" style="zoom:80%;" />

### 2、代码

```java
package com.xiaolun.sort;

//插入排序
public class InsertSort {
   public static void main(String[] args) {
      //测试1：从小到大排序
      int[] arr = {101, 34, 119, 1};

      //测试2：速度测试
//    int[] arr = new int[80000];
//    for (int i = 0; i < 80000; i++) {
//       arr[i] = (int) (Math.random() * 8000000);
//    }
//
//    Date data1 = new Date();
//    SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
//    String date1Str = simpleDateFormat.format(data1);
//    System.out.println("排序前时间=" + date1Str);
//
//    insertSort(arr); //插入排序算法
//
//    Date data2 = new Date();
//    String date2Str = simpleDateFormat.format(data2);
//    System.out.println("排序后时间=" + date2Str);
   }

   public static void insertSort(int[] arr) {
      int insertVal = 0;
      int insertIndex = 0;
      for (int i = 1; i < arr.length; i++) {
         //定义待插入的数
         insertVal = arr[i];
         insertIndex = i - 1; // 即arr[1]前面的这个数的下标

         /**
          * 给insertVal找到插入的位置
          * 1. insertIndex >= 0 保证在给insertVal找插入位置，不越界
          * 2. insertVal < arr[insertIndex] 待插入的数，还没找到插入位置
          *  3.将arr[insertIndex] 后移
          *  比如插入34时：
          *  {101, 34, 119, 1} => {101, 101, 119, 1} =>{34, 101, 119, 1}
          *  插入1时：
          *  {34, 101, 119, 1} => {34, 101, 119, 119} => {34, 101, 101, 119}
          *  {34, 34, 101, 1} => {1,34, 101, 119}
          */
         while (insertIndex >= 0 && insertVal < arr[insertIndex]) {
            arr[insertIndex + 1] = arr[insertIndex];
            insertIndex--;
         }

         /**
          * 当退出while循环时,说明插入的位置找到，insertIndex + 1
          * 判断是否需要赋值
          */
         if (insertIndex + 1 == i) {
            //不符合while循环，直接跳出循环了
         } else {
            arr[insertIndex + 1] = insertVal;
         }
      }
      
      /*
      //逐步推导
      //第1轮：{101, 34, 119, 1};  => {34, 101, 119, 1}
      //{101, 34, 119, 1}; => {101,101,119,1}
      //定义待插入的数
            //定义待插入数 arr[1]（34）
      int insertVal = arr[1];
      //定义待插入数的索引，即arr[1]前面这个数的下标
      int insertIndex = 1 - 1;

      //{101, 34, 119, 1} =》 {101, 101, 119, 1}
      //insertVal 保存着34这个数据
      while (insertIndex >= 0 && insertVal < arr[insertIndex]) {
         arr[insertIndex + 1] = arr[insertIndex];
         insertIndex--; //arr[1]和前面的数相比较
      }

      arr[insertIndex + 1] = insertVal;

      System.out.println("第1轮插入：");
      System.out.println(Arrays.toString(arr));
      
      //第2轮
      insertVal = arr[2];
      insertIndex = 2 - 1; 
      
      while(insertIndex >= 0 && insertVal < arr[insertIndex] ) {
         arr[insertIndex + 1] = arr[insertIndex];// arr[insertIndex]
         insertIndex--;
      }
      
      arr[insertIndex + 1] = insertVal;
      System.out.println("第2轮插入");
      System.out.println(Arrays.toString(arr));
      */
   }
}
```

## 5、希尔排序（Shell Sorting）

### 1、介绍

1、简单插入排序存在的问题

数组 arr = {2,3,4,5,6,1} 这时需要插入的数 1(最小), 这样的过程是：

```
{2,3,4,5,6,6}
{2,3,4,5,5,6}
{2,3,4,4,5,6}
{2,3,3,4,5,6}
{2,2,3,4,5,6}
{1,2,3,4,5,6}
```

当需要插入的数是较小的数时，后移的次数明显增多，对效率有影响。

2、概述

希尔排序也是一种插入排序，它是简单插入排序经过改进之后的一个更高效的版本，也称为缩小增量排序。

3、思想

希尔排序是把记录按下标的一定增量分组，对每组使用直接插入排序算法排序；随着增量逐渐减少，每组包含的关键词越来越多，当增量减至1时，整个文件恰被分成一组，算法便终止。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200927161131943.png" alt="image-20200927161131943" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200927161154124.png" alt="image-20200927161154124" style="zoom:80%;" />

### 2、代码

```java
package com.xiaolun.sort;

import java.util.Arrays;

public class ShellSort {
   public static void main(String[] args) {
      //测试1：从小到大排序
      int[] arr = {8, 9, 1, 7, 2, 3, 5, 4, 6, 0};
      //希尔排序的第一轮，将10个数据分成5组
//    int temp = 0;
//    for (int i = 5; i < arr.length; i++) {
//       // 遍历各组中的所有元素（共5组，每组有2个元素），步长为5
//       for (int j = i - 5; j >= 0; j -= 5) {
//          if (arr[j] > arr[j + 5]) {
//             temp = arr[j];
//             arr[j] = arr[j + 5];
//             arr[j + 5] = temp;
//          }
//       }
//    }
//    System.out.println("希尔排序1轮后=" + Arrays.toString(arr));
//
//    for (int i = 2; i < arr.length; i++) {
//       for (int j = i - 2; j >= 0; j -= 2) {
//          if (arr[j] > arr[j + 2]) {
//             temp = arr[j];
//             arr[j] = arr[j + 2];
//             arr[j + 2] = temp;
//          }
//       }
//    }

      //测试2:运行时间，比插入排序时间满了
//    int[] arr = new int[8000000];
//    for (int i = 0; i < 8000000; i++) {
//       arr[i] = (int) (Math.random() * 8000000);
//    }
//
//    Date data1 = new Date();
//    SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
//    String date1Str = simpleDateFormat.format(data1);
//    System.out.println("排序前时间=" + date1Str);
//
//    //shellSort(arr); //交换方式
//    shellSort2(arr);//移位方式
//
//    Date data2 = new Date();
//    String date2Str = simpleDateFormat.format(data2);
//    System.out.println("排序后时间=" + date2Str);
   }

   //对有序序列在插入时采用交换法
   public static void shellSort(int[] arr) {
      int temp = 0;
      int count = 0;
      for (int gap = arr.length / 2; gap > 0; gap /= 2) {
         for (int i = gap; i < arr.length; i++) {
            /**
             * 遍历各组中所有元素（共gap组，每组个元素），步长gap
             * {1（交换）, 5, 3(交换), 6, 0（交换）, 8, 9, 4, 7, 2}
             * j -= gap 作用是将3，0交换后，再将1，0进行比较，然后将
             * 0放到前端,如下：
             * {0(), 5, 1(), 6, 3(), 8, 9, 4, 7, 2}
             */
            for (int j = i - gap; j >= 0; j -= gap) {
               // 如果当前元素大于加上步长后的那个元素，进行交换
               if (arr[j] > arr[j + gap]) {
                  //发现一个交换一个，耗费时间很长
                  temp = arr[j];
                  arr[j] = arr[j + gap];
                  arr[j + gap] = temp;
               }
            }
         }
      }
      
      /*
       //希尔排序的第一轮，将10个数据分成5组
      for (int i = 5; i < arr.length; i++) {
         // 遍历各组中的所有元素（共5组，每组有2个元素），步长为5
         for (int j = i - 5; j >= 0; j -= 5) {
            if (arr[j] > arr[j + 5]) {
               temp = arr[j];
               arr[j] = arr[j + 5];
               arr[j + 5] = temp;
            }
         }
      }
      System.out.println("希尔排序1轮后=" + Arrays.toString(arr));//
      
      // 希尔排序第2轮
      // 将10个数据分成5/2 = 2组
      for (int i = 2; i < arr.length; i++) {
         for (int j = i - 2; j >= 0; j -= 2) {
            if (arr[j] > arr[j + 2]) {
               temp = arr[j];
               arr[j] = arr[j + 2];
               arr[j + 2] = temp;
            }
         }
      }
      System.out.println("希尔排序2轮后=" + Arrays.toString(arr));//
      */
   }

   //对交换式的希尔排序进行优化 -》移位法，运行时间很快
   public static void shellSort2(int[] arr) {
      // 增量gap，并逐步缩小增量
      for (int gap = arr.length / 2; gap > 0; gap /= 2) {
         // 从第gap个元素，逐个对其所在的组进行直接插入排序
         for (int i = gap; i < arr.length; i++) {
            int j = i; //待插入的下标保存起来
            int temp = arr[j]; //记录要插入的数
            if (arr[j] < arr[j - gap]) {
               while (j - gap >= 0 && temp < arr[j - gap]) {
                  //移动
                  arr[j] = arr[j - gap];
                  j -= gap;
               }
               //当退出while后，就给temp找到插入的位置
               arr[j] = temp;
            }
         }
      }
   }
}
```

## 6、快速排序（Quicksort）

### 1、介绍

1、概述

快速排序是对冒泡排序的一种改进。基本思想是：通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，以此达到整个数据变成有序序列。

2、示意图

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200927174804152.png" alt="image-20200927174804152" style="zoom:67%;" />

3、思路分析

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200927174839000.png" alt="image-20200927174839000" style="zoom:67%;" />

### 2、代码

```java
package com.xiaolun.sort;

import java.text.SimpleDateFormat;
import java.util.Arrays;
import java.util.Date;

public class QuickSort {
   public static void main(String[] args) {
      //从小到大排序 6, 9, 2, 4, 5, 1, 8, 7
//    int[] arr = {-9,78,0,23,-567,70};
      int[] arr = {2,1};

//    int[] arr = new int[8000000];
//    for (int i = 0; i < 8000000; i++) {
//       arr[i] = (int) (Math.random() * 8000000);
//    }
//
//    Date data1 = new Date();
//    SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
//    String date1Str = simpleDateFormat.format(data1);
//    System.out.println("排序前时间=" + date1Str);
//
      quickSort(arr, 0, arr.length - 1);
      System.out.println(Arrays.toString(arr));
//
//    Date data2 = new Date();
//    String date2Str = simpleDateFormat.format(data2);
//    System.out.println("排序后时间=" + date2Str);
   }

   public static void quickSort(int[] arr, int left, int right) {
      int l = left; //左下标
      int r = right; //右下标
      //pivot 中轴值
      int pivot = arr[(left + right) / 2];
      int temp = 0; //临时变量，作为交换时使用
      //while循环的目的就是让比pivot值小的放到左边，大的放到右边
      while (l < r) {
         //在pivot左边一直找，找到大于等于pivot值，才退出
         while (arr[l] < pivot) {
            l += 1;
         }
         //在pivot右边一直找，找到小于等于pivot值，才退出
         while (arr[r] > pivot) {
            r -= 1;
         }
         /**
          * 如果 1 >= r ,说明pivot左右两边的值，已经按照左边全部是
          * 小于等于pivot值，右边全部是大于等于pivot值
          */
         if (l >= r) {
            break;
         }

         //交换
         temp = arr[l];
         arr[l] = arr[r];
         arr[r] = temp;

         //如果交换完，发现arr[l] == pivot值相等，r--，前移
         if (arr[l] == pivot) {
            r -= 1;
         }
         //如果交换完后，发现arr[r] == pivot值相等， l++后移
         if (arr[r] == pivot) {
            l += 1;
         }
      }

      // 如果 l == r, 必须l++, r--,将中轴值去掉，否则出现栈溢出。
      if (l == r) {
         l += 1;
         r -= 1;
      }
      //向左递归 left第一次递归时为0
      if (left < r) {
         quickSort(arr, left, r);
      }
      //向右递归 第一次递归时为 length - 1
      if (right > l) {
         quickSort(arr, l, right);
      }
   }
}
```

## 7、归并排序（merge sort）

### 1、介绍

1、概述

归并排序是利用归并的思想实现的排序方法，该算法采用经典的分治（divide-and-conquer）策略（分治法将问题分(divide)成一些小的问题然后递归求解，而治(conquer)的阶段则将分的阶段得到的各答案"修补"在一起，即分而治之)。

2、基本思路

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200928112401494.png" alt="image-20200928112401494" style="zoom:80%;" />

3、示意图

再来看看治阶段，我们需要将两个已经有序的子序列合并成一个有序序列，比如上图中的最后一次合并，要将[4,5,7,8]和[1,2,3,6]两个已经有序的子序列，合并为最终序列[1,2,3,4,5,6,7,8]，来看下实现步骤：

![image-20200928112453627](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200928112453627.png)

### 2、代码

```java
package com.xiaolun.sort;

import java.util.Arrays;

//归并排序
public class MergetSort {
   public static void main(String[] args) {
      //测试1：从小到大排序
//    int arr[] = {8, 4, 5, 7, 1, 3, 6, 2};
      int arr[] = {8, 4, 5, 7};
      //归并排序，需要额外的时间开销
      int temp[] = new int[arr.length];
      mergeSort(arr, 0, arr.length - 1, temp);
      System.out.println(Arrays.toString(arr));

      //测试2：运行时间测试
//    int[] arr = new int[8000000];
//    for (int i = 0; i < 8000000; i++) {
//       arr[i] = (int) (Math.random() * 8000000);
//    }
//
//    Date data1 = new Date();
//    SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
//    String date1Str = simpleDateFormat.format(data1);
//    System.out.println("排序前时间=" + date1Str);
//
//    int temp[] = new int[arr.length]; //?鲢????????????????
//    mergeSort(arr, 0, arr.length - 1, temp);
//
//    Date data2 = new Date();
//    String date2Str = simpleDateFormat.format(data2);
//    System.out.println("排序后时间=" + date2Str);
   }

   /**
    * 分+合方法
    * 1、首先分解到的是 8 4 ，然后是 5 7
    * 2、分解到8 4 之后，会在 if (left < right)中不满足条件而跳出循环，即此时已经弹出栈
    * 然后程序会继续往下运行 mergeSort(arr, mid + 1, right, temp);
    * @param arr
    * @param left
    * @param right
    * @param temp
    */
   public static void mergeSort(int[] arr, int left, int right, int[] temp) {
      if (left < right) {
         int mid = (left + right) / 2; //中间索引
         //向左递归进行分解
         mergeSort(arr, left, mid, temp);
         //向右递归进行分解,分解到最后 8 4 5 7 1 3 6 2
         mergeSort(arr, mid + 1, right, temp);
         //合并
         merge(arr, left, mid, right, temp);
      }
   }

   /**
    * 合并的方法
    *
    * @param arr   排序的原始数组
    * @param left  左边右序序列的初始索引
    * @param mid   中间索引
    * @param right 右边索引
    * @param temp  做中转的数组
    */
   public static void merge(int[] arr, int left, int mid, int right, int[] temp) {
      int i = left; // 初始化i,左边有序序列的初始索引
      int j = mid + 1; //初始化j,右边有序序列的初始索引
      int t = 0; // 指向temp数组的当前索引

      //(一)
      //先把左右两边（有序）的数据按照规则填充到temp数组
      //直到左右两边的有序序列，有一边处理完毕为止
      while (i <= mid && j <= right) {//继续
         /**
          * 如果左边的有序序列的当前元素，小于等于右边序列的当前元素
          * 即将左边的当前元素，填充到temp数组中
          * 然后 t++,i++
          */
         if (arr[i] <= arr[j]) {
            temp[t] = arr[i];
            t += 1;
            i += 1;
         } else { //反之，将右边的有序序列的当前元素，填充到temp数组
            temp[t] = arr[j];
            t += 1;
            j += 1;
         }
      }

      //(二)
      //把有剩余数据一边的数据全部填充到temp
      while (i <= mid) { //左边的有序序列还有剩余的元素，就全部填充到temp
         temp[t] = arr[i];
         t += 1;
         i += 1;
      }

      while (j <= right) { //右边的有序序列还有剩余的元素，就全部填充到temp
         temp[t] = arr[j];
         t += 1;
         j += 1;
      }

      //(三)
      /**
       * 将temp数组的元素拷贝到arr。注意：并不是每次拷贝所有
       * 第一次合并 tempLeft = 0 , right = 1
       * 第二次合并 tempLeft = 2  right = 3
       * 第三次合并 tempLeft=0    right = 3(将上面两个合并的再拷贝过去)
       * ...
       * 最后一次合并 tempLeft = 0  right = 7
       */
      t = 0;
      int tempLeft = left;
      while (tempLeft <= right) {
         arr[tempLeft] = temp[t];
         t += 1;
         tempLeft += 1;
      }
   }
}
```

## 8、基数排序（radix sort）

### 1、介绍

1、概述

（1）基数排序）属于“分配式排序”（distribution sort），又称“桶子法”（bucket sort）或bin sort，顾名思义，它是通过键值的各个位的值，将要排序的元素分配至某些“桶”中，达到排序的作用。

（2）基数排序法是属于稳定性的排序，基数排序法的是效率高的稳定性排序法。

（3）有负数的数组，我们不用基数排序来进行排序**,** 如果要支持负数

```http
https://code.i-harness.com/zh-CN/q/e98fa9 
```

2、基本思想

将所有待比较数值统一为同样的数位长度，数位较短的数前面补零。然后，从最低位开始，依次进行一次排序。这样从最低位排序一直到最高位排序完成以后, 数列就变成一个有序序列。

3、示意图

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200928153156758.png" alt="image-20200928153156758" style="zoom: 50%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200928153222766.png" alt="image-20200928153222766" style="zoom:50%;" />

### 2、代码

```java
package com.xiaolun.sort;

import java.util.Arrays;

//基数排序
public class RadixSort {
   public static void main(String[] args) {
      int arr[] = {53, 3, 542, 748, 14, 214};
      radixSort(arr);
      System.out.println(Arrays.toString(arr));
   }

   public static void radixSort(int[] arr) {
      //1.得到数组中最大的数的位数
      int max = arr[0]; //假设第一个数就是最大数
      for (int i = 1; i < arr.length; i++) {
         if (arr[i] > max) {
            max = arr[i];
         }
      }
      //得到最大数是几位数
      int maxLength = (max + "").length();

      //定义一个二维数组，表示10个桶，每一个桶就是一个一维数组
      //1. 二维数组包含10个一维数组
      //2. 为了防止在放入数的时候，数据溢出，则每个一维数组（桶），大小定义为arr.length
      //3. 基数排序使用空间换时间的经典算法
      int[][] bucket = new int[10][arr.length];

      /**
       * 为了记录每个桶中，实际放了多少个数据，我们定义一个一维数组
       * 来记录每个桶的每次放入数据个数
       * 比如：
       * bucketElementCounts[0]记录的就是bucket[0]桶放入数据的个数
       */
      int[] bucketElementCounts = new int[10];

      for (int i = 0, n = 1; i < maxLength; i++, n *= 10) {
         //（针对每个元素的对应位进行排序处理），第一个是个位，依次是十位，百位
         for (int j = 0; j < arr.length; j++) {
            //取出每个元素对应位的值
            int digitOfElement = arr[j] / n % 10;
            //放入对应的桶中
            bucket[digitOfElement][bucketElementCounts[digitOfElement]] = arr[j];
            bucketElementCounts[digitOfElement]++;
         }
         //按照桶的顺序（一维数组的下标依次取出数据，放入原来数组）
         int index = 0;
         //遍历每一桶，并将桶中的数据放入到原数组
         for (int k = 0; k < bucketElementCounts.length; k++) {
            //如果桶中有数据，我们才放入原数组
            if (bucketElementCounts[k] != 0) {
               //循环该桶即第K个桶（即第k个一维数组），放入
               for (int l = 0; l < bucketElementCounts[k]; l++) {
                  //将元素取出放入到arr
                  arr[index++] = bucket[k][l];
               }
            }
            //第i+1轮处理后，需要将每个bucketElementCounts[k] = 0 ！！
            bucketElementCounts[k] = 0;
         }
      }
      
      /*
      //第1轮（针对每个元素的个位进行排序处理）
      for(int j = 0; j < arr.length; j++) {
         //取出每个元素的个位的值
         int digitOfElement = arr[j] / 1 % 10;
         //放入对应的桶中
         bucket[digitOfElement][bucketElementCounts[digitOfElement]] = arr[j];
         bucketElementCounts[digitOfElement]++;
      }
      //按照桶的顺序（一维数组的下标依次取出数据，放入原来数组）
      int index = 0;
      for(int k = 0; k < bucketElementCounts.length; k++) {
         if(bucketElementCounts[k] != 0) {
            for(int l = 0; l < bucketElementCounts[k]; l++) {
               arr[index++] = bucket[k][l];
            }
         }
         bucketElementCounts[k] = 0;
      }
      System.out.println("第一轮处理后 arr =" + Arrays.toString(arr));

     //第2轮（针对每个元素的十位进行排序处理）
      for (int j = 0; j < arr.length; j++) {
         int digitOfElement = arr[j] / 10  % 10; //748 / 10 => 74 % 10 => 4
         bucket[digitOfElement][bucketElementCounts[digitOfElement]] = arr[j];
         bucketElementCounts[digitOfElement]++;
      }
      index = 0;
      for (int k = 0; k < bucketElementCounts.length; k++) {
         if (bucketElementCounts[k] != 0) {
            for (int l = 0; l < bucketElementCounts[k]; l++) {
               arr[index++] = bucket[k][l];
            }
         }
         bucketElementCounts[k] = 0;
      }
      System.out.println("第一轮处理后 arr =" + Arrays.toString(arr));
      */
   }
}
```

## 9、总结

1、常用排序算法对比

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200928153424779.png" alt="image-20200928153424779" style="zoom:67%;" />

```
稳定：如果a原本在b前面，而a=b，排序之后a仍然在b的前面；
不稳定：如果a原本在b的前面，而a=b，排序之后a可能会出现在b的后面；
内排序：所有排序操作都在内存中完成；
外排序：由于数据太大，因此把数据放在磁盘中，而排序通过磁盘和内存的数据传输才能进行；

时间复杂度： 一个算法执行所耗费的时间。
空间复杂度：运行完一个程序所需内存的大小。
n: 数据规模
k: “桶”的个数
In-place:    不占用额外内存
Out-place: 占用额外内存
```





























































































