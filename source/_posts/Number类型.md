---
title: Number 类型
date: 2019-02-13 11:16:16
categories: javascript基础
tags: [笔记迁移, number]
---

> 笔记迁移系列，由于原始笔记所在网站即将下线，现将笔记全部迁移，并对原有笔记进行修正或添加

<!-- More -->

## Number 

正数 负数 小数 0 NaN（not a number）

在不能计算或者数据类型转换不成功的时候，可能出现NaN

```js
console.log( NaN == NaN ) // false
```

### 全局方法

| 方法 | 含义 |
| ------- | ------- |
| isNaN | 判断是否是 NaN |
| Number | 强制转化为数字类型 转化不成功返回NaN |
| parseInt | 非强制转换 |
| parseFloat() | 非强制转换, 比parseInt多识别小数点 |

* isNaN

```js
isNaN(NaN) // true

isNaN(78) // false

isNaN('09') // false

isNaN(undefined) //true

isNaN(null) //false
```
可以看出，当执行该isNaN 方法, 会先使用Number方法会参数进行转化，再进行判断

* Number()

当全部是数字时候才转化成功

```js
Number('34') //34
Number('34lp') //NaN

Number(['0']) //0
Number(['009iu']) //NaN
Number([8,0]) //NaN 有逗号，不符合全部都是数字这一条件
Number(undefined) //NaN

// 几个特殊的
Number(null) //0
Number([]) // 0
Number('') //0

//布尔值转化为0 或者1
Number(true) //1
Number(false) //0
```

* parseInt

将转化内容从左到右依次转化，直到不是有效数字，停止查找。返回。 转化不成功返回NaN

```js
parseInt('509s5yyy') //509
parseInt(12.3) //12
parseInt('yy') //NaN
```
* parseFloat

```js
parseFloat('123.45#') // 123.45
```

#### es6

es6中 将以前的一些全局的方法 移动到Number对象上了，有的行为发生了变动

| 方法 | 含义 |
| ------- | ------- |
| Number.isNaN() | 判断是否是 NaN |
| Number.parseInt() | 非强制转换，与之前保持一致 |
| Number.parseFloat() | 非强制转换, 比parseInt多识别小数点，与之前保持一致 |

```js
isNaN(NaN) // true
isNaN("NaN") // true

Number.isNaN('15') // false
Number.isNaN(true) // false
Number.isNaN(9/NaN) // true
Number.isNaN('true' / 0) // true
Number.isNaN(NaN) // true
Number.isNaN("NaN") // false
Number.isNaN(1) // false
```

与传统的全局方法isNaN()的区别在于，传统方法先调用Number()将非数值的值转为数值，再进行判断，

Number.isNaN()只有对于NaN才返回true，非NaN一律返回false。 ( 或者说判断的是 参数本身 是不是 NaN )

### NaN

表示不是数字

* 什么情况下得到NaN

1  类型转化不成功
2 计算不成功

### Math 对象

#### 方法

1 Math.abs()

2 Math.floor()  向下取整

3 Math.ceil()  向上取整

4 Math.min()

5 Math.max()

6 Math.round()  四舍五入

```js
Math.round(11.5) // 12 
Math.round(-11.5) // -11
```

7 Math.random()  0到1之间的随机数 [0,1)

取[n,m]之间的随机整数

```js
Math.round( Math.random * (m-n) + n  )
```

8 Math.sqrt()  开平方

10 Math.pow(x,y)    x的y次方

```js
Math.pow(3,2) //9
```

* es6 新增

1 Math.trunc()

去除一个数的小数部分，返回整数部分。 对于非数值，Math.trunc内部使用Number方法将其先转为数值。对于空值和无法截取整数的值，返回NaN。

```js
Math.trunc(4.1) // 4
Math.trunc(4.9) // 4
Math.trunc(-4.1) // -4
Math.trunc(-4.9) // -4
Math.trunc('123.456') // 123
Math.trunc(true) //1
Math.trunc(false) // 0
Math.trunc(NaN);      // NaN
Math.trunc('foo');    // NaN
Math.trunc();         // NaN
Math.trunc(undefined) // NaN
```