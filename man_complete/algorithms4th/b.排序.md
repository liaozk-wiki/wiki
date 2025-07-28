---
layout: post
title: 排序
---


算法

<br>
首要任务一定是写出清晰正确的代码，其次才是优化；编程领域，不成熟的优化是所有的罪恶之源。
其次，也不应该完全忽略程序性能。
<br>
计算机可以做什么？
computing：encode+process+decode
<br>
问题： order、graph
strategies：traversal+optimization
<br>
计算模型：一般采用 RAM Model 
输入、计算、内存、输出
<br>
证明：数学归纳法
弱数学：
p(b) = true
p(n) = p(n-1)
all n>b p(n)=true
<br>
强数学:
b到n 都是true p(n)=true
分析：

1. critical operation
2. how many critical operation are conducted 
   

<br>

# 高效乘积

```java
public class PerformancePow {

    public static long pow(long x, int n) {
        if (n == 0) {
            return 1;
        }
        if (n == 1) {
            return x;
        }

        if ((x & 1) ==0) {
            return pow(x*x, n/2);
        } else {
            return pow(x*x, n/2)*n;
        }
    }
}
```

<br>

# 最大求和子串

````java
/**
 * 此处主要介绍求解 数组最大子序列和 的两种高效算法
 * 1.分治 左+右+中间 maxSumRec
 * 2.遍历 一旦小于0则从该处重新统计计算
 */
public class DemoMaxSubSum {

    public static int maxSumRec(int[] a, int left, int right) {

        //临界条件 只包含一个元素
        if (left == right) {
            if (a[left] > 0) {
                return a[left];
            } else {
                return 0;// 抛弃
            }
        }

        int center = (left + right)/2;
        int maxLeftSum = maxSumRec(a, left, center);
        int maxRightSum = maxSumRec(a, center+1, right);

        //计算中间
        int maxLeftBorderSum = 0 , leftBorderSum = 0;
        for (int i = center; i >= left; i--) {
            leftBorderSum += a[i];
            if (leftBorderSum > maxLeftBorderSum) {
                maxLeftBorderSum = leftBorderSum;
            }
        }

        int maxRightBorderSum = 0, rightBorderSum = 0;
        for (int i = center+1; i <= right; i++) {
            rightBorderSum += a[i];
            if (rightBorderSum > maxRightBorderSum) {
                maxRightBorderSum = rightBorderSum;
            }
        }

        return  max3(maxLeftSum, maxRightSum, maxLeftBorderSum+maxRightBorderSum);
    }

    private static int max3(int a, int b, int c) {
        int temp = Math.max(a, b);
        return Math.max(temp, c);
    }

    public static int maxSubSum(int[] a) {
        int maxSum = 0, thisSum = 0;

        for (int i = 0; i < a.length; i++) {
            thisSum += a[i];

            if (thisSum > maxSum) {
                maxSum = thisSum;
            } else if (thisSum < 0) {
                thisSum = 0;
            }
        }

        return maxSum;
    }
}
````

<br>

排序

<br>
对排序算法领域的第一次探索。
排序：将一组对象按照某种顺序重新排列的过程。
按额外内存开销分类：
<br>
原地排序算法：额外只需要函数调用所使用的栈，以及固定数目的实例变量。
其他：需要额外空间存储另一份数组副本。
<br>

# 1.一个基本的排序算法模板：

```java
package define;

import edu.princeton.cs.algs4.StdIn;
import edu.princeton.cs.algs4.StdOut;

/**
 * 一个基本的排序算法模板
 *
 * 核心-具体的实现：sort
 * 核心-比较：less
 * 核心-交换：exch
 * 验证：show isSorted
 *
 * @author liaozk
 */
public class BasicSort {

    public static void sort(Comparable[] a) {
        //具体的实现
    }

    private static boolean less(Comparable a, Comparable b) {
        return a.compareTo(b) < 0;
    }

    private static void exch(Comparable[] a, int i, int j) {
        Comparable t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    private static void show(Comparable[] a) {
        for (int i = 0; i < a.length; i++)
            StdOut.print(a[i]+" ");
        StdOut.println();
    }

    public static boolean isSorted(Comparable[] a) {
        for (int i = 1; i < a.length; i++) {
            if (less(a[i],a[i-1])) return false;
        }
        return true;
    }

    public static void main(String[] args) {
        String[] a = StdIn.readAllStrings();
        sort(a);
        assert isSorted(a);
        show(a);
    }
}
```

<br>

# 2.选择排序

<br>
则是本文的第一个排序算法，也是比较简单初级的算法，在给出这个算法书中的标准实现之前，很有必要去介绍一下在一般编程中解决问题的思路。
<br>
对事物的高度抽象后的结果总是非常简单的，可是要去理解这种结果又必须忘掉它，深入到具体的实现细节里面去，就像经常听到的“这是为你好”，不去趟一下坑，又怎能真正理解呢？简单的去记那些高度抽象的东西，不如回家睡睡觉，打打游戏，这二者至少不会让你产生“我已经掌握了”的那种感觉。
<br>
程序是一种语言，要实现某个功能：
<br>

1. 理解要干什么（软件很少被物理条件限制，几乎是一个纯粹的创造性活动，最大的限制是对我们所构建的东西的理解。）
2. 用口语描述出如何解决，最好在草稿中写下解决步骤。
3. 将口语翻译成计算机语言。

定义：首先找到数组中最小的那个元素，其次将他和数组第一个元素交换位置。再次，在剩下的元素中找到最小的元素，与数组的第二个位置进行交换，如此往复直至整个数组排序。
<br>

解决步骤：
<br>

1. 找到最小的元素
2. 将其移动（交换）到前面去
3. 重复上述步骤
   

1.需要一个方法找到最小的元素

```java
		public static int findSmallest(Comparable[] a, int start, int end) {
        int smallest = start;
        for (int i = start; i < end; i++) {
            if (a[i].compareTo(a[smallest]) == -1) {
                smallest = i;
            }
        }
        return smallest;
    }
```

<br>
2.其次需要一个交换元素的方法

```java
  	public static void exch(Comparable[] a, int b, int c) {
        Comparable temp = a[b];
        a[b] = a[c];
        a[c] = temp;
    }
```

<br>
3.在一个循环中重复上述步骤

````java
		public static void SelectSort(Comparable[] a) {
        for (int i = 0; i < a.length; i++ ) {
            int smallest = findSmallest(a, i, a.length);
            exch(a,smallest,i);
        }
    }
````

<br>
4.for循环的实现在这里是非常符合直觉的，循环与递归，他们总是可以相互替代的，尝试用递归来实现一下

```java
		public static void SelectSortOfRecursion(Comparable[] a) {
        sort(a,0);
    }

		private static void sort(Comparable[] a, int start) {
        if (start >= a.length) return;
        int smallest = findSmallest(a,start , a.length);
        exch(a, start, smallest);
        sort(a, start+1);
    }
```

<br>
5.最终的所有实现

````java
package define;

import java.util.Arrays;

public class HowToDevSelectSort {

    public static void SelectSort(Comparable[] a) {
        for (int i = 0; i < a.length; i++ ) {
            int smallest = findSmallest(a, i, a.length);
            exch(a,smallest,i);
        }
    }

    public static void SelectSortOfRecursion(Comparable[] a) {
        sort(a,0);
    }

    private static void sort(Comparable[] a, int start) {
        if (start >= a.length) return;
        int smallest = findSmallest(a,start , a.length);
        exch(a, start, smallest);
        sort(a, start+1);
    }

    public static void exch(Comparable[] a, int b, int c) {
        Comparable temp = a[b];
        a[b] = a[c];
        a[c] = temp;
    }

    public static int findSmallest(Comparable[] a, int start, int end) {
        int smallest = start;
        for (int i = start; i < end; i++) {
            if (a[i].compareTo(a[smallest]) == -1) {
                smallest = i;
            }
        }
        return smallest;
    }

    public static void main(String[] args) {
        Integer[] a = new Integer[]{1,2,7,6,5,4,3,8,9};
        SelectSortOfRecursion(a);
        System.out.println(Arrays.toString(a));
    }
<br>
}
````

<br>
下面是书中的实现：
<br>

```java
package define;

/**
 * 选择排序算法
 * 1.找到数组中最小的元素，将其与数组第一个元素交换位置
 * 2.在剩下的元素中找到最小的，将其与数组第二个元素交换位置
 * 3. ...
 *
 * @author liaozk
 */
public class SelectionSort {

    public static void sort(Comparable[] a) {
        int N = a.length;
        for (int i = 0; i < N; i++) {
            int min = i;
            for (int j = i + 1; j < N; j++) {
                if (less(a[j],a[min])) min = j;
            }
            exch(a, i, min);
        }

    }

    private static boolean less(Comparable a, Comparable b) {
        return a.compareTo(b) < 0;
    }

    private static void exch(Comparable[] a, int i, int j) {
        Comparable t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

}
```

<br>
选择排序是一个非常稳定的排序，有两个鲜明的特点：
<br>

1. 其运行时间与输入无关，交换次数n，比较次数1+2+3+...n-1 = n*(n-1)/2
2. 其数据移动是最少的

<br>

# 3.插入排序

<br>
原理：将每一个元素抽出来，插入到已有有序的序列中的合适位置。
<br>
实现方式一
<br>
步骤：
<br>

1. 取出一个元素
1. 插入到前面有序的序列中去
1. 前面有序的序列中找到合适的位置
2. 插入其中，并将后面所有元素的位置右移一位



```java
package define;

import java.util.Arrays;

public class HowToDevInsertSort {

    public static int findCorrectPosition(Comparable[] a, int end, Comparable target) {
        int position = 0;
        for (; position < end; position++) {
            if (a[position].compareTo(target) > 0) {
                return position;
            }
        }
        return end;
    }

    public static void rightMove(Comparable[] a,int start, int end) {
        for (int j = end-1; j >= start; j--) {
            a[j+1] = a[j];
        }
    }

    public static void insertSort(Comparable[] a) {
        for (int i = 0; i < a.length; i++) {
            Comparable t = a[i];
            int correct = findCorrectPosition(a,i,t);
            rightMove(a,correct,i);
            a[correct] = t;
        }
    }

    public static void main(String[] args) {
        Integer[] a = new Integer[]{1,2,3,7,8,9,4,5,6};
        insertSort(a);
        System.out.println(Arrays.toString(a));
    }

}
```

<br>
实现方式二：
<br>
书中实现-便利过程中一一将元素与该元素之前的每一个元素进行对比，交换位置，直到合适的位置
more elegant！

```java
package define;

/**
 * 插入排序
 * 将当前元素插入到前面已经有序的元素中去
 *
 * @author liaozk
 */
public class InsertionSort {

    public static void sort(Comparable[] a) {
        int N = a.length;
        for (int i = 1; i < N; i++) {
            for (int j = i; j > 0 && less(a[j], a[j-1]); j-- ) {
                exch(a, j, j-1);
            }
        }
    }

    private static boolean less(Comparable a, Comparable b) {
        return a.compareTo(b) < 0;
    }

    private static void exch(Comparable[] a, int i, int j) {
        Comparable t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

}
```

```python
"""
手搓一个python版的插入排序,原书中的排序有意思
"""

a = [1,2,7,8,4,9,5,6]

def insert_sort():
    length = len(a)
    for i in range(1,length):
        for j in range(i,0,-1):
            if a[j] < a[j-1] :
                a[j],a[j-1] = a[j-1],a[j]
            else:
                break

insert_sort()
print(a)
```

<br>
如果数组相对有序，则插入排序，每次比较的次数会大幅减少，尽可能将大的元素移动到后半段将会极大提升性能。
<br>
更一般的抽象：
inversion：倒置 数组中顺序颠倒的两个元素。
插入排序，需要交换的次数与数组中存在的倒置数量是相同的，需要比较的次数>=倒置的数量 & <= 倒置的数量+数组大小-1 ；
<br>
例子：一个完全有序的数组，不存在倒置，则无需任何交换，需要进行的比较次数就是数组大小-1
<br>
分析算法的基本步骤：

1. 实现并调试他们
2. 分析他们的基本性质
3. 对相对性能作出猜想
4. 实验验证

<br>

做这些工作总是十分繁琐又耗时的，尤其是到了后期，对每一个算法进行分析与实验将会是，精力上的巨大挑战，基于对自己“平庸”的有效认知，于我而言，完成一件事情的重要性与意义在当前，至少是对于《算法》这本书的学习而言一定是远大于将一件事情做的完美的。所以至少对于当前这个最简单的例子还是完整的走完所有的步骤。
<br>

1. 实现：这一步已经在前面做了
2. 分析基本性质：
   1. 对于选择排序 需要进行交换的次数=n，需要进行比较的次数=(n-1)+(n-2)+(n-3)...3+2+1 = n*(n-1)/2 ~n^2^/2
   2. 对于插入排序 最理想情况下，需要进行n-1次比较&0次交换，最糟糕的情况下（每一个元素都需要挪到最前面），需要进行 1+2+3...+(n-2)+(n-1)次的比较与交换 (～n^2^/2 ) 

1. 作出猜想，基于前面的分析，两个算法的复杂度应该都是O(n**2)，所以性能的差距应该是常数级别。
1. 实验验证



<br>

```java
package prepare;

import define.InsertionSort;
import define.SelectionSort;
import edu.princeton.cs.algs4.StdOut;
import edu.princeton.cs.algs4.StdRandom;
import edu.princeton.cs.algs4.Stopwatch;

public class SortCompare {

    public static double time(String alg, Comparable[] a) {
        Stopwatch timer = new Stopwatch();
        if (alg.equals("Insertion")) InsertionSort.sort(a);
        if (alg.equals("Selection")) SelectionSort.sort(a);
        return timer.elapsedTime();
    }

    //使用算法alg将t个长度为n的数组进行排序
    public static double timeRandomInput(String alg, int n, int t) {
        double total = 0.0;
        Double[] a = new Double[n];
        for (int i = 0; i < t; i++) {
            for (int j = 0; j < n; j++) {
                a[j] = StdRandom.uniformDouble();
            }
            total += time(alg,a);
        }

        return total;
    }

    public static void main(String[] args) {
        String alg1 = args[0];
        String alg2 = args[1];

        int n = Integer.parseInt(args[2]);
        int t = Integer.parseInt(args[3]);

        double t1 = timeRandomInput(alg1, n, t);
        double t2 = timeRandomInput(alg2, n, t);

        StdOut.printf("For %d random Doubles\n %s is ",n,alg1);
        StdOut.printf("%.1f times faster than %s \n",t2/t1,alg2);

    }
}
```

<br>

```shell
prepare % java -classpath /Users/liaozk/devMyself/algorithm/homework/practice/out/production/practice:/Users/liaozk/devMyself/algorithm/library/algs4.jar prepare.SortCompare Insertion Selection 100 1000
```

<br>
最终实验结果表明：

数组的长度越小，插入排序比选择排序快1～2倍

数组的长度越大，插入排序的性能接近选择排序，甚至更慢
<br>

# 4.shell排序

<br>
在学习了前面的两种基础排序后，终于来到了一个稍微高级一点的排序，理解起来很困难，以至于我看到它就会下意识的排斥。
<br>
插入排序，是一个不稳定的排序，其最终耗时与输入数组自身的原始顺序有关，不稳定与比较耗费性能的操作都在于需要一个一个的交换位置直至到了正确的位置。正确的目标位置距当前元素的距离是不可控的。
<br>
希尔排序这是对插入排序的优化，尽可能在运行插入排序之前，将小的元素位置尽量移动的靠前，以大幅减少插入排序时的交换次数。无序 -> 部分有序 -> 最终有序。
<br>
希尔排序的思想：使数组中任意间隔h的元素都是有序的，我们将h由大到小，h比较大，一次交换，就可以移动很远的距离，为更小的h创造有利的条件。
<br>
可以将多个数组进行排列再合并，也可以将元素一次移动h个位置。
<br>
“彻底理解希尔排序的性能至今仍是一种挑战” 
<br>
别说性能了，这代码写出来，即使debug走两遍都很难理解。
<br>

````java
package define;

/**
 * shell 排序
 * 希尔排序是对插入排序的一种优化，我只能讲这么多，因为我只懂这么多😓
 *
 * ftist ：75316942 step：3 couple：[7,1][5,6][3,9] remainder：4，2  result：15376942       下面的实现：[7,1][5,6][3,9][7,4][6,2] result = 12345976
 * second：15376942 step：2 couple：[1,3][5,7][6,4][9,2] remainder: null result:15374269   
 * final: common insertSort result:12345679
 *
 * 希尔排序是对插入排序的优化，那么具体优化的是什么呢？
 * 插入排序的操作是将当前元素与前面已经看过的元素一一比较，插入到合适的位置，如果在非常靠后的位置，出现了特别小的元素，则需要比较的次数会较多
 * 希尔排序的优化即在此，尽可能的将小的元素通过较小的成本，使其靠前。
 *
 * 下面的实现则是 135 & 246 两个数组各自插入排序，然后再插入排序
 * todo 另一种实现 & 递归实现
 * @author liaozk
 */
public class ShellSort {

    public static void sort(Comparable[] a) {
        int N = a.length;
        int h = 1;
        while (h < N/3) h = 3*h + 1;

        while (h >= 1) {
            for (int i = h; i < N ; i++) {
                for (int j = i; j >= h && less(a[j], a[j-h]); j -= h) {
                    exch(a, j, j-h);
                }
            }
            h = h / 3;
        }
    }

    private static boolean less(Comparable a, Comparable b) {
        return a.compareTo(b) < 0;
    }

    private static void exch(Comparable[] a, int i, int j) {
        Comparable t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    public static void main(String[] args) {
        for (int i = 0; i < 100 && i % 2 == 0; i++) {
            System.out.println(i);
        }
    }
}
````

```python
"""
shell 排序，又是一个非常精巧的算法
"""

a = [32,2,7,22,33,11,8,4,14,9,18,5,6]

def shell_sort():
    length = len(a)
    h = 1
    while(h < length/3): 
        h = h*3
    while h >= 1:
        for i in range(h, length):
            for j in range(i,0,-h):
                if (j>=h) and (a[j] < a[j-h]) :
                    a[j],a[j-h] = a[j-h],a[j]
        h = h//3
    



shell_sort()
print(a)
```

注意：这里最开始的h是3的倍数，后续//3 ，假设h=9，后续h=3时，恰好可以完整的遍历完9.





<br>
通过提升速度来解决其他方式无法解决的问题，是研究算法的设计与性能的主要原因之一。
<br>
有经验的程序员有时候会选择希尔排序，因为对于中等大小的数组它的速度是可以接受的，代码也简单，还不需要使用额外的空间，除了对于特别大的n接下来介绍的其他算法速度可能只会比希尔排序快不到两倍，而且更加的复杂。
<br>

# 5.归并排序

<br>
虽然一般都认为快速排序是最好的排序算法，这仅仅只是依赖于我所接触的有限资料而言，并没有实际的论证，但从接触算法（今年六月份）到现在为止，我依旧认为归并排序是最代码实现与性能二者平衡最好的。上面的希尔排序也是十分优雅的，不过有那么一点不好理解。
<br>
归并：将两个有序的数组，归并成一个更大的数组，归并排序的优点在于时间总是NlogN，缺点则在于需要额外的空间N.
<br>
基础操作—归并：

1. 将需要参与归并的两部分的元素，复制到辅助数组中去
2. 在辅助数组中定义两个指针（分别指向各自有序的两个部分的开头）
3. 遍历参与归并的数组，从辅助数组中将值一一按照顺序放进去



Show me code:

```java
public static void merge(Comparable[] a,int lo, int mid, int hi) {
        //i 表示数组左半边的指针
        //j 表示数组右半边的指针
        int i = lo, j = mid + 1;

        //复制到辅助数组
        for (int k = lo; k <= hi; k++) {
            aux[k] = a[k];
        }

        for (int k = lo; k <= hi; k++) {
            if (i > mid) {
                a[k] = aux[j++]; //左边取完了，右边取一个，指针+1
            } else if (j > hi) {
                a[k] = aux[i++]; //右边取完了，左边取一个，指针+1
            } else if (less(aux[i], aux[j])) {
                a[k] = aux[i++]; //取小的那个放过来，并将指针+1
            } else {
                a[k] = aux[j++];
            }
        }

    }
```

<br>
先来看一个自顶向下的归并排序：从上面将数组一直划分到不可分割的地步，再归并回去。
<br>

```java
package define;

/**
 * 归并排序
 *
 * @author liaozk
 */
public class MergeSort {

    private static Comparable[] aux;

    public static void sort(Comparable[] a) {
        sort(a,0, a.length-1);
    }

    public static void sort(Comparable[] a, int lo, int hi) {
        //递归终止条件
        if (lo >= hi) return;
        int mid = lo + (hi - lo)/2;
        sort(a, lo, mid);
        sort(a, mid+1, hi);
        merge(a, lo, mid, hi);
    }

    public static void merge(Comparable[] a,int lo, int mid, int hi) {
        //i 表示数组左半边的指针
        //j 表示数组右半边的指针
        int i = lo, j = mid + 1;

        //复制到辅助数组
        for (int k = lo; k <= hi; k++) {
            aux[k] = a[k];
        }

        for (int k = lo; k <= hi; k++) {
            if (i > mid) {
                a[k] = aux[j++]; //左边取完了，右边取一个，指针+1
            } else if (j > hi) {
                a[k] = aux[i++]; //右边取完了，左边取一个，指针+1
            } else if (less(aux[i], aux[j])) {
                a[k] = aux[i++]; //取小的那个放过来，并将指针+1
            } else {
                a[k] = aux[j++];
            }
        }

    }

    private static boolean less(Comparable a, Comparable b) {
        return a.compareTo(b) < 0;
    }

}
```

```python
a = [3,7,5,1,2,4,6,11,8,9,12,15,14,13]



def merge_sort(a, low, hi):
    if low >= hi :
        return
    midle = low + (hi-low)//2
    merge_sort(a, low, midle)
    merge_sort(a, midle+1,hi)
    merge(a,low, midle, hi)


def merge(a,low,midle,hi):
    b = a[:]
    i, j, k = low, midle+1, low
    while k <= hi :
        if i > midle :
            a[k] = b[j]
            j+=1
        elif j > hi :
            a[k] = b[i]
            i+=1
        elif b[i] < b[j]:
            a[k] = b[i]
            i+=1
        else :
            a[k] = b[j]
            j+=1
        k+=1

print(f"排序前a={a}")
merge_sort(a,0,len(a)-1)
print(f"排序后a={a}")

#不得不承认，这些排序真的很巧妙
```





<br>
自顶向下的归并排序的基本性质分析：
<br>
O(NlogN)：递归的每一层都需要进行N次比较，而递归的深度则是logN
访问数组的次数：6NlogN：复制数组2n，把数组塞回去2n，比较2n
<br>
对于自定向下的归并排序的优化：

1. 对小规模的子数组，使用插入排序。当数组长度小于某个阈值之后，不在递归而是插入排序。
2. 测试数组是否已经有序，添加条件如果a[mid]<a[mid+1]则认为数组已经有序，不再进行merge。
3. 不将元素复制到辅助数组中。？如何实现？不知说云啊！！！



“每一节中我们将书中的每个算法看作某种应用的关键，整体上我们希望学习的是为每种应用找到最合适的算法。我们并不是在推荐读者一定要实现这些改进方法，而是提醒大家不要对算法的初始性能盖棺定论。研究一个新问题，最好的方法是先实现一个你能想到的最简单的方法，当他成为瓶颈的时候再继续改进。实现那些某个常数因子的改进措施可能并不值得。”

<br>
下面我们再来看一个自底向上的归并排序：
<br>
show me code:

```java
package define;

public class MergeSortBU {
    private static Comparable[] aux;

    public static void sort(Comparable[] a) {
        int n = a.length;
        aux = new Comparable[n];
        for (int sz = 1; sz < n; sz *= 2) {//子数组大小1，2，4，8，16...
            for (int lo = 0; lo < n - sz; lo += sz+sz) {//每一次参与merge的数组下标
                merge(a, lo, lo+sz-1, Math.min(lo + sz + sz -1, n-1));
            }
        }
    }

    public static void merge(Comparable[] a,int lo, int mid, int hi) {
        //i 表示数组左半边的指针
        //j 表示数组右半边的指针
        int i = lo, j = mid + 1;

        //复制到辅助数组
        for (int k = lo; k <= hi; k++) {
            aux[k] = a[k];
        }

        for (int k = lo; k <= hi; k++) {
            if (i > mid) {
                a[k] = aux[j++]; //左边取完了，右边取一个，指针+1
            } else if (j > hi) {
                a[k] = aux[i++]; //右边取完了，左边取一个，指针+1
            } else if (less(aux[i], aux[j])) {
                a[k] = aux[i++]; //取小的那个放过来，并将指针+1
            } else {
                a[k] = aux[j++];
            }
        }
    }

    private static boolean less(Comparable a, Comparable b) {
        return a.compareTo(b) < 0;
    }
}
```

<br>
学习归并排序的一个重要原因就是它是证明算法复杂性领域的一个重要结论的基础。计算复杂度的第一步是建立计算模型，对于排序而言我们研究的对象是基于比较的算法，只能通过主键之间的比较来得到关于某个主键的信息。
<br>

# 6.下面是排序算法复杂度的一个重要结论

<br>
任何基于比较的算法，所需要进行的最少的比较次数都是大于等于 lgN! 即~NlgN。
<br>
证明：大小为n的数组，有n！的排列组合，将排序后的结果用叶子结点表示，将每一次的比较作为内部结点来构造二叉树，则二叉树的高度为最糟糕的比较次数。一颗高度为h的完全二叉树，其叶子结点最多为2^h^个。所以：当要有n！个叶子结点时，其二叉树的最小高度为lg(n!) ~nlgn。
<br>
告诉了排序算法的边界，不要尝试去构造小于nlgn的算法。
<br>
基于第六点，归并排序是一种渐进最优的基于比较的排序算法。
<br>
但在实际中，依旧有一些局限：
<br>
1.空间复杂度不是最优的
<br>
2.实践中不一定会遇到最坏的情况
<br>
3.除了比较，其他操作也很重要
<br>
4.不进行比较也能将某些数据排序
<br>

# 7.快速排序

<br>
啊哈，终于来到了传说中20世纪最伟大的十大算法之一的快速排序，最快的排序，在各种类库中屡见不鲜其各种形式的变种（算了，Arrays类里面涉及的各种sort实在太复杂了，不想去了解，留个坑//todo）。
<br>
快速排序的主要优点在于：
<br>
1.原地排序（只需要消耗很小的辅助栈）
<br>
2.所需时间与NlgN成正比
<br>
缺点则在于比较脆弱，稍不注意，性能就会降低到平方级别。
<br>
核心思想：
<br>
选择排序：找到最小的元素，将它放到前面来
<br>
堆排序：利用优先级队列找最大/最小的元素，放到前面来
<br>
插入排序：找到当前元素应该插入到那个位置
<br>
shell排序：插入排序的优化，核心思想与插入排序一致
<br>
归并排序：合并两个排好序的小数组到一个排好序的大的完整数组
<br>
快速排序：一个更陌生的核心思想partitioning：选一个标记然后不停的分区
<br>
快速排序的基本思想：
<br>
选择一个元素，让其左边的元素都不大于它，其右边的元素都不小于它（最终的结果是该元素在数组中找到了正确位置）。
<br>
递归调用，排序该元素左边的数组
<br>
递归调用，排序该元素右边的数组
<br>
而关于如何实现「左边的元素都不大于它，右边的元素都不小于它」呢？
<br>
1.两个指针分别指向数组的两端，然后向中间移动，将扫过的每一个元素都与选中的元素做对比。
<br>
2.满足要求，指针继续移动。
<br>
3.不满足要求，指针停止，只移动另外一个
<br>
4.当两个指针都停止，且指针未交差，则交换两个元素
<br>
5.当指针交差的时候，congratulation，你找到了指定元素的正确位置，将指定元素与其中某个位置交换
<br>
show me code:
<br>

```java
package define;

import edu.princeton.cs.algs4.StdRandom;

public class QuickSort {
    
    public static void sort(Comparable[] a) {
        StdRandom.shuffle(a);

        sort(a,0, a.length-1);
    }

    private static void sort(Comparable[] a, int lo, int hi) {
        if (lo >= hi) return;
        int j = partition(a, lo, hi);
        sort(a, lo, j-1);
        sort(a, j+1, hi);
    }

    private static int partition(Comparable[] a, int lo, int hi) {
        int i = lo, j = hi+1;
        Comparable v = a[lo];

        while (true) {
            while (less(a[++i],v)) {
                if (i == hi) break;
            }

            while (less(v, a[--j])) {
                if (j == lo) break;
            }

            if (i >= j) break;
            exch(a, i, j);
        }
        exch(a,lo, j);//注意--j 此时j已经比i小了
        return j;
    }
    

    private static boolean less(Comparable a, Comparable b) {
        return a.compareTo(b) < 0;
    }

    private static void exch(Comparable[] a, int i, int j) {
        Comparable t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

}
```

```python
a = [3,7,5,1,2,4,6,11,8,9,12,15,14,13]



def quick_sort(a,low,hi):
    if hi <= low : 
        return
    cur = partion(a, low, hi)
    quick_sort(a,low,cur)
    quick_sort(a,cur+1,hi)


def partion(a,low,hi):
    i,j = low+1, hi
    while 1:
        while i <= hi and a[i] < a[low]:
            i += 1

        while j >= low and a[j] > a[low]:
            j -= 1
        
        if j < i:
            break
        a[i],a[j] = a[j],a[i]
    a[low],a[j] = a[j],a[low]
    return j

print(f"before sort a ={a}")
quick_sort(a,0,len(a)-1)
print(f"after sort a = {a}")
```





<br>
上述代码已经考虑了数组中出现重复元素的情况，虽然会造成一点不必要的性能损失。
<br>
快速排序对随机性的要求比较高，假如我们选择的是一个有序的数组，每次选中用于分区的元素都是最左边的元素，则需要比较的次数为：n-1+n-2+...+2 ~N**2
<br>
若是每次都能保证平均切分，则比较次数 ～NlgN
<br>
对于快速排序的经验改进：
<br>
1.数组小到一定程度，切换到插入排序
<br>

```java
if (hi <= lo + m) {
 InsertSort.sort(a, lo, hi);
  return;
}
```

<br>
2.随机性既可以通过打乱数组来保证，也可以通过随机取样来保证，选择一部分元素的中位数来切分数组，一般效果也是不错，但代价则是需要计算中位数，经验表明，选三个元素，使用居中的来切分效果最好，并可以将取样元素作为哨兵放到数组末尾，来取消partition中的边界判断。
<br>
show me code：
<br>
已经20:41了，今晚注定不能写完了，不过这会儿感觉到来了，感觉应该到金拱门里继续的。但今晚还是算了，正好明天跟三向切分与部分习题一起。
<br>

````java
package define;

import edu.princeton.cs.algs4.StdRandom;

import java.util.Arrays;

public class QuickSortOptimal {

    public static void sort(Comparable[] a) {
        StdRandom.shuffle(a);

        sort(a,0, a.length-1);
    }

    private static void sort(Comparable[] a, int lo, int hi) {
        if (lo >= hi) return;
        int j = partition(a, lo, hi);
        sort(a, lo, j-1);
        sort(a, j+1, hi);
    }

    private static int partition(Comparable[] a, int lo, int hi) {
        processMid(a, lo, hi);
        if ((hi - lo) < 2) return hi; //只有两个元素，前面就已经交换了，这里不用再进来了
        int i = lo-1, j = hi;
        Comparable v = a[j];

        while (true) {
            while (less(a[++i],v));//因为此时v就是最末尾的那个，所以可以取消边界判断，即大到等于v

            while (less(v, a[--j]));//因为我们已经将中位数与末尾的数据进行了交换，所以开头的三个数中一定有一个小于等于v，所以边界可以取消

            if (i >= j) break;
            exch(a, i, j);
        }
        exch(a, i, hi);//因为是与数组的最后一个元素进行交换所有要选i
        return i;
    }

    private static boolean less(Comparable a, Comparable b) {
        return a.compareTo(b) < 0;
    }

    private static void exch(Comparable[] a, int i, int j) {
        Comparable t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    //取数组的前三位来参与计算获取中位数，并将中位数放置在数组末尾
    private static void processMid(Comparable[] a, int lo, int hi) {
        if ((hi - lo) < 2) { //将大的那个移动到末尾，确保前面有数据可以小于所选元素
            if (less(a[hi],a[lo])) {
                exch(a,hi,lo);
            }
        } else {
            if ((a[lo].compareTo(a[lo+1]))*(a[lo].compareTo(a[lo+2])) < 0) {
                exch(a, lo, hi);
            } else if ((a[lo+1].compareTo(a[lo])*(a[lo+1].compareTo(a[lo+2]))) < 0) {
                exch(a, lo+1, hi);
            } else {
                exch(a, lo+2, hi);
            }
        }

    }

    public static void main(String[] args) {
        Integer[] a = new Integer[]{1,2,3,4,5,7,8,6};
        sort(a);
        System.out.println(Arrays.toString(a));
    }

}
````

<br>
一定要进行疯狂的测试，今晚本来计划搞定快速排序的，结果测试加修改足足改了一个多小时...
<br>
测试的结果就是，对100万的数组，10次排序，优化后的基本1.1～1.2倍于原来的。
<br>
3.熵最优的排序
<br>
可以简单将其理解为尽可能的减少不必要的比较，主要针对的则是数组中出现大量的重复值，如果递归到一个小数组全部都是重复值的时候，完全没有必要再进行递归&比较排序。为了优化这种情况，采取的措施便是将数组切分为三部分（大于，小于，等于）。
<br>
前面说过排序算法的极限～NlgN，归并排序似乎已经是最优解了，为何经验上快速排序一般优于归并排序呢？（额，我没有去实践过）因为NlgN是任意输入的最差情况，如果数组中包含了任意概率的重复值，归并排序无法保证最佳性能。
<br>
简单的三向切分原理：
<br>
从左到右遍历数组，维护一个指针 lt 使得a[lo ... lt-1]的元素都小于v，一个指针 gt 使得a[gt+1 ... hi]中的元素都大于v，一个指针 i 使的a[lt ... i-1]中的元素都等于v，a[i ... gt]中的元素则都还未确定。
<br>
a[i] 小于 v ，将 a[lt] 与 a[i] 交换，lt+1 ，i+1
<br>
a[i] 大于 v ，将 a[hi] 与 a[i] 交换，hi-1
<br>
a[i] 等于 v ，将i+1
<br>
i 大于 gt 的时候，遍历结束
<br>
show me code
<br>

````java
package define;

import edu.princeton.cs.algs4.StdRandom;

public class QuickSort3Way {

    public static void sort(Comparable[] a) {
        StdRandom.shuffle(a);
        sort(a,0, a.length - 1);
    }

    private static void sort(Comparable[] a, int lo, int hi) {
        //1.递归终止条件
        if (lo >= hi) return;
        //2.初始化三个指针
        int lt = lo, gt = hi, i = lo + 1;
        //3.获取要排序的元素
        Comparable v = a[lo];
        //4.循环中遍历数组，并处理
        while (i <= gt) {
            int cmp = a[i].compareTo(v);
            if (cmp < 0) {
                exch(a, i++, lt++);
            } else if (cmp > 0) {
                exch(a, i, gt--);
            } else {
                i++;
            }
        }
        //5.排除相等的元素，将更大&更小的那部分继续排序
        sort(a, lo, lt-1);
        sort(a,gt+1, hi);
    }

    private static void exch(Comparable[] a, int i, int j) {
        Comparable t = a[i];
        a[i] = a[j];
        a[j] = t;
    }
}
````

<br>
因为三分中涉及大量的交换，如果数组没有重复的数据很少，比一般的快速排序会更慢，对于全随机的数组，简单测试的结果是普通快速排序1.5倍于三分快速排序。
<br>
 算法p190 涉及香农信息量 todo...
<br>
4.对三向切分还能再优化吗？有一个快速三向切分的算法。不过似乎今晚人并不能兴奋起来，不想处理这个了，展示跳过 todo...
<br>

# 8.优先队列

<br>
因排序而产生的一个数据结构，当面对“最大的十个元素”这样的需求时，我们没有必要将全部元素排好序再取其中最大的十个。只需要一种数据结构，在这个数据结构中，保存的十个元素永远是当前已遍历的所有元素中最大的十个。
<br>
Priority Queue only trace smallest element;
<br>
用法之一：节约内存，需要最大的5个，永远只需要5个位置，存放对象地址即可。
<br>
Min-Heap:
<br>
Children >=father
<br>
Complete 
<br>
（一种可能的实现）新增/删除 都基于这两点，新增：新增到末尾，再移动；删除：先删除root，末尾那一个移过去，再移动到正确位置。
<br>

## 1.定义api

<br>

```java
package define;

/**
 * 优先队列的api
 *
 * @param <Key>
 *
 * @author liaozk
 */
public interface MaxPQ <Key extends Comparable<Key>>{

    void insert(Key v);//向优先队列中插入元素

    Key max();//返回最大的元素

    Key delMax();//删除队列中最大的元素

    boolean isEmpty();//返回队列是否为空

    int size();//返回优先队列中的元素个数

}
```

<br>

## 2.简单实现

<br>
有序数组+插入时排序：这样每次插入复杂度n，删除复杂度1。
<br>
无序数组+删除时排序：每次插入复杂度1，删除复杂度n。
<br>
heap 数据结构堆：插入于与删除的复杂度都是lgn。
<br>

## 3.堆数据结构

<br>
介绍堆就需要先介绍完全二叉树，而完全二叉树又是基于二叉树，树来的。关于树的相关描述我倾向于放在接下来的查找算法中（与书本保持一致）。
<br>
实现树的几种方式：
<br>
Approach1:key+pointer，key+pointer[]，key+subpointer+brotherpointer;
<br>
Approach2:key[]+parents[]. Like disjoinSets;
<br>
Approach3:assume full tree, just key[],ignore parent[]；parent=(n-1)/2;left=?;right=?
<br>
那么堆又该怎么描述呢？基于完全二叉树的数学特性，所谓的堆 heap 这一数据结构就是，用数组表示的完全二叉树。
<br>
「在二叉堆中，每个父节点都要大于等于两个子结点！」
<br>
其数学特性则是：从1作为根结点开始，k的上一层就是k/2（注意：程序中此处会向下取整），k的下一层则是2k / 2k+1 。
<br>
什么？你要看代码？ 本体就是[]，over，其他功能的实现放在下面的算法中。
<br>

## 4.堆数据结构涉及的算法

<br>
因为堆，这一数据结构自身的定义，决定了其父节点一定不会小于子结点，如果子节点比父节点大，那么：
<br>
1.子节点与父节点交换位置（原来的子节点<父节点<新的这个子节点）
<br>
2.交换过后可能比其父节点还要大，则继续交换
<br>
则一步骤的不停重复 统称为 “上浮”。
<br>
show me code：
<br>

````java
private void swim(int k) {
  while (k > 1 && less(k/2, k)) {
    exch(k/2, k);
    k = k/2;
  }
}
````

<br>
上浮是将底层的元素方法合适的位置，这里假设的是堆的有序状态被破坏是因为，某个节点变得比其父节点大。如果被破坏的原因是某个节点变得比其父节点小，则需要：
<br>
1.将该节点与两个子节点中较大的一个进行交换。（因为要确保父节点不小于子节点，所以是与较大的那个进行交换）
<br>
2.交换后其可能比其子节点还是小，则继续交换至合适的位置
<br>
上述操作称之为下沉
<br>
show me code：
<br>

```java
private void sink(int k) {
  while (2*k <= N) {//没有超数组长度
    int j = 2*k;//child node
    if (j < N && less(j, j+1)) j += 1;//取子节点中较大的一个
    if (!less(k, j)) break; //k 不再小于其子节点中较大的一个
    //走到这里来了，说明，子节点还在数组长度内，且父节点小于较大的子节点
    exch(k, j);
    k = j;
  }
}
```

<br>
插入：增加数组的大小，将新元素插入到末尾，再上浮到合适的位置。
<br>
删除：删掉root，将末尾元素移动过去，再下沉到合适的位置。
<br>
基于堆的优先队列实现：

````java
package define;

public class MaxPriorityQueue<Key extends Comparable<Key>> implements MaxPQ<Key>{

    private Key[] pq;
    private int N = 0;

    public MaxPriorityQueue(int max) {
        this.pq = (Key[]) new Comparable[max + 1];
    }

    @Override
    public void insert(Key v) {
        pq[++N] = v;
        swim(N);
    }

    @Override
    public Key max() {
        return pq[1];
    }

    @Override
    public Key delMax() {
        Key max = pq[1];
        pq[1] = null;
        exch(N--, 1);
        sink(1);
        return max;
    }

    @Override
    public boolean isEmpty() {
        return N == 0;
    }

    @Override
    public int size() {
        return N;
    }

    private  boolean less(int a, int b) {
        return pq[a].compareTo(pq[b]) < 0;
    }

    private  void exch(int i, int j) {
        Key t = pq[i];
        pq[i] = pq[j];
        pq[j] = t;
    }

    private void swim(int k) {
        while (k > 1 && less(k/2, k)) {
            exch(k/2, k);
            k = k/2;
        }
    }

    private void sink(int k) {
        while (2*k <= N) {//没有超数组长度
            int j = 2*k;//child node
            if (j < N && less(j, j+1)) j += 1;//取子节点中较大的一个
            if (!less(k, j)) break; //k 不再小于其子节点中较大的一个
            //走到这里来了，说明，子节点还在数组长度内，且父节点小于较大的子节点
            exch(k, j);
            k = j;
        }
    }

}
````

<br>
基于堆的优先队列带来的性能：
<br>
1.插入操作 lgN
<br>
2.删除操作 2lgN
<br>

## 5.索引优先队列

<br>
在上面构造的最大堆优先级队列，暴露给外面的只有最大的那个元素，实际代码中允许用例引用已经进入了队列中的元素是非常有必要的，so 一个新的数据结构又来了：索引优先队列
<br>
1.api
<br>

```java
package define;

public interface IndexMinPQ<Item extends Comparable<Item>> {
    
    /** 插入一个元素并将其与索引k相关联  */
    void insert(int k, Item item);
    
    /** 将索引k的元素设置为item  */
    void change(int k, Item item);
    
    /**  是否存在索引为k的元素 */
    boolean contains(int k);
    
    /** 删去索引k及其相关联的元素  */
    void delete(int k);
    
    /** 返回最小的元素  */
    Item min();
    
    /** 返回最小元素的索引  */
    int minIndex();
    
    /** 删除最小的元素并返回其索引 */
    int delMin();
    
    boolean  isEmpty();
    
    boolean size();
    
}

```

<br>
通过对索引优先队列的api定义，似乎依旧还是对接下来将要创造的东西不甚了解，索引在这一数据结构中扮演的是什么？它又是如何实现的？
<br>

````java
package define;

public class IndexMinPriorityQueue<Item extends Comparable<Item>> implements IndexMinPQ<Item> {

    private int N;//队列中元素的数量
    private int[] pq;//堆结构的保存地,保存的处于该位置的元素在数组items中的索引
    private int[] qp;//保存的是items中的索引在堆结构中的索引。如果items中的某个索引对应的元素不存在于堆中则qp中其保存的值 = -1
    private Item[] items;//最终保存元素的位置。

    public IndexMinPriorityQueue(int maxN) {
        this.items = (Item[]) new Comparable[maxN + 1];
        this.pq = new int[maxN + 1];
        this.qp = new int[maxN + 1];
        for (int i = 0; i <= maxN; i++) qp[i] = -1;
    }

    @Override
    public void insert(int k, Item item) {
        N++;
        qp[k] = N;
        pq[N] = k;
        items[k] = item;
        swim(N);
    }

    @Override
    public void change(int k, Item item) {
        items[k] = item;
        swim(qp[k]);
        sink(qp[k]);
    }

    @Override
    public boolean contains(int k) {
        return qp[k] != -1;
    }

    @Override
    public void delete(int k) {
        int index = qp[k];
        exch(index, N--);
        swim(index);
        sink(index);
        qp[k] = -1;
    }

    @Override
    public Item min() {
        return items[pq[1]];
    }

    @Override
    public int minIndex() {
        return pq[1];
    }

    @Override
    public int delMin() {
        int minIndex = pq[1];
        exch(N--,1);
        sink(1);
        items[pq[N++]] = null;
        qp[pq[N++]] = -1;
        return minIndex;
    }

    @Override
    public boolean isEmpty() {
        return N == 0;
    }

    @Override
    public int size() {
        return N;
    }

    private void swim(int k) {
        while (k > 1 && less(k/2, k)) {
            exch(k/2, k);
            k = k/2;
        }
    }

    private void sink(int k) {
        while (2*k <= N) {//没有超数组长度
            int j = 2*k;//child node
            if (j < N && less(j, j+1)) j += 1;//取子节点中较大的一个
            if (!less(k, j)) break; //k 不再小于其子节点中较大的一个
            //走到这里来了，说明，子节点还在数组长度内，且父节点小于较大的子节点
            exch(k, j);
            k = j;
        }
    }

    private  boolean less(int a, int b) {
        return items[pq[a]].compareTo(items[pq[b]]) > 0;//因为现在是最小堆了，所以比较方法需要修改
    }

    private  void exch(int i, int j) {
        int t = pq[i];
        pq[i] = pq[j];
        pq[j] = t;
    }
}
<br>
````

<br>
一个很经典的案例（至少我是看了好一阵子才明白😳）：
<br>

````java
package define;

import edu.princeton.cs.algs4.In;
import edu.princeton.cs.algs4.StdOut;

public class Multiway {

    public static void main(String[] args) {
        int N = args.length;
        In[] streams = new In[N];
        for (int i = 0; i < N; i++) {
            streams[i] = new In(args[i]);
        }
        merge(streams);
    }

    public static void merge(In[] streams) {
        int N = streams.length;
        IndexMinPriorityQueue<String> pq = new IndexMinPriorityQueue<>(N);
        for (int i = 0; i < N; i++) {
            if (!streams[i].isEmpty()) {
                pq.insert(i, streams[i].readString());
            }
        }

        while (!pq.isEmpty()) {
            StdOut.println(pq.min());
            int i = pq.delMin();
            if (!streams[i].isEmpty()) {
                pq.insert(i,streams[i].readString());
            }
        }
    }
}
````

<br>

## 6.堆排序

<br>
将所有元素插入一个查找最小元素的优先队列，然后依次删除最小的元素从而得到有排序。如果用无序的数组来实现这个优先队列的话，上述操作，可以理解为选择排序。如果用有序的堆来实现这个优先级队列的话，一个全新的排序诞生了：堆排序！
<br>
现在比较困顿，也一直比较油，关于堆排序，暂时先跳过。与其稀里糊涂的浪费时间，不如开把游戏。

<br>