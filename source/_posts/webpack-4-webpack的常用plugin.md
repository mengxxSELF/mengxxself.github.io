---
title: webpack-4 - webpack的常用plugin
date: 2017-11-30 17:14:19
categories: 前端自动化
tags: webpack
---

webpack的常用plugin

<!-- more -->

### CommonsChunkPlugin

打包公共文件

```javascript
new webpack.optimize.CommonsChunkPlugin({
  name: ['vendor', 'manifest'],
  minChunks: Infinity,
  // (随着 entry chunk 越来越多， 这个配置保证没其它的模块会打包进 vendor chunk)
})
```

CommonsChunkPlugin 的配置项

```javascript
new webpack.optimize.CommonsChunkPlugin('init')
```

编译之后的文件命名将按照传入的文件名 + 配置在output中 filename格式 进行命名

比如 在filename中是

```javascript
output:{
  filename: '[name].[chunkhash:4].js'
}
```
则上面公共文件输出为 init.89sk.js 也就是也会配置相应的哈希值


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

#### vendor 哈希值一直变动问题

（一下的测试全部指的是单入口应用）

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

### HashedModuleIdsPlugin 与 NamedModulesPlugin

这个需要配合上面说的 commonsCunkPlugin 使用

[参考文章](http://www.jb51.net/article/120195.htm)


### clean-webpack-plugin

每次重新打包都会清理 pbblic 文件夹的内容

```javascript
const CleanWebpackPlugin = require('clean-webpack-plugin')

plugins: [
  new CleanWebpackPlugin(
    ['public/index-*.js','public/manifest-*.js'],　 // 匹配删除的文件
    {
      root: __dirname,       　　　　　　　　　　//根目录
      // verbose: true,   // 开启在控制台输出信息 其实默认为TRUE
      // dry: false  // 删除文件 默认FALSE
    }
  )
]
```

这样每次重新打包的时候 public文件夹都会删除掉

![clean](/img/webpack/webpack-clean.png)

| 属性名 | 默认的属性值 | 含义 |  
| --- | --- | --- | --- |
| root | __dirname | 根节点 |
| verbose | true | 在控制台输出log |
| dry | false | 执行删除操作 |
| watch | false | 在编译的时候 不去 删除旧的文件 |
| exclude | 数组 | 删除的时候 这个数组中的不进行删除 |
| allowExternal | false | 不可以删除在上面配置的root之外的文件 |

其实还可以通过在命令行添加参数实现清除功能

```javascript
"webpack": "rm -rf public; webpack --watch"
```

[github地址](https://github.com/johnagan/clean-webpack-plugin)

###  UglifyJsPlugin

```javascript
  new webpack.optimize.UglifyJsPlugin()
```

### extract-text-webpack-plugin

此插件用于分离CSS和JS文件

将css文件单独打包到css文件，而不是写在js文件中（因为webpack将一切都视为模块，所以默认会打包到js文件中）

它会将所有的入口 chunk(entry chunks)中引用的 sass less 或者 styles 文件，移动到独立分离的 CSS 文件

因此，你的样式将不再内嵌到 JS bundle 中，而是会放到一个单独的 CSS 文件（即 styles.css）当中。 如果你的样式文件大小较大，这会做更快提前加载，因为 CSS bundle 会跟 JS bundle 并行加载

在Module中配置 loader 在 plugin中添加ExtractTextPlugin 属性

```javascript
module: {
  loaders: [
    {
      test: /\.css$/,
      use: ExtractTextPlugin.extract({
        fallback: "style-loader",
        use: "css-loader"
      })
    }
  ]
}
......
plugins: [
  new ExtractTextPlugin('index.css')
]
```

所有chunk中被引入的 CSS文件被打包到一个CSS文件中 并被引入index

![style](/img/webpack/style.png)

如果是多个入口文件 需要各自引入需要的js 以及 css 文件呢 则需要这么配置

```javascript
new ExtractTextPlugin('[name].css')
```

![style](/img/webpack/style2.png)


需要同时配置 modules 和  plugins

```javascript
const ExtractTextPlugin = require('extract-text-webpack-plugin')
const extractCSS = new ExtractTextPlugin('style/[name]-css.css')
const extractLESS = new ExtractTextPlugin('style/[name]-less.css')
const extractSCSS = new ExtractTextPlugin('style/[name]-scss.css')

module.exports = {
  module: {
    rules: [
      {
        test: /\.less$/,
        use: extractLESS.extract([ 'css-loader', 'less-loader' ])
      },
      // 处理 sass 相关
      {
        test: /\.scss$/,
        use: extractLESS.extract([ 'css-loader', 'sass-loader' ])
      },
      // 处理css
      {
        test: /\.css$/,
        use: extractCSS.extract([ 'css-loader', 'postcss-loader' ])
      }
    ]
  }

  plugins: [
    extractCSS,
    extractLESS,
    extractSCSS,
  ]  
}
```

[github](https://github.com/webpack-contrib/extract-text-webpack-plugin)
