---
layout: post
category: jvm
---



# Runtime Data Areas

大头，重要的部分，状态机的状态。



<img src="https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250507141655435.png" alt="image-20250507141655435" style="zoom:50%;" />



1.**jvm中使用 操作数stack 进行传参，类似汇编中的寄存器**

2.每个栈帧都有一个指向运行时常量池中这个栈帧所属方法的指针

3.本地方法有独立的stack



## 方法区

方法区是所有线程共享，前面提到加载器输入byte array，输出Class< T > 到heap + 字段方法等到方法区

方法区中，每个类有8种matedata

<img src="https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250507143637756.png" alt="image-20250507143637756" style="zoom:50%;" />



1.Runtime Constant Pool 

运行时常量池是每个类有各自的，区别于字符串常量池（heap中，所有类共享jvm只有一个）。

class文件中的常量池+other信息



2.Type Information

类的：全限定名称+接口/类+父类/父接口的全限定名称+访问修饰（public，private，abstract）



3.Field Information

對於一個類別的所有字段 都必須存在方法區

1.字段名稱

2.字段類別

3.字段修飾符(public, private, static, final, transient, volatile)



注意：类static的变量存在于方法区Class variable（下图的f2 与 e）



所以 列出所有排列組合給你

|                             | 原始類型(primitive type) | 引用類型(reference) |
| --------------------------- | ------------------------ | ------------------- |
| 靜態變量(static variable)   | 值:方法區                | 引用: 方法區 值: 堆 |
| 實例變量(instance variable) | 值:堆                    | 引用: 堆 值: 堆     |
| 局部變量(local variable)    | 值:棧                    | 引用: 棧 值: 堆     |

上範例程式

```
public class Demo {
 public static void main(String args[]){
   int a = 1;
   Integer b = 2;
   Hello c = new Hello();
 }
}
public class Hello {
 int d  = 3;
 static int e = 4;
 Foo f1 = new Foo();
 static Foo f2 =  new Foo();
}
public class Foo {
}
```



<img src="https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250507144616385.png" alt="image-20250507144616385" style="zoom:50%;" />


4.Method Information

方法：名称+返回类型+参数+修饰符+...



5.Class variable

非 final 的 static 变量

final的static 存在与常量池



6.Method table

简单理解为保存了实例方法的字节码，提供一个entry



7.Reference to ClassLoader table

是那个加载器加载的类



8.Reference to Class object

指向对应的Class对象





## 堆

存放了所有实例对象，所有线程共享



堆里有一个全局共享的字符串常量池：

及其繁琐的细节就不探究了

注意下面：

```java
String s = "abc";
//可以简单等价为下面,区别：除了在常量池中多了abc 还会多一个new String("abc)
String s = new String("abc").intern();
```









## 总结

stack：引用+基本类型

heap：实例+Class

方法区：类的相关信息



<img src="https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250507151551954.png" alt="image-20250507151551954" style="zoom:50%;" />



````java
Hello hello1 = new Hello();
Hello hello2 = new Hello();
World world = new World();
````





