---
title: pm2
date: 2017-10-01 19:35:35
categories: 服务端
tags: [server]
---


### 什么是pm2

pm2 是一个带有负载均衡功能的Node应用的进程管理器

[GitHub地址](https://github.com/Unitech/pm2)

<!--more -->


### 安装

```javascript
npm install -g pm2
```

还可以使用淘宝滤镜

```javascript
npm install -g pm2 --registry=https://registry.npm.taobao.org
```

### 使用

* pm2 list  显示所有进程状态

* pm2 start app.js 开始进程

-- watch 监控变化并自动重启

* pm2 stop 停止进程

pm2 stop 0             # 停止指定的进程

pm2 stop all           # 停止所有进程

* pm2 restart 重启进程

pm2 restart 0          # 重启指定的进程

pm2 restart all        # 重启所有进程

pm2 reload all         # 0秒停机重载进程 (用于 NETWORKED 进程)


* pm2 delete 杀掉进程

pm2 delete 0  杀死指定的进程

pm2 delete all  杀死全部进程

* pm2 logs  查看日志

比如某个node服务突然异常重启了，那么可以通过pm2提供的日志工具来查看实时日志，看是不是脚本出错之类导致的异常重启

