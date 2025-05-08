---
layout: post
category: jvm
---



# Class Loader

输入=类文件（如Hello.class）

输出=有两个：

1. 类对象（`Class<Hello>`对象）存在堆里（反射的入口+实例化的模板）
2. 类的各种信息如字段、方法等存在方法区



过程：

1. 加载阶段：读取.class文件并转换为字节码数据
2. 内存映射：
   1. heap 创建Class对象作为运行时访问类的入口
   2. 方法区（元空间）存储类结构信息，供jvm执行方法解析字段
3. 初始化：静态变量赋值，静态代码块





## 类加载的时机

动态加载，并不是一开始将所有类都加载，而是用到时再加载。

主动使用：完整的加载流程

被动使用：会被加载，但不会被初始化



主动使用-类：

1.用了new 關鍵字 代表新物件被創建

2.static method被呼叫

3.static variable被存取(**但compile-time 常數例外**)

4.如果此類別的子類被初始化時 此類別還沒被初始化 則會先初始化此類別

5.從命令列被執行時 用戶指定的主類(就是public static void main(String[] args) 所在的類)

6.任何的反射調用

主动使用-接口：

1.static method被呼叫

2.static variable被存取(但compile-time 常數例外)

3.從命令列被執行時 用戶指定的主類(就是public static void main() 所在的類)

4.任何的反射調用



接口与类相比，区别在于：

**1.接口没有new**

**2.子类被初始化时，父类也会被加载，接口则不一定，接口子类被初始化时并不强制要求初始化接口父类**





## 类加载详解

更详细的加载周期：

加载：去类路径下面找到文件的byte流，载入后生成instance of java.lang.Class

验证：

准备：为static variable 分配内存，并给予预设值（int类型默认为0），static final 则赋予给定的具体值

解析：属于java的连接，将class中的全限定名称引用转变为内存地址/偏移量

初始化：将static 非final的变量初始化为各自对应的真正初始值



验证准备解析：又可统称为链接

解析也可以放在初始化之后





### 加载

在這個階段 有三件事情必須要完成

1.用其中一個加載器 透過一個類別的完全限定名找到.class檔案(字節流)

2.將這個字節流中的類別資訊存進方法區

3.在Heap中 生成一個這個類別的類物件



<img src="https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250506160351868.png" alt="image-20250506160351868" style="zoom:50%;" />

1.類加載器先去看這個類物件是不是已經在heap裡了

2.如果已經在heap裡了 就從heap回傳class object

3.沒有在heap的話 代表這個type第一次被存取 那類加載器就會去尋找這個class

4.如果找不到 就拋出ClassNotFoundException

5.找到的話 就生成類物件 並且存進heap



#### 加载器

<img src="https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250506160546375.png" alt="image-20250506160546375" style="zoom:30%;" />



启动类加载器：负责加载$JAVA_HOME/lib下能被jvm识别的类库，无法被java程序引用

扩展类加载器：负责加载$JAVA_HOME/lib/ext/*.jar及系统变量java.ext.dirs 指定的目录下的文件，java程序可以直接用扩展类加载器

应用程序加载器：负责加载用户路径中的文件



### Parent Delegation Model

双亲委派模型，更应该叫父亲委派模型：收到加载请求时委派给父类，父类无法加载才会自己去尝试加载

子加载器收到加载`java.lang.String`的请求时，会逐级委派至Bootstrap ClassLoader（核心类由其加载

注意，这里不是一种继承关系！











### Class Object

类物件，class对象，所有的type都有

Class

Interface

Primitives(byte, short, int, long, float, double, boolean, char)

Void

Arrays





## 验证

略



## Preparation

分配空间&默认值

| Data Type              | Default Value (for fields) |
| ---------------------- | -------------------------- |
| byte                   | 0                          |
| short                  | 0                          |
| int                    | 0                          |
| long                   | 0L                         |
| float                  | 0.0f                       |
| double                 | 0.0d                       |
| char                   | ‘u0000’                    |
| String (or any object) | null                       |
| boolean                | FALSE                      |





## Resolution

想起了c语言的GTO 与PTL 了

oop-klass模型，暂时不了解

大致就是将全限定符号解析为地址引用，有点类似于c的动态链接



## Initialization



执行静态代码块



## 类加载顺序

1. 所有的静态初始块会被合并，包括静态变量的声明
2. 所有的实例初始化也会被合并
3. 实例初始化优先于构造器



Jvm 新增选项 `-verbose:class`

