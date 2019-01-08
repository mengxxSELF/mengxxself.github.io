---
title: SVG + CSS3动画实现文字描边
date: 2017-09-24 21:02:12
categories: CSS3
tags: [svg, css]
---

## svg 定义

SVG 是使用 XML 来描述二维图形和绘图程序的语言

```html
<svg></svg>
```
## stroke 属性

<!-- more -->

| 属性名 | 含义 |
| --- | --- | --- | --- |
| stroke | 用于设置线条颜色 |
| stroke-width | 线条粗细 |
| stroke-opacity | 线条不透明度 |
| stroke-linecap | 描边方式 |
| stroke-linejoin | 两个描边之间用什么链接 |
| stroke-dasharray | 虚线样式 |
| stroke-dashoffset | 定义虚线开始的位置 |


### stroke-dasharray

> stroke-dasharray属性的参数，是一组用逗号分割的数字组成的数列。

```html
stroke-dasharray="5,10,5"
stroke-dasharray="5,5"
```

注意，和path不一样，这里的数字必须用逗号分割（空格会被忽略）。每一组数字，第一个用来表示填色区域的长度，第二个用来表示非填色区域的长度。

所以在上面的例子里，第二个路径会先做5个像素单位的填色，紧接着是5个空白单位，然后又是5个单位的填色。如果你想要更复杂的虚线模式，你可以定义更多的数字。

第一个例子指定了3个数字，这种情况下，数字会循环两次，形成一个偶数的虚线模式（奇数个循环两次变偶数个）。所以该路径首先渲染5个填色单位，10个空白单位，5个填色单位，然后回头以这3个数字做一次循环，但是这次是创建5个空白单位，10个填色单位，5个空白单位。通过这两次循环得到偶数模式，并将这个偶数模式不断重复


## 实例展示

创建元素

```html
<svg width="100%" height="100">
  <text text-anchor="middle" x="50%" y="50%" class="text">
      cancan
  </text>
</svg>
```

控制CSS

```css
.text{
  font-size: 64px;
  font-weight: bold;
  text-transform: uppercase;
  fill: none;
  stroke: #3498db;
  stroke-width: 2px;
  stroke-dasharray: 90 310;
  animation: stroke 6s infinite linear;
}
@keyframes stroke {
  100% {
    stroke-dashoffset: -400;
  }
}
```
开启Animation动画 通过不断变动虚线开始的位置 来实现文字边线闪动

![svg](/img/svg/can2.gif)

这样可以发现 视觉上的文字边界移动效果


如果创建多个重复元素 可以实现以下效果

![svg](/img/svg/can.gif)
