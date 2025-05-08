---
layout: post
category: jvm
---



# Garbage Collector

每一个javaer 第一次开始写c代码时就会感受到jvm一直以来的默默付出。

where：heap ；方法区中的信息也有可能被回收，但条件非常严格。

why：厨房的垃圾桶，永远都不够大

what：无法被引用的对象

when：

how：





## What如何找到那些无法被引用的对象

1.引用计数 - 无法解决循环引用

2.可达性分析：从gc root出发，无法到达的对象就应该被回收



### GCroot

略：从各类引用出发

那什麼是GC Root呢 下列的人選都是我們的GC Root

1.JVM棧楨裡的局部變量 指到的對象: 理所當然 棧楨還在 那局部變量指到的都不應該被回收

2.方法區裡的static變數 指到的對象: 理所當然 類別等級的變量 即使沒有任何的實例 只要方法區的類別沒有被回收 那方法區中跟這個類別相關的靜態變數都不該被清理 因為那些靜態變數已經被加載了 代表隨時可能會用到

3.系統類別(System Class): 也就是被啟動類加載器加載的類別 比如說`rt.jar`(Runtime enviroment) 或是`java.util.*`等類別

4.本地方法棧中的本地方法所引用的對象: 也是理所當然 人家棧裡面的東西都不該清掉 包含了本地方法的局部變數跟全局變量

5.線程(Thread): 所有正在跑的線程以及這些線程所指到的物件

6.在finalizer queue裡面 還沒跑finalizer方法的物件

被這些指到的 都是菁英中心裡面的人 沒被指到的就是垃圾



### 引用

强

软：即将溢出时，这类引用也会被清理掉

弱：下一次gc会被清理

虚：跟没引用一样

Reference Queue





## how 回收

1.Mark-Sweep标记并清除

<img src="https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250507164304708.png" alt="image-20250507164304708" style="zoom:33%;" />

碎片多

2.Mark-Compact标记并整理

<img src="https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250507164953553.png" alt="image-20250507164953553" style="zoom:33%;" />

整理耗时，适合垃圾少

3.Mark-Copy

<img src="https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250507165058672.png" alt="image-20250507165058672" style="zoom:33%;" />

需要额外空间，适合垃圾多





杂糅：

<img src="https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250507165448259.png" alt="image-20250507165448259" style="zoom:33%;" />



再來就剩下一些細節

1.新大區又稱為伊甸園區(Eden) 新小1稱為Survivor1 新小2稱為Survivor2

2.伊甸區跟Survivor區的比例可以在跑程式的時候自己給定 `-Xx:SurvivorRatio`

3.在Survivor區存活幾次才進老年代的次數也可以自己給定 `-XX:MaxTenuringThreshold`

4.如果有個對象太大根本無法在伊甸區分配空間 那就直接在老年代分配

5.如果有對象太大 無法在Minor GC之後放進Survivor區 那也是直接進老年代



override 了 finalize 会给对象一次缓行，第一次GC时，发现有finalize 且未被执行，会缓行，下次GC再回首。



元空间（方法区）的回收，略过。

