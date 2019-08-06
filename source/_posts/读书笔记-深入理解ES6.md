---
title: 读书笔记-深入理解ES6
date: 2019-07-04 18:50:41
categories: 读书笔记
tags: [阅读, 学习]
---

深入理解ES6

<!-- more -->

### chapter 1 

#### 暂时性死区  temporal dead zone - TDZ

> TDZ: 代码块中， 在let 或者 const 声明变量之前，变量都不可访问

js引擎在扫描代码的时候，发现变量生命的时候， 遇到var声明的，则提升到作用域顶部。 遇到const/let 则将其放在暂时性死区中

只有在执行过变量声明语句之后，变量才会从 TDZ 中 移出，然后就可以正常访问了

```js
if (isName) {
  console.log(typeof clones) // 这里会报错
  let clones = 'xxxxx'
}
```

### 对于全局变量的影响

let命令、const命令、class命令声明的全局变量，不属于顶层对象的属性。从 ES6 开始，全局变量将逐步与顶层对象的属性脱钩

```js
var a = 123
console.log(window.a) // 123
window.b = 999
const b = 234
console.log(window.b) // 999
console.log(b) // 234
```

let/const 定义的变量 不影响全局变量，但是可以“遮盖”

### chapter 3 

#### es6中的 arguments

在es6中，如果一个函数使用了默认值，那么他与arguments 对象保持分离

```js
function change (num, level = 1) {
  console.log(arguments.length) // 1
  console.log(arguments[0] === num) // true
  console.log(arguments[1] === level) // false
}
```

#### 默认参数是在函数调用时传值

默认参数是在函数调用时传值, 所以可以使用先定义的参数作为后定义参数的默认值，比如

```js
function add (first, second = first) {
  return first + second
}

```

#### instanceof 

#### 箭头函数

* 没有 this/super/arguments/mew.target 
* 不能通过new关键字调用
* 没有原型


* 不能改变this指向

可以在箭头函数上调用 call /bind/ apply ，但是箭头函数的this不会被影响

补充阅读 [javascript中new关键字详解](https://juejin.im/post/59c9bc855188254f58413aa0)

#### 尾调用优化

补充阅读 [JS 调用栈机制与 ES6 尾调用优化介绍](https://juejin.im/post/5cd7bdcbf265da03a97b0934)


### chapter 3

#### 写法简化

```js
const target = {
  name: 'target',
  action: function got () { ... } // 写法1
  action () { ... } // 写法2 省略冒号 和 function 
}
```
#### 可计算的属性名

```js
const attr = 'age'
const person = {
  [attr]: 20
}
```

在对象字面量中使用 [] 表示此属性名称是可计算的，他的内容会被求值

#### 原型相关

* Object.create()

Object.create()方法创建一个新对象，使用现有的对象来提供新创建的对象的__proto_

在 ES5中，可以通过设置对象的 __proto__ 来设置其原型对象

```js

let protoTarget = { age: 19 }

let user = {name: 'wyb'}
user.__proto__ = protoTarget

console.log(user.age) // 19
```

在 ES6 中

```js
let protoTarget = { age: 19 }

let user = Object.create(protoTarget)
user.name = 'wyb'
```

* Object.getPrototypeOf()

获取原型

* Object.setPrototypeOf()

设置原型

