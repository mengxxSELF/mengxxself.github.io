---
title: 有趣的tips
date: 2018-08-11 14:47:56
categories: js
tags: [react, js, ndoe]
---

![tips](https://user-gold-cdn.xitu.io/2018/8/31/1659022b84159eb5?w=1354&h=544&f=png&s=43243)

> 在阅读文章的时候，一些有意思的知识点

<!-- more -->

## react中请求数据为什么在 componentDidMount 这个生命周期

componentWillMount -> render -> componentDidMount

如果在 componentWillMount 中做请求，setState 不会引起重新render （如果强制render？）

并且在react16中，componentWillMount 可能会被调用好多次


## overflow-y

iOS中 使用overflow-y: scroll; 导致滚动卡顿 

* [overflow-scroll](https://segmentfault.com/q/1010000007323949)

* [scroll](https://segmentfault.com/a/1190000012014962)

## 回流 重绘

* [article](http://caibaojian.com/css-reflow-repaint.html)

* [reflow](https://segmentfault.com/a/1190000017491520)

```js
/*
 * 根据上面的结论
 * 将 2d transform 换成 3d
 * 就可以强制开启 GPU 加速
 * 提高动画性能
 */
div {
  transform: translate(10px, 10px);
}
div {
  transform: translate3d(10px, 10px, 0);
}
```

