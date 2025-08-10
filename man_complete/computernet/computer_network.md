---
layout: post
title: computer network
---



# Overview
![网络总结](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E7%BD%91%E7%BB%9C%E6%80%BB%E7%BB%93.jpg)











<br>

<br>

<br>

应用层
<br>
传输层
<br>
		tcp，udp 等。进程到进程；不可靠，变为可靠等；其中web服务的端口就是运行在传输层。网络层通过ip将分组送到主机，传输层再通过端口将数据给到监听端口的应用。
<br>
网络层
<br>
		端到端；best ever 尽力，无保障；ip到ip；
<br>
		端到端 E2E ；点到点的基础上，实现端到端；
<br>
		传统：ip协议+路由协议。 分组-路由表-转发
<br>
		sdn（Software-Defined Networking）： 数据+控制（运行应用，流表）；流表给交换机，可以泛洪，block，修改 。

> **SDN 把网络的“大脑”（控制平面）集中到控制器，把“手脚”（数据平面）交给交换机；控制器通过下发“流表”告诉交换机：哪些流量要泛洪、阻断、修改或转发。**



<br>
数据链路层
<br>
		点到点 P2P；以帧为单位的点到点之间的传输；
<br>
		网络层的端到端是源主机到目标主机，链路层的点到点是，网卡到网卡，路由节点也算。76
<br>
物理层



<br>

<br>

<br>

网络： 节点+边
<br>

计算机网络：
<br>

主机节点，中转节点（路由器，网络层；交换机，链路层，负载均衡设备等）
<br>

接入网链路（与方有关），骨干链路（与圆有关）
<br>

互联网：
<br>

网之网
<br>

internet
<br>

节点+链路+协议



<br>

协议：对等层实体，通信的过程中应该遵循的规范的集合； 包含 格式（语法，语义）+次序+动作
<br>

PDU:Protocol Data Unit

![image-20250807105052810](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250807105052810.png)





<br>
服务角度：分布式进程+提供通信服务的基础设施 （面向连接的服务，无连接的服务）
<br>



边缘+核心+接入系统
<br>

边缘 通过核心 连接另一个边缘；核心（一个大的开关）
<br>

网络边缘：
<br>

主机
<br>

c/s 模式
<br>

peer to peer
<br>

面向端系统的连接： 端与端知道连接，中间节点不知道tcp ；
<br>

中间节点也知道的连接：you？ 连接 
<br>

tcp：可靠，流量控制，阻塞控制



网络资源复用：

<br>

线路交换：频分，时分，波分，码分；长连接，只占部分；
<br>

分组交换：packet switch 用就全用，每个节点存储转发 按需使用；存储时间，排队时间。此时网络核心的功能就是。转发（局部）与路由（全局）；非固定时分（统计多路复用）
<br>

分组交换：
<br>

数据报（无连接，类似寄信）数据报携带目标主机的全部信息，无状态路由，只管存储转发
<br>

虚电路，通过信令 建立虚拟电路，数据携带虚电路号，体现在每个交换节点
<br>





tcp 面向连接
<br>

接入网络：
<br>

家庭
<br>

电话线，调制 解调 猫
<br>

电话线，调制 解调 adsl
<br>

电缆，
<br>

电线，
<br>

路由器：路由+交换等
<br>

企业
<br>

级联，
<br>

无线
<br>

物理媒体：
<br>

bit
<br>

物理链路（导引/非导引）
<br>

双绞线，同轴电缆，光缆
<br>

isp 网络；互联网 isp互联
<br>

分布式中心机房，机房通过转属网连接，机房接近各地区isp
<br>

分组延时：

<br>

1. 处理延时（检查是否出错，查路由表）
2. 排队延时（到达速率大于输出速率，队列排队 ）流量强度L*A/R. 趋近1 排队延迟趋于无穷
3. 传输延时（L/R 100M/(10M/s) 打比特时间）
4. 传播延时  (d/s 信号在线路中传输的时间，光纤就是光在路线中传递的耗时）



<br>

信道容量

<br>

<br>
traceroute：

icmp：ttl。每经过一个节点就减1，0的时候就给告诉原主机，我的ip是我干掉了这个分组 ；

利用icmp，统计rtt。  round trip time；放出不同ttl的分组，收集信息，最终目标端口不可达即停止；
<br>



<br>

分组丢失：队列满了，丢失；
<br>

链路可靠，上个节点重传，不可靠源主机重传或丢就丢了

<br>

吞吐量：
<br>

源到目标的传输速率
<br>

瞬时/平均
<br>

每一层协议利用下层提供的服务实现功能（包含一些额外功能）以向上一层提供服务；上一层包含下一层；

<br>

服务提供者--服务访问点sap--上次用户；socket 传下来加标准，传上去可以区分 
<br>

原语primitive：使用服务/提供服务的形式  sockte api 函数；以api函数的形式提供服务，以api函数的形式使用服务

<br>

Du  数据单元
<br>

![截屏2023-04-13 23.06.43](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-13%2023.06.43.png)
<br>
pdu 协议数据单元
<br>

物理层：比特，电磁波
<br>

链路层：两点之间，帧为单位的传输 p2p
<br>

网络层：分组为单位的，主机到主机端到端之间的传输 e2e
<br>

传输层：进程到进程之间，可靠的
<br>

应用层：
<br>

http->tcp->ip->802.11
<br>

Message->segment->packet/datagram->frame->bit

<br>

# 应用层
<br>

应用层一般架构体系：
<br>

c/s
<br>

p2p
<br>

混合：napster





<br>

<br>
分布式应用进程之间通信；
<br>



1. 唯一标识（必须附带寻址作用）
2. 如何利用下层服务，使用服务的形式，与使用哪些服务sap 
3. 定义协议等，实现应用

对于1:端节点；ip+端口；有一些约定俗成 tcp 80 ftp 21 telnet23等

对于2: from to what（sdu ）

对于3:定义自己的协议（a for apple ;b for banana ）
<br>

<br>



端口号：tcp 16bit 6万多；udp 16bit 6万多 ；tcp与udp 对端口号的使用有所不同 
<br>

应用进程上守护的端口号；end point 端节点，ip+tcp/udp端口号
<br>

端节点每次传输，from与to ，不需要每次都传输，用socket标识，socket就是一个整数，类似句柄
<br>

socket是本地操作系统管理的一个整数（tcp：代表4元组；udp则代表 2元组），本地使用，就本机知道如何翻译 

<br>

因为是操作系统内部分配，同一个连接的两端，可能值不一样 ，各自操作系统维护翻译表
<br>

 socket与端口号不同 ，使用socket：1方便管理；2传输信息量最小 
<br>



- tcp socket代表着一个会话，abc三个主机，b上2.2.2.2:80  与a的连接中socket=111 与c的连接中socket=222，虽然是相同ip，相同端口，但不同会话，不同socket
- udp socket 只有本地ip+udp端口，但传输报文中必须指定 目标ip+目标端口；接受报文传输层需要提供对方ip+端口 



<br>



应用协议只是应用在网络交换这一部分的规范，公开：http smtp  私有：skype
<br>

传输层向应用层提供的服务，评价指标：1.数据丢失率；2.延迟；3.吞吐；4.安全； 
<br>

ssl 在tcp之上；应用-ssl-tcp；https ssl之上(现在全部是TSL：client请求server时，server会向client发送公钥，后续client的所有数据都会加密，server使用自己的私钥解密)
<br>

web与http
<br>

web页由一些对象组成，包含一个基本的html页面，该文件又包含若干对象的引用；
<br>

可以通过url对每个对象进行引用
<br>

Prot://user:pwd@www.lzk.com/somedept/a.jpg:port
<br>

协议+用户密码+主机+路径+端口
<br>

http 无状态，支持的连接远程超有状态
<br>

Http1.0 非持久

1. tcp连接，一个来回
2. http请求与对象的返回，一个来回
3. 连接关闭

<br>





Http2.0持久

1. tcp连接建立，一来回
2. http请求与响应，一个来回
3. 连接不关闭，后续有请求继续使用

<br>





持久也有两种方式
<br>

流水线popline  一次请求10个
<br>

非流水线non-popline  回来一个再请求下一个 
<br>





<br>

<br>

rtt round trip time
<br>

Http1.0 2rtt+传输时间
<br>

Http1.1 
<br>

email 
<br>

控制连接，传输连接
<br>

smtp
<br>

用户-邮件服务器-邮件服务器-目标
<br>

smtp-smtp-（pop3/http/...）
<br>

dns
<br>

层级：根-顶级域-权威域。dns，命名空间；根有13台主机
<br>

域的划分是逻辑的，而不是物理的，可能a域的主机，管理的ip可能在北京/上海/成都/广州等
<br>

域名服务器：维护资源记录（域名-ip的映射）；格式【name,value,type,ttl,class】；internet class=in
<br>

ttl:有效期，可长可短，缓存是为了性能，删除是为了一致性，默认是2天
<br>

Application--resolver--local name server
<br>

上网：ip地址+子网掩码+localnameserver+default gateway  
<br>

local name server：每个isp一般有一个，默认的名字服务器
<br>

p2p
<br>

c-s模式：下载瓶颈=max(n*f/supload,f/cdown)
<br>
p2p模式：下载瓶颈=max（f/cdown,f/sup,n*

f/(su+求和cu)） 
<br>

非结构化p2p：点----边----点，无序构成的网，任意连接
<br>

dht结构化p2p：构成有序的网，环/树等
<br>



- 集中化的目录管理：napster
- 完全分布式的：gnutella，查询泛洪，a-所有邻居-邻居的所有邻居，有资源则反向返回信息（获取到了目录）
- 泛洪的控制 
- ping-pong 建立网
- 离开则邻居另选一个
- 混合式kazaa：组长与组长类似gnutella,组长与组员类似napster
  <br>





文件-描述-hash；返回描述最匹配的hash，再以hash请求文件
<br>

bt
<br>

bitmap：256k，相互之间交换bitmap信息
<br>

cdn
<br>

dash
<br>

cdn
<br>

socket：
<br>

服务器：
<br>



1. 创建
2. 捆绑
3. 等待





在整个应用层重点了解：

## TSL

1. Client 发起连接：客户端向服务器发送支持的 TLS 版本、加密套件、随机数等信息。
2. Server 回应
   1. 服务器选择加密套件，返回自己的随机数
   2. 服务器发送 **数字证书（Certificate）**，其中包含服务器的公钥+服务器身份信息
3. Client 验证证书，验证失败，则连接终止
4. 密钥交换
   1. 客户端生成一个 **预主密钥（pre-master secret）**，并使用从证书中获取的 **服务器公钥加密** 它，然后发送给服务器。
   2. 服务器用自己的 **私钥解密** 得到预主密钥
5. 双方使用“预主密钥” + 之前交换的随机数，通过算法生成相同的 对称加密密钥（session key）
6. 切换到对称加密通信



简单理解为，服务器先给个公钥，然后使用该公钥加密客户端的预主密钥，再然后生成对称密钥加密双方数据。



对称密钥：加解密使用同一个密钥

非对称密钥：公钥，私钥





## HTTP

http的请求头等不再详细描述

![image-20250807120733407](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250807120733407.png)





## SMTP

通过smtp 将邮件发送到服务器&中继到邮件服务器，用户通过pop3/imap从服务器拉取邮件到本地。









## DNS



1.DHCP（Dynamic Host Configuration Protocol） 本地电脑获取了在局域网中的ip，网关地址，及域名服务器地址

2.缓存再到域名服务器层层向上查找解析



<br>

<br>





# 传输层
<br>

 应用层-报文--传输层--拆分成报文段；对方再接受报文段，重组成报文段，返回给应用层；
<br>

对网络层的加强
<br>

tcp：可靠保序

1. 多路复用与解复用
2. 拥塞控制
3. 流量控制
4. 建立连接



udp：不可靠，不保序





多路复用与解复用



关键点就是 ip协议只提供了主机到主机的连接保证，端口是运行在传输层。复用与解复用，理解为端口即可。提供了进程到进程之间的连接。

<br>

都不提供：延时保证，带宽保证 
<br>

数据报与字节流？？？
<br>

多路复用与解复用：
<br>

从主机细化到端口

<br>

## UDP



udp：user datagram  protocol
<br>

![截屏2023-04-23 23.35.33](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-23%2023.35.33.png)
<br>
udp校验和(edc error-detection and-correction)：
<br>
d分为16bit为单位，求加法和
<br>

- 进位回滚
- 求返码



## 可靠传输



<br>

rdt（Reliable Data Transfer）：可靠数据传输原理
<br>

rdt在应用层，传输层，数据链路层 都很重要
<br>

信道的不可靠，决定了可靠数据传输协议rdt的复杂性

<br>

**Rdt1.0**:下层传输可靠
<br>

本层协议不需要干什么，接受上层数据，封装，传输给下层

<br>



**Rdt2.0**:下层传输，具有bit差错，数据反转 
<br>

发送-p-接受（ack/nak）-发送

每次发送需要收到ack才会发送下一个，如果收到nak就重发。

<br>

![截屏2023-04-25 22.01.33](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-25%2022.01.33.png)
<br>
 Ack/nak也可能出错：接收方发送了ack但损坏了，发送方无法读取信息，此时重新发送pack1，接收方无法判断pack1是重复数据还是新的数据。
<br>

**rdt2.1**:package新增编号，没有正确读到ack就将p0再发一次（冗余分组，所以要编号，明确是冗余)，直到收到ack再发p1
<br>

发送方

- 发送的分组中加入序号
- 有ack才发下一个分组

<br>



接收方

- 接受分组，校验，返回ack/nak
- 接收方不校验ack是否收到，依据给的分组决定 



<br>

![image-20250807152636817](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250807152636817.png)



**Rdt2.2**:无nak的协议
<br>

没有nak，但给ack编号；发1但收到ack0 就表示1发送 失败
<br>

没有正确读到ack1再发一次package0 

<br>

接收方永远返回最后收到的正确的那个ack

<br>

**rdt3.0:**具有bit差错+分组丢失
<br>

因为分组丢失可能永远都收不到ack rdt2.0会出现死锁----**新增超时重传**
<br>

超时不合适，重复分组
<br>

当链路容量非常大时，rdt3.0 会有极大的浪费
<br>

来回30ms但是我的传播延时只有8us，大部分时间都浪费了
<br>

升级为一次打多个分组--流水线协议pipeline诞生了 
<br>





pipeline 又分为了两类：

- GBN 回退n步
- SR选择重传



流水线协议（管道化协议 ）：
<br>

1. 增加序号范围，多个bit标识序号
2. 两边增加缓存：发送方记录已发送未收到反馈的，接收方 上层调用速度不一定等于接受速度

<br>

滑动窗口协议（slide window）

[sw=send window, rw = receive window]

1. sw=1  rw=1  s-w 停止等待协议；rdt
2. sw>1 rw=1  gbn  累计确认，只有一个超时定时，超时重传所有未确认的分组
3. sw>1 rw>1 sr 选择重传
4. sw>1 就是流水线协议



发送缓冲区
<br>

发送窗口：发送缓冲区的一个范围 ，已发送未确认的分组构成的空间
<br>

窗口的最大值<=发送缓冲区的值
<br>

开始：前沿=后沿，窗口尺寸为0
<br>

每发送一个分组，前沿移动1 单位
<br>



<br>
接受窗口：接受缓冲区
<br>

rw=1 顺序接，不是要的这个，丢弃 gbn
<br>

rw>1可以乱序接
<br>

 低序号到，可以滑动窗口，右移；rw>1 高序号到，可以缓存但不提交，不滑动（实现rdt） 
<br>





## TCP

**面向连接的传输协议tcp：**
<br>

长的字节流按照mss（最大报文段大小）大小，分成数据报文段+tcp头部=tcp报文段
<br>

mss：以太网，1500B，tcp1460+20，ip+20
tcp特点：

1. 点到点
2. 可靠的，按顺序的，数据流
3. 管道化，流水线
4. 全双工
5. 面向连接
6. 有数据控制



![截屏2023-04-26 21.30.25](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-26%2021.30.25.png)
<br>
序号：载荷的第一个字节在字节流中的偏移量
<br>



**Ack555 表示554及之前的数据已经接收完毕** 
<br>

tcp超时： 
<br>
![截屏2023-04-26 21.44.34](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-26%2021.44.34.png)
<br>
![截屏2023-04-26 21.45.57](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-26%2021.45.57.png)
<br>

<br>
tcp的可靠性传输：
<br>

pipeline gbn+sr：tcp是二者的混合 
<br>
![截屏2023-04-26 21.52.11](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-26%2021.52.11.png)
<br>
超时重传，快速重传 
<br>

接收方给的是顺序到来的最后一个字节+1
<br>

tcp流量控制：接收方控制发送方发送速度 ，告诉发送方空闲缓冲区的大小 
<br>

头部rwnd告知空闲buffer，发送方确保未确认字节数量<rwnd 
<br>
![截屏2023-04-26 22.22.04](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-26%2022.22.04.png)
<br>

<br>
tcp piggybacking (捎带ack信息)
<br>

tcp：连接管理
<br>
![截屏2023-04-26 22.42.06](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-26%2022.42.06.png)
<br>
两次握手：
<br>

请求超时，虚假的半连接，服务器资源耗尽 
<br>

两次超时，旧数据当作新连接了
<br>
![截屏2023-04-26 22.49.21](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-26%2022.49.21.png)
<br>
三次握手
<br>
![截屏2023-04-26 22.50.46](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-26%2022.50.46.png)
<br>
  半连接无了
<br>

旧数据因为序号，可以区分
<br>

tcp拆除：
<br>

可以理解为两个半连接，分别拆除，再启动一个定时器，结束后算是连接真的关闭了

<br>

拥塞控制原理
<br>

拥塞的几个问题：
<br>

都在往里面打bit，但没有活着出来的
<br>

一般拥塞控制：

1. 端到端拥塞控制，端系统，依据自身信息进行判断  
2. 网络辅助的拥塞控制 

<br>



atm（数据异步传输)的  abr模式 提供的拥塞控制
<br>

不拥塞，一直发；拥塞则限制一个带宽
<br>

数据信原中塞一些资源管理信原
<br>

tcp拥塞控制
<br>

检测-控制-消除控制
<br>

检测：

1. 超时（1.1确实拥塞，丢失了；1.2校验失败被抛弃了（概率小，不影响整体））
2. 某个段的3次重复ack（1+3）





Congestionwindow/rtt=rate。单位时间，未得到确认的情况下，向网络注入的字节数量；拥塞控制所限制的速度
<br>

![截屏2023-04-27 22.53.44](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-27%2022.53.44.png)
<br>
![截屏2023-04-27 22.54.22](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-27%2022.54.22.png)
<br>
慢启动
<br>

线性增，乘性减 1，2，4，8，16，1，2，4，8，9，10，5，6，7
<br>

超时事件后的保守启动
<br>
![截屏2023-04-27 23.11.06](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-27%2023.11.06.png)
<br>



拥塞控制这里的逻辑非常有意思：我永远试图发送更多，直到收到负反馈。



# 网络层-数据

<br>

网络服务模型
<br>

转发-路由 （核心功能）
<br>

路由器工作原理
<br>

通用转发
<br>

转发：进来的报文，从那个口出去；局部的，那个端口入，那个端口出；数据平面
<br>

路由：源-路由选择-目的；全局的 ，全局找路径；控制平面
<br>

控制平面：是一个全局的；数据平面是一个本地的功能；
<br>

传统（路由器中实现），地址+转发表；sdn（software-defined newworking ，远程服务器中实现）：多个字段+流表 

<br>

传统，分布式，计算路由表；sdn，集中式计算路由表
<br>

网络层向上层提供的服务：

<br>

1. 单个数据报：可靠传输+延迟
2. 数据报流，保序，保证流的最小带宽，分组之间的延迟差（）

<br>

有些网络，网络层还需要维持连接
<br>

数据：ip
<br>

路由：路由选择算法
<br>

输入端口缓存：
<br>

为何存在：头端阻塞，多个输入，都走一个端口输出，多打1
<br>

交换结构：
<br>

局部交换速率>= n*端口速率
<br>

memory
<br>

bus
<br>

crossbar
<br>

memory：第一代，输入-系统bus-memory-系统bus-输出
<br>

bus：输入-bus-输出。适合骨干
<br>

crossbus：网络，并发交换
<br>

缓存-调度
<br>

传输：fifo，轮询（平均/优先级）
<br>

丢弃：tail，priority，random
<br>

ip协议
<br>

网络层：路由协议（控制），ip协议，icmp协议（信令协议，信号）
<br>

ip协议：

1. 地址
2. 格式
3. 处理约定

![截屏2023-04-30 12.16.47](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-30%2012.16.47.png)
<br>
ip数据重组与分片
<br>

以太网mtu=1500bit，传输层给的mtu可能比这个大，需要分片传输，目标主机再组装
<br>

![截屏2023-04-30 13.44.18](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-30%2013.44.18.png)
<br>
ip编址
<br>

对主机/路由器的接口编址，路由器通常有多个接口，主机也可以有多个；
<br>

同一个物理网络，ip层面一跳可达（链路层）； 
<br>

子网：
<br>

高位相同的节点（子网号相同）构成的网络
<br>

无需路由器介入，子网内各主机可以在物理上相互直接到达
<br>

子网聚集 ，纯子网/非纯子网
<br>

承接ip的物理网络一般两种形式，1。干线点到点；2。一堆点-交换机-一堆点
<br>

ip地址分类
<br>

![截屏2023-04-30 14.15.45](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-30%2014.15.45.png)
<br>
network网络号
<br>

host主机号
<br>

0开头的网络号128-2，2的24-2个主机地址 
<br>

abc为单播地址；
<br>

（单播，广播，组播）
<br>

互联网中的路由，是以网络为单位，一个子网在路由器中算是一个表项
<br>

![截屏2023-04-30 14.33.01](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-30%2014.33.01.png)
<br>

127开头为回路地址，传输层到达ip层就会返回去
<br>

![截屏2023-04-30 14.34.58](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-30%2014.34.58.png)
<br>
ip地址编码：
<br>

cidr：classless interdomain routing 无类域间路由
<br>

最开始分为abc类等，后来按照无类域间路由划分；
<br>

掩码：与ip 做与操作，得到子网，网络号
<br>

子网-路由表（得到下一跳的ip）（本机mac知道，通过ip也知道下一跳的mac，网卡就知道怎么发送)-下一个路由器再解析封装-目标子网-目标主机

<br>
如何获取一个IP地址：

1. 管理员，直接配置到一个文件 unix ：/etc/rc.config  (ip地址，子网掩码，默认网关，localnameserver域名解析ip地址)
2. dhcp（dynamic host configuration protocol）动态获取 plug and play 
    主机广播 dhcp discover 
    服务器 响应dhcp offer
    主机请求ip dhcp request
    服务器响应dhcp ack



<br>

路由聚集：
![截屏2023-04-30 19.20.12](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-30%2019.20.12.png)
<br>

<br>
路由器收集到信息后会，可以聚集会聚集后再通告
<br>

网络地址转换：nat network address translation
<br>
![截屏2023-04-30 20.15.38](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-30%2020.15.38.png)
<br>
![截屏2023-04-30 20.20.42](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-30%2020.20.42.png)
<br>
![截屏2023-04-30 20.21.25](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-30%2020.21.25.png)
<br>
nat穿越

1. 静态配置
2. upnp
3. 中继，两边都与中继连接





![截屏2023-04-30 20.24.25](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-30%2020.24.25.png)
<br>
ipv6
<br>

不用分片了，mtu太大直接丢掉给一个icmpv6的信息，让主机分小一点；
<br>
![截屏2023-04-30 20.31.53](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-30%2020.31.53.png)
<br>
next header
<br>

tlv  type-length-value
<br>

V4-v6 过度：
<br>

隧道：v6的分组，完整封装进v4，到了v6网络再解封 
<br>

原路由器：垂直集成，分布，难管理

1. 数据+控制
2. 控制功能，分布式实现 



<br>

sdn：数据平面与控制平面分离，水平集成
<br>

控制协议 open flow。更强大的流表，功能大大加强【匹配+行动。match-action】
<br>



# 网络层-控制
<br>
一些协议：

1. 传统路由选择算法。
2. sdn控制器。上报状态，下发流表
3. icmp： internet control message protocol



这些协议的具体实现：
<br>

bgp等
<br>

1. 路由选择算法
2. 因特网中自治系统内部的路由选择
3. isp之间的路由选择



路由选择算法（link state 全局式。distance vector分布式）
<br>

子网到子网的路由，最优路径，最小代价路径
<br>

拓扑-图
<br>

输入：拓扑+边的代价
<br>

输出：最优路径 
<br>

汇集树：此节点到所有其他节点的最优路径形成的树。
<br>

路由选择算法就是：为所有路由器找到并使用汇集树。
<br>

链路状态算法：dijkstra 
<br>

ls实现

1. 泛洪，获取拓扑（为了避免广播风暴：版本编号，ttl等）协议实现
2. dj-计算路由表 （这个才是算法）





ls应用

1. ospf  internet上
2. is-is：intermediate system -- intermediate system。internet 主干中

![截屏2023-04-30 23.05.49](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-30%2023.05.49.png)
<br>
![截屏2023-04-30 23.10.36](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-30%2023.10.36.png)
<br>
可能会有路径震荡
<br>

dv距离矢量算法：
<br>

维护 自身到所有其他目标的代价
<br>

定期测量
<br>

定期交换
<br>

![截屏2023-04-30 23.35.30](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-30%2023.35.30.png)
<br>
![截屏2023-04-30 23.40.24](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-04-30%2023.40.24.png)
<br>
dv无穷计算问题：好消息传的快，坏消息传的慢
<br>

为了解决无穷计算问题：水平分裂（虚假消息，没懂）
<br>

dv:
<br>
![截屏2023-05-01 00.05.34](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-05-01%2000.05.34.png)
<br>
![截屏2023-05-01 00.09.31](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-05-01%2000.09.31.png)
<br>
自治系统内的路由选择算法（内部网关协议）：
<br>

rip（routing information protocol）：基于距离矢量算法
<br>

 25个子网
<br>

30s/请求 就交换信息
<br>

代价为跳数
<br>

毒性逆转，最大跳数=16 作为无限不可达
<br>

ospf （open shortest path frist）：基于ls算法
<br>

可以层次化，支持汇集
<br>

boundary router 
<br>

back bone
<br>

area boundary router
<br>

isp之间的路由：自治区之间的路由 
<br>

前面介绍的都是平面路由，一个平面：管理+规模 等 搞不定，分层
<br>

层次与路由聚集的异同？？？  人为区分？
<br>

 ![截屏2023-05-01 00.40.31](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-05-01%2000.40.31.png)
<br>
as之间的路由协议
<br>

bgp：基于距离矢量（改进：路径矢量）
<br>

ebgp：从相邻节点获取子网可达信息
<br>

ibgp：子网可达信息传遍as内部
<br>

自治区之间的路由，更加强调策略 
<br>

![截屏2023-05-01 01.02.55](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-05-01%2001.02.55.png)
<br>
路由表 由内部网关协议+外部网关协议 共同决定；
<br>

内部ospf。外部bgp
<br>

网络前缀的多个路径：
<br>

本地偏好
<br>

最短as
<br>

最近的next-hop 路由器：热土豆路由(不管域间代价，域内选择最小的作为出口，最快甩出去)
<br>

sdn控制平面

<br>

# 链路层与局域网
<br>
相邻两个节点之间，以帧为单位的传输 

1. 成帧，链路接入
2. 相邻两个节点，完成可靠数据传输



网卡（网络适配器，网络接口卡）：实现了链路层和相应物理层功能，写死了自己的mac地址
<br>

检错与纠错：
<br>

奇偶校验，两维奇偶校验；（对偶错误校验不出来）
<br>

checksum
<br>

crc循环冗余校验：
<br>

模2运算：异或
<br>

位串的两种表示：1011=1x2^3^+0x2^2^+1x2^1^+1x2^0^
<br>

生成多项式：r次方，r+1 位
<br>

数据位+r位的冗余，正好可以被生成多项式整除
<br>

现在的问题变成了：如何添加冗余位，使得正好可以被整除
<br>

![截屏2023-05-01 23.19.25](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/%E6%88%AA%E5%B1%8F2023-05-01%2023.19.25.png)
<br>
多点访问协议：
<br>

共享的网络，多个节点同时发送，会有多个信号叠加，无法区分
<br>

多路访问协议，介质访问控制协议mac：决定什么时候使用共享信道，即什么时候发送
<br>

三大类：
<br>

信道划分：分片，每一片一个节点
<br>

随机访问：允许冲突，冲突后恢复
<br>

轮流：
<br>

随机Mac：aloha，时隙aloha，csma，【csma/cd】 ，【csma/ca】
<br>

时隙aloha：
<br>

分为时隙
<br>

节点只在某个时隙发送
<br>

发送前检测是否冲突，冲突在下一个时隙发送（概率p)
<br>

纯aloha：
<br>

没有时隙，有帧直接就发
<br>

csma：发之前先监听下冲突
<br>

也可能有冲突，传输延时，侦听的时候，还在路上
<br>

csma/cd：冲突检测，边说边听；以太网，有形的网络，比较容易实现



1. 创建帧
2. 发送前cs 监听，闲才发送
3. 发送中cd，冲突检测，没有就算成功，检测到就放弃
4. 所有适配器只要检测到冲突就发送一个强化jam信号，让所有站点都知道冲突了
5. 如果放弃发送，则进入指数退避{0,2k次方-1}。r*512位时再发
    <br>

wlan：csma/ca 冲突避免，因为不好检测
<br>
发送方

1. 发送之间监听信道，侦测到空闲时间持续difs时长，则传输整个帧on cd
2. 忙，则选一个随机回退值，空闲时递减，到0发送，如果没有收到ack就增加回退值 重复2

<br>

接收方

1. 正确，sifs后发送ack
  1.  信道划分：低负载差，高负载好
  2. 随机：低负载好，高负载差
  3. 轮询：taking turns mac 
2. master 轮询 slave；可靠性差
3. 令牌，拿到令牌才能发送，用完再生成一个令牌
    <br>

<br>
lans：
<br>

mac地址：网卡 48位地址， 
<br>

arp协议：ip映射mac
<br>

交换机：ip映射mac；ip是分层的，mac是平面的；hub的升级，存储转发，交换表（自学习，没有就泛洪，生成树），源，目标一致不会转发
<br>

以太网：最主流的lan技术
<br>

同轴电缆
<br>

hub
<br>

switch
<br>



<br>
以太网：无连接，不可靠，csma/cd
<br>

虚拟局域网
<br>



# 网络安全
<br>

加密：
<br>

加密密钥与解密密钥 是否一致来区分为 对称/非对称 加密
<br>

对称加密：key的分发
<br>

des
<br>

公钥+私钥模式 （rsa）
<br>

认证：
<br>

对称加密 可以实现认证
<br>

公开+私钥  也可以实现认证（给一个一次性整数，挑战，私钥加密再公钥解密）
<br>

公+私：中间攻击
<br>

（对称加密，如何获取key，公开加密。如何获取公钥）
<br>

数字签名：公+私，一般：报文--报文摘要--私钥加密
<br>

为了解决key与公钥的获取： 

1. kdc。 key distribution center
2. ca   centification authority   ca私钥加密【实体+对应公钥】ca给实体证书（就是捆绑关系）可以拿到公钥，实体再给其他实体证书，建立信任树





安全在各个层级的实现
<br>

安全电子邮件
<br>

随机密钥对明文加密
<br>

公钥对随机密钥加密 
<br>

ssl secure sockets layer
<br>

应用层之下，tcp/udp之上
<br>

提供 认证+加密
<br>

ipsec
<br>

防火墙
<br>

内部网络与互联网隔离，进出分组进行过滤
<br>

可以防止：
<br>

拒绝服务攻击：占满你的tcp连接 dos/ddos
<br>

通过一些信息，与规则进行判断，决定放行or阻塞
<br>

无状态：防火墙不维护状态，对每个分组进行判断
<br>

有状态：放行之前，还需要判断连接是否已经建立
<br>

web应用网关：外-gateway-内； 都与中间这个网关建立连接，他来作为中间人
<br>







# wireshark

seq：表示这次我将从seq这个字节开始发送

ack：表示我已经收到了ack-1的所有包，下次给我ack开始的包

```shell
curl http://httpbin.org/get
```

beautiful，非常完美的抓包

![image-20250807194522298](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250807194522298.png)

请求域名解析：

![image-20250807194628967](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250807194628967.png)

域名解析返回结果：

![image-20250809130231983](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250809130231983.png)

开始TCP连接：

客户端向服务器第一次握手：

flag = SYN；seq = 1160364176；ack = 0

![image-20250809133426368](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250809133426368.png)



tcp连接的第二次握手，服务端向客户端返回自己的seq，并将ack置为客户端的seq+1（syn占用了一个序列号）：

flag=SYN，ACK；seq=1607886011；ack=1160364177

![image-20250809133837265](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250809133837265.png)



tcp连接的第三次握手，客户端向服务器发送seq = next；ack = 服务器发送的seq+1=1607886012:

seq=1160364177，ack = 1607886012，flag=ack

![image-20250809134251683](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250809134251683.png)



客户端开始向服务器发送数据，注意这里的seq与ack与第三次握手的一样，因为seq表示tcp流中的第几个字节，第三次并没有发送什么数据过去故未变，并且第三次过后到这一次客户端请求服务器也没有其他数据，故ack依旧相同。seq=1160364177，ack = 1607886012 flag=push&ack：

![image-20250809141258642](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250809141258642.png)



next 是一个客户端的重传包，seq与ack同上seq=1160364177，ack = 1607886012 flag =push&ack：

![image-20250809141455225](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250809141455225.png)



Fram8:客户端的回复：seq=1607886012，ack = 1160364177 + 77 = 1160364254 

这是一个纯粹的ack包，flag=ack，此时客户端收到ack，不再重传，更新发送窗口，没有数据则将等待服务器返回。

![image-20250809142612083](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250809142612083.png)

Q：为什么会有fram8呢？

A：因为收到有序数据后会延时确认，等待期有数据就一并返回，没数据且过了等待期就只发一个ack。此时数据还没准备好且等待期已过，就单独发一个ack确认。

<br>

Fram9 ：服务器的返回发送数据+确认，seq=1607886012，ack=1160364254 flag=push&ack：

![image-20250809143827274](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250809143827274.png)



Fram10 ：客户端向服务器的ack确认 seq=1160364254，ack=1607886012+484= 1607886496 flag=ack：

![image-20250809144849711](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250809144849711.png)



开始关闭：

Fram11 是客户端发起的关闭请求，因为fram10是纯粹的ack控制包，不带数据，故此次seq与ack同上flag = FIN & ACK

![image-20250809145342741](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250809145342741.png)



Fram12 ：服务器回应收到关闭请求并也发送关闭请求

![image-20250809150050687](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250809150050687.png)



Fram13:客户端回应收到了关闭请求

![image-20250809150935716](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20250809150935716.png)

至此一次完整的http请求就结束了。

在客户端发送了最后一个ack后客户端进入wait期，服务器在收到ack后断开连接。wait期结束后客户端也正式断开连接。



现在我们单纯的只看浏览器的一次http请求是如何从客户端走到服务器的：

前置：

1.dhcp确保了我们的一台电脑联网后可以获得一个唯一的ip

2.arp确保了ip与网卡mac地址之间的映射



过程：



1. DNS请求获取ip
2. http 到 tcp ，tcp可能涉及分段。每一段有最大限制。

2. tcp到ip，ip也可能分包。一般tcp分段后ip无需再分包。
3. ip再到以太网frame
4. 数据从本机网卡，到网关（dhcp已经知道了网关ip）网卡
5. 路由器将fram解析层ip包，然后ISA内部通过ospf确定路由
6. 到达isa网关，isa之间通过bgp确定路由
7. 另一个isa将数据传输到服务器网关
8. 服务器网关再向目标服务器网卡传输数据
9. 服务器，再网卡 - ip - tcp - http - 进程























