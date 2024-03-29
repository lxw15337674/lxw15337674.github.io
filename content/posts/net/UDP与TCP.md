---
title: "UDP与TCP.md"
date: 2022-09-04T12:32:33+08:00
draft: false
tags: [""]
categories: [""]
typora-root-url: ..\..\static
---

## 总结
TCP (`Transmission Control Protocol`)：传输控制协议；提供面向连接的，可靠的数据传输服务。
UDP（`User Datagram Protocol`）：用户数据报协议；提供无连接的，尽最大努力的数据传输服务（不保证数据传输的可靠性）。

## UDP
### 概念
全称`User Datagram Protocol`,用户数据报协议。它不需要所谓的握手操作，从而加快了通信速度，允许网络上的其他主机在接收方同意通信之前进行数据传输。
> 数据报是与分组交换网络关联的传输单元。
### 特点
- UDP 是无连接的；
- UDP 使用尽最大努力交付，即不保证可靠交付，因此主机不需要维持复杂的链接状态；
- UDP 是面向报文的；
- UDP 没有拥塞控制，因此网络出现拥塞不会使源主机的发送速率降低（对实时应用很有用，如直播，实时视频会议等）；
- UDP 支持一对一、一对多、多对一和多对多的交互通信；
- UDP 的首部开销小，只有 8 个字节，比 TCP 的 20 个字节的首部要短。

## TCP
### 详解
https://juejin.im/post/5ea040f96fb9a03c786f1ffc
### 概念
全称`Transmission Control Protocol`， 传输控制协议。它能够帮助你确定计算机连接到 Internet 以及它们之间的数据传输。通过三次握手来建立 TCP 连接，三次握手就是用来启动和确认 TCP 连接的过程。一旦连接建立后，就可以发送数据了，当数据传输完成后，会通过关闭虚拟电路来断开连接。
### 特点
- TCP 是面向连接的（需要先建立连接）；
- 每一条 TCP 连接只能有两个端点，每一条 TCP 连接只能是一对一；
- TCP 提供全双工通信。TCP 允许通信双方的应用进程在任何时候都能发送数据。TCP 连接的两端都设有发送缓存和接收缓存，用来临时存放双方通信的数据；
- TCP提供可靠交付的服务。通过TCP连接传送的数据，无差错、不丢失、不重复、并且按序到达；
- 面向字节流。TCP 中的“流”（Stream）指的是流入进程或从进程流出的字节序列。

>1. 单工数据传输只支持数据在一个方向上传输
>2. 半双工数据传输允许数据在两个方向上传输，但是，在某一时刻，只允许数据在一个方向上传输，它实际上是一种切换方向的单工通信；
>3. 全双工数据通信允许数据同时在两个方向上传输，因此，全双工通信是两个单工通信方式的结合，它要求发送设备和接收设备都有独立的接收和发送能力。

### 可靠原因
1. 数据包校验：目的是检测数据在传输过程中的任何变化，若校验出包有错，则丢弃报文段并且不给出响应，这时 TCP 发送数据端超时后会重发数据；

2. 对失序数据包重排序：既然 TCP 报文段作为 IP 数据报来传输，而 IP 数据报的到达可能会失序，因此 TCP 报文段的到达也可能会失序。TCP 将对失序数据进行重新排序，然后才交给应用层；

3. 丢弃重复数据：对于重复数据，能够丢弃重复数据；

4. 应答机制：当 TCP 收到发自 TCP 连接另一端的数据，它将发送一个确认。这个确认不是立即发送，通常将推迟几分之一秒；

5. 超时重发：当 TCP 发出一个段后，它启动一个定时器，等待目的端确认收到这个报文段。如果不能及时收到一个确认，将重发这个报文段；

6. 流量控制：TCP 连接的每一方都有固定大小的缓冲空间。TCP 的接收端只允许另一端发送接收端缓冲区所能接纳的数据，这可以防止较快主机致使较慢主机的缓冲区溢出，这就是流量控制。

### TCP/IP协议
IP 协议的作用是把各种数据包传送给对方。而要保证确实传送到对方那里，则需要满足各类条件，其中两个重要的条件是 IP 地址和 MAC 地址（Media Access Control Address）。
> IP 地址和 MAC 地址：指明了节点被分配到的地址，MAC 地址是指网卡所属的固定地址，IP 地址可以和 MAC 地址进行配对。IP 地址可变换，但 MAC 地址基本上不会更改。
>

使用 ARP 协议凭借 MAC 地址进行通信
1. IP 间的通信依赖 MAC 地址。
2. ARP 是一种用以解释地址的协议，根据通信方的 IP 地址就可以反查出对应方的 MAC 地址。

![image](https://mmbiz.qpic.cn/mmbiz_jpg/iccXN8sGPLT5NB9m3hYAibU72RswyO9mQ4PcfzSHetWTGKoeCojNVGawF7MPDply9KIvia7dtmLKdWz5zlZ1YUx2g/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

## 区别

| TCP                                                  | UDP                                      |
| ---------------------------------------------------- | ---------------------------------------- |
| 面向连接的协议                                       | 无连接的协议                             |
| 发送数据前都需要经过三次握手建立连接、然后再发送数据 | 无需建立连接就可以直接发送大量数据       |
| 会按照特定顺序重新排列数据包                         | 数据包没有固定顺序，所有数据包都相互独立 |
| 传输速度慢                                           | 传输速度快                               |
| 头部字节有20字节                                     | 头部字节有8字节                          |
| 会进行错误校验，并能够进行错误恢复                   | UDP会错误检查，但会丢失错误的数据包      |
| 有发送确认                                           | 没有发送确认                             |
| 会使用握手协议，例如SYN,SYN-ACK,ACK                  | 无握手协议                               |
| 可靠的，可以确保数据传送到目标                       | 不能保证讲数据传送到目标                 |

## 详解
[TCP和UDP比较](https://github.com/ljianshu/Blog/issues/61)