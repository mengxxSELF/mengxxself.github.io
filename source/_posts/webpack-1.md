---
title: webpack-1
date: 2017-08-27 14:44:30
categories: 前端自动化
tags: webpack
---

## 为什么使用webpack

现在的应用大多是webAPP的形式 通常都是单页面应用 每一个视图异步加载文件信息 在页面初始化过程中 会加载很多的JavaScript代码

页面中所需的资源通过增量加载的方式运行到浏览器 那么在开发环境中如何很好的将这些碎片资源组织起来 更加优雅迅速的在浏览器中展现 就需要一个模块化系统的支持

<!-- more -->

### 模块化系统的演进

模块系统主要解决模块的定义 依赖 导入

#### 常用的模块系统

```html
<script src='./jquery.js'></script>
<script src='./zeptojs'></script>
```
这种是最原始的模块加载方式（将每一个js文件看做一个模块） 这种加载方式有很多不足之处

* 由于是暴露在全局作用域中 所以很容易造成变量冲突
* 文件的加载顺序是js文件引入顺序
* 开发人员需要处理模块与各个代码库的依赖关系

#### 前端模块加载

前端模块是需要加载到浏览器中

预想的加载方式有两种 一种为将所有模块打包为一个文件 一次性将其加载到页面中 但这种处理方式会导致流量浪费、初始化过程慢 或者每一个模块都单独请求 但是这样会导致请求次数发送或多

## webpack 介绍

> WebPack可以看做是模块打包机：它做的事情是，分析你的项目结构，找到JavaScript模块以及其它的一些浏览器不能直接运行的拓展语言（Scss，TypeScript等），并将其转换和打包为合适的格式供浏览器使用

webpack 处理文件方式是  把你的项目当做一个整体，通过一个给定的主文件（如：index.js），Webpack将从这个文件开始找到你的项目的所有依赖文件，使用loaders处理它们，最后打包为一个（或多个）浏览器可识别的JavaScript文件

webpack 在命令行中打包项目

```javascript
webpack {entry file} {build file}
```
但是一般我们都是在项目中通过一个配置文件来将所需的webpack信息进行完整的配置

### 启动webpack

在命令行执行编译命令

```javascript
webapck
```
这条命令会自动引用webpack.config.js文件中的配置选项

如果你不是在全局安装的 webpack 就需要另行指定路径

```javascript
./node_modules/.bin/webpack --config webpack.config.js
```

或者在package.json中配置

```javascript
"start": "webpack",
"dev": "webpack --config webpack.config.js"
```

命令行执行此命令后 可以看到有bundle文件生成

### chunk

chunk：code splitting 后的产物，也就是按需加载的分块，装载了不同的 module

## webpack 基本配置

在项目中新建 webpack.config.js 的文件 用于配置webpack

```javascript
module.export = {...}
```

### context 上下文

```javascript
context: path.resolve(__dirname, "app")
```
绝对路径，用于从配置中解析入口起点(entry point)和 loader

默认值是当前目录

### entry 入口

Webpack 会分析入口文件，解析包含依赖关系的各个文件。这些文件（模块）都打包到 bundle.js

入口文件

| 属性名 | 参数类型 |  
| --- | --- | --- | --- |
| entry | String Array Object |

```javascript
// String
module.export = {
  entry: './index.js'
}
// Array
module.export = {
  entry: ['./index.js', './main.js']
}
// Object
module.export = {
  entry: {
    index: './index.js',
    home: './home.js'
  }
}
```

当为数组时

> 向 entry 属性传入「文件路径(file path)数组」将创建“多个主入口(multi-main entry)”。在你想要多个依赖文件一起注入，并且将它们的依赖导向(graph)到一个“chunk”时，传入数组的方式就很有用

也就是 将所有的文件会打包到最后一个chunk文件中

```javascript
module.export = {
  entry: ['./index.js', './main.js']
}
```

会发现dist中只会生成一个文件

![bubdle](/img/webpack/array.png)


当为对象格式时 Webpack会为每一个html生成一个 bundle 文件  这是应用程序中定义入口的最可扩展的方式

> 在多页应用中，（译注：每当页面跳转时）服务器将为你获取一个新的 HTML 文档。页面重新加载新文档，并且资源被重新下载

![bubdle](/img/webpack/object.png)

* 关于生成的 bundle 文件

![bubdle](/img/webpack/hash.png)

发现如果文件中有代码变动 则这里的哈希值就会发生变化 表示webpack打包之后的bundle.js文件也变化了


####  vendor

分离第三方应用

```javascript
module.export = {
  entry: {
    vendor: ['jquery', 'react', 'redux'],
    home: './home.js'
  }
}
```

分离第三方应用主要是因为vendor.js 的内容基本上很少更新，所以如果下一次更改了 app.js 的内容时，vendor.js 仍然在浏览器的缓存中，那么用户就只需要重新下载 app.js 就可以了

注意如果这里配置了vendor属性 需要在plugins只能怪添加

```javascript
new webpack.optimize.CommonsChunkPlugin({
  name: 'vendor',
  filename: 'vendor-[hash].min.js',
})
```

但是 在测试中发现 虽然vendor中的是第三方代码 不经常更新 但是每次有其余代码更新 都会生成一个新的vendor文件 哈希值发生变化

![vendor](/img/webpack/vendor.png)

[关于此问题的讨论](https://github.com/webpack/webpack/issues/1315)

然后将配置文件修改为

```javascript
entry: {
  vendor: ['react', 'react-dom'],
  index: path.resolve('views')
},
output: {
  path: path.resolve('public'),
  filename: `[name]-[chunkhash:8].js`,
  chunkFilename: `[name]-[chunkhash:8].js`
},
plugins: [
  new webpack.optimize.CommonsChunkPlugin({
    name: ['vendor', 'manifest'],
  })  
]
```
* manifest是为了解决vendor再次编译的问题

公共文件被编译为vendor.js 并且不再变化  但是还会生成一个manifest.js 这个会一直有哈希值变化生成新文件

** 注意 **

如果此时有在plugin中配置热加载

```javascript
new webpack.HotModuleReplacementPlugin()
```

就会发现一直报错

![vendor](/img/webpack/vendor2.png)

是因为在 热更新(HMR)不能和[chunkhash]同时使用 所以可以把 HMR 注释掉


### output 输出

webpack的输出配置 控制 webpack 如何向硬盘写入编译文件

> 即使可以存在多个入口起点，但只指定一个输出配置。

```javascript
module.export = {
  output: {
    path:  path.resolve('dist'),
    filename: 'bundle.js'
  }
}
```

#### path

此选项规定了文件被写入硬盘的位置

也可以采用绝对路径的方式

```javascript
path: path.resolve(__dirname, 'dist')
```

#### filename

进行文件的命名

```javascript
entry: {
  home: "./home.js",
  server: "./server.js"
},
output: {
  path: 'build',
  filename: '[name].js'
}
```

> 如果配置创建了多个单独的 "chunk"（例如，使用多个入口起点或使用像 CommonsChunkPlugin 这样的插件），则应该使用占位符(substitutions)来确保每个文件具有唯一的名称

[name] 被 chunk 的 name 替换 也就是entry的对象键名 则生成的文件为

```javascript
build/home.js 与  build/server.js
```

#### name], [id] and [hash]

如果考虑缓存问题，使用缓存的最好方法是保证你的文件名和文件内容是匹配的（内容改变，名称相应改变）

webpack可以把一个哈希值添加到打包的文件名中，将特殊的字符串混合体（[name], [id] and [hash]）到输出文件名前

```javascript
output: {
  path: 'build',
  filename: '[name]-[hash].js'
}
```

#### publicPath

通常用来进行开发者模式与正式环境的路径替换

```javascript
output: {
 path: path.resolve(__dirname, '../dist'),
 publicPath: isDev ? 'http://localhost:9333/' : '/',
 filename: '[name].bundle.js'
}
```

比如在本地开发者模式中 加载一个图片的方式为

```css
.bg{
  background: url('../img/webpack/bg.png')
}
```
但是在正式环境中 路径可能是CDN的路径 所以需要对文件中的路径进行修改

因此借助环境变量 来处理此问题

```javascript
// 如果当前是生产模式，使用前者，否则[也就是本地开发者模式]使用localhost:3000
const ASSET_PATH = isProd ? `${QNCDN}/project/` : 'http://locahost:3000'

export default {
  output: {
    publicPath: ASSET_PATH
  }
}
```
![publicPath](/img/webpack/publicPath.png)

此属性 有默认值 为''

```javascript
publicPath: ''
```

publicPath参数跟path参数的区别是：path参数其实是针对本地文件系统的，而publicPath则针对的是浏览器；

因此，publicPath既可以是一个相对路径，如示例中的''，也可以是一个绝对路径如http://www.xxxxx.com/。

一般来说，我还是更推荐相对路径的写法，这样的话整体迁移起来非常方便。那什么时候用绝对路径呢？其实也很简单，当你的html文件跟其它资源放在不同的域名下的时候，就应该用绝对路径了，这种情况非常多见于后端渲染模板的场景

#### chunkFilename

chunkFilename 参数指定的是除入口文件外的chunk（这些chunk通常是由于webpack对代码的优化所形成的)

```javascript
chunkFilename: `[name]-[chunkhash:8].js`
// 或者
chunkFilename: `[name]-[hash].js`
```

### module && loaders 模块加载器

决定了项目中如何处理不同类型的模块

> loader 用于对模块的源代码进行转换。loader 可以使你在 import 或"加载"模块时预处理文件 loader 可以将文件从不同的语言（如 TypeScript）转换为 JavaScript，或将内联图像转换为 data URL。loader 甚至允许你直接在 JavaScript 模块中 import CSS文件！

简而言之  loaders 用以将浏览器无法解析的 全部转化为可解析的代码

#### 基本使用

```javascript
rules: [
  {
    test: /\.css$/,
    use: ["style-loader", "css-loader"],
  }
]    
```
loader 会以数组逆序运行。这意味着 css-loader 会在 style-loader 之前运行

#### 处理style

css-loader 和 style-loader，二者处理的任务不同

css-loader使你能够使用类似@import 和 url(...)的方法实现 require()的功能

style-loader将所有的计算后的样式加入页面中，二者组合在一起使能够把样式表嵌入webpack打包后的JS文件中

#####  css-loader

#####  style-loader

##### postcss-loader

可以自动补全css中那些兼容性前缀

[github](https://github.com/postcss/postcss)

* 配置Module

```javascript
module: {
  rules: [
    {
      test: /\.scss$/,
      use: ExtractTextPlugin.extract({
        fallback: "style-loader",
        use: ['css-loader', 'postcss-loader', 'sass-loader']
      })
    }
  ]
}
```

因为我的项目中是将CSS与js抽离开的 所以使用了 ExtractTextPlugin 如果你不需要这么做 可以直接配置

```javascript
module: {
  rules: [
    {
      test: /\.scss$/,
      use: ['style-loader', 'css-loader', 'postcss-loader', 'sass-loader']
    }
  ]
}
```

* 但是需在目录中添加一个文件 postcss.config.js 内容为

```javascript
module.exports = {
  plugins: [
    require('autoprefixer')
  ]
}
```
这样就可以成功的添加样式各种浏览器前缀了

关于参数 这里可以传入一个对象作为参数 用于指定哪些浏览器来使用postcss

```javascript
module.exports = {
  plugins: [
    require('autoprefixer')({
      browsers: ['last 2 versions', '> 5%', 'last 5 iOS versions', 'Android >= 3']
    })
  ]
}
```

![img](/img/webpack/webpack-plugin-css.png)


如果不在这里写 还可以配置在package.json 中 添加支持哪些浏览器

```javascript
"browserslist": [
  "defaults",
  "not ie < 11",
  "last 2 versions",
  "> 1%",
  "iOS 7",
  "last 3 iOS versions"
]
```

*  ** 坑 **

网上有的文章是将 autoprefixer 配置在 plugin中 这样写的

```javascript
require('autoprefixer')

// 或者是添加一个对象参数

require('autoprefixer')({
  browsers: ['last 2 versions', '> 5%', 'last 5 iOS versions', 'Android >= 3']
})

```

但是我没有调试出结果来 所以采用了 添加 postcss.config.js 文件的形式


##### 配置 module.rules

创建模块时，匹配请求的规则数组

这些规则能够修改模块的创建方式 这些规则能够对模块(module)应用加载器(loader)，或者修改解析器(parser)

允许你在 webpack 配置中指定多个 loader

```javascript
module: {
  rules: [
    { test: /\.css$/, use: 'css-loader' },
    { test: /\.ts$/, use: 'ts-loader' }
  ]
}
```

或者 使用多个loader解析同一个类型的文件 use 这里就可以配置数组

```javascript
module: {
  rules: [
    {
      test: /\.css$/,
      use: [
        { loader: 'style-loader' },
        {
          loader: 'css-loader',
          options: {
            modules: true
          }
        }
      ]
    }
  ]
}
```

[如何编写loader](https://doc.webpack-china.org/development/how-to-write-a-loader)

#### 处理图片

```javascript
{
  test: /\.(gif|png|jpe?g|svg)$/,
  include: path.resolve(__dirname, 'component'),
  use: [
    {
      loader: 'file-loader',
      options: {
        name: `[name]-[hash:8].[ext]`
      }
    }
  ]
}
```

然后图片格式就成了这样子了

![img](/img/webpack/webpack-plugin-img.png)


#### 配置 module.noParse

防止 webpack 解析那些任何与给定正则表达式相匹配的文件

忽略的文件不应该被 import, require, define 或者任何其他导入机制调用

用这个忽略大型库文件(library)可以提高构建性能

```javascript
module: {
  noParse: /jquery|backbone/
}
```

### resolve

> 配置模块如何解析 解析规则也可以称之为检索，索引规则  配置索引规则能够缩短webpack的解析时间，提升打包速度

```javascript
resolve: {
  modules: [path.resolve(__dirname, "src"), "node_modules"]
}
```
首先指定了我们自己的源文件目录，然后是 node_modules。这样子 Webpack 解决起来就会处理得更好一些，按照那个顺序先找我们的源文件目录，然后是已安装的 Node Modules

#### modules

> webpack 解析模块时应该搜索的目录

可以使用绝对路径和相对路径

* 如果使用相对路径将类似于 Node 查找 'node_modules' 的方式进行查找 也就是现在当前文件中查找 然后去父级查找 层层向上查

* 如果使用绝对路径，将只在给定目录中搜索

默认值是  node_modules  表示从 node_modules 文件夹中搜索所有组件中引入的模块

```javascript
modules: ['node_modules']
```

* 手动添加到数组中

```javascript
modules: [path.resolve(__dirname, 'src'), 'node_modules']
```

#### extensions

```javascript
resolve: {
 extensions: ['.js', '.jsx']
}
```

配置此属性 则可以在项目中 引入模块的时候 省略后缀名 因为webpack会根据这里配置的数组作为后缀  分别进行匹配

比如引入 jQuery.js 可以忽略后缀 .js  直接写

```javascript
import $ from 'jquery'
```

#### alias

配置别名 在引入模块的时候 就可以使用更加简单的方式 例如

```javascript
import Utility from '../../src/utilities/utility'
```

可以在alias中 配置好路径 然后 组件中使用别名


```javascript
alias: {
  Utilities: path.resolve(__dirname, 'src/utilities/utility')
}
```

模块引入

```javascript
import Utility from 'Utilities'
```

项目中使用到别名的案例: 通过环境变量的不同 来区分引入模块

```javascript
alias: {
 'react': needReact
 ? path.resolve(__dirname, `../node_modules/react/dist/react${isProd ? '.min' : ''}.js`)
 : path.resolve(__dirname, `../node_modules/preact-compat/dist/preact-compat${isProd ? '.min' : ''}.js`),
}
```
这样在项目中模块引入是相同的

```javascript
import React from 'react'
```

#### mainFiles

属性值为数组格式  表示解析目录时要使用的文件名

默认值为 index

```javascript
mainFiles: ['index']
```
一般我们都是默认是index 所以想要引入某一个组件 可以使用

```javascript
import main from './components'
```

不用具体书写为

```javascript
import main from './components/index'
```

如果你想使用main 来作为默认的文件名

```javascript
mainFiles: ['main']
```

### 插件(Plugins)

属性值为数组格式 数组元素是通过new 创建出来的每一个实例 表示 webpack 使用的插件列表

```javascript
plugins: [
  new webpack.optimize.UglifyJsPlugin(),
  new HtmlWebpackPlugin({template: './src/index.html'})
]
```

####  CommonsChunkPlugin

当多个 bundle 共享一些相同的依赖，CommonsChunkPlugin 有助于提取这些依赖到共享的 bundle 中，来避免重复打包

```javascript
plugins: [
  new webpack.optimize.CommonsChunkPlugin({
    name: "common",
    filename: "common.js",
    minChunks: 2
  })
]
```
在 output 的文件里，如果有任意模块加载了两次或更多（通过 minChunks 设置该值），它就会被打包进一个叫 commons.js 的文件里，后面你就可以在客户端缓存这个文件了

当然，这肯定会造成一次额外的请求，但是却避免了客户端多次下载相同库的问题。所以在很多场景下，这都是提升速度的举措

[常用的webpack插件列表](https://doc.webpack-china.org/plugins)

### source map

多个文件打包到一个bundle文件中 只要其中一个出错 控制台的错误提示都将指到编译后的 bundle文件

source map 可以追踪代码错误 准确地知道错误来自于哪个源文件

需要配置 devtool 属性

```javascript
devtool: 'inline-source-map'
```

当没有配置devtool 属性的时候 倘若文件中有错误 则提示很粗暴

![noSource](/img/webpack/noSource.png)

当配置了此属性 则可以明确指出错误文件位置

![noSource](/img/webpack/source.png)

其实一般在生产环境 可以关掉 source map

```javascript
devtool: isProduction ? null : 'inline-source-map'
```

常用的属性值

| 属性值 | 含义 |
| --- | --- | --- | --- |
| source-map | 在一个单独的文件中产生一个完整且功能完全的文件。这个文件具有最好的source map，但是它会减慢打包速度 |
| cheap-module-source-map | 在一个单独的文件中生成一个不带列映射的map，不带列映射提高了打包速度，但是也使得浏览器开发者工具只能对应到具体的行，不能对应到具体的列（符号），会对调试造成不便 |
| eval-source-map | 使用eval打包源文件模块，在同一个文件中生成干净的完整的source map。这个选项可以在不影响构建速度的前提下生成完整的sourcemap，但是对打包后输出的JS文件的执行具有性能和安全的隐患。在开发阶段这是一个非常好的选项，在生产阶段则一定不要启用这个选项 |
| cheap-module-eval-source-map | 这是在打包文件时最快的生成source map的方法，生成的Source Map 会和打包后的JavaScript文件同行显示，没有列映射，和eval-source-map选项具有相似的缺点 |

### HtmlWebpackPlugin

没用 html-webpack-plugin 插件之前 我们需要在html文件中手动引用我们之前经过webpack打包好的js文件 而此插件会自动生成html文件并将打包好的js插入文件

HtmlWebpackPlugin 简化创建服务于 webpack bundle 的 HTML 文件，尤其是对于在文件名中包含了 hash 值，而这个值在每次编译的时候都发生变化的情况 可以让这个插件来帮助你自动生成 HTML 文件

```javascript
module.exports = {
  entry: {
     index: './index.js'
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].bundle.js'
  },
  plugins: [new HtmlWebpackPlugin()]
}
```

执行相应命令后 在dist文件中生成一个 index.html 以及 index.bundle.js 文件

其中 index.html已经引入了script文件

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Webpack App</title>
  </head>
  <body>
  <script type="text/javascript" src="index.bundle.js"></script></body>
</html>
```

如果配置多个入口文件呢

```javascript
entry: {
   index: './index.js',
   server: './server.js'
}
```
会发现所有文件都被引入了

```html
<script type="text/javascript" src="index.bundle.js"></script><script type="text/javascript" src="server.bundle.js"></script></body>
```

#### 属性配置

| 属性 | 含义 | 属性值 |
| --- | --- | --- | --- |
| title | 用来生成页面的 title 元素 | 无 |
| filename | 输出的 HTML 文件名，默认是 index.html, 也可以直接配置带有子目录 | 无 |
| template |  模板文件路径，支持加载器，比如 html!./index.html | 无 |
| inject | 注入所有的资源到特定的 template 或者 templateContent 中，如果设置为 true 或者 body，所有的 javascript 资源将被放置到 body 元素的底部，'head' 将放置到 head 元素中 | {true / 'head' / 'body' / false} |
| favicon | 添加特定的 favicon 路径到输出的 HTML 文件中 |  无 |
| minify | 传递 html-minifier 选项给 minify 输出 | {} / false |
| hash | 如果为 true, 将添加一个唯一的 webpack 编译 hash 到所有包含的脚本和 CSS 文件，对于解除 cache 很有用 | true /  false |
| cache | 如果为 true, 这是默认值，仅仅在文件修改之后才会发布文件 | true /  false |
| showErrors | 如果为 true, 这是默认值，错误信息会写入到 HTML 页面中 | true /  false |
| chunks | 允许只添加某些模块 从entry 中定义的入口文件中选择 |  无 |
| chunksSortMode | 允许控制块在添加到页面之前的排序方式 | 'none' / 'default' / {function}-default:'auto' |
| excludeChunks | 允许跳过某些块，(比如，跳过单元测试的块) |  无 |

#### 使用案例

* 指定title 以及模板

```javascript
new HtmlWebpackPlugin({title: 'new html title', template: './index.html'}),
```
* 如果有多个单页面 需要配置多个 HtmlWebpackPlugin 实例

```javascript
new HtmlWebpackPlugin()
new HtmlWebpackPlugin({title: 'new html title', template: './server.html'}),
```

* 可以自己创建模板文件

```javascript
  new HtmlWebpackPlugin({
    title: 'default template',
    template: 'myself.html',
    inject: 'body'
  })
```

编译 myself.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta http-equiv="Content-type" content="text/html; charset=utf-8"/>
    <title><%= htmlWebpackPlugin.options.title %></title>
  </head>
  <body>
  </body>
</html>
```

![HtmlWebpackPlugin](/img/webpack/html.png)

[延伸阅读](http://www.cnblogs.com/haogj/p/5160821.html)

#### chunk

当指定了chunks属性，webpack会按照这个属性定义的数组，将数组中所有片段完成打包，并用script标签将打包的js插入到生成的页面中，没有在数组中的片段，则不插入页面

比如你的入口文件中有三个 但是只想让其中一个被打包引入文件 就可以使用chunk属性

```javascript
entry: {
   index: './index.js',
   server: './server.js'
}
......
plugins: [
  new HtmlWebpackPlugin({
    chunks: ['server'],
    title: 'cancan',
    template: './index.html'
  })
]
```

#### title

有一个奇怪的问题 当我同时指定title 和 template [不是那种自己写的模板] 之后 这里书写的title值将失效

![title](/img/webpack/title2.png)

也就是当你指定了 template 的时候 编译之后的模板使用的将是初始模板中的title 即使这里重新指定title也是无效的

![title](/img/webpack/title1.png)

还有一个title失效问题

如果使用自己写的模板

```javascript
  new HtmlWebpackPlugin({
    title: 'default template',
    template: 'myself.html',
    inject: 'body'
  })
```

发现无法正确解析title部分

![title](/img/webpack/title3.png)

原因是webpack.config.js的配置文件里面加了 html-loader，加了之后会正常解析html文件作为模版，就会直接把 <%= htmlWebpackPlugin.options.title %> 解析成字符串

如果有html-loader, 去掉就可以了

![title](/img/webpack/title4.png)

所以使用别的模板进行解析

![title](/img/webpack/title5.png)

#### hash

```javascript
hash: true
```

设置了此属性为true 则在编译之后的文件中 引入的外部文件会加上哈希值

```html
<script type="text/javascript" src="server.bundle.js?81641f9e16cafa3fb408"></script></body>
```


#### minify

```javascript
  minify: {    
     removeComments:true,    //移除HTML中的注释
     collapseWhitespace:true    //删除空白符与换行符
  }
```

![minify](/img/webpack/minify.png)

配置里的hash属性虽然可以给html引入的所以css文件后面加hash字符串，可以达到清除缓存的效果，但缺点是有些不需要清除缓存的css文件它也清除了

因为每次编译它会给所有css文件加同样的hash字符串。即时其他css没有变化。所以推荐用extract-text-webpack-plugin插件在编译提取css属性的时候用contenthash配置一下就可以解决这个问题


### Babel

babel-loader的功能就是将项目中使用到ES6或者jsx的代码转化为es5,并且会把项目中所有的js文件都进行转化一遍，其中包括node_modules文件里面的内容

如果直接在webpack中配置babel

```javascript
module: [
  {
    test: /\.js$/,
    use: [{
      loader: 'babel-loader',
      options: { presets: ['es2015', 'react'] }
    }],
  }
]      
```

但是考虑到babel具有非常多的配置选项，在单一的webpack.config.js文件中进行配置往往使得这个文件显得太复杂，因此一些开发者支持把babel的配置选项放在一个单独的名为 ".babelrc" 的配置文件中

如果不在webpack中写入babel的options部分 则webpack会自动调用.babelrc里的babel配置选项


```javascript
module: [
  {
    test: /\.js$/,
    use: [{
      loader: 'babel-loader'
    }],
  }
]      
```

一个简单的 .babelrc 文件

```javascript
{
  "presets": [ "es2015", "stage-0", "react" ],
  "plugins": [
    ["import",
      [
        { "libraryName": "antd", "style": "css" },
        { "style": "css","libraryName": "antd-mobile" }
      ]
    ]
  ]
}
```
这里控制台可能会报错  

![babelrc](/img/webpack/webpack-babelrc.png)

根据[api](https://github.com/ant-design/babel-plugin-import)  需要安装一个模块

```javascript
npm i babel-plugin-import
```
