---
title: webpack系列之-功能篇
date: 2018-05-07 18:42:28
categories: 前端自动化
tags: webpack
---

功能篇从代码分割和按需加载 两个方面来分析

## 代码分割

为什么使用代码分割？

我们的项目采用相同的一套技术栈 互相之间有很多相同的代码。如果每一个页面都不做任何处理 直接打包，会造成 1 相同的资源重复加载 很浪费流量 2 页面加载速度慢 用户体验不好

<!--more-->


如果将多个页面的公共部分抽取成一个独立的文件 就能优化这些问题。

用户在访问A页面的时候 已经将代码缓存起来了 那么访问B的时候 有公共代码部分 就会直接读取缓存文件 

* 一个规范的项目应该有如下三部分chunk

| chunk | 含义 |
| --- | --- | --- | --- |
| vendor.js | 项目使用的技术栈 |
| common.js | 剔除技术栈之后的  所有页面都依赖的公共代码 |
| [name].js | 每一个网页各自独立的代码 |


### commonschunkplugin

commonschunkplugin 是webpack内置的插件

> CommonsChunkPlugin 插件，是一个可选的用于建立一个独立文件(又称作 chunk)的功能，这个文件包括多个入口 chunk 的公共模块。通过将公共模块拆出来，最终合成的文件能够在最开始的时候加载一次，便存起来到缓存中供后续使用

用来处理在多入口下提取公共模块

```javascript
entry: {
  index: './index.js',
  main: './main.js'
}
-- other --

plugin: {
  new webpack.optimize.CommonsChunkPlugin({
    name: 'bundle'
  })
}

```

会将 多个入口文件中都引入的公共模块打包到一个独立的文件中，以便在其他的入口和模块中使用

然而我们的项目全部都是单入口文件 为什么这里还配置了 CommonsChunkPlugin ？ ---  因为有vendor

当我们在入口中加入 vendor 参数  

```javascript
entry: {
  vendor: ['react', 'react-dom'],
  index: './index.js',
  main: './main.js'
}
```

one.js 是 index 与 mian都引入的文件  在没有加入vendor的时候 文件编译结果为

![bundle](/img/webpack5/bundle.png)

如果添加 vendor 属性 文件编译之后 会发现 bundle 中提取的公共chunk 将会是空的

公共组件 one.js  并没有进入 bundle.js 中 而是在两个入口文件中存在着

所以这就涉及到 公共业务模块与类库或框架分开打包 的问题

#### 公共业务模块与类库或框架分开打包

```javascript
module.exports = {
  entry: {
    vendor: ['react'],
    index: './index.js',
    main: './main.js'
  },
  -- --
  plugins: [
    new webpack.optimize.CommonsChunkPlugin({
      name: ['bundle', 'vendor']
    })
  ]
}
```
![commonsCunkPlugin](/img/webpack5/commonChunk.png)

但是这里有一个问题 如果项目中有 非 vendor 中的文件发生了重新编译  vendor 的文件也会被重新编译 
[issure](https://github.com/webpack/webpack/issues/1315)

![vendor](/img/webpack-code/vendor.png)

可以看到虽然两次打包的结果大小相同 但是vender还是被处理了新的哈希值

#### vendor 哈希值一直变动问题

##### manifest

为了最小化生成的文件大小，webpack使用标识符而不是模块名称，在编译期间生成标识符，并映射到块文件名，然后放入一个名为chunk manifest的JS对象中。重点就在于！！当我们使用CommonsChunkPlugin分离代码时，被分离出来的代码（比如React库，被打包为vendor），会默认被移动到entry中最后一个入口进行打包（第一个入口是index.js。重要的是，chunk manifest将随着这些被分离出来的代码共同打包！！！

由于我们更改源代码后，不但会更新app的hash值，还会生成新的映射，然后新的映射又会和资源代码一同打包，又由于chunkhash是根据内容生成hash的，那么加入了新的映射对象chunk manifest的资源代码被打包后，hash自然也会发生改变。这反过来，产生的新hash将使长效缓存失效。

那么接下来我们需要做的就是把 manifest分离出来

```javascript
plugins: [
  new webpack.optimize.CommonsChunkPlugin({
    name: ['vendor', 'manifest']
  })
]
```

添加一个entry中未用到的名称 就可以manifest分离出来了 这样每次文件变动的时候  manifest.js 会发生变动 而 vendor 不会再发生变动

##### NamedChunksPlugin

根据文件内容进行打包

## 按需加载

单页面应用中，如果所有一次性加载所有功能对应的代码，可能会导致网页加载缓慢，交互卡顿的现象。

处理方案： 最好用户需要什么功能 就加载对应的代码【按需加载】

### require.ensure 

```javascript

require.ensure([dependencies], function () {
  require(‘./a.js)
}, chunkname)

```

require.ensure 引入但是不执行   要在函数里面载 require一次才执行

### 动态加载 import

```javascript
if (lan === 'en') {
  import(/* webpackchunkname: m1 */ './module1.js' )
} else {
  import(/* webpackchunkname: m2 */ './module2.js' )
}
```
动态加载 一旦加载进来就会被执行

## 参考文章

* [什么是持久化缓存？](https://sebastianblade.com/using-webpack-to-achieve-long-term-cache/#id)