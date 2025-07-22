---
layout: post
title: cs61b
---


### CS61b
<br>
Software Engineer:
<br>
Complexity Defined:
<br>
Complexity is anything related to the structure of a software system that makes hard to understand modify the system.
<br>

如何定义复杂性（Symptoms of Complexity）:
<br>
![截屏2023-06-16 10.24.04](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-16%2010.24.04.png)
<br>
![截屏2023-06-27 10.26.06](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-27%2010.26.06.png)
<br>
软件很少被物理条件限制，几乎是一个纯粹的创造性活动，最大的限制是对我们所构建的东西的理解。
<br>
1.代码简单并直观，消除特例。hiding complexity
<br>
2.封装到模块。modular design
<br>
The best modules are those whose interfaces are much simpler than their implementation. 
<br>
The best modules are those that provide powerful functionality yet have simple interfaces,i use term deep to describe such modules.simple interfaces do complicated thing.
<br>
!!avoid over-reliance on 'temporal decomposition';时间上的先后顺序并不等同于逻辑上的先后顺序。
<br>
!!be strategic,not tactical;
<br>
重点关注信息泄露：什么是编程中的信息泄漏？
<br>

Java中的值：

1.基本类型声明：
<br>
变量及之后的一串比特空间
<br>
应用类型：
<br>
new ：开创一块比特空间，并返回地址（64bit address）（my treasure is no.1312312312th)；
<br>
基本类型的变量，后面跟着对应的比特空间；
<br>
引用类型的变量，后面跟着64bit地址空间，all 0=null；
<br>
裸递归
<br>
单向链表-sentinel
<br>
双向链表-two sentinel。 circle list
<br>
array  -- size*2. loiter
<br>
inheritance:
<br>
hypernyms 上位词
<br>
hyponym 下位词
<br>
Dynamic method:
<br>
Step1:compile time determine **signature s** of the method to be called;
<br>
​	s is decided using **only static types**
<br>
Step2:run time invoking **object`s signature** s;  
<br>
Managing complexity:
<br>
Hierarchical abstraction:
<br>

- ​	create layers of abstraction with clear abstraction barriers!
<br>
"Design for abstraction"(D.parnas)"
<br>
- ​	Organize program around objects
<br>
- ​	Let objects decide how things are done
<br>
- ​	Hide information others don`t need



继承对封装的破环：
<br>
Super：method1(){method2}  method2(){...}
<br>
Subclass: overrider method2(){method1}
<br>
自己的method2 调用父类的method1 父类method1 又调用自己的method2
<br>
原本安全的封装变得不安全了
<br>
Higher Order Functions:高阶函数，以其他函数为数据的函数
<br>
java没有函数类型，不允许存在指针指向函数
<br>
传统方式：通过接口来实现
<br>
![截屏2023-06-03 11.30.01](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-03%2011.30.01.png)
<br>
q1：子类与父类有相同名称的变量？
<br>
q2：子类与父类拥有相同 signature 的方法？静态方法没有overrid
<br>
two practices to solve -- hiding （？）（不提倡)
<br>
comparable-->Natural Order
<br>
other order --> comparator
<br>
==:compare bits。for references means address
<br>
一些好的demo：



```java
public static IntList of(int ...argList) {
        if (argList.length == 0)
            return null;
        int[] restList = new int[argList.length - 1];
        System.arraycopy(argList, 1, restList, 0, argList.length - 1);
        return new IntList(argList[0], IntList.of(restList));
    }
```
<br>
```java
public static boolean squarePrimes(IntList lst) {
        // Base Case: we have reached the end of the list
        if (lst == null) {
            return false;
        }

        boolean currElemIsPrime = Primes.isPrime(lst.first);

        if (currElemIsPrime) {
            lst.first *= lst.first;
        }

        return currElemIsPrime || squarePrimes(lst.rest);  // java 一个为真就不会再去计算后面的表达式了
    }
```
<br>
Section2:
<br>
An engineer will do for a dime what any fool will do for a dollar;
<br>
Efficient :
<br>
1.Programming cost(help method!)
<br>
2.Excution cost
<br>
Measure Computational Cost:
<br>
Operation times --> cost model --> simplification-->order of growth
<br>
Formalize:Big-Theta
<br>
![截屏2023-06-09 13.29.28](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-09%2013.29.28.png)
<br>
Big O: ... less than or equal O(?);
<br>
![截屏2023-06-09 13.38.31](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-09%2013.38.31.png)
<br>
Find strategies:
<br>
1.Find exact sum
<br>
2.Write out examples
<br>
3.Draw pictures
<br>
Common Big O:
<br>
1. Recursion :2的n次方
2. Binary Search:log以2为底n的对数～logN
3. Merge Sort:merge(比较两个数组中最小的一个，按顺序将两个数组进行合并)，将数组直接从1开始一直合并NlogN
4. 1,2,2,4,4,4,4,8,8,8,8,8,8,8,8,16.  N(1:1;2:1+2;3:1+2;4:1+2+4;5:1+2+4)



(自适应排序)
<br>
ADT: Abstract Data Types. Defined only by its operations not by implementation;
<br>
Stack:filo.  堆内存：系统分配，比较小；  link & array
<br>
GrabBag:(insert, remove,sample,size);  array
<br>

### 1.Disjoint Sets
<br>
Inverse acrmen function;
<br>
不相交集合/并查集 的核心功能：（connect）（isConnected）
<br>
Implement：
<br>

0. 追踪每一个点及边
1. ListOfSetsDS
2. QuickFindDS:数组存储id
3. QuickUnionDS:数组存储parentid
   1. WeightedQuickUnionDS:新增权重（数量）（1.并行数组维护，2.将root标识为数量的负数）来确定谁合并到谁。（以数量作为权重与树的高度作为权重差异不大）
   2. WeightedQuickUnionWithPathCompressionDS:每一次isConnected 将修改链路上的元素的parentid





### 2.Binary Search Trees
<br>
(Skip List?)
<br>
Define:
<br>
Tree:

1. A set of nodes;
2. A set of edges that connect those nodes(only one path exists between two nodes);



Rooted Tree:

1. one node is root;
2. Every node have only one parent except root;
3. no child node is leaf;



Rooted Binary Trees:

1. every node has either 0,1 or 2 children;



Binary Search Trees:

1. Rooted Binary Trees;
2. Left means less,Right means greater;
3. can`t have duplicate key;



Operations:

1. search
2. insert
3. delete



![截屏2023-06-10 12.17.19](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-10%2012.17.19.png)
<br>

### 3.B-Tree
<br>
Worst case BST hight is O(N);
<br>
Height,Depth,Performance:
<br>
Depth:distance of node to root;
<br>
Height: deepest leaf; worst runtime to find a node;
<br>
Average depth:(node-num*depth)/sum(node);
<br>
Good news:random operation BST ~ O(log(N))
<br>
Bad news:neraly can`t operate random; data comes in over time;`



<br>
B-tree:avoiding imbalance;
<br>
1.hight no change; -- leaf become leaves;
<br>
2.leaf nodes avoid too juice;-- limit ,over limit give one to parent(left middle)
<br>
So Wired:
<br>
![截屏2023-06-10 15.02.16](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-10%2015.02.16.png)
<br>
3.when no-leaf become too full:
<br>
![截屏2023-06-10 15.10.21](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-10%2015.10.21.png)
<br>
B-tree also called 2-3-4 Tree/2-4 Tree:
<br>
到此处，一直添加大值，似乎会发生只有一边会出现leaves；
<br>
Ex:L=2, insert:2,3,4,5,6,1,7 与 1,2,3,4,5,6,7 得到的是不同的树
<br>
B-Trees most popular in two context:
<br>
1.small L
<br>
2.very large L
<br>
Why B-Tree is always bushy:
<br>
B-Tree`s invariants:

1. all leaves must be same distance from source;
2. A non-leaf node with K items must have exactly K+1 children;



### 4.Red Black Tree
<br>
Catalan(N)?
<br>
Tree Rotation:
<br>
Left-Leaning Red Black Binary Search Tree (LLRB):
<br>
![截屏2023-06-10 17.36.29](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-10%2017.36.29.png)
<br>
LLRB:Max Higeht: H+H+1
<br>
![截屏2023-06-10 17.42.14](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-10%2017.42.14.png)
<br>
LLRB与2-3Tree 之间存在一一对应;
<br>
Red-Black Tree Insert:
<br>
![截屏2023-06-11 10.34.19](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-11%2010.34.19.png)
<br>
### 5.Hash:Data Index
Using data as an index;
<br>
![截屏2023-06-11 11.45.30](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-11%2011.45.30.png)
<br>
![截屏2023-06-11 12.06.46](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-11%2012.06.46.png)
<br>
core question is :compute & collision
<br>
Buckets :M
<br>
Items:N
<br>
Length of list=N/M. also load factor
<br>
Operation times:1~1/M*N  =O(N)
<br>
How to guarantee O(1)?  N/M~1 :With increase of N  also increase M;
<br>
Resize Cost: O(N);
<br>
process negative:Math.floorMod(-1,4);because -1%4=-1 in java;
<br>
![截屏2023-06-11 13.20.27](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-11%2013.20.27.png)
<br>
Use a prime base:乘以一个基数（质数最好)以获取更好的随机性。
<br>

### 6.Priority Queue Interface & Heap
<br>
Queue only trace smallest element;
<br>
用法之一：节约内存，需要最大的5个，永远只需要5个位置，存放对象地址即可。
<br>
Min-Heap:
<br>
Children >=father
<br>
Complete 
<br>
新增/删除 都基于这两点，新增：新增到末尾，再移动；删除：先删除root，末尾那一个移过去，再移动到正确位置。
<br>
实现树的几种方式：
<br>
Approach1:key+pointer，key+pointer[]，key+subpointer+brotherpointer;
<br>
Approach2:key[]+parents[]. Like disjoinSets;
<br>
Approach3:assume full tree, just key[],ignore parent[]；parent=(n-1)/2;left=?;right=?
<br>
### 7.Search Data Structures
<br>
Search information form data;
<br>
List,Map,Set,PQ,Disjoint Sets 
<br>
![截屏2023-06-13 12.23.58](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-13%2012.23.58.png)
<br>

### 8.Graph
<br>
Tree are more general concept
<br>
Tree traversal:
<br>
Level Order: 
<br>

1. Top-bottom;left -right



Depth Frist Traversals:

1. 3types:Preorder,Inorder,Postorder
2. Traverse deep nodes before shallow ones
3. Traversing a node is different than visit



Preorder:visit a node, then traverse childre
<br>
Inorder:traverse left, visit ,traverse right
<br>
...
<br>
![截屏2023-06-13 14.32.13](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-13%2014.32.13.png)
<br>
打印目录：前序；统计大小：后序；
<br>
point + relation
<br>
Tree with strict hierarchical: only one path between any two nodes;
<br>
Graph with no hierarchical:one or more edges, each of which connects two nodes;
<br>
Simple graph:
![截屏2023-06-13 14.48.36](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-13%2014.48.36.png)
<br>
![截屏2023-06-13 14.59.03](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-13%2014.59.03.png)
<br>
Often solve problem about graph with traversal;
<br>
s-t Connectivity:depth frist traversal;
<br>
![截屏2023-06-13 16.15.34](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-13%2016.15.34.png)
<br>
要实现广度优先/深度优先算法，需要构建图，图api的不同，决定了最终的具体不同实现。
<br>
![截屏2023-06-13 16.30.40](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-13%2016.30.40.png)
<br>
underling implement：
<br>
二维数组？
<br>
adjacency list：
<br>
![截屏2023-06-13 16.51.29](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-13%2016.51.29.png)
<br>
DFS&BFS  exploring graph;
<br>
Shortest Path:
<br>
Djstra
<br>
结果永远是一颗树，无向也是如此。
<br>
优先级队列+释放
<br>

### 9.Minimum spaning trees
<br>
如何判断一个图是否存在环？
<br>
1.DFS，排除来的点，只要找到之前已经便利过的点就存在环。
<br>
2.WeightedQuickUnionUF object
<br>
![截屏2023-06-14 22.48.48](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-14%2022.48.48.png)
<br>
Minimum spanning tree is spanning tree of minimum total weight
<br>
only in **undirected** graph as subgraph:
<br>
1. is connected
2. is acyclic
3. includes all of the vertices



1&2 determine it is tree,3 makes is spanning
<br>
是否存在某个顶点，其最短路径树，恰好是该图的最小生成树？--no
<br>
最短路径树，依赖于起始节点，而最小生成树是图的属性。
<br>

<br>
计算最小生成树：
<br>
Cut Property
<br>
最小交叉边，一定是最小生成树的一部分。（边如果不是唯一的，最小生成树也可能不是唯一的）
<br>
 Prim`s  Algorithm 有效具体实现：

<br>
类似dj，dj关注的是到源最短的路径，prim关心树的总权重，确定保留那一条路径的偏好不同，其余基本一致。
<br>
优先级队列，移除节点时，relax到非已确定点的边，决定保留那一条边。
<br>
Kruskal`s Algorithm:
<br>
边排序，从小到大，依次添加边直到v-1（避免环）；
<br>
优先级队列
<br>
不相交集：检测环
<br>
每次添加非环的边，可以理解为两个割中取最短。
<br>
![截屏2023-06-15 10.51.10](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-15%2010.51.10.png)
<br>

### 10.Range Searching and Multi-Dimensional Data
<br>
需求：最小的一个/最大的一个/范围（3～20）的数据
<br>
nearest:Tree-search
<br>
Prune: cut off tree;
<br>
如何存储多维的数据？
<br>
2D:
<br>
QuadTree:
<br>
Every node has 4 children:northwest,northeast,southwwest,southeast
<br>
Insert order decide final QuadTree;
<br>
K-D:
<br>
层级1-x,2-y,3-x,4-y  xyxyxy...
<br>
Nearset:
<br>
don\`t explore subspaces that can\`t possible have a better answer than current;
<br>
Uniform partitioning: bucket
<br>
1-D version:Hash Table;
<br>
Multi-Dimensional Date summary:
<br>
Range Finding
<br>
Nearest
<br>
implement:
<br>
Uniform Partitioning
<br>
Quadtree
<br>
K-d Tree:K-dimensional Binary Search Tree;
<br>

### 11.Tries
<br>
Short for Retrieval Tree
<br>
implement:
<br>
1.Each Node have 128Array;
<br>
2.BST
<br>
3.Hash Table



### 12.Topological Sort



一个方法：从入度为0的节点开始 运行深度优先搜索后序，多个则重启，然后反转即我们得到的。
<br>
Depth First Search: 有时，包含重启，有时不包含重启。
<br>
Directed Acyclic Graphs
<br>
DAG SPT Algorithm:Relax in Topological Order;
<br>
DAG Longest Path Algorithm:模拟最短，赋值负数。
<br>

### 13.Reduction
<br>
if any subrortine for task Q can be used to solve P ,we say P reduces to Q;
<br>
(Karp and Cook reductions)
<br>
### 14.Sort
<br>
三角性：a>b a=b a<b. 有一个为真
<br>
传递性：。。。
<br>
![截屏2023-06-25 09.49.34](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-25%2009.49.34.png)
<br>
选择最小排序  n平方--堆排序nlogn（放到堆里面再取出来）--in-plance heap sort
<br>
数组堆化？？
<br>
归并排序
<br>
插入排序(类似最小选择排序)：
<br>
可以理解为，消灭inversion。（三角形，理想O(N),worst O(N平方)）
<br>
插入排序的消耗与inversion成比例
<br>
一般需要排序的数量小于15 插入排序是最快的，arrays.sort() 使用的归并排序，数量小于15就切换为插入排序。
<br>
终极王者：quick sort
<br>
![截屏2023-06-26 13.04.37](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-26%2013.04.37.png)
<br>
选一个标记，分区，然后再对分区进行分区。
<br>
快速排序--BST sort
<br>
n平方～nlongn。受到所选择元素（主元）的位置影响，随机试验是接近nlogn。
<br>
很难理论上证明快速排序比归并排序快，但经验如此。
<br>
![截屏2023-06-29 12.30.16](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-29%2012.30.16.png)
<br>
avoid the worst case:
<br>
![截屏2023-06-29 12.32.59](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-29%2012.32.59.png)
<br>
![截屏2023-06-29 12.46.49](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-29%2012.46.49.png)
<br>
Heap sort:很少被使用。
<br>
one quick sort implement (random):
<br>
![截屏2023-06-29 13.05.05](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-29%2013.05.05.png)
<br>
Another implement (smart pivot):
<br>
![截屏2023-06-29 14.29.39](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-29%2014.29.39.png)
<br>
However  Quick Select : partition
<br>
![截屏2023-06-29 14.36.48](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-29%2014.36.48.png)
<br>
Arrays.sort
<br>
TimSort
<br>
![截屏2023-06-30 12.48.34](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-30%2012.48.34.png)
<br>
![截屏2023-06-30 12.50.06](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-06-30%2012.50.06.png)
<br>
more information of sort:
<br>
几个数学例子：
<br>
N!>N/2的N/2次方。
<br>
log(N!)>NlogN;
<br>
puppy-cat-dog，需要多少信息可以确定三个的顺序？
<br>
假设4个：一共用4!=24种可能排序，一颗完全二叉树需要多少层才能有24个叶子？log2（24）～5
<br>
决策树：log(N!)个问题，neraly：nlogn
<br>
任何基于比较的排序，需要nlogn 次比较。
<br>
radix sort
<br>
sleep sort -- genre counting sort(alphabet keys only) -- radix sort
<br>
![截屏2023-07-05 14.45.49](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-07-05%2014.45.49.png)
<br>
 对 counting sort 的扩展 推广到非alphabet范围：radix sort
<br>
LSD Radix Sort: 最低有效位排序。w*（n+r）
<br>
MSD Radix Sort:最高有效位排序。n+r 到 w*（n+r）， 分组
<br>
 归并排序vs基数排序msd:
<br>
100字符串，每个1000个字符：
<br>
msd:1000*n
<br>
merged sort：1000*nlogn
<br>
从比较的支付而言，mergesort更多，但实验下来，mergesort更快
<br>
cost model not well ， just-in-time complier （slid&lecture demo）
<br>
关掉优化 vm-option xint。merge sort slower
<br>
compare integer in radix sort，注意进制
<br>
![截屏2023-07-09 14.43.31](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-07-09%2014.43.31.png)
<br>
