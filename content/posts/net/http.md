---
title: "Http各版本"
date: 2022-08-01T23:01:14+08:00
draft: false
tags: [""]
categories: [""]
typora-root-url: ..\..\static
---

## 总结

|            | 1.0                                                 | 1.1      | 2.0                                                          |
| ---------- | --------------------------------------------------- | -------- | ------------------------------------------------------------ |
| 长连接     | 需要使用`keep-alive` 参数来告知服务端建立一个长连接 | 默认支持 | 默认支持                                                     |
| HOST域     | ✘                                                   | ✔️        | ✔️                                                            |
| 多路复用   | ✘                                                   | -        | ✔️                                                            |
| 数据压缩   | ✘                                                   | ✘        | 使用`HAPCK`算法对header数据进行压缩，使数据体积变小，传输更快 |
| 服务器推送 | ✘                                                   | ✘        | ✔️                                                            |

### 概念

HTTP是一种`超文本传输协议（Hypertext Transfer Protocol)`，HTTP是一个基于TCP实现的应用层协议。一个两点之间传输数据的约定和规范。


### 组成

分为三部分，超文本、传输、协议

- 超文本是不止文本，还包含图片、音频、视频等数据
- 传输是数据从一端系统传送到另一端系统的过程。通常我们把传输数据包的一方称为请求方，把接到二进制数据包的一方称为应答方。
- 协议是指传输的规范、规则。

### 特点

1. 灵活可扩展。一个是语法上只规定了基本格式，空格分隔单次，换行分隔字段等。另外一个就是传输形式上不仅可以传输文本，还可以传输图片，视频等任意数据。
2. 请求-应答模式。通常而言，就是一发发送消息，另外一方接受消息 。
3. 可靠传输，http是基于TCP/IP,因此把这一特性继承下来。
4. 无状态，只负责发信息，不保存信息，需要通过cookie等保存信息。

### 缺点

1. 明文传输。即协议中的报文（主要指头部）不适用二进制数据，而是文本形式。这让HTTP的报文信息暴露给了外界，给攻击者带来了便利。
2. 队头阻塞。当http开启长连接时，共用一个TCP连接，当某个请求时间过长时，其他的请求只能处于阻塞状态。
3. 无状态，只负责发信息，不保存信息。

## http 1.0

- 标准：
  - 任何格式的内容都可以发送，这使得互联网不仅可以传输文字，还能传输图像、视频、二进制等文件。
  - 通常每台计算机只能绑定一个 IP，所以请求消息中的 URL 并没有传递主机名（hostname）
- 性能：
  - HTTP 1.0 被设计用来使用短连接，即每次发送数据都会经过 TCP 的三次握手和四次挥手，效率比较低。
  - 不支持断点续传，也就是说，每次都会传送全部的页面和数据。
  - 只使用 header 中的 If-Modified-Since 和 Expires 作为缓存失效的标准。

- 方法：
  - 支持GET、POST、HEAD
- 安全：
  - HTTP 1.0 仅仅提供了最基本的认证，这时候用户名和密码还未经加密，因此很容易收到窥探。


## http 1.1

### 与http1.0区别

- 标准
  - 虚拟主机的支持：使用虚拟网络，在一台服务器上可以存在多个虚拟主机（Multi-homed Web Servers），并且共享一个IP地址
  - 引入Cookie。

- 性能：
  - 引入持久连接（ persistent connection），即TCP连接默认不关闭，可以被多个请求复用，不用声明Connection: keep-alive。长连接的连接时长可以通过请求头中的 `keep-alive` 来设置。
  - 引入管道机制。即在同一个TCP连接里，客户端可以同时发送多个请求。
  - 缓存处理：HTTP 1.1 中新增加了 E-tag，If-Unmodified-Since, If-Match, If-None-Match 等缓存控制标头来控制缓存失效。
  - 支持断点续传，通过使用请求头中的`range`来实现。
- 方法：
  - 新增PUT、 PATCH、 OPTIONS、 DELETE。
- 安全：
  - 使用摘要算法进行身份验证。

### 存在问题

2. 队头阻塞问题，HTTP/1.1 默认允许复用TCP连接，但是在同一个TCP连接里，所有数据通信是按次序进行的，服务器通常在处理完一个回应后，才会继续去处理下一个，这样子就会造成队头阻塞。
2. 在传输数据过程中，所有内容都是明文，客户端和服务器端都无法验证对方的身份，无法保证数据的安全性。

## http 2.0

### 特性

- 多路复用（即一个tcp/ip连接可以并发请求多个资源）  
- 头部压缩（http头部压缩，减少体积）  
- 二进制分帧（在应用层跟传送层之间增加了一个二进制分帧层，将数据切分为数据帧，改进传输性能，实现低延迟和高吞吐量）  
- 服务器端推送（即SSE，服务端可以对客户端的一个请求发出多个响应，可以主动通知客户端）  
- 请求优先级（如果流被赋予了优先级，它就会基于这个优先级来处理，由服务器决定需要多少资源来处理该请求。）  

### 与http1.1不同点

- http1.1中，一个TCP请求可以发送多个请求，但只能按顺序一个一个请求。如果想并发多个请求，必须使用多个 TCP/ip链接，且浏览器为了控制资源，还会对单个域名有 6-8个的TCP链接请求限制。 
- http2.0中，只要一个tcp请求可以并发请求多个资源，分割成更小的帧请求，速度明显提升。

### 多路复用

- HTTP1.0： 每次请求晌应，建议一个 Tcp 连接，用完关闭。
- HTTP1.1： 「长连接 J 若干个谓求排队串行化单线程处理，后面的谓求等待前面诵求的返回才能获得执行机会，一旦有某请求超时等，后续请求会被阻塞，也就是人们常说的线头阻塞；
- HTTP2.0： [多路复用]多个请求可同时在一个连接上并行执行，某个请求任务耗时亚种，不会形晌到其它连接的正常执行；

> HTTP/2解决的问题，就是HTTP/1.1存在的问题。

**TCP慢启动：** TCP连接建立后，会经历一个先慢后快的发送过程，就像汽车启动一般，如果我们的网页文件(HTML/JS/CSS/icon)都经过一次慢启动，对性能是不小的损耗。另外慢启动是TCP为了减少网络拥塞的一种策略，我们是没有办法改变的。

**多条TCP连接竞争带宽：** 如果同时建立多条TCP连接，当带宽不足时就会竞争带宽，影响关键资源的下载。

### 关于队头拥塞

**HTTP/1.1：** 尽管HTTP/1.1长链接可以通过一个TCP连接传输多个请求，但同一时刻只能处理一个请求，当前请求未结束前，其他请求只能处于阻塞状态。

为了解决以上几个问题，HTTP/2一个域名只使用一个TCP⻓连接来传输数据，而且请求直接是并行的、非阻塞的，这就是多路复用。

**实现原理：** HTTP/2引入了一个二进制分帧层，客户端和服务端进行传输时，数据会先经过二进制分帧层处理，转化为一个个带有请求ID的帧，这些帧在传输完成后根据ID组合成对应的数据。

**HTTP/2.0**：HTTP2 协议是基于 TCP 的，但是 TCP 本身是无法解决队头拥塞，为什么呢？因为 HTTP2 会把一次传输所有的文件都放在一个 TCP 连接中，只要这个 TCP 中发生一个丢包，连接就必须重新建立，之前所有传输内容进行必须重传，从而造成拥塞。

## Http 3.0



## 引用

[关于http2.0的HTTP 2.0 的二进制帧、流、多路复用](https://juejin.im/post/5c88f2066fb9a049c043e420)
[HTTP2 详解](https://blog.wangriyu.wang/2018/05-HTTP2.html)
[http发展史(http0.9、http1.0、http1.1、http2、http3)梳理笔记](https://juejin.im/post/5dbe8eba5188254fe019dabb#heading-9)

[HTTP协议头部与Keep-Alive](https://www.cnblogs.com/happy-king/p/9603395.html)
[解读HTTP/2 及 HTTP/3特性](https://github.com/ljianshu/Blog/issues/57)
[HTTP/1.0、HTTP/1.1、HTTP/2、HTTPS](https://zhuanlan.zhihu.com/p/43787334)

[HTTP/1.1，HTTP/2和HTTP/3的区别 视频讲解](https://www.bilibili.com/video/BV1vv4y1U77y/?buvid=XX7F2E679C472834BFEA30EBF7C2E86E3B0BB&is_story_h5=false&mid=N9gFrUhL925gIojyS36eOA%3D%3D&p=1&plat_id=168&share_from=ugc&share_medium=android&share_plat=android&share_session_id=8a07f717-9646-47ec-afff-58bdbc0445d8&share_source=WEIXIN&share_tag=s_i&timestamp=1677857095&unique_k=90sxO90&up_id=327247876&vd_source=0ab35fe7d5d150155b5838efffedc59d)