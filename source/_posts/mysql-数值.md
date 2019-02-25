---
title: mysql-数值
date: 2019-02-22 18:54:16
categories: 数据库
tags: [mysql]
---

> mysql float

<!-- More -->

操作mysql数据库的时候，有一列是金额，设置的字段类型是 float。 然后有一个SUM求和的方法，发现SUM 之后的结果有问题，

小数点的位数不是预期的两位。

这个的操作和js一样处理的，在js中浮点数相加，也是有位数不准确的问题

网上找到两个处理方案

* 将float字段改为 decimal(16,6)

![decimal](https://user-gold-cdn.xitu.io/2019/2/22/16914eb5f11e5557?w=273&h=32&f=png&s=3143)

decimal 类型是数值的一种类型

decimal（M, D）

·M是数字的最大数（精度）。其范围为1～65，M 的默认值是10。

·D是小数点右侧数字的数目（标度）。其范围是0～30，但不得超过M。

* 使用binary关键字解决   select sum(binary 字段名(float类型))

```js
select sum(binary(money)) as total
```
