---
title: switch-case
date: 2017-02-15 17:55:18
categories: javascript基础
tags: [switch]
---

> 笔记迁移系列，由于原始笔记所在网站即将下线，现将笔记全部迁移，并对原有笔记进行修正或添加

<!-- More -->

#### 基本规则

switch 使用的是严格比较 === 

#### 使用

在项目中使用的了switch的另一种写法

是用的是表达式进行判断的

代码如下

```js
let {dailyCount ,totalCount:mileage } = userinfo
 let mile = 0 
 switch (true) {
 case mileage < 500 && mileage > 200: // 200-499
 mile = 1
 break;
 case mileage < 1000 && mileage > 4999: 
 mile = 2
 break;
 case mileage < 20000 && mileage > 10000: 
 mile = 3
 break;
 case mileage > 200000:
 mile = 4
 break;
 default:
 mile = 0
 }
```

这里switch写的是布尔值 TRUE

其实开始这里写的是

switch (mile)
自然不对 这不符合switch的比较规则

switch的比较按照的是 将case 语句后面的结果 与switch中的参数进行严格比较  比较结果为TRUE 则执行对应case语句

所欲当在case  条件为布尔值的时候 switch语句里的也应该是布尔值

因此switch 判断条件应该为

switch (true)

#### 注意

以上使用方式虽然正确，但是从代码规范来讲，并不符合switch的使用语义，不推荐这样写
