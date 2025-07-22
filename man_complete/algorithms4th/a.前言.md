---
layout: post
title: 前言
---


# 写在前面
<br>
An engineer will do for a dime what any fool will do for a dollar.
<br>基础概念上的匮乏让我在b工作的一年半多时间总是充满不自信，一种强烈的碌碌之感，而我对数据结构产生兴趣这是在对数据库的“索引”的学习中产生的，为什么添加了索引sql就会变快，把对索引的理解仅仅停留在索引就是书的目录阶段是完全不够的，依旧无法解答为什么加了索引，sql就会变快这一疑惑？正是带着这一疑惑，我才了解到了b+树，正式接触数据结构，并感叹于它的精美与所包含的人类智慧结晶。转行的坏处在于基础概念的缺失极大的限制了人的作用发挥，好处至少在这篇文章中让我对数据结构与算法的学习没那么厌恶。
我有一种强烈的直觉即：任何好的想法与创意都将会被我这该死的执行力击溃so move faster even break things！
<br>
那么他会烂尾吗？ -- 写下第一个字的一个多月之后的20231025.
<br>
出发：
<br>
杂糅cs61b，算法第四版，数据结构与算法分析java语言描述。
<br>
我期待他的最终呈现，却又如此的不相信自己，强烈的觉得会烂尾。
<br>

20250722:准备将其整理到wiki上时，我发现了一个被我忽略了很久的事实：我已经基本忘记了这里涉及的内容，甚至在图，串相关的算法中，连基本的概念都无法理解，即使看了当时留下的文字依旧徒劳。还是得看原书。

# 基本概念

<br>

- 数据：描述客观事物的符号记录。
- 数据元素：有一定意义的基本单位，也被称为记录（人）
- 数据项：相对于数据元素，一个数据元素由若干个数据项组成（五官）
- 数据对象：性质相同的数据元素的集合。
- 数据结构：结构=数据元素之间的关系；数据结构=存在关系的数据元素的集合。数据结构是指一组数据的存储结构，算法就是操作数据的一组方法。



计算机操作的基本单位是数据
<br>
批量=集合
<br>
集合内部各元素存在关系=数据结构
<br>
-- 以上是内存中
<br>
外部：数据库
<br>
数据太多了：大数据，数据仓储
<br>
递归：函数用自己来定义：f(x)=2f(x-1)+x; 递归反复调用，直到基准情况出现。
<br>
递归的法则：
<br>
1.基准情形
<br>
2.不断推进
<br>
3.设计法则：假设所有递归调用都能运行
<br>
4.compound interest rule ：求解一个问题的同一实例时切勿在不同的递归调用中做重复性的工作
<br>
递归不是简单for循环的代替物



````java
//递归的糟糕使用 严重违反：计算任何事情不要超过一次
<br>
public static long fib(int n) {
  
  if (n<=1) {
    return 1;
  } else {
    return fin(n-1) + fib(n-2);
  }
  
}
````
<br>
java中数组是协变的，而范型不支持协变，需要使用通配符。



````java
public <anytype extends Comparable> anytype findMax(anytype[] array){
  
};

//better
public <anytype extends Comparable<? super anytype>> anytype findMax(anytype[] array){
  
};
````
<br>
协变数组与类型擦除，数组可以协变，运行时检查类型，范型则是在运行时擦除，编译时检查类型。
<br>
函数对象
<br>
## 内存开销
<br>
java中一般对象本身的开销是16字节，引用8字节。例如一个Integer对象=16+4；数组=16+4（保存长度）+4（填充字节）+8*n
<br>
## 逻辑结构与物理结构
<br>
逻辑结构：
<br>
1.集合
<br>
2.线性 1 v 1
<br>
3.树形 1 v n
<br>
4.图形 n v n
<br>
物理结构：
<br>
1.顺序存储结构
<br>
2.链式存储结构
<br>
数据类型：
<br>
c语言
<br>
1.原子类型
<br>
2.结构类型
<br>
抽象数据类型 ADT:Abstract Data Types. Defined only by its operations not by implementation;
<br>
## 算法的复杂度
<br>
算法：解决特定问题求解的步骤描述。输入、输出、有穷、确定、可行。
<br>
Big-Theta: 实际增长率 between random*big-theta
<br>
Big-O: 实际增长率 小于 big-o
<br>
时间复杂度：
<br>
最好情况时间复杂度：
<br>
最坏情况时间复杂度：
<br>
平均情况时间复杂度：每一种的概率，加权平均。
<br>
均摊时间复杂度：对有规律的复杂的那一次，均摊到前面简单的每一次上。
<br>
空间复杂度：
<br>
# 具体数据结构
<br>
## 线性表
<br>
有限元素的序列，前驱+后继。
<br>
### 数组
<br>
数组实现线性表的顺序存储结构，注意数组的长度 >=	线性表长度（实际元素个数）
<br>
连续的内存空间+相同的数据类型
<br>
数组基本寻址公式：

```
a[i]_address = base_address + i * data_type_size
```
```c
int main(int argc, char* argv[]){
    int i = 0;
    int arr[3] = {0};
    for(; i<=3; i++){
        arr[i] = 0;
        printf("hello world\n");
    }
    return 0;
}
// 无限循环 a[3] = i 
```
<br>
数组的删除，可以标记删除 jvm的标记清除垃圾回收算法。
<br>
数组的下标 表示偏移量。
<br>
### 链表
<br>
链表是一种递归的数据结构，为 null 或者指向一个「结点」的引用，该结点含有一个范型元素和一个指向另一条链表的引用。
<br>
#### 普通链表
<br>
线性表的链式存储结构。数据域+指针域=结点。
<br>
头指针=第一个结点存储的位置，有sentinel则为sentinel。
<br>
头结点=sentinel
<br>
缓存淘汰策略：
<br>
FIFO
<br>
LFU: least frequently used
<br>
LRU:least recently used
<br>
#### 静态链表
<br>
用数组描述链表，游标实现法。数组中的元素「data+point」 point存放next的下标。
<br>
array[0]=备用链表的第一个结点下标
<br>
Array[last]=头结点
<br>
所有可以存放数据的空格组成一个链表（备用链表）。
<br>
#### 双向链表
<br>
#### 循环链表
<br>
### 队列
<br>
#### API
```JAVA
/**
 * 队列的API
 * @author liaozk
 */
public interface Queue<Item> extends Iterable<Item> {

    /**
     * 添加一个元素
     * @param item
     */
    void enqueue(Item item);

    /**
     * 删除最早添加的元素
     * @return
     */
    Item dequeue();

    /**
     * 队列是否为空
     * @return
     */
    boolean isEmpty();

    /**
     * 队列中的元素个数
     * @return
     */
    int size();
}
```
<br>
队列的顺序存储结构：
<br>
普通队列
<br>
循环队列：
<br>
rear=front 可能为空，也可能是满
<br>
Solution1: 新增flag
<br>
Solution2:允许一个空的 (rear+1)%size=front 则为满
<br>
计算元素个数=(rear-front+size)%size
<br>
队列的链式存储结构：
<br>
### 栈
<br>
#### API
```JAVA
/**
 * 栈的API定义
 * @author liaozk
 */
public interface Stack<Item> extends Iterable<Item> {

    /**
     * 添加一个元素
     * @param item
     */
    void push(Item item);

    /**
     * 删除最近添加的元素
     * @return
     */
    Item pop();

    /**
     * 栈是否为空
     * @return
     */
    boolean isEmpty();

    /**
     * 栈中元素的数量
     * @return
     */
    int size();
}
```
<br>
#### 栈的顺序存储结构：数组实现
```java
/**
 * 栈的 顺序存储结构实现 数组实现
 * @author liaozk
 */
public class ResizingArrayStack<Item> implements Stack<Item> {

    //实际保存数据的数组
    private Item[] a = (Item[]) new Object[1];

    //元素数量
    private int n = 0;

    @Override
    public void push(Item item) {
        if (n == a.length) reSize(2*n);
        a[n++] = item;
    }

    @Override
    public Item pop() {
        Item result = a[--n];
        a[n] = null; //避免游离对象
        if (n >0 && n == a.length/4) reSize(n/2);
        return result;
    }

    @Override
    public boolean isEmpty() {
        return n == 0;
    }

    @Override
    public int size() {
        return n;
    }

    @Override
    public Iterator<Item> iterator() {
        return new ReverseArrayIterator();
    }
    
    private void reSize(int max) {
        Item[] temp = (Item[]) new Object[max];
        System.arraycopy(a, 0, temp, 0, n);
        a = temp;
    }

    private class ReverseArrayIterator implements Iterator<Item> {

        int i = n;

        @Override
        public boolean hasNext() {
            return i > 0;
        }

        @Override
        public Item next() {
            return a[--i];
        }
    }
}
```
<br>
为了最大限度利用数组，可以利用一个数组存储两个栈。
<br>
栈的链式存储结构：链栈
<br>
头就是栈顶
<br>
应用：
<br>
递归
<br>
后缀表示法，四则运算，计算：压数字；转换：压符号
<br>
在括号匹配中的应用
<br>
在函数调用中的应用，栈帧
<br>
## 串
<br>
零个/多个字符组成的有限序列
<br>
## 背包
<br>
### 定义：
<br>
1.可以往里面塞元素
<br>
2.不能删除元素
<br>
3.支持 foreach遍历，但不保证遍历顺序
<br>
### API
```java
/**
 * 背包的API定义
 * @author liaozk
 */
public interface Bag<Item> extends Iterable<Item> {

    /**
     * 添加一个元素
     * @param item
     */
    void add(Item item);

    /**
     * 背包是否为空
     * @return
     */
    boolean isEmpty();

    /**
     * 背包中的元素数量
     * @return
     */
    int size();
}
```
<br>


# 其他
<br>
## 日期的一种表示
```java
//x= 512y+32m+d

// year = x%512
// month = (x/32)%16
// day = x%32
```
<br>
# 例子
<br>
## 交并集
<br>
数据结构与算法本就是天然一体的，算法某种目的的具体实现手段，存储结构的不同，实现目的的手段也自然不一样。
<br>
下面来看一个例子：交并集，《算法》中的第1.5节
<br>
### 1.定义api及通用方法的实现
```java
package define;

import edu.princeton.cs.algs4.StdIn;
import edu.princeton.cs.algs4.StdOut;

/**
 * UnionFind 算法
 *
 * 优秀的算法，因为能够解决实际问题而变得重要
 * 高效算法的代码，也可以很简单
 * 理解某个实现的性能特点是一项有趣而令人满足的挑战（惭愧，1.4被我跳过了...）
 *
 * 背景：存在多个集合，输入两个整数（来自集合），判断这两个整数是否相连，即集合是否包含，若没有则提供方法将两个集合连接
 * api:
 * 1. void union(int p, int q):将pq连接起来
 * 2. int find(int p) :返回p所在集合的标识符
 * 3. boolean connected（int p, int q)：返回 pq 是否处于同一集合
 * 4. int count()：返回集合的数量
 *
 * 数据结构的设计会直接影响到算法的实现：数组的下标代表数字，值则代表集合
 *
 * @author liaozk
 */
public abstract class UnionFind {

    protected int[] id;
    protected int count;

    public UnionFind(int n) {
        count = n;
        id = new int[n];
        for (int i = 0; i < n; i++) {
            id[i] = i;
        }
    }

    public boolean connected(int p ,int q) {
        return find(p) == find(q);
    }

    public int count() {
        return count;
    }

    public abstract int find(int p);

    public abstract void union(int p, int q);

    public static void main(String[] args) {

        int n = StdIn.readInt();
        UnionFind uf = new QucikFind(n);

        while (!StdIn.isEmpty()) {
            int p = StdIn.readInt();
            int q = StdIn.readInt();
            if (uf.connected(p,q)) continue;
            uf.union(p, q);
        }
        StdOut.println(uf.count +"componets");
    }
}
```
<br>
### 2.QuickFind
```java
package define;

import edu.princeton.cs.algs4.StdOut;
import java.util.Arrays;

/**
 * quick find ：数组下标对应的值 即 连通分量（集合的标识）
 * 执行一次 find 的成本为 1
 * 执行一次 union 的成本为 n+3 ～ 2n-1
 * big o = n**2
 * 
 * @author liaozk
 */
public class QucikFind extends UnionFind{

    public QucikFind(int n) {
        super(n);

    }

    @Override
    public int find(int p) {
        return id[p];
    }

    @Override
    public void union(int p, int q) {
        int pID = find(p);
        int qID = find(q);

        if (pID == qID) return;

        for (int i = 0; i < id.length; i++) {
            if (id[i] == pID) {
                id[i] = qID;
            }
        }
        count --;
    }
  
}
```
<br>
### 3.QuickUnion
```java
package define;

/**
 * quick union 中数组保存的是同一个集合中下一个元素的下标
 * 顺序输入pq 会退化成线性
 * 
 * @author liaozk 
 */
public class QuickUnion extends UnionFind {

    int current = 0;
    int sum = 0;

    public QuickUnion(int n) {
        super(n);
    }

    @Override
    public int find(int p) {
        while (p != id[p]) {
            p = id[p];
        }
        return p;
    }

    @Override
    public void union(int p, int q) {
        int pRoot = find(p);
        int qRoot = find(q);

        if (pRoot == qRoot) return;
        id[pRoot] = qRoot;
        count -- ;
    }

}
```
<br>
### 4.WeightedQuickUnion
```java
package define;

/**
 * 加权快速union ，在每次union时，都是将小的树合并到大的树
 * 
 * @author liaozk 
 */
public class WeightedQuickUnion extends UnionFind{

    int current = 0;
    int sum = 0;

    private int[] sz;

    public WeightedQuickUnion(int n) {
        super(n);
        sz = new int[n];
        for (int i = 0; i < n; i++) {
            sz[i] = 1;
        }
    }

    @Override
    public int find(int p) {
        while (p != id[p]) {
            p = id[p];
        }
        return p;
    }

    @Override
    public void union(int p, int q) {
        int pRoot = find(p);
        int qRoot = find(q);

        if (pRoot == qRoot) return;

        if (sz[pRoot] < sz[qRoot]) {
            id[pRoot] = qRoot;
            sz[qRoot] += sz[pRoot];
        } else {
            id[qRoot] = pRoot;
            sz[pRoot] += sz[qRoot];
        }
        count -- ;
    }
}
```
<br>
### 5.PathCompressionQuickUnion
```java
package define;

import edu.princeton.cs.algs4.StdOut;
import java.util.Arrays;

/**
 * 路径压缩的加权QuickUnion
 * 
 * @author liaozk 
 */
public class PathCompressionQuickUnion extends UnionFind {

    int current = 0;
    int sum = 0;

    public PathCompressionQuickUnion(int n) {
        super(n);
    }

    @Override
    public int find(int p) {
        int original = p;
        current ++;
        while (p != id[p]) {
            p = id[p];
            current ++;
        }
        //压缩路径：将p 到 root 之间所有的节点的值修改为root
        while (original != id[original]) {
            int next = id[original];
            id[original] = p;
            original = next;
        }

        return p;
    }

    @Override
    public void union(int p, int q) {
        int pRoot = find(p);
        int qRoot = find(q);

        if (pRoot == qRoot) return;
        id[pRoot] = qRoot;
        count -- ;
    }
}
```
<br>
### 6.如何实现 可变大小的UnionFind?
<br>
