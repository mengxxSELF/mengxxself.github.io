---
title: webapck-5
date: 2017-12-30 20:22:02
categories: 前端自动化
tags: webpack
---

### hash 与 chunkhash

hash 指打包文件的hash值

chunkhash：每个chunk的hash值，你可以理解为版本号或则md5值，主要是保证每个文件的唯一性。

测试中发现

hash 有的时候代码不变动 保存的时候 也会新增 index.hash文件

chunkhash 只有当变动代码的时候 才会新生成文件

<!--more-->

** 并且如果配置的是多个入口 这时候选择 chunkhash 比较合理 ** 也就是输出的时候 会有两个js文件

两个入口文件会生成两个js编译脚本，如果配置中使用的是 hash  

```javascript
module.exports = {
  entry: {
    index: './index.js',
    main: './main.js'
  },
  output: {
    filename: '[name].[hash].js',
    path: path.resolve(__dirname, 'public')
  },
  watch: true
}
```

在public中查看打包完后会生成两个hash值文件 然后现在对其中的一个文件进行修改，（这里开启了watch模式） 可以直接看到 没有改动的那个js文件 也被重新编译了

![hash](/img/webpack5/hash.png)

而且会发现 index 和 Main 后面的hash是一样的

如果我们这里使用的是 chunkhash  

```javascript
output: {
  filename: '[name].[chunkhash:4].js',
  path: path.resolve(__dirname, 'public')
}
```
当修改其中一个文件的时候 只会对修改的文件进行重新编译

![chunkhash](/img/webpack5/chunkhash.png)


看官方文档的解释

> [hash] is replaced by the hash of the compilation.
[chunkhash] is replaced by the hash of the chunk

hash 代表的是compilation的hash值  而 chunkhash 是根据模块内容计算出的hash值

### compilation

compilation对象代表某个版本的资源对应的编译进程。

当使用Webpack的development中间件时，每次检测到项目文件有改动就会创建一个compilation，进而能够针对改动生产全新的编译文件。compilation对象包含当前模块资源、待编译文件、有改动的文件和监听依赖的所有信息。

所以 只要文件有改动，compilation就会被重新创建 从而导致了哈希值变动

并且hash值是由compilation对象计算所得，而不是具体的项目文件计算所得，因此index 与 main 会具有相同的hash


## chunkFilename 与 CommonsChunkPlugin

### chunkFilename

异步加载

```javascript

require.ensure(['./two.js'], function(require) {
    console.log('那花草香')
}, 'tips');

```

当你的js文件中异步加载模块的时候 会把异步的文件编译的 chunkFilename.js 的文件中

按需加载的文件编译之后

![chunkFilename](/img/webpack5/chunkFilename.png)

## Externals 外部扩展

项目中 我们使用第三方的库的时候 一般都是用过 import 导入

如果现在有一个第三方文件 并没有发布在npm中 那么除了在页面中直接通过 script 标签引入 还可以使用 import这种方式

这时候就需要在webpack中添加external配置项

```javascript
module.exports = {
  ...
  external: {
    otherSdk: 'game_user'
  }
  ...
}
```

> 防止将某些 import 的包(package)打包到 bundle 中，而是在运行时(runtime)再去从外部获取这些扩展依赖

也就是说 通过externals 配置的文件 将不会被编译到bundle中  而是直接从第三方提供的link链接去获取资源

还可以将其挂载在window中

main.js

```javascript
window.smile = (name) => {
  console.log(`hi, ${name}`)
}
```

然后随便将其传入到在线网址 那么我们在项目中 如何通过URL的方式 引入这个文件呢



## 参考文章

* [Webpack中hash与chunkhash的区别，以及js与css的hash指纹解耦方案](http://www.cnblogs.com/ihardcoder/p/5623411.html)
