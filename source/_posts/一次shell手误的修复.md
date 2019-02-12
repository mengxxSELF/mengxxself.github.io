---
title: 一次shell手误的修复
date: 2019-01-17 10:40:13
tags: [linux]
---

![error](https://user-gold-cdn.xitu.io/2019/1/17/16859dfeabf97b15?w=875&h=385&f=png&s=67477)

> 手误之后 基本命令失效

<!-- More -->

在shell中 变量的设置是 

```js
name=123
```

变量的追加是

```js
name=$name:9090
```


之前在使用 umi 创建项目的时候 为了解决出现的问题 在 ~/.zshrc 中添加了一个 PATH 路径

应该是

```js
export PATH=$PATH:/Users/zhengzaijiazai/.nvm/versions/node/v8.11.2/bin
```

但是第一次写的时候 没有加 $PATH


```js
export PATH=/Users/zhengzaijiazai/.nvm/versions/node/v8.11.2/bin
```

保存之后 source 使其生效 

然后，基本命令不可用了

![ls](https://user-gold-cdn.xitu.io/2019/1/17/16859bec41c0a55e?w=257&h=158&f=png&s=14748)

最常用的ls vim 都不可用了 这下想重新打开 zshrc 重新修改也没法了

解决方案  定义临时变量

```js
export PATH=/usr/bin:/usr/sbin:/bin:/sbin:/usr/X11R6/bin
```

这个相当于定义了一个局部的  PATH 变量 然后基本命令可以使用了 重新vim 调整 zshrc 就可以了