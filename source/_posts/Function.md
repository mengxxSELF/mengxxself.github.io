---
title: Function
date: 2017-10-15 19:31:32
categories: javascript基础
tags: [笔记迁移, 对象]
---

> 笔记迁移系列，由于原始笔记所在网站即将下线，现将笔记全部迁移，并对原有笔记进行修正或添加

<!-- More -->

1 所有的函数数据类型都是Function 的实例      所有的对象，都是Object这个基类的实例

2 构造函数的三种身份

1 普通函数

2 构造函数    类 实例 原型 打交道

3 对象   就是普通对象，具有属性和方法两个特征

3 基类Object与函数类Function之间的关系



1 Function instanceof Object // true    Function的一种身份是对象 ， 是Object的实例

2 Object instanceof Function //true     Object是构造函数，函数都是Function的实例

3 Object.protorype 是Function.prototype 的上一级

4 Object.prototype 是 Function.__proto__的上一级

