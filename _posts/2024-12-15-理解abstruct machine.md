---
layout: post
title: 理解jyy-os课程的abstruct machine
category: 计算机
---

这个其实应该属于jyy-os课程的内容，不太方便将其归纳到os模块，故单独放置于此。
理解abstruct machine：
<br>

https://jyywiki.cn/OS/AbstractMachine/AM_Programs.html
<br>

程序是如何在计算机上跑起来的：如何让c程序运行起来？
<br>

编译：汇编代码
<br>

链接：可执行文件：这一步就处理好了_start函数与调用main（main在原文件中的位置并不固定）
<br>

加载运行：execve 将代码加载到进程的特定地址空间后，将pc指针指向程序运行处
<br>



```text

#
 Building
 hello-image
 [x86_64-qemu]

#编译原始文件
x86_64-linux-gnu-gcc main.c

x86_64-linux-gnu-gcc say.c

make
 -s
 -C
 /learn/os/os-workbench-2022/LAB/ud_abs/../abstract-machine/am
 archive

#
 Building
 am-archive
 [x86_64-qemu]

#编译abs相关文件
x86_64-linux-gnu-gcc start64.S

x86_64-linux-gnu-gcc trap64.S

x86_64-linux-gnu-gcc trm.c

x86_64-linux-gnu-gcc cte.c

x86_64-linux-gnu-gcc ioe.c

x86_64-linux-gnu-gcc vme.c

x86_64-linux-gnu-gcc mpe.c

echo
 +
 AR
 "->"
 build/am-x86_64-qemu.a

#生成静态库 am-x86_64-qemu.a
ar
 rcs
 /learn/os/os-workbench-2022/LAB/abstract-machine/am/build/am-x86_64-qemu.a
 /learn/os/os-workbench-2022/LAB/abstract-machine/am/build/x86_64-qemu/src/x86/qemu/start64.o
 /learn/os/os-workbench-2022/LAB/abstract-machine/am/build/x86_64-qemu/src/x86/qemu/trap64.o
 /learn/os/os-workbench-2022/LAB/abstract-machine/am/build/x86_64-qemu/src/x86/qemu/trm.o
 /learn/os/os-workbench-2022/LAB/abstract-machine/am/build/x86_64-qemu/src/x86/qemu/cte.o
 /learn/os/os-workbench-2022/LAB/abstract-machine/am/build/x86_64-qemu/src/x86/qemu/ioe.o
 /learn/os/os-workbench-2022/LAB/abstract-machine/am/build/x86_64-qemu/src/x86/qemu/vme.o
 /learn/os/os-workbench-2022/LAB/abstract-machine/am/build/x86_64-qemu/src/x86/qemu/mpe.o

#编译klib
make
 -s
 -C
 /learn/os/os-workbench-2022/LAB/ud_abs/../abstract-machine/klib
 archive

#
 Building
 klib-archive
 [x86_64-qemu]

x86_64-linux-gnu-gcc stdlib.c

x86_64-linux-gnu-gcc stdio.c

x86_64-linux-gnu-gcc cpp.c

x86_64-linux-gnu-gcc string.c

x86_64-linux-gnu-gcc int64.c

echo
 +
 AR
 "->"
 build/klib-x86_64-qemu.a

#生成静态库klib-x86_64-qemu.a
ar
 rcs
 /learn/os/os-workbench-2022/LAB/abstract-machine/klib/build/klib-x86_64-qemu.a
 /learn/os/os-workbench-2022/LAB/abstract-machine/klib/build/x86_64-qemu/src/stdlib.o
 /learn/os/os-workbench-2022/LAB/abstract-machine/klib/build/x86_64-qemu/src/stdio.o
 /learn/os/os-workbench-2022/LAB/abstract-machine/klib/build/x86_64-qemu/src/cpp.o
 /learn/os/os-workbench-2022/LAB/abstract-machine/klib/build/x86_64-qemu/src/string.o
 /learn/os/os-workbench-2022/LAB/abstract-machine/klib/build/x86_64-qemu/src/int64.o

echo
 +
 LD
 "->"
 build/hello-x86_64-qemu.elf

# 链接成 hello-x86_64-qemu.elf
x86_64-linux-gnu-ld
 -melf_x86_64
 -N
 -Ttext-segment=0x00100000
 -o
 /learn/os/os-workbench-2022/LAB/ud_abs/build/hello-x86_64-qemu.elf
 --start-group
 /learn/os/os-workbench-2022/LAB/ud_abs/build/x86_64-qemu/main.o
 /learn/os/os-workbench-2022/LAB/ud_abs/build/x86_64-qemu/say.o
 /learn/os/os-workbench-2022/LAB/ud_abs/../abstract-machine/am/build/am-x86_64-qemu.a
 /learn/os/os-workbench-2022/LAB/ud_abs/../abstract-machine/klib/build/klib-x86_64-qemu.a
 --end-group

#生成镜像
echo
 \#
 Creating
 image
 [x86_64-qemu]

make
 -s
 -C
 /learn/os/os-workbench-2022/LAB/ud_abs/../abstract-machine/am/src/x86/qemu/boot

echo
 +
 CC
 start.S
 main.c

x86_64-linux-gnu-gcc
 -static
 -m32
 -fno-pic
 -Os
 -nostdlib
 -Ttext
 0x7c00
 -I/learn/os/os-workbench-2022/LAB/ud_abs/../abstract-machine/am/src
 -o
 bootblock.o
 start.S
 main.c

python3
 genboot.py
 bootblock.o

echo
 +
 CREATE
 "->"
 build/hello-x86_64-qemu
<br>
(
 cat
 /learn/os/os-workbench-2022/LAB/ud_abs/../abstract-machine/am/src/x86/qemu/boot/bootblock.o;
 head
 -c
 1024
 /dev/zero;
 cat
 /learn/os/os-workbench-2022/LAB/ud_abs/build/hello-x86_64-qemu.elf
 )
 >
 /learn/os/os-workbench-2022/LAB/ud_abs/build/hello-x86_64-qemu

```
<br>

大致逻辑：

1. 编译我们编写的代码
2. 编译AM的代码（TRM、IOE、CTE、VME、MPE），并生成静态库
3. 编译Klib的代码，并生成静态库
4. 将我们的代码与两个静态库进行链接，生成最终的可执行文件
5. 对最终的可执行代码进行处理，生成可供qemu启动的镜像



