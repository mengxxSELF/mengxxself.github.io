---
title: electron开发笔记
date: 2019-02-12 16:16:11
categories: javascript
tags: [electron]
---

![electron](https://user-gold-cdn.xitu.io/2019/2/12/168e0ca57656a566?w=1556&h=732&f=png&s=242686)

<!-- More -->

> electron


### electron

#### 主进程 && 渲染进程

渲染进程像是一个普通的web组件,主进程中一看就是偏向 node 风格

运行 package.json 里 main 脚本的进程被称为主进程。在主进程运行的脚本可以以创建 web 页面的形式展示 GUI - 相当于一个 Chrome的浏览器

由于 Electron 使用 Chromium 来展示页面，所以 Chromium 的多进程结构也被充分利用。每个 Electron 的页面都在运行着自己的进程，也就是渲染进程。主进程管理所有页面和与之对应的渲染进程。每个渲染进程都是相互独立的，并且只关心他们自己的网页。

##### 进程通信

electron中的主进程和渲染进程进行通信的时候，就需要用到ipc这个特性。而ipc又分为ipcMain和ipcRenderer两个方面，分别用于主进程和渲染进程

* 主进程
  
主进程中，使用 ipcMain 只能接受消息， 但是 ipcMain 没有提供在主进程中发送消息的方法

```js
const electron = require('electron'); //electron 对象的引用
const { app, ipcMain } = electron;

// 接受信息
ipcMain.on('checkVersion', function(event, arg) {
  // 发送信息
  event.sender.send('send back', "主进程接受到消息了')
}

// 发送信息
mainWindow.webContents.send('message', message);

```

如果主进程想要给渲染进程主动通信，可以使用两种方法 1 ipcMain 回调中的event 对象 2 借助 webContents

* 渲染进程

渲染进程 中通过 ipcRenderer 来接受消息和发送消息
  
```js
  const { ipcRenderer } = require('electron')

  const ipcRenderer = electron.ipcRenderer

  // 接受信息
  ipcRenderer.on('newversion', function (event, message) {
    console.log('newversion')
  })

  // 发送信息
  ipcRenderer.send('checkVersion');

```

### electron-vue 

electron-vue  是一个结合 vue-cli 与 electron 的项目，主要避免了使用 vue 手动建立起 electron 应用程序，很方便。

#### electron-packager vs electron-builder

提供了两种打包方式

* electron-packager

![pack](https://user-gold-cdn.xitu.io/2019/3/1/16937ea5221f9ae7?w=1746&h=868&f=png&s=256326)

针对个人系统选择打包之后的结果是


* electron-builder

![build](https://user-gold-cdn.xitu.io/2019/3/1/16937ec93bd86a85?w=1714&h=728&f=png&s=196330)

#### electron-upadter

打包之后可以进一步实现自动更新

这里有个需要注意的问题，upadter 功能处理自动更新的时候，需要使用应用签名

对于 Mac而言，要先具备 Apple Developer 中开发者资格 才能在本机创建相关 签名

* [Electron 打包Mac安装包代码签名问题解决方案](https://segmentfault.com/a/1190000012902525)

更换了查询更新的方案，通过服务器上的接口进行通信，将版本信息写入服务器的静态文件来判断

### 个人应用

之前用vue写了一个本地开启服务器demo，[git 地址](https://github.com/mengxxSELF/vue-link)  核心思路是在server端直接开启一个启动shell脚本的子进程。本来想丢到服务器给大家用，这样不用每次都去翻服务器配置文件了。

核心代码为

![](https://user-gold-cdn.xitu.io/2019/1/31/168a2f22a898a688?w=1050&h=1324&f=png&s=243579)

然而，组内大佬过来看了两眼就发现问题了：开启登录服务器的那个进程是在server端处理的，那么项目如果部署到服务器之后，怎么能开启用户自己的 终端去登录服务器呢？

![electron](https://user-gold-cdn.xitu.io/2019/1/31/168a2ef8a7ec4f42?w=189&h=172&f=png&s=54222)

大佬建议使用electron包裹一层，也就是把开启终端的那个部分的逻辑放到electron操作 而不是在server中处理

赞！

项目之前是vue版本的，刚好vue 有一套配合electron的轮子可以直接用。


* 项目安装

vue init simulatedgreg/electron-vue vvbuild

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

#### 功能

##### 基本配置

用于进行展示列表数据

个人服务器配置信息都在本地 /tmp/config 文件中 直接从这里读取

```js
  mounted() {
    // 读取用户本地有没有 /tmp/
    const isEixst = fs.existsSync(configRoot);
    if (!isEixst) return;
    const result = JSON.parse(fs.readFileSync(configRoot, 'utf-8'));
    this.tableData = result;
  },
```

后期增加了数据共享功能，通过接口将数据写入mysql数据库中，因此页面中增加了分组配置展示部分

![groups](https://user-gold-cdn.xitu.io/2019/3/15/169807afa26d0e20?w=881&h=158&f=png&s=19159)

* 用户本地 /tmp/config.json  其实在这里存储有一个不好的地方 -- 一旦关机 此文件就会被清除

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

由于electron 提供了node环境 可以直接打开终端 

electron 中可以使用shell，比如开启一个网页

```js
import { shell } from 'electron';

shell.openExternal('http://www.baidu.com')

```

### 遇到的问题

#### eslint

electron-vue 默认开启 eslint检查  开发中一旦不遵守规则就会报错 非常不友好  后期调整了eslint配置

涉及的文件有三个，都位于目录.electron-vue/下，分别是：

```js
.electron-vue/webpack.main.config.js
.electron-vue/webpack.renderer.config.js
.electron-vue/webpack.web.config.js
```

由于在 webpack中加入了eslint-loader检测 所以会有编译检查 可以直接注释掉

#### build 停滞

开始使用的是 npm 去执行build 但是在 

![](https://user-gold-cdn.xitu.io/2019/1/22/1687575fa826f103?w=1291&h=115&f=png&s=23819)

这里就一直停止不动 等了好久 后面尝试了 删除 node_modules 重新来依旧不动

最后换成yarn 重新来就可以了

```js
yarn build:darwin
```

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

#### 跨域处理

渲染进程中有涉及多个接口请求，但是会有跨域问题，electron提供了一个参数可以处理

```js
mainWindow = new BrowserWindow({
  webPreferences: {webSecurity: false},
}) 
```

完全不需要设置服务端，非常方便

#### 无法使用复制粘贴

mac 在开发中是可以是用复制粘贴功能的，但是一旦打包编译，就会发现快捷键失效，需要开发者自行实现这种功能


主进程中配置

```js
import { Menu } from 'electron'

const template = [
  {
    label: 'Application',
    submenu: [
      {
        label: 'Quit',
        accelerator: 'Command+Q',
        click: function () {
          app.quit()
        }
      }
    ]
  },
  {
    label: 'Edit',
    submenu: [
      {label: 'Copy', accelerator: 'CmdOrCtrl+C', selector: 'copy:'},
      {label: 'Paste', accelerator: 'CmdOrCtrl+V', selector: 'paste:'}
    ]
  }
]

Menu.setApplicationMenu(Menu.buildFromTemplate(template))

```

#### 莫名其妙的问题

遇到奇奇怪怪的问题，一律尝试 删除node_modules 重新 yarn 安装


### 参考文章

* [Electron-vue开发实战1——Main进程和Renderer进程的简单开发](https://molunerfinn.com/electron-vue-2/#ipcMain%E5%92%8CipcRenderer)