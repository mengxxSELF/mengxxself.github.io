---
title: Node中包的查找机制
date: 2018-10-26 11:06:34
categories: node
tags: [基础知识]
---

![npm](https://user-gold-cdn.xitu.io/2018/12/25/167e37f97791f498?w=944&h=351&f=png&s=57015)

> 模块查找

<!-- More -->

### 项目中引用的模块

Node中的模块可以分为三类，根据模块类型不同，其使用方式也有差别

核心模块  自定义模块  文件模块

核心模块是Node本身就提供的模块，会在Node源码编译时就编译为二进制执行文件并在Node进程启动时加载进内存

自定义模块和文件模块从加载形式上可以分为一类，都是在运行时动态加载，区别在于文件查找的策略不同

Node对引入过的模块都会缓存，每一次引入模块时都会首先检查缓存，如果不存在，则进入模块查找流程

查看模块缓存

```js
console.log(require.cache)
```

对于每一个被加载的文件模块 都在创建的时候 会自动携带一个path属性 是根据当前文件路径计算的 可以输出一个module.paths 会得到问文件的查找路径

一般是在当前文件目录下查找node_modules 目录 然后进入父级目录的node_modules  最后会查找到根级目录node_modules

看一个模块的查找路径

```js
console.log(module.paths)
```

![path](https://user-gold-cdn.xitu.io/2018/10/26/166ae676f4494713?w=657&h=190&f=png&s=38578)

层级逐渐向上

### scripts

在package.json中一般会配置项目一些启动方式

```js
scripts: {
  "start": "nodemon index.js",
  "build": "webpack --config webpack.config.js"
}
```
那么这里所需的 nodemon 或者是  webpack 模块加载机制，并不是上面那种逐级向上查找的方式了

每当执行npm run，就会自动新建一个 Shell，在这个 Shell 里面执行指定的脚本命令

比较特别的是，npm run新建的这个 Shell，会将当前目录的node_modules/.bin子目录加入PATH变量，执行结束后，再将PATH变量恢复原样。

这意味着，当前目录的node_modules/.bin子目录里面的所有脚本，都可以直接用脚本名调用，而不必加上路径

看下这里 .bin 文件里有什么命令可被执行

```js
vim node_modules/.bin
```

![bin](https://user-gold-cdn.xitu.io/2018/10/26/166ae763718ebc4e?w=656&h=842&f=png&s=84408)

* 如果全局和本地都安装了webpack 那么使用的是哪一个呢

 使用本地，如果本地没有，获取查找全局


### 命令行直接输入命令

在项目中，如果直接在命令行输入执行命令，则其使用全局的模块还是本地的模块

全局会判断本地是否有此模块，有就使用本地，没有就使用全局

* 如果本地和全局都有安装

会使用本地模块

* 本地没有安装，只有全局有

使用全局模块




### 文章参考

* [《Node的模块与包管理机制》](https://www.sweetalkos.com/post/97)

* [《npm scripts 使用指南》](http://www.ruanyifeng.com/blog/2016/10/npm_scripts.html)
