---
title: electron折腾记录
date: 2019-02-12 16:16:11
tags:
---

![electron](https://user-gold-cdn.xitu.io/2019/2/12/168e0ca57656a566?w=1556&h=732&f=png&s=242686)

<!-- More -->

> electron

之前用vue写了一个本地开启服务器demo，[git 地址](https://github.com/mengxxSELF/vue-link)  核心思路是在server端直接开启一个启动shell脚本的子进程。本来想丢到服务器给大家用，这样不用每次都去翻服务器配置文件了。

核心代码为

![](https://user-gold-cdn.xitu.io/2019/1/31/168a2f22a898a688?w=1050&h=1324&f=png&s=243579)

然而，组内大佬过来看了两眼就发现问题了：开启登录服务器的那个进程是在server端处理的，那么项目如果部署到服务器之后，怎么能开启用户自己的 终端去登录服务器呢？

![electron](https://user-gold-cdn.xitu.io/2019/1/31/168a2ef8a7ec4f42?w=189&h=172&f=png&s=54222)

大佬建议使用electron包裹一层，也就是把开启终端的那个部分的逻辑放到electron操作 而不是在server中处理

赞！

项目之前是vue版本的，刚好vue 有一套配合electron的轮子可以直接用。


### 项目安装

vue init simulatedgreg/electron-vue vvbuild

* 选择区别

electron-packager vs electron-builder

后者会帮助配置一些打包相关的问题，要比前面的全

### 开发

#### 入口文件

在 oackage.json 中的main参数 是electron的入口文件 （ 主进程 ） 

```js
  "main": "./dist/electron/main.js",
```

electron运行package.json中的main字段标明脚本的进程称为主进程

在主进程创建web页面来展示用户页面，一个electron有且只有一个主进程

electron使用Chromium来展示web页面，每个页面运行在自己的渲染进程中

不过上面的那个路径是编译后的，我们自开发的时候，主进程文件地址是 src/main/index.js

这里面的配置已经由轮子创建了好了，对比直接使用electron，多了一些环境配置

#### vue 组件部分

##### Table

用于进行展示列表数据

所有的服务器配置信息都在本地 /tmp/config 文件中 直接从这里读取

```js
  mounted() {
    // 读取用户本地有没有 /tmp/
    const isEixst = fs.existsSync(configRoot);
    if (!isEixst) return;
    const result = JSON.parse(fs.readFileSync(configRoot, 'utf-8'));
    this.tableData = result;
  },
```
所有的服务器配置信息全部是在用户本地 /tmp/config.json  其实在这里存储有一个不好的地方 -- 一旦关机 此文件就会被清除

#### 开启进程


![link](https://user-gold-cdn.xitu.io/2019/2/1/168a78ccc529b72a?w=1096&h=120&f=png&s=13034)

```js
    handleClick() {
        const content = `#!/bin/bash \n  \n ${server} ${address}`;
        fs.writeFileSync(shRoot, content);
        // 2 给脚本增加运行权限
        exec(`chmod +x ${shRoot}`, { silent: true });
        // 3 打开一个新的终端去执行这文件
        exec(`open -a Terminal  ${shRoot}`)
    }    
```

点击链接服器之后  由于electron 提供了node环境 可以直接打开终端


### 遇到的问题

#### build 停滞

开始使用的是 npm 去执行build 但是在 

![](https://user-gold-cdn.xitu.io/2019/1/22/1687575fa826f103?w=1291&h=115&f=png&s=23819)

这里就一直停止不动 等了好久 后面尝试了 删除 node_modules 重新来依旧不动

最后换成yarn 重新来就可以了

yarn build:darwin


#### 打开开发者工具

在开发过程中 这个app使用快捷键可以打开开发者 但是一旦编译 就打不开了

在build -> electron.js 中

添加代码如下

```js
  mainWindow.webContents.openDevTools({ mode: 'left' });
```
相当于自动打开APP的开发者工具

#### 编译之后的APP 打开出错

前一天可以编译成功的APP 今天忽然就不可以了

![](https://user-gold-cdn.xitu.io/2019/1/22/16874ece27f3f6d9?w=774&h=638&f=png&s=210608)

本地启动是可以的

尝试解决方案

* 删除 node_modules 重新安装

#### 莫名其妙的问题

遇到奇奇怪怪的问题，一律尝试 删除node_modules 重新 yarn 安装