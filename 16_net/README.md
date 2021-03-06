# 网络编程 _ 概念

- 什么是网络 网络的基础概念
  - 不同机器上的程序要通信，才产生网络
  - 网络通信基于 socket 套接字文件
    - TCP
    - UDP
- 并发编程

- qq 微信 飞秋 网游 微博 歪歪  _基于应用的网络程序
- 百度 微博 知乎 博客园 网易   _基于浏览器的网络程序

- 网络编程中的 - C/S架构
  - c client  客户端
  - s server  服务端
- 网络编程中的 - B/S架构
  - b broser  浏览器
  - s server  服务端
  - 不需要额外的安装客户端了,只需要一个网址就可以访问
  - 轻量级  - 使用成本低
- B/S架构是C/S架构的一种特殊形式
- 手机上 : 浏览器 app

- 两个py程序想要通信
  - 写文件
- 在不同机器上的两个py程序之间想要通信
  - 网络

- 网络的发展史
  - 网卡\网口
  - 两台机器之间 插上网线就可以通信
  - 网卡上 - mac地址
  - ip地址
    - 是4个点分十进制  - ipv4协议
    - 0.0.0.0 - 255.255.255.255
    - 127.0.0.1 本机
    - 内网字段 192.168.*.*
      - 10.*.*.*
      - 172.*.*.*
    - 6个点分十进制  - ipv6协议
      - 0.0.0.0.0.0  - 255.255.255.255.255.255
  - 交换机
    - 广播
    - 单播
    - 组播
  - arp协议 : 通过IP地址获取某一台机器的mac地址
  - 子网掩码
    - 子网掩码 和 ip地址进行 按位 与 运算 就能得出一个机器所在的网段
    - 192.168.21.36
    - 11000000.10101000.00010101.00100100
    - 255.255.255.0   255.255.0.0
    - 11111111.11111111.11111111.00000000
    - 11000000.10101000.00010101.00000000
    - 192.168.21.0 网段
  - 网关地址 : 整个局域网中的机器能沟通过网关ip与外界通信
  - 网段 : 子网掩码 和 ip地址进行 按位 与 运算
  - 端口 : 0-65535
    - 8000-酷狗音乐  22-ssh  3306-mysql
    - python 网络应用  8000
    - ip地址+端口号 : 在全网找到唯一的一台机器+唯一的应用
    - 我们选择端口 : 8000之后
  - tcp协议
    - 全双工的通信协议
    - 一旦连接建立起来,那么连接两端的机器能够随意互相通信
    - 面向连接的通信方式
    - 数据安全不容易丢失
    - 建立连接的 三次握手
    - 断开连接的 四次挥手

- 网络开发的架构 :C/S B/S
- mac地址是什么 -身份证号
- ip地址 - 学号
  - ip地址不是一成不变的
- 一个局域网内的几台机器之间 可以进行通信
- 我们找寻一台机器 : ip地址
  - 如何判断 要找的机器在不在同一个局域网内:
    - 用自己的ip地址和子网掩码按位与运算
    - 192.168.21.36 11000000.10101000.00010101.00100100
    - 255.255.255.0 11111111.11111111.11111111.00000000
    - 192.168.21.0  11000000.10101000.00010101.00000000  # ---网段
    - 用要找的机器ip和子网掩码按位与运算
    - 192.168.21.21
    - 如果上面的两个结果相同说明两台机器在同一个网段
    - 子网掩码
    - 2进制 8进制 10进制 16进制

    - 16 8 4 2 1 (填位法)
    - 1  0 1 0 0
  - 要找的机器和你在同一个局域网内:
    - 通过交换机
    - 广播
    - 和要找机器ip地址相同的服务器做出响应,将自己的ip地址返回给交换机
    - 单播 : 把返回的信息再传递给要找机器的服务器
  - 要找的机器和你不在同一个局域网内
    - 先走交换机
    - 交换机直接将消息传递给"网关ip",通过ip找到对应机器所在的局域网
    - 路由表 :网段 网关ip
    - 通过对方局域网中的交换机进行广播
    - 把信息回传给交换机

- TCP协议属于 : 传输层
  - 面向连接 可靠的 字节流传输  长连接
- UDP协议属于 : 传输层
  - 面向数据包的 无连接的 不可靠的 速度快 不占用连接

## socket 服务

## 黏包现象

> TCP粘包是指发送方发送的若干包数据到接收方接收时粘成一包，从接收缓冲区看，后一包数据的头紧接着前一包数据的尾。

### 为什么出现粘包现象

1. 发送方原因：我们知道，TCP默认会使用Nagle算法。而Nagle算法主要做两件事：1）只有上一个分组得到确认，才会发送下一个分组；2）收集多个小分组，在一个确认到来时一起发送。所以，正是Nagle算法造成了发送方有可能造成粘包现象。

2. 接收方原因：TCP接收到分组时，并不会立刻送至应用层处理，或者说，应用层并不一定会立即处理；实际上，TCP将收到的分组保存至接收缓存里，然后应用程序主动从缓存里读收到的分组。这样一来，如果TCP接收分组的速度大于应用程序读分组的速度，多个包就会被存至缓存，应用程序读时，就会读到多个首尾相接粘到一起的包。

### 什么时候需要处理粘包现象

1. 如果发送方发送的多个分组本来就是同一个数据的不同部分，比如一个很大的文件被分成多个分组发送，这时，当然不需要处理粘包的现象；

2. 但如果多个分组本毫不相干，甚至是并列的关系，我们就一定要处理粘包问题了。比如，我当时要接收的每个分组都是一个有固定格式的商品信息，如果不处理粘包问题，每个读进来的分组我只会处理最前边的那个商品，后边的就会被丢弃。这显然不是我要的结果。

### 如何处理粘包现象

1. 发送方：对于发送方造成的粘包现象，我们可以通过关闭Nagle算法来解决，使用TCP_NODELAY选项来关闭Nagle算法。

2. 接收方：遗憾的是TCP并没有处理接收方粘包现象的机制，我们只能在应用层进行处理。

3. 应用层处理：应用层的处理简单易行！并且不仅可以解决接收方造成的粘包问题，还能解决发送方造成的粘包问题。解决方法就是循环处理：应用程序在处理从缓存读来的分组时，读完一条数据时，就应该循环读下一条数据，直到所有的数据都被处理；但是如何判断每条数据的长度呢？

###　两种途径

1. 格式化数据：每条数据有固定的格式（开始符、结束符），这种方法简单易行，但选择开始符和结束符的时候一定要注意每条数据的内部一定不能出现开始符或结束符；

2. 发送长度：发送每条数据的时候，将数据的长度一并发送，比如可以选择每条数据的前4位是数据的长度，应用层处理时可以根据长度来判断每条数据的开始和结束。

当时在做购物车的时候，我最开始的做法是设置开始符（0x7e）和结束符（0xe7），但在测试大量数据的时候，发现了数据异常。正如我所猜测，在调试过程中发现某些数据内部包含了它们。因为要处理的数据是量非常庞大，为做到万无一失，最后我采用了发送长度的方式。再也没有因为粘包而出过问题。

### tcp协议的拆包机制


当发送端缓冲区的长度大于网卡的MTU时，tcp会将这次发送的数据拆成几个数据包发送出去。 
MTU是Maximum Transmission Unit的缩写。意思是网络上传送的最大数据包。MTU的单位是字节。大部分网络设备的MTU都是1500。
如果本机的MTU比网关的MTU大，大的数据包就 会被拆开来传送，这样会产生很多数据包碎片，增加丢包率，降低网络速度

### 面向流的通信特点和Nagle算法

TCP（transport control protocol，传输控制协议）是面向连接的，面向流的，提供高可靠性服务。
收发两端（客户端和服务器端）都要有一一成对的socket，因此，发送端为了将多个发往接收端的包，更有效的发到对方，使用了优化方法（Nagle算法），将多次间隔较小且数据量小的数据，合并成一个大的数据块，然后进行封包。
这样，接收端，就难于分辨出来了，必须提供科学的拆包机制。 即面向流的通信是无消息保护边界的。 
对于空消息：tcp是基于数据流的，于是收发的消息不能为空，这就需要在客户端和服务端都添加空消息的处理机制，防止程序卡住，而udp是基于数据报的，即便是你输入的是空内容（直接回车），也可以被发送，udp协议会帮你封装上消息头发送过去。 
可靠黏包的tcp协议：tcp的协议数据不会丢，没有收完包，下次接收，会继续上次继续接收，己端总是在收到ack时才会清除缓冲区内容。数据是可靠的，但是会粘包。

### 黏包有两种：

一种是因为发送数据包时，每次发送的包小，因为系统进行优化算法，就将两次的包放在一起发送，减少了资源的重复占用。多次发送会经历多次网络延迟，一起发送会减少网络延迟的次数。因此在发送小数据时会将两次数据一起发送，而客户端接收时，则会一并接收。#即出现多次send会出现黏包

第二种是因为接收数据时，又多次接收，第一次接收的数据量小，导致数据还没接收完，就停下了，剩余的数据会缓存在内存中，然后等到下次接收时和下一波数据一起接收。

[黏包的解决方案](https://www.cnblogs.com/kakawith/p/8378425.html)

- udp
  - 基于数据包传输
  - 不会黏包，但会丢包
- tcp
  - 基于数据流传输
  - 出现黏包，但不会丢包

### 黏包现象情况

1. 连续send两个小数据
2. 两个recv, 第一个recv特别小；recv(数据的长度)

- 远程执行命令的程序
- ipconfig -> 2000
  - recv 1024
- dir -> 200
  - 976 + 200 -> 黏包
  
黏包问题：不知道到底要接受多大的数据

### 解决黏包

1. 首先发送一个这个数据到底有多大

- 文件中配置一个配置项：每一次recv的大小 buffer - 4096
- 当我们需发送大数据的时候，要明确的告诉接受方要发多大的数据，以便接收方能够准确的接受到所有数据
- 多用在文件传输的过程中
  - 大文件的传输一定是按照字节读 每一次读固定的字节
  - 传输的过程中 一边读一边传 接收端 一边收一边写
  - send 大文件之前，35672字节 send(4096) 35672 4096 4096 --> 0
  - recv 大文件，recv 35672 字节 recv(2048) 35672 2048 --> 0

- 不好的地方：多了一次交互
  - send sendto 在超过一定范围的时候都会报错
  - 程序的内存管理

2. 再按照数据的长度接受数据

### struct 模块

1. 什么事固定长度的 bytes
2. 为什么要转成固定长度的 bytes

