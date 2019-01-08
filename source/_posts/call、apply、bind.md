---
title: call、apply、bind
date: 2017-10-07 18:53:19
categories: javascript基础
tags: [this, call, apply, bind]
---

call apply bind 这三个方法都是Function 函数类上的方法

<!--more-->

## call

### call的作用：

改变obj1中this指向  并执行call前面的函数obj1

```javascript
obj1.call(obj2)
```

#### call 的应用

2.1  前面的函数中没有this 相当于只是执行前面的函数

```javascript
function father(){
  console.log('father')
}
function son(){
 console.log('son')
}
father.call(son); // 控制台打印出father  
```
2.2  前面的函数有this

```javascript
function father(){
  console.log(this);
}
function son(){}
father.call(son); // 控制台打印出 function son(){}
```


2.3 多个call 连续使用

```javascript
function f1(){console.log('i am f1')} ;  
function f2(){console.log('i am f2')} ;
f1.call.call.call(f2)
```

相当于执行f2 函数  所以输出 i am f2

f1.call 是走到函数的定义阶段

## apply

apply使用和call 相同,只不过是在传入参数的使用的是数组

```javascript
f1.apply(f2,['tom',24])
```

### apply的应用   

获取数组中最大值

2.1 sort 排序

2.2 假设法

2.3 利用apply 传参为数组这个特点

```javascript
var arys =[3,4,2,61,99];
Math.max.apply(Math,arys)
```

2.4 eval   将字符串转为JS代码执行

数组与字符串进行拼接 会连接为新的字符串

```javascript
eval('Math.max('+ arys +')')
```

## bind

bind 和call有点类似   改变this指向,传入参数，但是不执行函数。所以属于预处理机制。

```javascript
f1.bind(f2,'tom',24)
```
