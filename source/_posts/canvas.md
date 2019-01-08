---
title: canvas
date: 2017-09-27 16:52:39
categories: javascript
tags: canvas
---

之前写过一篇 [canvas初识](http://web.blued.cn/2017/07/03/Canvas%E5%88%9D%E8%AF%86/)

简单使用了canvas进行图形绘制

<!-- more -->

### canvas 绘制弧度三角

![canvas](/img/canvas/bubble.png)

这个气泡的左下部分 是一个有弧度的三角 最开始是使用的一个图片来处理 后来想想 使用canvas应该也可以做到这个效果

主要是canvas的弧度处理 也就是画圆

### ctx.arc(x, y, radius, start, end, boolean)

这个方法是用来进行画圆的

参数解析

| 参数 | 含义 |
| --- | --- | --- | --- |
| x , y | 圆心 |
| radius | 半径 |
| start | 开始角度 |
| end | 结束角度 |
| Boolean | 顺时针或者逆时针 |

最后一个布尔值 默认FALSE 表示顺时针开始画画

![canvas](/img/canvas/API.png)

#### 半圆

```html
ctx.arc(190, 190, 90, 0, Math.PI * 3, true)
ctx.fill()
```
![canvas](/img/canvas/canvas.png)

#### 四分之一

```html
ctx.arc(190, 190, 90, 0, Math.PI * 0.5, false)
ctx.fill()
```

![canvas](/img/canvas/line.png)

#### 带有弧度的三角形

```javascript
ctx.arc(100, 100, 50, Math.PI, Math.PI * 1.5, false)
ctx.lineTo(50, 50)
ctx.lineTo(50, 100)
ctx.stroke()
```
![canvas](/img/canvas/draw.png)
