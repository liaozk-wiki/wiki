---
layout: post
category: jvm
---



# Object in heap

为什么要再特别讲一下heap中的一个对象存储呢？我想起了21年刚转行入职在b公司，当时将数据库从oracle迁移到mysql，有许多慢sql需要优化（必须承认oracle是如此的强大），当时实在没法优化了，最后搬到内存中来处理连接，当时主管问了一个问题：几十万个对象，会不会传输很久？会不会将内存爆了？那会儿刚自学转行，完全不知道一个对象需要大致占用多少字节。



java在heap中的每个实例，除了自身的实例数据与对其填充外，额外有一个对象头。

Object Header：

Mark Word‌：标记字段，记录了锁标志位，GC分代，hash等，一般8字节

‌Klass Pointer：指针，指向方法区类的元数据



数组对象多了长度信息：4字节

<img src="https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250507174603208.png" alt="image-20250507174603208" style="zoom:50%;" />







不开启压缩指针

```java
class A{
  int a = 1;//4字节
  boolean b = false;//1字节
  classC c ;//指针8字节
}
```

对象头：8+8 = 16字节

对象实例：4+1+8 = 13字节

对齐：32-29 = 3字节

总计占用内存：32字节。



```java
//jol可以查看对象布局
public static void main(String[] args) {
    User user=new User();
    //查看对象的内存布局
    System.out.println(ClassLayout.parseInstance(user).toPrintable());
}
```



对象头的5种状态：一共有64个bit

<img src="https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250507174759491.png" alt="image-20250507174759491" style="zoom:50%;" />



更多的关于锁与对象，此处暂略。

















