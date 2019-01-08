---
layout: 《深入浅出node》
title: 网络编程
date: 2018-12-21 11:33:52
categories: 读书笔记
tags: [node, 编程]
---

![network](https://user-gold-cdn.xitu.io/2018/12/21/167ceda84509da96?w=920&h=450&f=png&s=122361)

> 第七章 网络编程

!-- More -->

###  OSI 七层模型

OSI定义了网络互连的七层框架（物理层、数据链路层、网络层、传输层、会话层、表示层、应用层）

![model](https://user-gold-cdn.xitu.io/2018/12/20/167cc05df95ccae3?w=982&h=532&f=png&s=68841)

每一层实现各自的功能和协议，并完成与相邻层的接口通信。

* 物理层

网线 

* 数据链路层 

比如网卡，处理与物理设备做交互

* 网络层

ip 协议什么的，基础的网络

数据在网络中分组传输，网络层处理这些分组数据

* 传输层

tcp 协议什么的

处理端口之间的通信

* 会话层

* 表示层

处理数据安全等

* 应用层

http  https

处理网络数据和应用程序的交互


平时有用过的那个 nginx 是处理的7层，http协议部分

在四层中（数据链路层 、网络层、 传输层、应用层）运行的协议，称之为 TCP/IP协议族

### TCP/IP协议族

![tcp](https://user-gold-cdn.xitu.io/2017/11/11/b3017842bdddd50386f54ffd64d5491c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

数据传输的时候 在每一层会做加工处理

通过上图可以看出来，这七层，每一层都可以做的事情

1 将上层传递数据包装为满足该层协议的数据包

2 将下层的数据解析为满足该层协议的数据包

3 与其余层的数据交互


看一下常见的协议与七层的关系

![tcp](https://user-gold-cdn.xitu.io/2018/12/20/167cc24f5f4055e6?w=1464&h=531&f=png&s=79201)

### 端口

TCP 的包是不包含 IP 地址信息的，那是 IP 层上的事，但是有源端口和目的端口。

端口是传输层的东西

在同一台计算机，两个不同的进程 （pid为进程的标识） 之前进行数据传递，可以通过共享内存等方式。那如果是不同计算机的两个进程呢，需要借助另外的方式处理 --- 借助 端口（全称是协议端口号 (protocol port number)，简称端口）

套接字概念 ： IP 层的 ip 地址可以唯一标识主机，而 TCP 层协议和端口号可以唯一标识主机的一个进程，这样我们可以利用：「ip地址＋协议＋端口号」唯一标示网络中的一个进程

这就是说，虽然通信的重点是应用进程，但我们只要把要传送的报文交到目的主机的某一个合适的端口，剩下的工作就由 TCP 来完成了

### IP协议 （Internet Protocol）

按层次分,IP(Internet Protocol)网际协议位于网络层。

TCP/IP 协议族中的 IP 指的就是网际协 议,协议名称中占据了一半位置,其重要性可见一斑。可能有人会把“IP”和“IP 地址”搞混,“IP”其实是一种协 议的名称。
IP 协议的作用是把各种数据包传送给对方。而要保证确实传送到对方那里,则需要满足各类条件。其中两个 重要的条件是 IP 地址和 MAC 地址(Media Access Control Address)

IP 地址指明了节点被分配到的地址,MAC 地址是指网卡所属的固定地址。IP 地址可以和 MAC 地址进行配 对。IP 地址可变换,但 MAC 地址基本上不会更改

* ARP 协议 

IP 间的通信依赖 MAC 地址。在网络上,通信的双方在同一局域网(LAN)内的情况是很少的,通常是经过多台计算机和网络设备中转才能连接到对方。

而在进行中转时,会利用下一站中转设备的 MAC 地址来搜索下一 个中转目标。这时,会采用 ARP 协议(Address Resolution Protocol)。

ARP 是一种用以解析地址的协议, 根据通信方的 IP 地址就可以反查出对应的 MAC 地址。


### tcp 

按层次分,TCP 位于传输层,提供可靠的字节流服务。

* 字节流服务(Byte Stream Service)

为了方便传输,将大块数据分割成以报文段(segment) 为单位的数据包进行管理。而可靠的传输服务是指,能够把数据准确可靠地传给对方。一言以蔽之,TCP 协 议为了更容易传送大数据才把数据分割,而且 TCP 协议能够确认数据最终是否送达到对方。

* 三次握手

为了准确无误地将数据送达目标处,TCP 协议采用了三次握手(three-way handshaking)策略。用 TCP 协议把数据包送出去后, TCP 不会对传送后的情况置之不理,它一定会向对方确认是否成功送达



* 服务端

```js
let net = require('net')

let server = net.createServer(function (socket) {
    console.log('服务端：收到来自客户端的请求')

    socket.on('data', function (data) {
        console.log('服务端：收到客户端数据，内容为{' + data + '}')

        // 给客户端返回数据
        socket.write('你好，我是服务端')
    })

    socket.on('close', function () {
        console.log('服务端：客户端连接断开')
    })
})
server.listen('8124', 'localhost', function () {
    console.log('服务端：开始监听来自客户端的请求')
})
```

talnet 可以和服务器进行对话

![net](https://user-gold-cdn.xitu.io/2018/12/21/167cf60d3a3194f2?w=1736&h=596&f=png&s=151626)


### udp


udp 和 tcp 同属于网络传输层

创建udp套接字

```js
let dgram = require('dgram')
let server = dgram.createSocket('udp4')
```

server 既可以做为客户端发送数据 也可以作为服务端接受数据

dgram模块用于创建基于UDP协议的网络服务，创建不分客户端不分客户端、服务器端，在一端使用dgram.createSocket即可发送数据也可以接收数据。


### DNS (Domain Name System)

DNS 服务是和 HTTP 协议一样位于应用层的协议。它提供域名到 IP 地址之间的解析服务 (也可以逆向从 IP 地址反查域名 的服务)


### http

![data](https://user-gold-cdn.xitu.io/2018/12/27/167ef852f76f0686?w=1040&h=636&f=png&s=88930)

首先作为发送端的客户端在应用层(HTTP 协议)发出一个想看某个 Web 页面的 HTTP 请求。
接着,为了传输方便,在传输层(TCP 协议)把从应用层处收到的数据(HTTP 请求报文)进行分割,并在 各个报文上打上标记序号及端口号后转发给网络层。
在网络层(IP 协议),增加作为通信目的地的 MAC 地址后转发给链路层。这样一来,发往网络的通信请求 就准备齐全了。
接收端的服务器在链路层接收到数据,按序往上层发送,一直到应用层。当传输到应用层,才能算真正接收 到由客户端发送过来的 HTTP 请求。

* 三次握手

```js

```

* 请求报文

```js
 GET /life/index HTTP/1.1
 Host: 127.0.0.1:9997
 User-Agent: curl/7.43.0
 Accept: */*
```

| 参数 | 含义 |
| ----------- | ---------- |
| GET | 表示请求访问服务器的类型 |
| /life/index | 请求的资源对象 |
| HTTP/1.1 | 客户端使用的 HTTP 协议 | 

这段请求内容的意思是:请求访问某台 HTTP 服务器上的 /life/index 资源

请求报文是由请求方法、请求 URI、协议版本、可选的请求首部字段和内容实体构成的。

* 相应报文

```js
 HTTP/1.1 200 OK
 Content-Type: text/html; charset=utf-8
 Content-Length: 13976
 Date: Thu, 27 Dec 2018 12:32:38 GMT
 Connection: keep-alive

 <!doctype html>html ....
```

| 参数 | 含义 |
| ----------- | ---------- |
| HTTP/1.1 | 服务器使用的 HTTP 协议 | 
| 200 ok |请求的处理结果的状态码(status code)和原因短语(reason-phrase) |

响应报文基本上由协议版本、状态码(表示请求成功或失败的数字代码)、用以解释状态码的原因短语、可选的响应首部字段以及实体主体构成