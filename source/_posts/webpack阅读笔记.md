---
title: webpack阅读笔记
date: 2017-12-24 16:15:27
categories: 前端自动化
tags: webpack
---

## output

配置 output 选项可以控制 webpack 如何向硬盘写入编译文件。注意，即使可以存在多个入口起点，但只指定一个输出配置。


## Loader 特性

loader 支持链式传递。能够对资源使用流水线(pipeline)。
一组链式的 loader 将按照先后顺序进行编译。loader 链中的第一个 loader 返回值给下一个 loader。在最后一个 loader，返回 webpack 所预期的 JavaScript。

<!--more-->

## 模块解析(Module Resolution)

enhanced-resolve

可以解析三种规则 相对路径 绝对路径 模块路径

* 指向一个文件

* 指向一个文件夹

[解析规则](https://doc.webpack-china.org/concepts/module-resolution/)

## 缓存

每个文件系统访问都被缓存，以便更快触发对同一文件的多个并行或串行请求。

在观察模式下，只有修改过的文件会从缓存中摘出。如果关闭观察模式，在每次编译前清理缓存。(Watch 模式默认关闭)


## 依赖图

webpack 从命令行或配置文件中定义的一个模块列表开始，处理你的应用程序。

从这些入口起点开始，webpack 递归地构建一个依赖图，【 这个依赖图包含着应用程序所需的每个模块，然后将所有这些模块打包为少量的 bundle 】 - 通常只有一个 - 可由浏览器加载。

## Targets ？

webpack.config.js

```JavaScript
module.exports = {
  target: 'node'
}
```

在上面例子中，使用 node webpack 会编译为用于「类 Node.js」环境（使用 Node.js 的 require ，而不是使用任意内置模块（如 fs 或 path）来加载 chunk）。

## runtime 和 manifest

* runtime 包含：在模块交互时，连接模块所需的加载和解析逻辑。包括浏览器中的已加载模块的连接，以及懒加载模块的执行逻辑。

* manifest

通过使用 bundle 计算出内容散列(content hash)作为文件名称，这样在内容或文件修改时，浏览器中将通过新的内容散列指向新的文件，从而使缓存无效。一旦你开始这样做，你会立即注意到一些有趣的行为。

即使表面上某些内容没有修改，计算出的哈希还是会改变。这是因为，runtime 和 manifest 的注入在每次构建都会发生变化

[长效缓存错综复杂之处](https://doc.webpack-china.org/guides/output-management#the-manifest)

你可能会感兴趣，webpack及其插件似乎“知道”应该哪些文件生成。答案是，通过 manifest，webpack 能够对「你的模块映射到输出 bundle 的过程」保持追踪

## HotModuleReplacementPlugin (hmr)

让我们从一些不同的角度观察，以了解 HMR 的工作原理……

### 在应用程序中

通过以下步骤，可以做到在应用程序中置换(swap in and out)模块：

1 应用程序代码要求 HMR runtime 检查更新。
2 HMR runtime（异步）下载更新，然后通知应用程序代码。
3 应用程序代码要求 HMR runtime 应用更新。
4 HMR runtime（异步）应用更新。


你可以设置 HMR，[ 以使此进程自动触发更新 ]，或者你可以选择要求在用户交互时进行更新

### 在编译器中

除了普通资源，编译器(compiler)需要发出 "update"，以允许更新之前的版本到新的版本。"update" 由两部分组成：

1 更新后的 manifest(JSON)
2 一个或多个更新后的 chunk (JavaScript)

manifest 包括新的编译 hash 和所有的待更新 chunk 目录。每个更新 chunk 都含有对应于此 chunk 的全部更新模块（或一个 flag 用于表明此模块要被移除）的代码。

编译器确保模块 ID 和 chunk ID 在这些构建之间保持一致。通常将这些 ID 存储在内存中（例如，使用 webpack-dev-server 时），但是也可能将它们存储在一个 JSON 文件中。

### 在模块中

HMR 是可选功能，【 只会影响包含 HMR 代码的模块 】。

举个例子，通过 style-loader 为 style 样式追加补丁。 为了运行追加补丁，style-loader 实现了 HMR 接口；当它通过 HMR 接收到更新，它会使用新的样式替换旧的样式。

类似的，当在一个模块中实现了 HMR 接口，你可以描述出当模块被更新后发生了什么。

然而在多数情况下，不需要强制在每个模块中写入 HMR 代码。如果一个模块没有 HMR 处理函数，更新就会冒泡。这意味着一个简单的处理函数能够对整个 模块树 ？？？ (complete module tree)进行更新。如果在这个模块树中，一个单独的模块被更新，那么整组依赖模块都会被重新加载。

#### 在 HMR Runtime 中 ？？？

对于模块系统的 runtime，附加的代码被发送到 parents 和 children 跟踪模块。在管理方面，runtime 支持两个方法 check 和 apply。

check 发送 HTTP 请求来更新 manifest。如果请求失败，说明没有可用更新。如果请求成功，待更新 chunk 会和当前加载过的 chunk 进行比较。对每个加载过的 chunk，会下载相对应的待更新 chunk。当所有待更新 chunk 完成下载，就会准备切换到 ready 状态。

apply 方法将所有被更新模块标记为无效。对于每个无效模块，都需要在模块中有一个更新处理函数，或者在它的父级模块们中有更新处理函数。否则，无效标记冒泡，并也使父级无效。每个冒泡继续直到到达应用程序入口起点，或者到达带有更新处理函数的模块（以最先到达为准）。如果它从入口起点开始冒泡，则此过程失败。

之后，所有无效模块都被（通过 dispose 处理函数）处理和解除加载。然后更新当前 hash，并且调用所有 "accept" 处理函数。runtime 切换回闲置状态，一切照常继续。
