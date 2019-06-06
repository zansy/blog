title: 《数据结构》6-10章
author: zansy
tags: []
categories:
  - 记忆面包系列
date: 2019-06-06 05:16:00
toc: true
---
《数据结构（C语言版）》严蔚敏编著，第6-10章重点详略
<!--more-->

## 树和二叉树
树有许多结点，每个结点拥有的子树数称为结点的**度**。

注意：<font color = 'green'>树的度是树内各结点的度的最大值</font>

### 二叉树

- 二叉树的性质
  - 在二叉树的第i层上至多有<font color = 'red'>2<sup>i-1</sup></font>个结点
  - 深度为k的二叉树至多有<font color = 'red'>2<sup>k</sup>-1</font>个结点
  - 任何一棵二叉树，如果其终端结点数为n<sub>0</sub>，度为2的结点数为n<sub>2</sub>，则<font color = 'red'>n<sub>0</sub> = n<sub>2</sub>+1</font>

- **满二叉树**：深度为k且有2<sup>k</sup>-1个结点的二叉树
- **完全二叉树**：每个结点都与满二叉树中结点对应的二叉树（满二叉树的不满形态）
- 完全二叉树的重要特性

  1. 具有n个结点的完全二叉树的深度为<font color = 'red'>log<sub>2</sub>n+1</font>
  2. 结点为i的，双亲结点为i/2；2i大于结点总数的无左孩子，否则左孩子结点为2i；同理，右孩子结点为2i+1

#### 二叉树存储结构
- 顺序存储结构：

类别 | 顺序存储结构
:-: | :-:
完全二叉树 | [1,2,3,4,5,6]
一般二叉树 | [1,2,0,0,0,6]

- 链式存储结构：

不同形式 |  | 结点结构|  | 
:-: | :-: | :-: | :-: | :-: 
两个指针域 | lchild | data | rchild
三个指针域 | lchild | data |parent|rchild

### 遍历二叉树

![二叉树](/images/image-20190606164208579.png)

- 如图所示二叉树：
  - 先序遍历（波兰式：先根=>左子树=>右子树）的先序序列为：<font color = 'red'>-</font>+a*b-cd/ef
  - 中序遍历（左子树=>中根=>右子树）的中序序列为：a+b*c-d<font color = 'red'>-</font>e/f(在根两端的结点肯定在不同左右子树上)
  - 后序遍历（逆波兰式：左子树=>右子树=>后根）的后序序列为：abcd-*+ef/<font color = 'red'>-</font> (确定最末是根)
- 任意两个序列即可确定一棵二叉树

### 树和森林

#### 树的存储结构

- 双亲表示法：一组连续空间存储树的结点，同时在每个结点中附设一个指示器只是其双亲结点在链表中的位置。

![树的双亲表示法示例](/images/image-20190606110340608.png)

- 孩子表示法：
  - 把每个结点的孩子结点排列起来，看成是一个线性表，且以单链表作存储结构。
  - 树的每个结点可能有多棵子树，则可用**多重链表**，即每个结点有多个指针域，每个指针指向一棵子树的根结点。其中结点同构的表示方法，因为树中有很多结点的度小于d，链表中会有很多空链域造成浪费。有n个结点度为k的树中必有<u>n(k-1)+1</u>个空链域。

不同|结点格式|备注
:-:|:-:|:-:
多重链表中结点同构| [data, child1, child2, …,childd ] |d为树的度
结点不同构| [data, degree, child1, child2, …,childk] |degree = k，是结点的度

- 孩子兄弟表示法：又称**二叉树表示法/二叉链表表示法**，即以二叉链表作树的存储结构。链表中结点的两个链域分别指向该结点的第一个孩子结点和下一个兄弟结点。

```C
typedef struct CSNode{
  ElemType data;
  struct CSNode *firstchild, *nextsibling;
}CSNode, *CSTree
```

#### 森林与二叉树的转换

酌情填坑

### 哈夫曼树

- 有n个权值[w1, w2, w3,…,wn]，构造一棵有n个叶子结点的二叉树，每个叶子结点带权为wi，其中<font color = 'red'>带权路径长度WPL最小</font>的二叉树称作**最优二叉树**或**哈夫曼树**。
- 构造哈夫曼树：
  1. 在权值集合中选择最小的两个权值构造二叉树，新二叉树的根结点的权值为左右子树上根结点的权值之和
  2. 在集合中删除这两个权值，同时将新权值加入集合中
  3. 重复以上两步，直到集合中只含一个权值。这棵树就是哈夫曼树。
- 哈夫曼编码：设计电文总长最短的二进制前缀编码即为以n种字符出现的频率作权，设计一棵哈夫曼树的问题，由此得到的二进制前缀编码就称为**哈夫曼编码**。(可理解为：<font color = 'green'>使用频率最高的字符要求在树的层次越浅，使用频率最低的字符在树最深的层次上</font>)

#### 例题

已知某系统在通信联络中只可能出现8种字符，其概率分别为0.05，0.29，0.07，0.08，0.14，0.23，0.03，0.11。试设计哈夫曼编码。

*解*：设权w = {5, 29, 7, 8, 14, 23, 3, 11}。

![哈夫曼树构造过程](/images/image-20190606124937112.png)

## 图

### 术语

- 图分为有向图和无向图
- 有(1/2)*n(n-1)条边的无向图称为**完全图**；有n(n-1)条弧的有向图称为**有向完全图**。(完全图之间各顶点连通)
- 顶点v的**度**是和v相关联的边的数目，其中有向图又分别细分为**入度**和**出度**。
- 子图：能重叠在原图上的部分图
- 无向图中如果任意两个顶点都是连通的，则称其为**连通图**，**连通分量**指的是无向图中不同的极大连通子图；类似的，在有向图中任意两个顶点互相之间都有路径，则称为**强连通图**，有向图中不同的极大强连通子图称作有向图的**强连通分量**。
- 一个连通图的**生成树**是一个极小连通子图，含有图中全部顶点，但只有足以构成一棵树的n-1条边。(可以理解成<font color = 'green'>图中不存在环，不存在冗余的边</font>)
- 如果一个图有n个顶点和小于n-1条边，则是非连通图；多于n-1条边，则一定有环。

### 图的存储结构

#### 数组表示法

邻接矩阵：以二维数组表示有n个顶点的图

```java
if(i,j连通){
  A[i][j] = 权值;//如果是无向图，需要再多加一条语句：A[j][i] = 权值;
}else{
  A[i][j] = ∞;
}
```

#### 邻接表

- 是图的一种链式存储结构，对图中每个顶点建立一个单链表，第i个单链表中的结点表示依附于顶点vi的边（有向图就是以顶点vi为尾的弧）。
- 每个结点由3个域组成：[int <font color ='red'>adjvex</font>邻接点域指示与顶点vi邻接的点,* <font color ='red'>nextarc</font>链域指针指示下一条边或弧的结点, <font color ='red'>info</font>数据域存储权值等信息]

​	![无向图的邻接表](/images/image-20190606150412964.png)

- 对于有向图来说，也有邻接表，记录顶点的出度；还有逆邻接表，记录顶点的入度。

### 图的遍历

- **深度优先搜索 (Depth First Search)**：类似于树的先根遍历。从图中一个顶点出发，访问此顶点，依次从该点未被访问的邻接点出发深度优先遍历图，直到图中所有和v有<u>路径相通</u>的顶点都被访问到。其余未被访问的顶点重复上述过程，直到图中所有顶点都被访问为止。
- **广度优先搜索(Breadth First Search)**：类似于树的按层次遍历的过程。从图中一个顶点出发，访问此顶点，依次访问该点的各个邻接点，然后分别从这些邻接点出发依次访问它们的邻接点。

### 图的连通性

- 最小生成树问题：[最小生成树小结](https://zansy.github.io/2018/01/05/MinimumSpanningTree/)
- Prim算法时间复杂度为O(n<sup>2</sup>)，n为顶点。Kruskal时间复杂度为(elog<sub>e</sub>)，其中e为边数，更适合求边稀疏的最小生成树。

### 有向无环图

- 拓扑排序：由某个集合上的一个偏序得到该集合上的一个全序，称为拓扑排序。不允许有环。(例：《软件专业的学生必须学习的课程》 先决条件)
- 进行拓扑排序（AOV Activity On Vertex 顶点表示活动的网）：
  1. 在有向图中选一个没有前驱的顶点且输出
  2. 从图中删除该顶点和它的后继弧
  3. 重复，直到所有顶点输出

- 关键路径(AOE Activity On Edge 边表示活动的网)：路径长度最长的路径，其上均是关键活动。

### 最短路径

#### 从源点到其余各顶点的最短路径

Dijkstra算法

(待补)

#### 每一对顶点之间的最短路径

Floyd算法

(待补)

## 查找
