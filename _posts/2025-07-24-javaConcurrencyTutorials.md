---
layout: post
title: java并发
category: 计算机
---


java concurrency

大部分内容均整理自 https://docs.oracle.com/javase/tutorial/essential/concurrency/index.html

<br>

<br>

# Processes and Threads

线程与进程的概念，没什么好介绍的

Most implementations of the Java virtual machine run as a single process. A Java application can create additional processes using a [`ProcessBuilder`](https://docs.oracle.com/javase/8/docs/api/java/lang/ProcessBuilder.html) object

<br>

<br>

# Thread Objects

一般情况我们创建的线程都与[`Thread`](https://docs.oracle.com/javase/8/docs/api/java/lang/Thread.html) 关联

构建并发应用的两种策略：

- To directly control thread creation and management, simply instantiate `Thread` each time the application needs to initiate an asynchronous task.
- To abstract thread management from the rest of your application, pass the application's tasks to an *executor*（[high-level concurrency objects](https://docs.oracle.com/javase/tutorial/essential/concurrency/highlevel.html)）.



<br>

<br>



## Defining and Starting a Thread

AI生成的太快了..

```java
package org.example;

public class HelloRunnable implements Runnable{

    @Override
    public void run() {
        System.out.println("Hello from a thread!");
    }

    public static void main(String[] args) {
        (new Thread(new HelloRunnable())).start();
        (new Thread(new HelloRunnable())).start();
        (new Thread(new HelloRunnable())).start();
    }
}

```

```java
package org.example;

public class HelloThread extends  Thread {

    @Override
    public void run() {
        System.out.println("Hello from a thread!");
    }

    public static void main(String[] args) {
        (new HelloThread()).start();
        (new HelloThread()).start();
        (new HelloThread()).start();
    }
}
```



一般使用runnable：Not only is this approach more flexible, but it is applicable to the high-level thread management APIs covered later.

<br>

<br>



## Pausing Execution with Sleep

sleep(1000) 相当于告诉OS，我这个线程先睡1000毫秒，你把我从等待队列中移除，1000ms后再加入。

1. 对于程序来说sleep是立即生效的
2. sleep的线程不占用cpu时间片
3. 正在sleep的线程遭遇interrupt会抛出异常。（意味可以用interrupt唤醒sleep）



<br>

<br>

## Interrupts

中断只是一个信号，告诉程序，需要停止当前正在做的。不是强制终止程序，对中断的响应，一般由程序自己控制。

1. 诸如sleep/wait/join之类的方法，检测到中断会InterruptedException
2. 长时间运行但不调用上述方法，是不会响应中断的需要Thread.interrupted()
3. interrupted，会clear flag，isInterrupted 只查询不clear，throw InterruptedException 也会clear



<br>

<br>

## Join

like pthread`s join

<br>

<br>

# Synchronization



进程之间的通信，可以有管道，信号，socket等等

线程之间的通信，主要要是 共享内存。This form of communication is extremely efficient, but makes two kinds of errors possible: *thread interference* and *memory consistency errors*

<br>

<br>



## Thread Interference

主要涉及到原子性

```java
class Counter {
    private int c = 0;

    public void increment() {
        c++;
    }

    public void decrement() {
        c--;
    }

    public int value() {
        return c;
    }

}

```



> single expression `c++` can be decomposed into three steps:
>
> 1. Retrieve the current value of `c`.
> 2. Increment the retrieved value by 1.
> 3. Store the incremented value back in `c`.
>
> 
>
> Suppose Thread A invokes `increment` at about the same time Thread B invokes `decrement`. If the initial value of `c` is `0`, their interleaved actions might follow this sequence:
>
> 1. Thread A: Retrieve c.
> 2. Thread B: Retrieve c.
> 3. Thread A: Increment retrieved value; result is 1.
> 4. Thread B: Decrement retrieved value; result is -1.
> 5. Thread A: Store result in c; c is now 1.
> 6. Thread B: Store result in c; c is now -1.
>
> Thread A's result is lost, overwritten by Thread B. This particular interleaving is only one possibility. Under different circumstances it might be Thread B's result that gets lost, or there could be no error at all. Because they are unpredictable, thread interference bugs can be difficult to detect and fix.



<br>

<br>

## Memory Consistency Errors

涉及可见性

java提供了编程语言层级的一致性保证：happen-before，不过此时我并不想去了解🫠

For a list of actions that create happens-before relationships, refer to the [Summary page of the `java.util.concurrent` package.](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/package-summary.html#MemoryVisibility).



<br>

<br>

## Synchronized Methods

java 提供了两个同步原语：*synchronized methods* and *synchronized statements*

方法层级的synchronized 实际锁的是instance，故即使thread1 调用的synchronized A，thread2 也不能调用synchronized B。

方法层级的synchronized有两个作用：

1. 锁
2. happen-before， it automatically establishes a happens-before relationship with *any subsequent invocation* of a synchronized method for the same object

注意：`final` 修饰的 fields 是不可变的，可以被非synchronized修饰的方法，正确的读取

注意：synchronized修饰构造方法，没有意义。不要在构造函数中将 `this` 引用暴露给外部（如加入全局列表），否则其他线程可能访问到一个尚未构造完成的对象，导致线程安全问题。

<br>

<br>





## Intrinsic Locks and Synchronization



这部分主要讲了java的synchronize的实现，*monitor lock*  锁的是每个对象的对象头。[more detail](https://liaozk.wiki/%E5%B9%B6%E5%8F%91.html#java%E4%B8%AD%E7%9A%84%E9%94%81) & [more more detail](https://liaozk.wiki/jvm%E5%B1%9E%E4%BA%8Ejava%E7%9A%84%E7%8A%B6%E6%80%81%E6%9C%BA.html#object-in-heap)

因为synchronize是通过监视器锁来保证互斥的，故 synchronize释放锁时也就隐式的建立了一个happen-before：When a thread releases an intrinsic lock, a happens-before relationship is established between that action and any subsequent acquisition of the same lock.



最后强调了：Reentrant ，synchronize 是可重入的。



<br>

<br>

## <span id=Atomic Access>Atomic Access</span>



- Reads and writes are atomic for reference variables and for most primitive variables (all types except `long` and `double`).
- Reads and writes are atomic for *all* variables declared `volatile` (*including* `long` and `double` variables).



volatile解决了可见性，但没有解决原子性，i++ 依然会出错。



<br>

<br>

# Liveness

活跃性：A concurrent application's ability to execute in a timely manner is known as its *liveness* 程序能及时的执行下去

<br>

<br>

## Deadlock

<br>

<br>

## Starvation and Livelock

活锁：你靠右让行，对向靠左让行🤣

<br>

<br>

# Guarded Blocks

保护块

本节讲了synchronize 的管程模型中隐藏的条件队列。通过obj.wait() obj.notifyAll()控制

一个基于synchronize 的生产者-消费者模型：[prod-consum](https://docs.oracle.com/javase/tutorial/essential/concurrency/guardmeth.html)

<br>

<br>

# Immutable Objects

因为对象的不可变，不存在 线程扰乱 & 状态不一致

一般程序员不太愿意使用不可变对象，因为过分担心创建一个新对象的开销大于修改，事实上，开销会被效率抵消：不需要同步，减少了GC的分析

即使我们给每个方法都加上了synchronize ，依然无法避免调用时先A后B，导致不一致，只能在外面调用处加锁：
```java
synchronized (color) {
    int myColorInt = color.getRGB();
    String myColorName = color.getName();
} 
```



非常有趣的一个方法，之前几乎没看到有地方提及。 [更多细节](https://docs.oracle.com/javase/tutorial/essential/concurrency/syncrgb.html)



<br>

<br>

# High Level Concurrency Objects

<br>



## Lock Objects

简单的介绍了下ReentrantLock，并演示了如何使用ReentrantLock，来避免前面的bow死锁：bow前，先试图获取两把锁，只成功了一把，就放弃另一把。

<br>

## Executors

it makes sense to separate thread management and creation from the rest of the application

<br>

### Executor Interfaces

- `Executor`, a simple interface that supports launching new tasks.基础
- `ExecutorService`, a subinterface of `Executor`, which adds features that help manage the life cycle, both of the individual tasks and of the executor itself.新增了周期管理
- `ScheduledExecutorService`, a subinterface of `ExecutorService`, supports future and/or periodic execution of tasks.继续新增了任务调度

<br>

### Thread Pools

线程池

<br>

### Fork/Join 

ForkJoinTask

有意思：*work-stealing* algorithm，可以从其他繁忙的线程中偷任务！[详情](https://docs.oracle.com/javase/tutorial/essential/concurrency/forkjoin.html)

每个线程有自己的队列，队列没东西了就从其他队列中偷

[`ForkBlur`](https://docs.oracle.com/javase/tutorial/essential/concurrency/examples/ForkBlur.java) 

```java
import java.util.concurrent.RecursiveTask;
import java.util.concurrent.ForkJoinPool;

public class SumTask extends RecursiveTask<Long> {
    private static final int THRESHOLD = 1000;  // 任务拆分阈值
    private final long start;
    private final long end;

    public SumTask(long start, long end) {
        this.start = start;
        this.end = end;
    }

    @Override
    protected Long compute() {
        if (end - start <= THRESHOLD) {
            // 小任务：直接计算
            long sum = 0;
            for (long i = start; i <= end; i++) {
                sum += i;
            }
            return sum;
        } else {
            // 大任务：拆分
            long mid = (start + end) / 2;
            SumTask left = new SumTask(start, mid);
            SumTask right = new SumTask(mid + 1, end);

            left.fork();  // 异步执行左任务
            right.fork(); // 异步执行右任务

            return left.join() + right.join();  // 等待并合并结果
        }
    }

    public static void main(String[] args) {
        ForkJoinPool pool = ForkJoinPool.commonPool();
        SumTask task = new SumTask(1, 1_000_000);
        long result = pool.invoke(task);
        System.out.println("Sum: " + result);
    }
}
```



<br>

<br>



## Concurrent Collections

并发集合

- [`BlockingQueue`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/BlockingQueue.html) defines a first-in-first-out data structure that blocks or times out when you attempt to add to a full queue, or retrieve from an empty queue.
- [`ConcurrentMap`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ConcurrentMap.html) is a subinterface of [`java.util.Map`](https://docs.oracle.com/javase/8/docs/api/java/util/Map.html) that defines useful atomic operations. These operations remove or replace a key-value pair only if the key is present, or add a key-value pair only if the key is absent. Making these operations atomic helps avoid synchronization. The standard general-purpose implementation of `ConcurrentMap` is [`ConcurrentHashMap`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ConcurrentHashMap.html), which is a concurrent analog of [`HashMap`](https://docs.oracle.com/javase/8/docs/api/java/util/HashMap.html).
- [`ConcurrentNavigableMap`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ConcurrentNavigableMap.html) is a subinterface of `ConcurrentMap` that supports approximate matches. The standard general-purpose implementation of `ConcurrentNavigableMap` is [`ConcurrentSkipListMap`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ConcurrentSkipListMap.html), which is a concurrent analog of [`TreeMap`](https://docs.oracle.com/javase/8/docs/api/java/util/TreeMap.html).



<br>

<br>

## Atomic Variables

虽然[前面](#Atomic Access)已经介绍过原子性访问（volatile），但对数据的操作依旧不是原子性的，i++依然存在，线程干扰。

atomic 不仅提供了内存一致性，也提供了原子性，同时也包含了happen-before

<br>

<br>

## Concurrent Random Numbers

```java
int r = ThreadLocalRandom.current() .nextInt(4, 77);
```

<br>

<br>



[习题](https://docs.oracle.com/javase/tutorial/essential/concurrency/QandE/questions.html) 有点意思，只有happen-before可以提供保证

<img src="https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/java%20concurrency.png" alt="java concurrency" style="zoom:50%;" />



<br>

<br>

# Memory model

[JSR-133 FAQ ](https://www.cs.umd.edu/~pugh/java/memoryModel/jsr-133-faq.html?spm=a2ty_o01.29997173.0.0.37d7c921QuFSU6) 暂时不去了解

<br>

<br>



# 并发工具类

## 线程池

ExecutorService

相比于Executor 多了线程周期管理相关的api

ThreadPoolExecutor

在当前生产环境偶尔能看见：

```java
ExecutorService ex = Executors.newFixedThreadPool(2)
```

点进去

```java
    /**
     * Creates a thread pool that reuses a fixed number of threads
     * operating off a shared unbounded queue.  At any point, at most
     * {@code nThreads} threads will be active processing tasks.
     * If additional tasks are submitted when all threads are active,
     * they will wait in the queue until a thread is available.
     * If any thread terminates due to a failure during execution
     * prior to shutdown, a new one will take its place if needed to
     * execute subsequent tasks.  The threads in the pool will exist
     * until it is explicitly {@link ExecutorService#shutdown shutdown}.
     *
     * @param nThreads the number of threads in the pool
     * @return the newly created thread pool
     * @throws IllegalArgumentException if {@code nThreads <= 0}
     */
    public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
    }
```



再点进去

```java
    /**
     * Creates a new {@code ThreadPoolExecutor} with the given initial
     * parameters, the
     * {@linkplain Executors#defaultThreadFactory default thread factory}
     * and the {@linkplain ThreadPoolExecutor.AbortPolicy
     * default rejected execution handler}.
     *
     * <p>It may be more convenient to use one of the {@link Executors}
     * factory methods instead of this general purpose constructor.
     *
     * @param corePoolSize the number of threads to keep in the pool, even
     *        if they are idle, unless {@code allowCoreThreadTimeOut} is set
     * @param maximumPoolSize the maximum number of threads to allow in the
     *        pool
     * @param keepAliveTime when the number of threads is greater than
     *        the core, this is the maximum time that excess idle threads
     *        will wait for new tasks before terminating.
     * @param unit the time unit for the {@code keepAliveTime} argument
     * @param workQueue the queue to use for holding tasks before they are
     *        executed.  This queue will hold only the {@code Runnable}
     *        tasks submitted by the {@code execute} method.
     * @throws IllegalArgumentException if one of the following holds:<br>
     *         {@code corePoolSize < 0}<br>
     *         {@code keepAliveTime < 0}<br>
     *         {@code maximumPoolSize <= 0}<br>
     *         {@code maximumPoolSize < corePoolSize}
     * @throws NullPointerException if {@code workQueue} is null
     */
    public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue) {
        this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue,
             Executors.defaultThreadFactory(), defaultHandler);
    }
```

代码解释了创建线程池的常用参数。



ForkJoinPool

<br>

<br>



## 同步工具

CountDownLatch

就不给出示例了，理解为join的增强版。join的使用需要Thread对象，但实际业务中多用线程池，所以就在每个线程countDown，主线程await。

<br>

CyclicBarrier

简单理解为自动重置的CountDownLatch

<br>

Semaphore

信号量

[基于锁、条件变量、信号量分别实现生产者-消费者](https://liaozk.wiki/%E5%B9%B6%E5%8F%91.html#%E5%90%8C%E6%AD%A5)

<br>

Phaser

比CyclicBarrier更灵活，允许多个线程，多个阶段同步。例如：1、2、3 阶段1；2、3、4阶段2；4、5、6阶段3.



<br>

## 并发集合

ConcurrentHashMap

直接在hashMap上给所有方法添加synchronize（Collections.synchronizedMap()），可以实现线程安全，当时代价是每次操作都要锁住instance。

ConcurrentHashMap，的线程安全原理：

1. CAS
   1. 初始化时，通过sizeCtl 进行compareAndSwapInt，避免锁住整个类（此时对象还不存在）
   2. hash数组为null时，直接设置桶，（此时node还是null，避免锁住instance）
2. volatile：确保一个线程对node的修改，其他线程可见
3. synchronize：桶级别的锁，hash冲突时才涉及抢占



<br>

CopyOnWriteArrayList

读不加锁。

写时，先获取锁，然后copy并添加新的元素，所有的写操作需要竞争同一把锁。

弱一致性，看到的可能是旧快照。



<br>

BlockingQueue

具体有好几种实现，基本的线程安全原理可以理解为：

ReentrantLock + 2个condition

enqueue 需要condition notFull满足

dequeue 需要condition notEmpty满足

因为java的设计，这两个condition都需要获取同一把lock



<br>

<br>



# 一些原理

## AQS

太...太..太.. 复杂了（来自图库的认证🤣）

![image-20250724160807052](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250724160807052.png)

xchg实现原子交换

futex实现线程的挂起or唤醒

然后才是AQS的抽象

![image-20250724161023225](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250724161023225.png)

![image-20250724161140698](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250724161140698.png)

至此可以简单的理解为

发现了一个惊人的秘密？：java的所有互斥实际都是在os提供的锁基础上实现的🤔（伟大的抽象，从cpu到assembly到os再到java再到synchronize，最后到业务代码...）

OS底层同步原语（如 futex、mutex、condition variable 等）来实现阻塞和唤醒。在这一层，其实线程也就是一个对象了，OS有算法来调度线程。



<br>

<br>



## CAS

compare and set



<br>

## COW

copy on write

想像一下进程fork时的内存处理



<br>



## synchronized 的优化

![image-20250724164419763](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250724164419763.png)

stack的锁记录是 lock record

![image-20250507174759491](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250507174759491.png)



1. 线程首次获取监视器锁时，是偏向锁（需要偏向锁已启用）记录线程的id，线程可再次直接获取锁。
2. 其他线程发现是偏向锁且自己没有持有，就撤销偏向，再尝试轻量级锁获取（CAS+lock record）
3. 尝试几次都没获取就升级为重量级锁调用OS的api

CAS：

线程 A 的栈：
+------------------+
|   Lock Record    |
| - obj: 指向对象  |
| - mark: 备份Mark  |
+------------------+

```java
if (CAS(object.markWord, oldMark, pointer_to_lock_record)) {
    // 成功：获得轻量级锁
} else {
    // 失败：有竞争 → 膨胀为重量级锁
}
```



<br>



## 线程池工作原理

生产者-消费者模型可以解决90%的并发问题



<br>



## ThreadLocal

threadlocal存在与heap中

每个Thread对象有`ThreadLocal.ThreadLocalMap threadLocals `

<br>

# 虚拟线程

首先一个疑问：虚拟线程是协程吗？

1. 传统线程是操作系统的线程，会调用操作系统的api
2. 虚拟线程是运行在JVM上的，一个OS线程上可以运行多个虚拟线程，JVM调度虚拟线程到os线程上去执行
3. 因为是在JVM上创建，资源占用小，遇到一些阻塞JVM就直接挂起切换了，适合IO密集型

协程完全是用户自己管理的，虚拟线程由jvm管理，对于OS来说，二者都是用户态，对于java来说虚拟线程属于JVM，与业务代码无关。



