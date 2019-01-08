---
title: webpack系列之-原理篇
date: 2018-05-02 12:15:50
categories: 前端自动化
tags: webpack
---

本系列将会从原理、开发、优化、对比四个方面给大家介绍webpack的工作流程。【默认是以webpack v3为例子】

<!--more-->

## 储备知识

### CommonJS 规范

```
// 模块引入
let moduleA = require('./a.js)

// 模块导出
module.exports = () => {}

```

### es6规范

```
// 模块引入

let {moduleA} from './a.js'

// 模块导出

export default () => {}

```

## 黑盒体验

我们可以把webpack看做一个黑盒，只要会用就可以。先来体验一次很简单的webpack打包过程

![webpack](/img/webpack-code/box.png)


```javascript
const webpack = require('webpack')
const path = require('path')

module.exports = {
  entry: './index.js',
  output: {
    filename: 'index.js',
    path: path.resolve(__dirname, 'public')
  }
}
```

启动编译，在命令行输入 node_modules/.bin/webpack 就可看到一次打包过程

[查看打包结果](https://github.com/mengxxSELF/webpackTest/blob/master/w6/code/webpack/webpack1/COPY/index1.js)

### 关于如何启动webpack

如果是全局安装了webpack，可以在命令行直接输入 webpack

如果只是项目文件夹安装，需要输入 node_modules/.bin/webpack

* npx

在 npmV5版本 会赠送一个npx

npx 会自动查找当前依赖包中的可执行文件，如果找不到，就会去 PATH 里找。如果依然找不到，就会帮你安装

所以也可以通过npx执行webpack

```
npx webpack
```

## require方法

实现一个require方法

common.js的规范中 引入一个模块需要

```
let getA = require('./a')
```

自己写一个require方法

```javascript
let fs = require('fs')
// 查找module
function myReq (myModule) {
  // 读取文件信息
  let cont = fs.readFileSync(myModule, 'utf-8')
  /* function (exports, require, module, __filename, __dirname) {
    moduel.exports = {a: 'apple'}
    return moduel.exports
  } */
  let nodeFn = new Function('exports', 'require', 'module', '__filename', '__dirname', cont + 'return module.exports')
  let module = {
    exports: {}
  }
  return nodeFn(module.exports, myReq, module, __filename, __dirname)
}
// let getA = require('./a')
let getA = myReq('./a.js')
console.log(getA, 'getA')
```

思路：读取文件内容，根据node的封装规范，传入几个必须的参数即可。

* 删减 webpack 编译后的文件

把刚刚打包之后的 dist/index.js 删减掉一些不用的代码

```javascript
(function(modules) {
	function myRequire(moduleId) {
		var module = {
			exports: {}
		};
    modules[moduleId].call(module.exports, module, module.exports, myRequire);
        // call 用于让  modules[moduleId] 函数执行 执行的是传入后面的参数
		return module.exports;
	}
  return myRequire(/* 下面的第一个函数参数 */);
})
([
  (function(module, exports) {
    console.log('123')
  })
]);

```

[在线查看](https://github.com/mengxxSELF/webpackTest/blob/master/w6/code/webpack/webpack1/COPY/index1_simple.js)

可以看出来， webpack打包生成之后的文件内容就和编译的require方法类似。这就是为什么打包之后的js文件可直接在浏览器中运行的原因

## 编译流程

### 常见名词解释

| 参数 | 说明 |
| --- | --- | --- | --- |
| entry | 项目入口 |
| module | 开发中每一个文件都可以看做module |
| chunk | 代码块 |
| loader | 模块转化器  |
| plugin | 扩展插件 自定义webpack打包过程 |
| bundle | 最终打包完成的文件 |

### 打包流程

webpack的运行流程是一个串行的过程，从启动到结束，会依次执行以下流程

* 参数初始化

从配置文件 【webpack.config.js】和 shell 语句中读取与合并参数

* 开始编译

初始化一个compiler对象 加载所有插件 执行对象的run方法开始编译

* 确定入口文件

根据配置文件找到项目所有的入口文件

* 编译模块

从入口开始 调用配置的loader对模块进行编译 【有一个递归寻找依赖模块的流程】

模块编译完成后 得到模块被转化后的最后内容以及他们之间的依赖关系

* 资源输出

根据入口文件和模块之间的依赖关系 组成chunk文件 【一个chunk可能包含多个模块】每一个chunk将会被转化成一个单独的文件加入输出列表中

* 输出

根据配置的输出参数 【路径和文件名】将输出内容写入文件系统

** 在以上的过程 WP会在特定的时间点广播特定的事件 插件在监听到感兴趣的事件后会执行特定的逻辑 **

#### 简化流程

其实以上流程可以简化为三个阶段

![webpack](/img/webpack-code/process.png)

## 源码分析

### 核心库 tapable

在node中有一个事件发射器 EventEmitter ，可以进行事件监听与发射。

```javascript
var EventEmitter = require('events').EventEmitter;
var event = new EventEmitter();
event.on('some_event', function () {
    console.log('some_event 事件触发');
});
setTimeout(function () {
    event.emit('some_event');
}, 1000);
```

webpack核心库 [tapable](https://github.com/webpack/tapable) 的原理和 EventEmitter 类似，通过事件的注册和监听，触发各个编译周期中的函数方法.
Tapable 还允许你通过回调函数的参数，访问事件的“触发者(emittee)”或“提供者(producer)”

[查看在线代码](https://github.com/mengxxSELF/webpackTest/blob/master/w6/code/simple-webpack/tapable.js)

### 核心对象 compiler

compiler 继承自 tapable 可以进行事件的广播和监听

compiler 进行事件的广播和监听的方式为

```javascript

// 广播事件  params 为附带参数
compiler.apply('event-name', params)

// 监听 名为 event-name 的事件
compiler.plugin('event-name', function (params) {

})

```

[查看177行代码](https://github.com/mengxxSELF/webpackTest/blob/master/w10-webpack-code/webpack/lib/Compiler.js)

webpack 在初始化的时候 会将 compiler对象传入到plugin中 可以使用它来访问 webpack 的主环境

[查看45行代码](https://github.com/mengxxSELF/webpackTest/blob/master/w10-webpack-code/webpack/lib/webpack.js)

compiler 对象代表了完整的 webpack 环境配置。这个对象在启动 webpack 时被一次性建立，并配置好所有可操作的设置，包括 options，loader 和 plugin。

### 核心对象 compilation
webpack系列之-原理篇
compilation 继承自 tapable 可以进行事件的广播和监听

[查看57行代码](https://github.com/mengxxSELF/webpackTest/blob/master/w10-webpack-code/webpack/lib/Compilation.js)

compilation 对象代表了一次资源版本构建。当运行 webpack 开发环境中间件时，每当检测到一个文件变化，就会创建一个新的 compilation，从而生成一组新的编译资源。

一个 compilation 对象表现了当前的模块资源、编译生成资源、变化的文件、以及被跟踪依赖的状态信息

### 打包模板

webpack打包的每一个chunk都是根据模板而生成的。webpack内部提供了很多模板文件。

![webpack](/img/webpack-code/template.png)
![webpack](/img/webpack-code/template2.png)

当webpack分析完所有的Module并准备输出chunk的时候 会分析每一个chunk找对其所对应的template 从而生成最后我们看到的文件格式

### plugin 实现机制

#### 作用原理

在webpack的编译流程，每一个阶段都会广播不同的事件，比如 run, done 等事件。plugin会监听到这些事件，一旦事件发生，就会执行注册好的函数方法

#### plugin分析

每一个plugin都是 一个具有 apply 属性的 JavaScript 对象

```
class MPlugin {
  // 这里获取用户为插件传入的配置参数
  constructor (options) {

  }
  // webpack 会调用 MPlugin 实例的apply方法 为插件实例传入 compiler 对象
  apply (compiler) {
    compiler.plugin('compilation', function (compilation) {
      // 回调函数中 传入了 compilation 对象

    })
  }
}

```

在webpack初始化的阶段  会往plugin中传递compiler对象

[查看45行代码](https://github.com/mengxxSELF/webpackTest/blob/master/w10-webpack-code/webpack/lib/webpack.js)

#### 编写plugin

```JavaScript
class StartWp {
    constructor(options) {
        this.options = options
    }
    apply(compiler) {
        let {name} = this.options
        // 监听事件 这是异步的 所以要执行cb  不然会卡到这里不动了
        compiler.plugin('run', function (compilation, cb) {
            console.log('run', name)
            // 每一次重新编译的时候又会触发
            // compilation.plugin('')
            cb();
        })
        compiler.plugin('done', function (compilation) {
            console.log('done', name)
        })
    }
}
module.exports = StartWp
```

* 传递给插件的compiler和compilation是相同的 也就是某一个插件有修改对象的话会影响后面的插件的使用

* 有的事件是异步的，所以在使用的时候，要执行 cb() 去通知webpack 本次事件监听结束了 要往下继续执行否则会卡到这里

如何使用此插件

```javascript
plugins: [
  new StartWp({
    name: 'v3 - plugin '
  })
]
```

## 自己来写一个简易版本的webpack打包器

实现原理: 根据打包的模板格式 读取文件信息并输入到指定的位置

* 借助ejs 

* 将简化的webpack打包结果拿出来作为 字符串模板

### 最简易的webpack 

```javascript
const fs = require('fs')

// 入口文件
let input = './index.js'
// 输出地址
let output = './dist/index.js'

const ejs = require('ejs')

const getIntry = fs.readFileSync(input, 'utf-8')

let template = `(function(modules) { 
	function __webpack_require__(moduleId) {
		var module = {
			exports: {}
		};
		modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);
		return module.exports;
	}
	return __webpack_require__(0);
})
([
  (function(module, exports) {
    <%- getIntry %>
  })
])`

let result = ejs.render(template, {
  getIntry
})

// 将结果输出到 dist 
fs.writeFileSync(output, result)
```

在命令行执行一次 node webpack.0.1.0.js

[执行之后的编译结果](https://github.com/mengxxSELF/webpackTest/blob/master/w11-myWebpack/COPY/index.0.1.0.js)

可以看到在dist目录有index.js生成 将其引入 html页面

![myWebpack](/img/webpack-code/myw.png)

这样就完成了一个非常非常简单的webpack

[在线查看简单的webpack](https://github.com/mengxxSELF/webpackTest/blob/master/w11-myWebpack/webpack.0.1.0.js)

### 加入 require 处理

如果入口文件中 有使用到 require  则需要将其替换为webpack提供的 __webpack_require__ 

先看一下如果有使用 require 之后的打包之后的结果

bundle.js

```javascript
 (function(modules) {
 	function __webpack_require__(moduleId) {
 		var module = {
 			exports: {}
 		};
 		modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);
 		return module.exports;
 	}
 	return __webpack_require__(0);
 })
 ([
  (function(module, exports, __webpack_require__) {
  __webpack_require__(1)
  console.log('index.js')
  }),
  (function(module, exports) {
    console.log(123)
  })
]);
```
[查看在线代码](https://github.com/mengxxSELF/webpackTest/blob/master/w6/code/webpack/webpack1/COPY/bundle_require.js)

我们使用这个模板来重新编写一个简易的webpack

```javascript
const fs = require('fs')
const path = require('path')

// 入口文件
let input = './index.js'
// 输出地址
let output = './dist/index.js'

const ejs = require('ejs')

const getIntry = fs.readFileSync(input, 'utf-8')

// 将getIntry 中的 require 进行处理
// require('./a.js')
const contAry = []
let dealIntry = getIntry.replace(/(require)\(['"](.+?)['"]\)/g, ($1, $2, $3, $4) => {
  // console.log($1, $2, $3, $4)
	// let filePath = path.join(__resolve, $2)
	let cont = fs.readFileSync($3, 'utf-8')
	// console.log(cont)
	contAry.push(cont)
	return $2 = `__webpack_require__(${contAry.length})`
})

// console.log(contAry)

let template = `(function(modules) {
 	function __webpack_require__(moduleId) {
 		var module = {
 			exports: {}
 		};
 		modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);
 		return module.exports;
 	}
 	return __webpack_require__(0);
 })
 ([
  (function(module, exports, __webpack_require__) {
	  <%- dealIntry %>
  }),
	<% for(var i=0;i < contAry.length; i++){ %>
		(function(module, exports) {
	    <%- contAry[i] %>
	  }),
  <%}%>
])`

let result = ejs.render(template, {
  dealIntry,
	contAry
})

// 将结果输出到 dist
fs.writeFileSync(output, result)

```

在命令行执行一次 node webpack.1.0.0.js

[执行之后的编译结果](https://github.com/mengxxSELF/webpackTest/blob/master/w11-myWebpack/COPY/index.1.0.0.js)

![myWebpack](/img/webpack-code/wp.png)

[在线查看加入require功能的webpack](https://github.com/mengxxSELF/webpackTest/blob/master/w11-myWebpack/webpack.1.0.0.js)

## 源码篇提问

* 在自己构建的plugin中 是否可以进行事件广播

可以。只要能拿到 compiler或者compilation对象 就可以广播事件，为其他插件监听使用
