---
title: webpack系列之-优化篇
date: 2018-05-08 17:17:18
categories: 前端自动化
tags: webpack
---

优化篇将注重提高开发速度，优化项目体验

<!--more-->

## happypack

项目构建过程有大量的文件需要解析和处理  涉及到大量的读写操作  而 webpack的运行的单线程的 【在node中运行】也就是webpack要一个一个的处理任务 无法同时处理

HappyPack 可以帮助webpack 将任务分解到多个子进程去并发执行 

```javascript

module.exports = {
  module: {
    rules: [
      // js 文件的处理 交给 id 为 babel的happypack的实例
      {
        test: /\.js$/,
        use: ['happypack/loader?id=babel']
      },
    ]
  },
  plugins: [
    // 处理 js
    new HappyPack({
      id : 'babel',
      loaders : ['babel-loader?cacheDirectory']
    })
  ]
}

```


## tree-shaking

## scope hoisting 


## 参考文章

* [happypack 原理解析](http://taobaofed.org/blog/2016/12/08/happypack-source-code-analysis/)