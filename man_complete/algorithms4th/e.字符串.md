---
layout: post
title: 字符串
---


字符串

<br>
在明显降低质量的情况下，当前进度把控简直是教科书级别。
<br>
从现在到春节前夕，我有两个周末，2月3日有一天的时间，剩下的就需要请年假了，由于2号是年会，3号必然就作废了。第六章作为一个总结性质的章节，显然很重要，那么我可能得在下周末来临之前，完成字符串章节内容，以期在正常的计划内能至少抽出一天的时间，投入到第六章节。意志的胜利！集中起来的意志可以击穿顽石！
<br>
对于以java入行的程序员而言，String几乎已经被当作基本类型来使用了，而不是一个对象。这里先介绍一下字母表Alphabet，对java而言，1.8及之前的String 是基于char[]实现，之后则是byte[]。对于一个只懂得0&1的机器，如何表示这些符号呢？编码CODE，UTF-8、ASCII等字母表。
<br>
字符串的算法不仅仅只适用于字符串，流也可以理解为字符串，一切皆是字符串！
<br>

# 字符串排序(radix sort)

<br>
在介绍索引计数法之前，可以思考下如何排序定长的数字，【1234，1235，1236，1321】。
<br>

## 键索引计数法( counting sort )

<img src="https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250727160736518.png" alt="image-20250727160736518" style="zoom:25%;" />

涉及 value 1,2,3,4； 我们需要按照 value 对key 进行排序

step1-统计每个value的出现的频率 = [0,0,3,5,6,6] （value+1）1出现了三次，2出现了5次

<br>

step2-将频率转化为索引 = [0,0,3,8,14,20]，1占用了3个名额，2出现了5次，排完序就变成了8
<br>

step3-辅助数组进行排序，扫描到的第一个2放到数组的第三个位置，同时下一个2需要放到第四的位置
<br>
在这里写下伪代码就是不折不扣的假努力了。

<br>

20250727：虽然再次复习时，补充了该算法的描述，我仍然不能确保时隔几年后看到这段文字，就能理解算法。经典书籍就是那种你的任何简化都意味着信息的丢失🫠



<br>

## 低位优先

<br>
定长字符串w，从左到右，去字符作为key进行w次索引计数法。
<br>
每一次循环都会将相同key的放在一起，下一轮循环，相同key组内的顺序，取决于上一次循环的顺序。

```java
package algorithm;

public class LSD {

    public static void sort(String[] a, int w) {
        int n = a.length;
        int r = 256;
        String[] aux = new String[n];

        for (int d = w-1; d >= 0; d--) {

            int[] count = new int[r + 1];

            //第一次循环，统计每一个字符串第d位的char，出现的次数
            for (int i = 0; i < n; i++) {
                count[a[i].charAt(d) + 1] ++;
            }
            //第二次循环，将频率转换为索引
            for (int i = 1; i <= r; i++) {
                count[i] += count[i-1];
            }
            //第三次循环，将字符串按照第d位的字符大小进行排序
            for (int i = 0; i < n; i++) {
                aux[count[a[i].charAt(d)]++] = a[i];
            }
            //第四次循环，回写
            for (int i = 0; i < n; i++) {
                a[i] = aux[i];
            }
        }
    }
}
```

<br>

## 高位优先

<br>
地位优先，要求字符串定长，要对不定长的字符串进行基数排序时，则需要使用高位优先基数排序。
<br>
高位优先在实现的时候，使用了一些技巧，不过总体而言这是一种递归算法，将字符串分组&排序，再在每一组内继续分组&排序。
<br>
理解这个算法的重点，我认为在于时刻关注count[] 到底是什么。

```java
package algorithm;

import define.InsertionSort;
import edu.princeton.cs.algs4.Insertion;

import java.util.Arrays;

public class MSD {
    private static  int r = 256;
    private static final int M = 0;// 15阈值，低于这个就切换排序算法
    private static String[] aux;

    private static int charAt(String s, int d) {
        if (d < s.length()) {
            return s.charAt(d);
        } else {
            return -1;
        }
    }

    public static void sort(String[] a) {
        int n = a.length;
        aux = new String[n];
        sort(a, 0, n-1, 0);
    }

    private static void sort(String[] a, int lo, int hi, int d) {
        //以第d个字符为键将数组排序
        if (hi < lo + M) {
            Insertion.sort(a, lo, hi);
            return;
        }

        int[] count = new int[r + 2];//1 是拓展了字母表0，1 是原本的基数排序就会+1；需要用到有多少个a这个信息的是b
        }
        //第一次循环，统计d位每次字符出现的次数
        for (int i = lo; i <= hi; i++) {
            count[charAt(a[i], d) + 2]++;
        }
        //第二次循环，频率转化为索引
        for (int i = 1; i < r + 2; i++) {
            count[i] += count[i - 1];
        }
        //第三次循环，对数据进行分组 & 排序
        for (int i = lo; i <= hi; i++) {
            aux[count[charAt(a[i], d) + 1] ++] = a[i];//为何是+1？
        //回写
        for (int i = lo; i <= hi; i++) {
            a[i] = aux[i - lo];
        }
        //递归，分组之分组
        for (int i = 1; i < r + 1; i++) {
            //0~1 这个区间，存放的是d位没有值的字符串，书中是 0～r
            sort(a,lo +count[i], lo + count[i+1] -1, d+1);
        }
    }
}
```

<br>
注意点：
<br>
1.小型子数组，在m=0 的情况下，百万字符参与排序，最终会产生的百万大小为1的子数组，而对每一个数组都需要初始化count[]。所以当字母表变得非常大的时候，及m设置不合理时，对时间/空间的浪费及其严重。
<br>
2.数组，全是等值键时，也会全部走一遍流程。
<br>
3.每一次递归，都会重新创建一次count[]数组，额外空间开销。
<br>
我以为我懂了，然后又花了近两个小时....
<br>
字符串中，其他基于compareTo的排序算法，都可以算是高位优先排序算法。
<br>

## 三向字符串快速排序

```java
package algorithm;

public class Quick3String {
    private static int charAt(String s, int d) {
        if (d < s.length()) {
            return s.charAt(d);
        } else {
            return -1;
        }
    }

    private static void exch(Comparable[] a, int i, int j) {
        Comparable t = a[i];
        a[i] = a[j];
        a[j] = t;
    }
    
    public static void sort(String[] a) {
        
    }
    
    private static void sort(String[] a, int lo, int hi, int d) {
        if (lo >= hi) return;
        int lt = lo, gt = hi;
        int v = charAt(a[lo], d);
        int i = lo + 1;
        
        while (i <= gt) {
            int t = charAt(a[i], d);
            if (t < v) {
                exch(a, lt++, i++);
            } else if (t > v) {
                exch(a, gt--, i);
            } else {
                i++;
            }
        }
        sort(a, lo, lt-1, d);
        if (v >= 0) {
            sort(a, lt, gt, d+1);
        }
        sort(a, gt+1, hi, d);
    }
    
}
```



<br>

# 单词查找树（trie）

<br>
Trie 与 字符串为key的符号表，之间有什么联系？
<br>
每一个结点，有r条链接，r为字母表的大小，结构中存在大量的（空链接/结点没有value），字符串对应的value，存放在该字符串最后一个char的节点上。
<br>
单词查找树中即没包含s/e/a，也不包含sea。
<br>
单词查找树的实现代码比较简单，但再看的时候，不禁会再次觉得已经忘之前实现二叉搜索树的场景了...

```java
package define;

import define.impl.ListQueue;

import java.util.Objects;

public class TrieST<Value> {

    private static int r = 256;
    private Node root;

    private static class Node {
        private Object val;
        private Node[] next = new Node[r];
    }

    public Value get(String key) {
        Node x = get(root, key, 0);
        if (x == null) return null;
        return (Value) x.val;
    }

    private Node get(Node x, String key, int d) {
        if (x == null) return null;
        if (d == key.length()) return x;
        char c = key.charAt(d);
        return get(x.next[c], key, d+1);
    }

    public void put(String key, Value val) {
        put(root, key, val, 0);
    }

    private Node put(Node x, String key, Value val, int d) {
        // charAt 0,1,2  length = 3
        if (x == null) x = new Node();
        if (d == key.length()) {
            x.val = val;
            return x;
        }
        char c = key.charAt(d);
        x.next[c] = put(x.next[c], key, val, d+1);
        return x;
    }

    public int size() {
       return size(root);
    }

    private int size(Node x) {
        if (x == null) return 0;
        int count = 0;
        if (x.val != null) count ++;
        for (char c = 0; c < r; c++) {
            count += size(x.next[c]);
        }
        return count;
    }

    public Iterable<String> keys() {
        return keyWithPrefix("");
    }

    public Iterable<String> keyWithPrefix(String key) {
        Queue<String> q = new ListQueue<>();
        collect(root, key, q);
        return q;
    }

    private void collect(Node x, String pre, Queue<String> q) {
        if (x == null) return;
        if (x.val != null) q.enqueue(pre);
        for (char c = 0; c < r; c ++) {
            collect(x.next[c], pre + c, q);
        }
    }

    public Iterable<String> keysThatMatch(String pat) {
        Queue<String> q = new ListQueue<>();
        collect(root, "",pat, q);
        return q;
    }

    private void collect(Node x, String pre, String pat, Queue<String> q) {
        if (x == null) return;
        if (pre.length() == pat.length() && x.val != null) q.enqueue(pre);
        if (pre.length() == pat.length()) return;
        char next = pat.charAt(pre.length());
        for (char c = 0; c < r; c ++) {
            if (next == c || next == '.') {
                collect(x.next[c], pre + c, pat, q);
            }
        }
    }

    public String longestPrefixOf(String s) {
        int length = search(root, s, 0, 0);
        return s.substring(0, length);
    }

    private int search(Node x, String s,int d, int length) {
        if (x == null) return length;
        if (x.val != null) length = d;
        if (d == s.length()) return length;
        char c = s.charAt(d);
        return search(x.next[c], s, d+1, length);
    }

    public void delete(String key) {
        delete(root, key, 0);
    }

    private Node delete(Node x, String key, int d) {
        if (x == null) return null;
        if (d == key.length()) {
            x.val = null;
        } else {
            char c = key.charAt(d);
            x.next[c] = delete(x.next[c], key, d + 1);
        }
        if (x.val != null) return x;
        for (char c = 0; c < r; c ++) {
            if (x.next[c].val != null) return x;
        }
        return null;
    }

}
```

<br>
单词查找树，的空间消耗，与r成比例。下面介绍三向单词查找树(TST)。
<br>
每个节点含有，一个字符，一个值，三条链接，不再每个节点保存大小为r的数组。

```java
package define;

public class TST<Value> {

    private Node root;

    private class Node {
        char c;
        Node left, mid, right;
        Value val;
    }

    public Value get(String key) {
        Node n  = get(root, key, 0);
        if (n == null) {
            return null;
        }
        return n.val;
    }

    private Node get(Node x, String key, int d) {
        if (x == null) return null;
        char c = key.charAt(d);
        if (c < x.c) {
            return get(x.left, key, d);
        } else if (c > x.c) {
            return get(x.right, key, d);
        } else {
            return get(x.mid, key, d + 1);
        }
    }

    private Node put(Node x, String key, Value val, int d) {
        char c = key.charAt(d);
        if (x == null) {
            x = new Node();
            x.c = c;
        }
        if (d == key.length() - 1) {
            x.val = val;
            return x;
        } 
        
        if (c < x.c) {
            x.left = put(x.left, key, val, d);
        } else if (c > x.c) {
            x.right = put(x.right, key, val, d);
        } else {
            x.mid = put(x.mid, key, val, d + 1);
        }
        return x;

    }

}
```

<br>
不想一遍又一遍的为自己低下的执行力找寻借口，三向单词查找树非常适合用作联系。
<br>
//todo 需要后续手撸一遍！并完成习题！！！
<br>

# 子字符串查找

<br>
整体的描述就是在一篇文章中查找目标字符串。相比于前面的算法，这个我是感觉难多了，之前稍微了解过一点kmp模式匹配，只能说完全没看懂。so，向曾经为能逾越的高峰发起挑战！
<br>
关于字符串这一数据结构的描述此处不再多言，关于字符串匹配最直观的算法便是：
<br>

## 暴力匹配算法(brute force)

```java
package algorithm;

public class BruteForce {

    public static int search(String pat, String text) {
        int m = text.length();
        int n = pat.length();

        for (int i = 0; i < m; i++) {
            int j;
            for (j = 0; j < n; j++) {
                if (text.charAt(i + j) != pat.charAt(j)) {
                    break;
                }
            }
            if (j == n) {
                return i;
            }
        }
        return m;
    }

    /**
     * 这个算是指针的艺术吗
     * @param pat
     * @param text
     * @return
     */
    public static int search1(String pat, String text) {
        int i, m = text.length();
        int j, n = pat.length();
        for (i = 0, j = 0; i < m && j < n; i++) {
            if (text.charAt(i) == pat.charAt(j)) j++;
            else {
                i -= j;
                j = 0;
            }
        }
        if (j == n) return i - n;
        return m;
    }
}
```

<br>

## KMP

<br>
相比于暴力匹配，kmp的核心则是想办法，当不匹配时，利用已有的信息，避免回退太多了，少回退几个字符。
<br>
dfa：**确定有限状态自动机（Deterministic Finite Automaton）**dfa[char] [j] :在匹配pat第j个时碰到了char，text下一个字符应该与pat的那一个进行匹配。
<br>
Dfa[pat.charAt(j)] [j] = j + 1;
<br>
匹配中，text索引会永远前进，不断调整的则是pat的索引，不匹配时，重置dfa状态。
<br>
构造dfa，只与pat有关。
<br>
理解dfa的一个关键便是，一个是在txt中匹配pat，一个是在pat中匹配pat[0 ~ x]，使得pat[0 ~ x] = pat[j-x, j]相同，我们要在pat中找到0到j中，找到首位相同的最长字符串，有些地方的讲解中使用的是next数组，next数组其实就是此处每一位j对应的x。
<br>
简单的几行代码，非常值得回味！

```java
package algorithm;

public class KMP {
    private String pat;
    private int[][] dfa;

    public KMP(String pat) {
        this.pat = pat;

        int m = pat.length();
        int r = 256;
        
        dfa = new int[r][m];
        dfa[pat.charAt(0)][0] = 1;
        
        for (int x = 0, j = 1; j < m; j++) {
            for (int i = 0; i < r; i ++) {
                dfa[i][j] = dfa[i][x];
            }
            dfa[pat.charAt(j)][j] = j + 1;
            x  = dfa[pat.charAt(j)][x];
        }
    }

    public int search(String text) {
        int i, j, m = pat.length(), n = text.length();
        
        for (i = 0, j = 0; i < n && j < m; i++) {
            j = dfa[text.charAt(i)][j];
        }
        if (j == m) return i - m;
        else return n;
    }
}
```

<br>
构造dfa需要的代价是pat.lenngth * r,遍历字符串需要的代价是 text.length，总的复杂度则是：pat.length+text.length。
<br>

## Boyer-Moore

<br>
启发式的处理不匹配字符。
<br>
bm算法的核心便是，计算一个right数组，记录每一个字符在pat中出现的最右边的位置，当不匹配时，text指针i 移动 i + j - right[text.charAt(i + j)] 个位置，如果小于1 则 右移动1位。
<br>
通俗的讲则是：将text中不匹配的那个char与pat中该char最后出现的位置匹配。
<br>
j - right[text.charAt(i + j)] : 
<br>

1. = 0 : 不可能出现，text参与匹配的第j个字符在pat中出现的最右位置就是j，那一定是匹配的。
2. ">" 0:
3. "<" 0: 当作1处理





```java
package algorithm;

public class BoyerMoore {
    private int[] right;
    private String pat;

    public BoyerMoore(String pat) {
        this.pat = pat;
        int m = pat.length();
        int r = 256;

        right = new int[r];
        for (int i = 0; i < r; i++) {
            right[i] = -1;
        }
        for (int i = 0; i < m; i++) {
            right[pat.charAt(i)] = i;
        }
    }

    public int search(String text) {
        int n = text.length();
        int m = pat.length();
        
        int skip;
        for (int i = 0; i < n - m; i += skip) {
            skip = 0;
            for (int j = m - 1; j > 0; j -- ) {
                if (text.charAt(i + j) != pat.charAt(j)) {
                    skip = j - right[text.charAt(i + j)];
                    if (skip < 1) skip = 1;
                    break;
                }
            }
            if (skip == 0) return i;
        }
        return n;
    }
}
```

<br>
bm算法，算是效率不错，也比较简单易懂的，与kmp一样，都是暴力搜索算法的优化，将不匹配时移动一个位置，尽可能的变为移动多个。
<br>

## Rabin-Karp

<br>
rk这是，对text所有可能的子字符串计算散列值，然后比较散列后的结果。暴力匹配，需要回退，rk则一直前进。
<br>
对于这个算法还有很多优化，此处就不贴完整的实现了。避免假努力，即时不做..
<br>
（蒙特卡洛法验证正确性、拉斯维加算法）

```java
		private long hash(String key, int m) {//m为pat长度
        long h = 0;
        for (int j = 0; j < m; j++) {
            h = ((R * h + key.charAt(j) % Q);
        }
        return  h;
    }
```

<br>

# 正则表达式

<br>
更通用的描述则是模式匹配。
<br>
对将要匹配的模式的精准描述：
<br>
模式是由三种基本操作，和作为操作数的字符组成。语言即模式所能匹配到的字符串集合，模式则是对语言的详细描述。
<br>
1.连接操作
<br>
2.或操作
<br>
3.闭包操作：将模式的部分重复任意的次数，模式的闭包是由将模式和自身连接任意多次（包括0次），而得到的所有字符串组成的语言。
<br>
优先级：闭包 > 连接> 或
<br>
空字符串，存在于任意字符串中。
<br>
括号可以改变默认的优先级顺序。
<br>
正则表达式，可以等价于一个所有能匹配的字符串集合。
<br>
上面便是正则表达式的基本规则，实际应用中，稍有拓展。
<br>
1.字符集的描述符：
<br>
· 表示任意字符
<br>
[] 表示括号内字符的任意一个
<br>
^ 匹配开头，后面跟 [] 则表示非
<br>
$ 匹配结尾
<br>
2.闭包的简写
<br>
+表示重复 1/n 
<br>
？表示重复 0/1
<br>
{} 表示重复的 具体次数/范围
<br>
3.转义
<br>
\ 
<br>
\s 任意空白字符
<br>
\t tab
<br>
\n 转行
<br>
模式&正则表达式的基本规则 & 正则表达式的拓展
<br>
本节的目标即是：判断字符串是否是在给定的 模式（正则表达式说描述的字符串集合）中。
<br>
模式匹配是一般化了的 子字符串查找：txt中找pat 等价于 txt是否存在于模式“· *pat· *”中。
<br>
为什么要读经典的书籍呢？在此之前我也看过一些正则表达式讲解的内容，但直到现在才有那种通透的感觉。简单而又强大！不过现在大模型又是降智碾压...
<br>
kmp利用限状态自动机，模式匹配则是非确定有限状态自动机，kmp中，下一步怎么走，是明确的。模式匹配中的下一步流转到那个状态则是不确定的，可能会有多个可供选择。nfa的匹配核心逻辑即：1.获取当前所有可以通过匹配转换/空转换到达的状态；2.遍历这些状态，查找能与下一个字符匹配的所有状态。3.然后再获取新的所有可达状态，加入集合。 Repeat。
<br>
上面讲了状态机的运行，如何构造了呢？show me code！

```java
package algorithm;

import define.Bag;
import define.Digraph;
import define.Stack;
import define.impl.LinkBag;
import define.impl.ListStack;

/**
 * 这是一段精简却又异常强大的代码，短短的代码，大大的智慧！
 */
public class NFA {

    private char[] re;//正则表达式自身，用于匹配转换。
    private Digraph g;//有向图，用于保存空转换。因为匹配转换是唯一的，空转换则可能有多个，所以有向图来表示。
    private int m;//状态数量，暂不知用来干什么。

    public NFA(String regexp) {
        Stack<Integer> ops = new ListStack<>();
        re = regexp.toCharArray();
        m = re.length;
        g = new Digraph(m + 1);//+1 是因为多了一个接收状态。

        for (int i = 0; i < m; i++) {//遍历regexp
            int lp = i;

            if (re[i] == '(' || re[i] == '|') {
                ops.push(i);
            } else if (re[i] == ')') {
                int or = ops.pop();
                if (re[or] == '|') {
                    lp = ops.pop();
                    g.addEdge(lp, or + 1);//规则之或：添加1：左括号 指向 或的右边的第一个字符
                    g.addEdge(or, i);//规则之或：添加2：或 指向 右括号
                } else {
                    lp = or;
                }
            }
            //上面这段处理了规则 或

            if (i < m - 1 && re[i+1] == '*') {
                g.addEdge(lp, i + 1);
                g.addEdge(i + 1, lp);
            }
            //上面这段处理了闭包

            if (re[i] == '(' || re[i] == '*' || re[i] == ')') {
                g.addEdge(i, i+1);
            }
        }
    }

    public boolean recognizes(String txt) {
        Bag<Integer> pc = new LinkBag<>();
        DirectDFS dfs = new DirectDFS(g, 0);//有向图的深度优先搜索，来获取可达性
        for (int v = 0; v < g.V(); v++) {
            if (dfs.marked(v)) pc.add(v);//pc中添加当前所有可达状态
        }
        
        for (int i = 0; i < txt.length(); i++) {
            Bag<Integer> match = new LinkBag<>();
            for (int v : pc) {
                if (v < m) {
                    if (re[v] == txt.charAt(i) || re[v] == '.') { //匹配上了
                        match.add(v + 1);//自动流转到下一个
                    }
                }
            }
            pc = new LinkBag<>();
            dfs = new DirectDFS(g, match);//获取匹配状态接下来的所有可到达状态
            for (int v = 0; v < g.V(); v++) {
                if (dfs.marked(v)) pc.add(v);//pc中添加当前所有可达状态
            }
        }
        
        //遍历完了
        for(int v : pc) if (v == m) return true;//最终走到了接收状态
        return false;
    }
}
```

<br>

# 数据压缩

<br>
想起了遥感导论。
<br>
数据压缩的基础模型-无损压缩模型：
<br>
压缩盒：将比特流B转化为压缩后的C(B)；
<br>
展开盒：将C(B)转化为B；
<br>
｜B｜表示比特的数量，则|C(B)|/|B|表示压缩率。
<br>
数据压缩算法的局限：不存在能够压缩任意比特流的算法，如果存在可以循环压缩至0!。
<br>
一般而言，比较好的压缩算法是找出数据的生成程序！
<br>
数据的压缩严重依赖于输入数据的自身特征，本节主要讨论具有一下结构的数据：
<br>
1.小规模字母表
<br>
2.较长的连续相同的位/字符
<br>
3.频繁使用的字符
<br>
4.较长的连续重复的位/字符
<br>

## 游程编码

<br>
000011000 -> 423(01交替出现，可以使用游程长度0来保证所有游程长度不至于超标)
<br>

## 霍夫曼压缩

<br>
能够大幅度压缩自然语言文件空间（以保存自然语言为目的的文件）。
<br>
核心思想：使用较少的比特表示频率高的字符，使用较长的比特表示频率低的字符。
<br>
20240128-1407:我现在完全不想再看下去了，一点都无法集中精力！可至少必须得完成数据压缩！
<br>
如果我们使用ascii编码，每一个字符都将使用8个bit来表示
<br>
一种压缩方法便是使用不定长的bit来映射字符：a-0,b-1,c-00,d-01
<br>
但这种方式，我们需要分隔符，以避免歧义。
<br>
在上面的基础上如果我们使得所有的字符编码都不会成为其他字符的前缀，自然就不需要分隔符了--前缀码 诞生了！
<br>
如何获得前缀码呢？单词查找树可以助一臂之力！（left = 0，right = 1）
<br>
前缀码 如何构造？以便进行压缩。
<br>
压缩后又如何利用前缀码进行解压缩？
<br>
我们已经有了前缀码，可如何才能实现 较少的bit来表示 频率较高的字符呢？-huffman编码
<br>
最后还需要考虑如何将我们构造的单词树进行传递与构建？-前序遍历--中间节点0，叶子结点1后紧跟字符的8位ascii码。
<br>
由于精力难集中，就直接po配套的代码了。

````java
/******************************************************************************
 *  Compilation:  javac Huffman.java
 *  Execution:    java Huffman - < input.txt   (compress)
 *  Execution:    java Huffman + < input.txt   (expand)
 *  Dependencies: BinaryIn.java BinaryOut.java
 *  Data files:   https://algs4.cs.princeton.edu/55compression/abra.txt
 *                https://algs4.cs.princeton.edu/55compression/tinytinyTale.txt
 *                https://algs4.cs.princeton.edu/55compression/medTale.txt
 *                https://algs4.cs.princeton.edu/55compression/tale.txt
 *
 *  Compress or expand a binary input stream using the Huffman algorithm.
 *
 *  % java Huffman - < abra.txt | java BinaryDump 60
 *  010100000100101000100010010000110100001101010100101010000100
 *  000000000000000000000000000110001111100101101000111110010100
 *  120 bits
 *
 *  % java Huffman - < abra.txt | java Huffman +
 *  ABRACADABRA!
 *
 ******************************************************************************/

package edu.princeton.cs.algs4;

/**
 *  The {@code Huffman} class provides static methods for compressing
 *  and expanding a binary input using Huffman codes over the 8-bit extended
 *  ASCII alphabet.
 *  <p>
 *  For additional documentation,
 *  see <a href="https://algs4.cs.princeton.edu/55compression">Section 5.5</a> of
 *  <i>Algorithms, 4th Edition</i> by Robert Sedgewick and Kevin Wayne.
 *
 *  @author Robert Sedgewick
 *  @author Kevin Wayne
 */
public class Huffman {

    // alphabet size of extended ASCII
    private static final int R = 256;

    // Do not instantiate.
    private Huffman() { }

    // Huffman trie node
    private static class Node implements Comparable<Node> {
        private final char ch;
        private final int freq;
        private final Node left, right;

        Node(char ch, int freq, Node left, Node right) {
            this.ch    = ch;
            this.freq  = freq;
            this.left  = left;
            this.right = right;
        }

        // is the node a leaf node?
        private boolean isLeaf() {
            assert ((left == null) && (right == null)) || ((left != null) && (right != null));
            return (left == null) && (right == null);
        }

        // compare, based on frequency
        public int compareTo(Node that) {
            return this.freq - that.freq;
        }
    }

    /**
     * Reads a sequence of 8-bit bytes from standard input; compresses them
     * using Huffman codes with an 8-bit alphabet; and writes the results
     * to standard output.
     */
    public static void compress() {
        // read the input
        String s = BinaryStdIn.readString();
        char[] input = s.toCharArray();

        // tabulate frequency counts
        int[] freq = new int[R];
        for (int i = 0; i < input.length; i++)
            freq[input[i]]++;

        // build Huffman trie
        Node root = buildTrie(freq);

        // build code table
        String[] st = new String[R];
        buildCode(st, root, "");

        // print trie for decoder
        writeTrie(root);

        // print number of bytes in original uncompressed message
        BinaryStdOut.write(input.length);
<br>
        // use Huffman code to encode input
        for (int i = 0; i < input.length; i++) {
            String code = st[input[i]];
            for (int j = 0; j < code.length(); j++) {
                if (code.charAt(j) == '0') {
                    BinaryStdOut.write(false);
                }
                else if (code.charAt(j) == '1') {
                    BinaryStdOut.write(true);
                }
                else throw new IllegalStateException("Illegal state");
            }
        }

        // close output stream
        BinaryStdOut.close();
    }

    // build the Huffman trie given frequencies
    private static Node buildTrie(int[] freq) {

        // initialize priority queue with singleton trees
        MinPQ<Node> pq = new MinPQ<Node>();
        for (char c = 0; c < R; c++)
            if (freq[c] > 0)
                pq.insert(new Node(c, freq[c], null, null));

        // merge two smallest trees
        while (pq.size() > 1) {
            Node left  = pq.delMin();
            Node right = pq.delMin();
            Node parent = new Node('\0', left.freq + right.freq, left, right);
            pq.insert(parent);
        }
        return pq.delMin();
    }

    // write bitstring-encoded trie to standard output
    private static void writeTrie(Node x) {
        if (x.isLeaf()) {
            BinaryStdOut.write(true);
            BinaryStdOut.write(x.ch, 8);
            return;
        }
        BinaryStdOut.write(false);
        writeTrie(x.left);
        writeTrie(x.right);
    }

    // make a lookup table from symbols and their encodings
    private static void buildCode(String[] st, Node x, String s) {
        if (!x.isLeaf()) {
            buildCode(st, x.left,  s + '0');
            buildCode(st, x.right, s + '1');
        }
        else {
            st[x.ch] = s;
        }
    }

    /**
     * Reads a sequence of bits that represents a Huffman-compressed message from
     * standard input; expands them; and writes the results to standard output.
     */
    public static void expand() {

        // read in Huffman trie from input stream
        Node root = readTrie();

        // number of bytes to write
        int length = BinaryStdIn.readInt();

        // decode using the Huffman trie
        for (int i = 0; i < length; i++) {
            Node x = root;
            while (!x.isLeaf()) {
                boolean bit = BinaryStdIn.readBoolean();
                if (bit) x = x.right;
                else     x = x.left;
            }
            BinaryStdOut.write(x.ch, 8);
        }
        BinaryStdOut.close();
    }

    private static Node readTrie() {
        boolean isLeaf = BinaryStdIn.readBoolean();
        if (isLeaf) {
            return new Node(BinaryStdIn.readChar(), -1, null, null);
        }
        else {
            return new Node('\0', -1, readTrie(), readTrie());
        }
    }

    /**
     * Sample client that calls {@code compress()} if the command-line
     * argument is "-" an {@code expand()} if it is "+".
     *
     * @param args the command-line arguments
     */
    public static void main(String[] args) {
        if      (args[0].equals("-")) compress();
        else if (args[0].equals("+")) expand();
        else throw new IllegalArgumentException("Illegal command line argument");
    }
}
````

<br>

## LZW压缩算法

<br>
霍夫曼是为输入中的定长，产生一张变长的编码表，lzw这是为变长模式生成一张定长编译表，重点是输出中无需附上这张表。
<br>
看不懂，放弃... todo
<br>

补充:

<br>

# 事件驱动模拟

<br>
这是一个非常有趣且逻辑比较简单但功能异常强大的模拟，揭示了一种完全不同于日常java web的计算机应用。
<br>
在模拟之前我们需要：完整的描述客观事实  -->  对现状进行抽象  -->  代码实现的抽象  --> 具体代码
<br>
客观事实：
<br>
按照弹性碰撞的原理模拟粒子碰撞。
<br>
现状抽象：
<br>

1. 物体抽象 - 刚性球体模型
   - 粒子与墙及相互之间的碰撞是弹性的
   - 每一个粒子都是 当前位置、速度、质量、直径 已知的球体
   - 不存在其他外力

2. 实现抽象之一 - 时间驱动模拟：最直观的抽象
   - 目的：记录粒子任意时刻的位置与速度
   - 简化：在获取了t时刻的速度与位置后，计算出t+m 时刻的位置与速度
   - 重点：放在时间上，每单位时间就进行一次计算。缺点在于：时间过短，会导致计算量飙升、时间过长则会错过碰撞，即在最终时刻之前是有碰撞发生的。

3. 实现抽象之二 - 事件驱动模拟
   - 目的：记录每一次潜在的碰撞
   - 简化：记录所有潜在的碰撞事件，并加上状态，如果因为前面的事件导致碰撞不会发生则该事件作废。
   - 重点：由时间 转换为 碰撞

4. 碰撞预测：
   - 一点高中物理知识，将速度分解为x,y上的分量

5. 碰撞的计算：pass

6. 排除无效事件：

   - 每个粒子保存一个碰撞计数器，在事件消费时，若粒子的计数器与事件中的不一致，则表明粒子在该事件之前已经发生过一次碰撞，事件作废。

   



即使现在我能在代码上进行些许修改，但依旧难掩这个给人带来的震撼！一个看起来，非常复杂的东西的雏形竟又如此简单，这个示例也是展示面向对象思想的绝佳粒子。总之，如果感到悲哀，感到无法行动，生命似乎一片暗淡，恰如这阴沉天空，run! 你可以有任何想法，但没有一个比不停的run更重要的！手不能停！

```java
package slove;

import edu.princeton.cs.algs4.MinPQ;
import edu.princeton.cs.algs4.Particle;
import edu.princeton.cs.algs4.StdDraw;

public class CollisionSystem {

    //事件
    private class Event implements Comparable<Event> {

        private final double time;
        private final Particle a, b;//作为事件参与方的两个粒子
        private final int countA, countB;//记录原本的计划中ab碰撞这一事件发生时的ab粒子各自的状态

        public Event(double t, Particle a, Particle b) {
            this.time = t;
            this.a = a;
            this.b = b;

            if (a != null) {
                countA = a.count();
            } else {
                countA = -1;
            }

            if (b != null) {
                countB = b.count();
            } else {
                countB = -1;
            }
        }

        public int compareTo(Event that) {
            if (this.time > that.time) return 1;
            else if (this.time < that.time) return -1;
            else return 0;
        }

        public boolean isValid() {
            if (a != null && a.count() != countA) return false;
            if (b != null && b.count() != countB) return false;
            return true;
        }

    }

    private MinPQ<Event> pq;//优先队列，取最早的事件
    private double t = 0.0D;// 模拟时钟
    private Particle[] particles;//所有粒子

    public CollisionSystem(Particle[] particles) {
        this.particles = particles;
    }

    //计算某一个粒子在时间范围内所有的可能碰撞
    private void predictCollisions(Particle a, double limit) {
        if (a == null) return;
        //粒子碰撞
        for (int i = 0; i < particles.length; i++) {
            double dt = a.timeToHit(particles[i]);
            if ((t + dt) < limit) {
                pq.insert(new Event(t + dt, a, particles[i]));
            }
        }
        //墙体碰撞
        double dtX = a.timeToHitVerticalWall();
        if (t + dtX < limit) {
            pq.insert(new Event(t + dtX, a, null));
        }
        double dtY = a.timeToHitHorizontalWall();
        if (t + dtY < limit) {
            pq.insert(new Event(t + dtY, null, a));
        }
    }

    public void redraw(double limit, double Hz) {
        StdDraw.clear();
        for (int i = 0; i < particles.length; i++) {
            particles[i].draw();
        }
        StdDraw.show(20);
        if (t < limit) {
            pq.insert(new Event(t + 1.0/Hz,null, null));
        }
    }

    //模拟
    public void simulate(double limit, double Hz) {
        pq = new MinPQ<>();
        for (int i = 0; i < particles.length; i++) {
            predictCollisions(particles[i], limit);
        }
        pq.insert(new Event(0, null, null));//触发重绘
        //消费事件
        while (!pq.isEmpty()) {
            Event event = pq.delMin();
            if (!event.isValid()) continue;
            //时间推进到事件发生的那一刻
            for (int i = 0; i < particles.length; i++) {
                particles[i].move(event.time - t);
            }
            t = event.time;

            //处理事件，更改粒子状态
            Particle a = event.a, b = event.b;
            if (a != null && b != null) {
                a.bounceOff(b);
            } else if (a != null && b == null) {
                a.bounceOffVerticalWall();
            } else if (a == null && b != null) {
                b.bounceOffHorizontalWall();
            } else if (a == null && b == null) {
                redraw(limit, Hz);
            }

            //预测更改后的粒子碰撞
            predictCollisions(a, limit);
            predictCollisions(b, limit);
        }

    }

    public static void main(String[] args) {
        StdDraw.show(0);
//        int n = Integer.parseInt(args[0]);
        int n = 5;
        Particle[] particles = new Particle[n];
        for (int i = 0; i < n; i++) {
            particles[i] = new Particle();
        }
        CollisionSystem collisionSystem = new CollisionSystem(particles);
        collisionSystem.simulate(10000, 0.5);
    }
}
```

<br>

# B树

<br>
好吧，这个才是今晚的计划与重点，mysql的索引，b+树将我引入到数据结构之中。
<br>
在海量数据中进行快速查找是非常重要的一项功能，在介绍之前，我们需要一个简单的模型：页 & 探查。
<br>
该结构中，m结点表示该结点含有m-1 到 m/2 条链接。结点又大致分为内部结点与外部结点，内部结点的每个key关联一个其他结点，叶子结点就是外部结点，key关联一个page。以内部结点key作为根的树其所有其他结点key都大于根key同时小于该内部结点的next key。
<br>
20240205-20:43 没看懂后缀数组的情况下，我想暂时就先终结这一部分了。这绝对不是一个很好的句号，但相比于一年前，已有所长进，不过还远远不够。
<br>

# 后缀数组

<br>
事实上，我只打算着重于事件驱动模拟&b-树。
<br>

# 网络流量

<br>

# P&NP