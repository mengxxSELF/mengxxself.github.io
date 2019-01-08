---
title: rem基本原理
date: 2018-10-20 18:38:50
categories: css
tags: [css3, rem]
---


> 移动端适配方案

<!-- More -->

什么是移动端适配

制作一套页面，在不同设备中，页面图片、文字、间隙大小会变化，但是在比例上保持一致。

### Rem

简单来说，将原来的px转为rem

需要判断根元素字体大小 每一个设备的根元素字体大小不同

设备根元素字体 

```js
document.documentElement.style.fontSize
```

* 如何判断使用多大根字体

假设设计稿为640px  当设备宽度也为640px  则可完全按照设计稿进行布局 但是单位要使用rem

将根元素字体大小设置为100px  比较好算

```js
body{font-size: 100px;}
p{margin-top:0.3rem;}// 相当于 X rem
30px/X rem = 100;  求出X 为 0.3
```

关于变化的根元素

在设计稿640px  有一个元素宽度 20px  根元素字体大小100px    写为自适应 使用rem 单位  0.2 rem

在设备  320px  有一个元素宽度 20px  根元素字体大小？？？PX         使用rem 单位  0.2 rem

所以变化的是设备的根元素

#### 求设备根元素大小

当设计稿宽度为desW    设备宽度为 docW

设备宽度的缩放倍数 = 根元素字体大小缩放倍数

设计稿宽度/设备宽度 = 100/ X

```js
var desW = 750;// 设计稿宽度
var winW = document.documentElement.clientWidth; // 设备宽度
document.documentElement.style.fontSize = winW/desW*100+"px"; // 求出当前设备根元素大小
```

注意，求出当前设备宽度使用的是document.documentElement.clientWidth  其实这个是文档宽度 而由于在开头有写

```js
<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0"/>
```

width=device-width 表明了将文档宽度设置为设备宽度

 
### flex布局

flex布局是Flexible Box的缩写  是弹性布局的意思  用来为盒状模型提供很大灵活性

所有的容器都可以指定为Flex布局 只要将其指定为

```js
div { display: flex; }
```

行内元素也可以flex布局  display设置为 inline-flex 即可

* 设为flex布局之后 子元素的float  clear vertical-align 属性失效

#### 基本概念

采用Flex布局的元素称为Flex容器 其子元素为容器成员 称为项目

容器默认存在主轴【水平方向】和交叉轴【垂直方向】

主轴开始位置 mian start 结束位置 main end

容器默认沿着主轴排列 单个项目占据的主轴空间是main size

#### 容器属性

flex-direction
flex-wrap
flex-flow
justify-content
align-items
align-content

1 flea-direction 决定主轴方向 【项目的排列方向  默认是水平 起点左侧】

row  【默认值】 主轴为水平方向 左侧为起点位置
reo-reverse  主轴为 水平   右侧是起点
column 主轴是垂直  上为起点
column-reverse  主轴是垂直 下为起点

2 flex-wrap

默认情况 项目排列为一条线 又称轴线  flex-warp 属性定义了如果在轴线排列不下的情况 如何换行

nowrap  【默认】 不换行
wrap  正常换行 第一行在上
wrap-reverse  换行 第一行在下方

3 flex-flow

这个属性其实是flex-direction和flex-wrap的简写形式  默认是 row nowrap

4 justify-content

属性定义了项目在主轴上的对齐方式

flex-start  【默认值】 左对齐
flex-end 右对齐
center 居中
space-between 两端对齐  项目之间间隔相同
space-around 每个项目两侧间隔相同  所以 项目之间的间隔比项目与边框之间的间隔大一倍

5 align-items

定义项目在交叉轴上 如何对齐  假设交叉轴为默认值 上到下

flex-strat  与交叉轴的起点对齐
flex-end 与交叉轴终点对齐
center 与交叉轴中点对齐
baseline  项目的第一行文字的基线对齐
stretch 【默认值】 如果项目没设置高度 或者设为auto  将沾满全部容器空间

6 align-content

定义多跟轴线的对齐方式 这里如果项目只有一根轴线 则设置无效

flex-start
flex-end
center
space-between
space-around

7 属于项目的属性

order
flex-grow
flex-shrink
flex-basis
flex
align-self

7.1 order

定义项目排列顺序 数值笑的排位靠前 默认0

7.2 flex-grow

定义项目的放大比例 默认0

7.3 flex-shrink

定义项目缩小比例 默认为1  如果空间不足 项目按照比例缩小

7.4 flex-basis

在分配多余空间前 项目占据的主轴空间大小 默认为auto

7.5 flex

这个属性是flex-grow flex-shrink flex-basis的简写 默认是 0 1 auto

7.6 align-self

允许单个项目与其余的不同 可以覆盖align-items属性 默认为auto 继承父级元素的align-items属性 如果此元素并没有父元素 则等用于stretch

 

 

 

 

