---
title: "从输入URL到页面呈现发生了什么"
date: 2022-09-03T17:01:05+08:00
draft: false
tags: [""]
categories: [""]
typora-root-url: ..\..\static
---

# 简单叙述

1. 首先浏览器主进程接管，开了一个下载线程。
2.  然后进行HTTP请求（DNS查询、IP寻址等等），中间会有三次捂手，等待响应，开始下载响应报文。
3.  将下载完的内容转交给Renderer进程管理。
4.  Renderer进程开始解析css rule tree和dom tree，这两个过程是并行的，所以一般我会把link标签放在页面顶部。
5.  解析绘制过程中，当浏览器遇到link标签或者script、img等标签，浏览器会去下载这些内容，遇到时候缓存的使用缓存，不适用缓存的重新下载资源。
6.  css rule tree和dom tree生成完了之后，开始合成render tree，这个时候浏览器会进行layout，开始计算每一个节点的位置，然后进行绘制。
7.  绘制结束后，关闭TCP连接，过程有四次挥手。

## 主流程

1. DNS解析
2. TCP连接
3. HTTP请求与响应
4. 客户端浏览器解析渲染
5. 连接结束

## 详细流程

### 从输入url到开启网络请求线程

![image-20220903173031430](https://raw.githubusercontent.com/lxw15337674/PicGo_image/main/image-20220903173031430.png)

> URL中文名叫做统一资源定位符。
> 主要包含
>
> - protocol（协议）
> - hostname（主机名）
> - port（端口号）
> - path（路径）

当输入网址时，浏览器会从历史记录、书签等智能匹配url给出提示。
输入完成后，浏览器会新开一个页面进程，然后这个进程会开启一个网络线程进行http请求。

### 网络请求

#### 查找强缓存

先检查强缓存，如果命中直接使用。

#### DNS解析

如果输入IP地址，则不需要DNS解析，如果输入域名，则需要进行DNS解析获取IP地址。

查找顺序：浏览器缓存-->操作系统缓存-->本地host文件-->路由器缓存-->ISP DNS缓存-->根DNS服务器

#### 建立TCP连接

如果是HTTPS，要进行TLS/SSL四次握手


以随机端口（1024<端口<63535)向服务器的web程序通过三次握手建立tcp连接。


#### 发送HTTP请求

完成tcp连接后，浏览器可以向服务器发送HTTP请求。浏览器HTTP请求包含三部分：请求行、请求头、请求体、

请求行包含请求方法、路径、HTTP协议版本。

 > // 请求方法是GET，路径为根路径（URI），HTTP协议版本为1.1
 > GET / HTTP/1.1

请求头包含各种属性，例如缓存，Cookie，时间，连接方式（是否长连接），CORS等相关。

### 服务器处理

- 负载均衡，利用nginx之类进行反向代理，根据调度算法把请求分给服务器执行。
- 服务器的容器根据端口接收到请求，然后由对应的后台程序对请求进行处理。
- 后台会判断协商缓存，如果是，则直接返回。如果不是会先由统一的验证（类似axios的响应拦截器）如安全拦截、跨域验证。
- 通过后再根据路径，执行对应方法，执行完毕后返回一个http响应包。

### 浏览器解析渲染页面

浏览器获得HTML、css、js文件后，开始进行解析：

1. 解析html建立dom树
2. 解析css构建style树
3. dom树和style树两者结合生成布局（Layout)树，然后计算布局树节点的坐标位置。

> 值得注意的是，这棵布局树值包含可见元素，对于 head标签和设置了display: none的元素，将不会被放入其中。

渲染过程：

1. 建立图层树（Layer Tree)
2. 生成绘制列表
3. 生成图块并栅格化
4. 显示器显示内容

图层树是根据节点的属性，例如z-index，产生的层叠上下文。
浏览器渲染的图层一般包含两大类：普通图层以及复合图层。
普通文档流可以理解为一个复合图层。
通过z-idnex、opacity、translate3D的css动画属性等会生成复合图层，两者可单独绘制，互不影响

![image](https://raw.githubusercontent.com/lxw15337674/PicGo_image/main/bVCZ1H)


## 引用

[输入URL背后的技术步骤](https://lq782655835.github.io/blogs/js/http-base-1.url.html)

[神三元](http://47.98.159.95/my_blog/browser-render/001.html)
[浏览器层合成与页面渲染优化](https://juejin.im/post/5da52531518825094e373372)

[[各种公司] 输入url后发生了什么](https://juejin.im/post/6844904110471249934#heading-3)

[面试官：浏览器输入URL后发生了什么？](https://mp.weixin.qq.com/s?__biz=MzI0MzIyMDM5Ng==&mid=2649826404&idx=1&sn=e4ccac5fe9d96b26ca1d8d347276d2b1&chksm=f175eea7c60267b1b5b91e527dcdf81027f2f36c97cb1837b13ffbb5a1699e013a77f84d1d16&scene=21#wechat_redirect)