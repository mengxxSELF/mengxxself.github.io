---
title: webpack-2
date: 2017-09-02 12:23:51
categories: 前端自动化
tags: webpack
---

webpack 配置 -- 监听文件变化 开启自动刷新

### webpack's Watch Mode

配置 --watch 开启观察模式

添加一个用于启动 webpack 的观察模式的 npm script

```javascript
"watch": "webpack --watch",
```

当使用此命令 会监听到文件变动 webpack会自动重新打包编译文件重新生成bundle文件

<!-- more -->

![watch](/img/webpack/webpack-watch.png)

当文件有变动的时候 会自动产生新的bundle文件

![watch](/img/webpack/webpack-refresh.png)

但是唯一的缺陷是无法刷新浏览器 因此页面的刷新还需要用户手动刷新

###  webpack-dev-server

webpack-dev-server 提供了一个简单的 web 服务器，并且能够实时重新加载页面

webpack-dev-server主要是启动了一个使用express的Http服务器。它的作用主要是用来伺服资源文件

此外这个Http服务器和client使用了websocket通讯协议，原始文件作出改动后，webpack-dev-server会实时的编译，但是最后的编译的文件并没有输出到目标文件夹

你启动webpack-dev-server后，你在目标文件夹中是看不到编译后的文件的,实时编译后的文件都保存到了内存当中

```javascript
devServer: {
  contentBase: path.join(__dirname, 'dist'),
  compress: true,
  port: 9000
}
```

以上配置表示在 localhost:9090 下建立服务，将 dist 目录下的文件作为可访问文件。

#### 通过 通过CLI选项 来配置 webapck-dev-server

也就是在命令行启动方式

```javascript
webpack-dev-server --hot --inline --open
```
那么webapck.config.js中配置如下

```javascript
devServer: {
  contentBase: path.join(__dirname, 'dist'),
  compress: true
}
```

* --open

表示自动开启一个浏览器去加载页面

#### 直接在 webapck.config.js 中配合参数

命令行中配置

```javascript
webpack-dev-server --open
```
那么webapck.config.js中配置如下

```javascript
devServer: {
  contentBase: path.join(__dirname, 'dist'),
  inline: true,
  hot:true,
  compress: true
},
plugins: [
  new HtmlWebpackPlugin({template: './index.html'}),
  new webpack.HotModuleReplacementPlugin()
]
```

注意plugin中这里添加HMR插件

#### contentBase

告诉服务器从哪里提供内容。只有在你想要提供静态文件时才需要 也就是本地服务器所加载的页面所在的目录

** devServer.publicPath 将用于确定应该从哪里提供 bundle，并且此选项优先 **

默认情况下，将使用当前目录(所谓当前的目录就是在哪里运行webpack-dev-server这个命令的目录)作为提供内容的目录

但是你可以修改为其他目录

```javascript
contentBase: path.join(__dirname, 'public')
```

#### compress

表示一切服务都启用gzip 压缩

#### port

默认监听端口，如果省略，默认为”8080“

#### inline

设置为true，当源文件改变时会自动刷新页面

#### proxy

属性值为 Object

> 如果你有单独的后端开发服务器 API，并且希望在同域名下发送 API 请求 ，那么代理某些 URL 会很有用。

比如当前项目 koa搭建 运行端口为 localhost:3000

然后你的route 有一个接口

```javascript
router.get('/api/try', function * () {
  this.body = {
    code: 9999999999999999999
  }
})
```

但是前端用webpack启动 打开的端口是 9090  那么如果想通过 /api/try 接口接受信息 就可以配置 proxy

```javascript
proxy: {
  "/api": "http://localhost:3000"
}
```

这样如果你发的请求是 /api/xxx 可以被http://localhost:3000/api/xxx 接口捕获到

所以如果你的服务和页面是相同的接口  就不用配置proxy的

#### hot

表示重新加载改变的部分，如果HRM失败则刷新页面

* 关于hot 以及 inline 参数

‘inline’选项会为入口页面添加“热加载”功能，'hot'选项则开启“热替换HMR”功能，即尝试重新加载组件改变的部分（而不是重新加载整个页面）

如果两个参数都传入，当资源改变时，webpack-dev-server将会先尝试HRM（即热替换），如果失败则重新加载整个入口页面。

### 自动刷新 Automatic Refresh

#### Iframe mode

这种刷新处理方式 在网页中嵌入了一个 iframe ，将我们自己的应用注入到这个iframe当中去，因此每次你修改的文件后，都是这个iframe进行了reload当资源改变的时候会重新加载

开启这种刷新方式只需要通过 http://localhost:8081/webpack-dev-server/index.html 来访问你的项目就可以

![iframe](/img/webpack/iframe.png)

每次更改数据 页面将会开启自动刷新

![iframe](/img/webpack/iframe2.png)

页面中被插入一个 iframe 并且引入 live.bundle.js 文件

这个iframe页面会请求 live.bundle.js ,其中里面会新建一个 Iframe ，你的应用就被注入到了这个 Iframe 当中

live.bundle.js 中含有 socket.io 的 client 代码，这样它就能和 webpack-dev-server 建立的 http server 进行 websocket 通讯了，并根据返回的信息完成相应的动作

** iframe 引起页面自动刷新的机制为 **

因为我们的访问此页面的时候加载了live.bundle.js，其具有websocket的client代码，所以当websocket-dev-server服务端代码发生变化的时候会通知到这个页面，这个页面只是需要重新刷新iframe中的页面就可以了

#### inline mode

* 命令行中使用 --inline

* webapck 配置文件中使用 设置inline: true [感觉webpackV1好像不支持这种处理方式]




** 每一个模式都是支持Hot Module Replacement的 ** 在HMR模式下，每一个文件都会被通知内容已经改变而不是重新加载整个页面。因此，在HMR执行的时候可以加载更新的模块，从而把他们注册到运行的应用里面。

### 模块热替换(Hot Module Replacement)

HMR 用于替换、添加或删除模块，无需重新加载整个页面

* HMR 与 chunkhash 的问题

项目中有在输出项配置 filename 为 chunkhash

```javascript
output: {
  filename: `[name]-[chunkhash:8].js`
}
```

这样使用webpack 启动项目是可以的 但是如果是通过 webpack-dev-server --hot --inline 就会一直报错

![server](/img/webpack/dev-hash.png)

原来是 热更新(HMR)不能和[chunkhash]同时使用

[chunkhash问题](http://ask.csdn.net/questions/258212)

```javascript
filename: `[name]-[hash].js`,
chunkFilename: `[name]-[hash].js`
```




#### HMR 的工作原理

* 在应用程序中置换模块的步骤

应用程序代码要求 HMR runtime 检查更新 -> HMR 下载更新，然后通知应用程序代码 -> 应用程序代码要求 HMR 进行处理去更新应用 ->
HMR runtime（异步）应用更新

* 在编译器中

编译器(compiler)需要发出 "update"，以允许更新之前的版本到新的版本。"update" 由两部分组成：

更新后的 manifest(JSON)
一个或多个更新后的 chunk (JavaScript)

manifest 包括新的编译 hash 和所有的待更新 chunk 目录。每个更新 chunk 都含有对应于此 chunk 的全部更新模块（或一个 flag 用于表明此模块要被移除）的代码。

编译器确保模块 ID 和 chunk ID 在这些构建之间保持一致。通常将这些 ID 存储在内存中（例如，使用 webpack-dev-server 时），但是也可能将它们存储在一个 JSON 文件中。

*

#### 启用 HMR

两种方式可以开启 webpack 的HMR 功能 上文已经提到过

* 命令行添加参数 --hot

* 直接在配置文件中修改并添加 HMR 配置plugin  

##### HotModuleReplacementPlugin

 如果已经通过 HotModuleReplacementPlugin 启用了模块热替换(Hot Module Replacement)，则它的接口将被暴露在 module.hot 属性下面


#### 参考文章

1 [webpack-dev-server问题](https://segmentfault.com/q/1010000007434045?_ea=1345250)

2 [webpack 插件拾趣 (1) —— webpack-dev-server](https://www.cnblogs.com/vajoy/p/7000522.html)
