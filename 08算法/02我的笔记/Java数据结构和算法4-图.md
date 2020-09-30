# 1、图

## 1、介绍

1、概述

当我们需要表示多对多的关系时， 这里我们就用到了图。

图是一种数据结构数据结构，其中结点可以具有零个或多个相邻元素。两个结点之间的连接称为边。 结点也可以称为点。

2、图的基本概念

```
顶点(vertex)
边(edge)
```

无向图： 顶点之间的连接没有方向，比如A-B，即可以是 A-> B 也可以 B->A 。

路径：: 比如从 D -> C 的路径有

D->B->C或者D->A->B->C。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200930154929108.png" alt="image-20200930154929108" style="zoom:67%;" />

有向图：顶点之间的连接有方向，比如A-B，只能是 A-> B 不能是 B->A 。

带权图：这种边带权值的图也叫网。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200930155048430.png" alt="image-20200930155048430" style="zoom:67%;" />

3、图的基本表示

图的表示方式有两种：二维数组表示（邻接矩阵）；链表表示（邻接表）。

（1）邻接矩阵

邻接矩阵是表示图形中顶点之间相邻关系的矩阵，对于n个顶点的图而言，矩阵是的row和col表示的是1....n个点。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200930155130164.png" alt="image-20200930155130164" style="zoom:80%;" />

（2）邻接表

邻接矩阵需要为每个顶点都分配n个边的空间，其实有很多边都是不存在,会造成空间的一定损失。

邻接表的实现只关心存在的边，不关心不存在的边。因此没有空间浪费，邻接表由数组+链表组成。

![image-20200930155209804](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200930155209804.png)

```
1、标号为0的结点的相关联的结点为 1 2 3 4
2、标号为1的结点的相关联结点为0 4，
3、标号为2的结点相关联的结点为 0 4 5
```

## 2、深度优先遍历(Depth First Search) 

### 1、介绍

（1）概述

所谓图的遍历，即是对结点的访问。一个图有那么多个结点，如何遍历这些结点，需要特定策略，一般有两种访问策略: (1)深度优先遍历 (2)广度优先遍历。

（2）基本思想

（1）深度优先遍历，从初始访问结点出发，初始访问结点可能有多个邻接结点，深度优先遍历的策略就是首先访问第一个邻接结点，然后再以这个被访问的邻接结点作为初始结点，访问它的第一个邻接结点， 可以这样理解：每次都在访问完当前结点后首先访问当前结点的第一个邻接结点。

（2）我们可以看到，这样的访问策略是优先往纵向挖掘深入，而不是对一个结点的所有邻接结点进行横向访问。显然，深度优先搜索是一个递归的过程

（3）算法步骤

```
（1）访问初始结点v，并标记结点v为已访问。
（2）查找结点v的第一个邻接结点w。
（3）若w存在，则继续执行4，如果w不存在，则回到第1步，将从v的下一个结点继续。
（4）若w未被访问，对w进行深度优先遍历递归（即把w当做另一个v，然后进行步骤123）。
（5）查找结点v的w邻接结点的下一个邻接结点，转到步骤3。
```

（4）需求

对下图进行深度优先遍历，从A开始遍历。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200930155518345.png" alt="image-20200930155518345" style="zoom:67%;" />

### 2、代码

```java
package com.xiaolun.graph;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.LinkedList;

public class Graph {
   private ArrayList<String> vertexList; // 存储顶点的集合
   private int[][] edges; //存储图对应的邻接矩阵
   private int numOfEdges; //边的个数
   //定义数组boolean[], 记录某个节点是否被访问
   private boolean[] isVisited;

   public static void main(String[] args) {
      String Vertexs[] = {"A", "B", "C", "D", "E"};

      Graph graph = new Graph(5);
      for (String vertex : Vertexs) {
         graph.insertVertex(vertex);
      }

      //A-B A-C B-C B-D B-E
      graph.insertEdge(0, 1, 1); // A-B
      graph.insertEdge(0, 2, 1);
      graph.insertEdge(1, 2, 1);
      graph.insertEdge(1, 3, 1);
      graph.insertEdge(1, 4, 1);
      
      graph.showGraph();
      System.out.println("深度优先遍历----------");
      graph.dfs(); // A->B->C->D->E
   }

   //构造器
   public Graph(int n) {
      //初始化矩阵和vertexList
      edges = new int[n][n];
      vertexList = new ArrayList<String>(n);
      numOfEdges = 0;
   }

   /**
    * 得到第一个邻接点的下标 w
    *
    * @param index 将当前节点的下标给我，我才能返回
    * @return 如果存在，就返回对应的下标，反之为 -1
    */
   public int getFirstNeighbor(int index) {
      for (int j = 0; j < vertexList.size(); j++) {
         if (edges[index][j] > 0) { //说明下一个邻接点是存在的
            return j;
         }
      }
      return -1;
   }


   /**
    * 根据v1的前一个邻接节点的下标来获取下一个邻接节点
    * 此时，v2仅仅代表被遍历过的一个数据，我们在其基础上进行遍历
    * @param v1
    * @param v2
    * @return
    */
   public int getNextNeighbor(int v1, int v2) {
      for (int j = v2 + 1; j < vertexList.size(); j++) {
         if (edges[v1][j] > 0) { //说明存在
            return j; //返回对应的下标
         }
      }
      return -1;
   }

   /**
    * 深度优先遍历算法
    *
    * @param isVisited 需要判断该节点是不是被访问，所以需要将该数组传进来
    * @param i         需要访问节点 i（第一次时，i = 0）
    */
   private void dfs(boolean[] isVisited, int i) {
      //首先我们访问该节点，输出
      System.out.print(getValueByIndex(i) + "->");
      //将该节点设置为已经访问
      isVisited[i] = true;
      //查找结点 i 的第一个邻接结点 w
      int w = getFirstNeighbor(i);
      while (w != -1) {//说明有（存在）
         //判断该节点是不是被访问过（当前假设没有被访问）
         if (!isVisited[w]) {
            dfs(isVisited, w); //递归
         }
         /**
          * 1、如果 w 结点已经被访问过的情况
          * 2、i表示正在被访问的节点，w表示找到的下一个节点
          */
         w = getNextNeighbor(i, w);
      }
   }

   //对 dfs 进行一个重载, 遍历我们所有的结点，并进行 dfs
   public void dfs() {
      isVisited = new boolean[vertexList.size()];
      //遍历所有的结点，进行 dfs[回溯]
      for (int i = 0; i < getNumOfVertex(); i++) {
         if (!isVisited[i]) {
            dfs(isVisited, i);
         }
      }
   }

   //图中常用方法
   //返回节点个数
   public int getNumOfVertex() {
      return vertexList.size();
   }

   // 显示图对应的矩阵
   public void showGraph() {
      for (int[] link : edges) {
         System.err.println(Arrays.toString(link));
      }
   }

   //得到边的个数
   public int getNumOfEdges() {
      return numOfEdges;
   }

   //返回节点i（下标）对应的数据，比如 0->"A" 1->"B" 2->"C"
   public String getValueByIndex(int i) {
      return vertexList.get(i);
   }

   //返回v1和v2的权值（默认为1）
   public int getWeight(int v1, int v2) {
      return edges[v1][v2];
   }

   //插入节点
   public void insertVertex(String vertex) {
      vertexList.add(vertex);
   }

   /**
    * 添加边（无向图）
    *
    * @param v1     表示点的下标，即使第几个顶点"A"-"B" "A"（第1个顶点）->0 "B"->1
    * @param v2     第二个顶点对应的下标，如果表示二者有关系v1,v2传入 0，1即可。
    * @param weight （0/1）矩阵里面想用什么来表示他们之间是关联的。
    */
   public void insertEdge(int v1, int v2, int weight) {
      edges[v1][v2] = weight;
      edges[v2][v1] = weight;
      numOfEdges++;
   }
}
```





























