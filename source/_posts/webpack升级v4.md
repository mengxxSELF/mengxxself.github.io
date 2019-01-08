---
title: webpack升级v4
date: 2018-11-02 10:28:11
tags:
---

![webpack4](https://user-gold-cdn.xitu.io/2018/11/21/16735279fb5413b5?w=497&h=304&f=jpeg&s=19072)

> webpack4

<!-- More -->

最近项目在做重构，刚好把webpack升级到V4

基本配置

```js
const webpackConfig = {
  watch,
  optimization,
  entry,
  output,
  module,
  plugins,
  resolve
}
```

### plugins

* CleanPlugin

* MiniCssExtractPlugin

代替 V3 中经常使用的 extract-text-webpack-plugin
