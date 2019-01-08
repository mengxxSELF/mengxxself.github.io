---
title: webpack-3
date: 2017-09-21 20:16:56
categories: 前端自动化
tags: webpack
---


当在命令行输入 webpack 发生了什么

执行脚本 bin/webpack.js

### 解析参数

每次在命令行输入 webpack 后，操作系统都会去调用 ./node_modules/.bin/webpack 这个 shell 脚本。这个脚本会去调用 ./node_modules/webpack/bin/webpack.js 并追加输入的参数，如 -p , -w

#### yargs 解析命令行参数

通过第三方模块 yargs 来解析命令行参数

```javascript
var argv = require('yargs').argv

console.log('hello ', argv.name)
```

可以在命令行输入

```javascript
$ hello --name=tom
// 或者
$ hello --name tom
```

解析 webpack 指令后面追加的与输出显示相关的参数（Display options）

比如命令行经常输入的 -w -color -hot 参数

#### webpack.config.js 中参数

./node_modules/webpack/bin/convert-argv.js，是用来通过判断 argv 中参数的值决定是否去加载对应插件的功能

```javascript
if(argv.watch) {
  options.watch = true;
}
// other code

ifBooleanArg("debug", function() {
  ensureArray(options, "plugins");
  var LoaderOptionsPlugin = require("../lib/LoaderOptionsPlugin");
  options.plugins.push(new LoaderOptionsPlugin({
    debug: true
  }));
});

// other code
return options
```

webpack 将 webpack.config.js 中的各个配置项拷贝到 options 对象中

通过 optimist 将用户配置的 webpack.config.js 和 shell 脚本传过来的参数整合成 options 对象

```javascript
var options = require("./convert-argv")(yargs, argv)
```

### plugins 插件的加载

在加载插件之前 webpack 将 webpack.config.js 中的各个配置项拷贝到 options 对象中，并加载用户配置在 webpack.config.js 的 plugins

接着 optimist.argv 会被传入到 ./node_modules/webpack/bin/convert-argv.js 中，通过判断 argv 中参数的值决定是否去加载对应插件

```javascript
ifBooleanArg("hot", function() {
  ensureArray(options, "plugins");
  var HotModuleReplacementPlugin = require("../lib/HotModuleReplacementPlugin");
  options.plugins.push(new HotModuleReplacementPlugin());
});

ifBooleanArg("debug", function() {
  ensureArray(options, "plugins");
  var LoaderOptionsPlugin = require("../lib/LoaderOptionsPlugin");
  options.plugins.push(new LoaderOptionsPlugin({
    debug: true
  }));
});

ifArg("devtool", function(value) {
  options.devtool = value;
});

return options;

```

至此当插件对象初始化完毕

option中包含了全部的参数设置 包括webpack.config.js 与 shell 命令行中的输入的参数

```javascript
{
  entry: {},//入口配置
  output: {}, //输出配置
  plugins: [], //插件集合(配置文件 + shell指令)
  module: { loaders: [ [Object] ] }, //模块配置
  context: //工程路径
  ...
}
```

然后options对象传到了下一个流程的控制对象

```javascript
var webpack = require("../lib/webpack.js")

var compiler;
try {
  compiler = webpack(options);
} catch (e) {
  ......
}
```

### lib/webpack.js 初始化 webpack 对象

在加载配置文件和 shell 后缀参数申明的插件，并传入构建信息 options 对象后，开始整个 webpack 打包最漫长的一步

而这个时候，真正的 webpack 对象才刚被初始化，具体的初始化逻辑在 lib/webpack.js


webpack 的实际入口是 Compiler 中的 run 方法，run 一旦执行后，就开始了编译和构建流程

不过在进行run之前 先判断是否配置watch参数 如果有则会开启 watch 线程

如果有配置此参数 则开启监听文件变化

```javascript
// 回调函数
function compilerCallback(err, stats) {......}

if(firstOptions.watch || options.watch) {
  ......
  compiler.watch(watchOptions, compilerCallback);
  console.log("\nWebpack is watching the files…\n");
} else {
  compiler.run(compilerCallback);
}

```
