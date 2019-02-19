---
title: requestAnimationFrame
date: 2018-02-19 19:06:18
categories: javascript基础
tags: [笔记迁移, 练习, 正则]
---

> 笔记迁移系列，由于原始笔记所在网站即将下线，现将笔记全部迁移，并对原有笔记进行修正或添加

<!-- More -->

在项目中有使用到定时器去实现动画效果 但是会有卡顿问题

这是因为如果使用window.setTimout()或者window.setInterval()不断更新元素的状态位置等来实现动画 就要求画面的更新频率要达到每秒60次才能让肉眼看到流畅的动画效果

window还有一个方法requestAnimationFrame 是用来处理这种问题

window.requestAnimationFrame() 将告知浏览器你马上要开始动画效果了，后者需要在下次动画前调用相应方法来更新画面   这个方法就是传递给window.requestAnimationFrame()的回调函数


浏览器可以优化并行的动画动作，更合理的重新排列动作序列，并把能够合并的动作放在一个渲染周期内完成，从而呈现出更流畅的动画效果

比如，通过requestAnimationFrame()，JS动画能够和CSS动画/变换或SVG SMIL动画同步发生。

另外，如果在一个浏览器标签页里运行一个动画，当这个标签页不可见时，浏览器会暂停它，这会减少CPU，内存的压力，节省电池电量
 
#### 使用

如果我们使用定时器来创建动画 是这么写的

```js
function render(){
     //动画
    setTimeout(render, 1000/60); // 一般浏览器是每秒60帧
}
render()
```

定时器的问题是  如果浏览器不是每秒60帧，会造成掉帧

还有，在性能方面，大部分在浏览器在标签页/窗口处于的时候非激活状态（如窗口最小化或标签页切换了）时，setTimeout/setInterval 还会执行

这个有人做过具体的对比 请参见文章[《对比时间测试》](https://segmentfault.com/a/1190000000386368)

* 标签页闲置时，时间间隔：

![time](https://user-gold-cdn.xitu.io/2019/2/19/16905731104aac80?w=859&h=302&f=png&s=28035)
 
那么采用  requestAnimationFrame 则是

```js
function render(){
     // 动画
     requestAnimationFrame(render)
}
render()
```

* 参数解析

接收一个函数作为回调，返回一个ID值

```js
requestAnimationFrame(callback)   //callback为回调函数
```

这个ID值传给window.cancelAnimationFrame()可以取消该次动画

#### Other

有人封装了一个raf的模块 可以之间引入使用 [click me](https://www.npmjs.com/package/raf)

