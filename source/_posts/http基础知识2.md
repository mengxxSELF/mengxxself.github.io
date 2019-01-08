---
title: http基础知识2
date: 2017-11-06 13:55:26
categories: http
tags: [http]
---

http 的报文由起始行 头部 主体构成 包含许多信息

请求行包含用于请求的方法，请求 URI 和 HTTP 版本。 状态行包含表明响应结果的状态码，原因短语和 HTTP 版本

<!--more-->

### http请求报文

#### 请求方式

> HTTP/1.1协议中共定义了八种方法（有时也叫“动作”）来表明Request-URI指定的资源的不同操作方式

本文只讲解两种 对其余有兴趣的同学可自行阅读

* GET 向特定的资源发出请求

* POST

向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。

POST请求可能会导致新的资源的建立和/或已有资源的修改

方法名称是区分大小写的。当某个请求所针对的资源不支持对应的请求方法的时候，服务器应当返回状态码405（Method Not Allowed）；当服务器不认识或者不支持对应的请求方法的时候，应当返回状态码501（Not Implemented）

#### GET POST

GET一般用于获取/查询资源信息，而POST一般用于更新资源信息

GET和POST的区别：

1、传递数据方式不同

GET提交的数据会放在URL之后，以?分割URL和传输数据，参数之间以&相连，如EditPosts.aspx?name=test1&id=123456.

POST方法是把提交的数据放在HTTP包的Body中。

2、数据大小限制不同

GET提交的数据大小有限制，最多只能有1024字节（因为浏览器对URL的长度有限制），而POST方法提交的数据没有限制。


3、安全方面

GET方式提交数据，会带来安全问题，比如一个登录页面，通过GET方式提交数据时，用户名和密码将出现在URL上，如果页面可以被缓存或者其他人可以访问这台机器，就可以从历史记录获得该用户的账号和密码。

所以涉及到用户信息的 最好使用post请求处理


### http响应报文

常用的http响应状态码

* 200 成功

* 3xx 重定向

* 4xx 出错

* 5xx 服务器出错


###  参考文章

1 [http协议扫盲](http://www.jianshu.com/p/d8a7becd0fd1)

2 [http网络请求 返回statusCode](http://blog.csdn.net/zhangmengleiblog/article/details/52513227)

3 [99%的人都理解错了HTTP中GET与POST的区别](http://mp.weixin.qq.com/s?__biz=MzI3NzIzMzg3Mw==&mid=100000054&idx=1&sn=71f6c214f3833d9ca20b9f7dcd9d33e4#rd)

4 [HTTP协议中GET和POST方法的区别](https://sunshinevvv.coding.me/blog/2017/02/09/HttpGETv.s.POST/)
