---
layout: post
category: jvm
---



# Execution Engine

类加载完后，字节码存放在方法区，然后由执行引擎一个指令一个指令去执行



解释执行+JIT（Just-in-time compiler）

基本代码都是在interpreter中执行，部分经常调用的会通过JIT编译成机器码

<img src="https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250506180530985.png" alt="image-20250506180530985" style="zoom:50%;" />



