---
title: 认识typescript
date: 2018-07-18 17:22:55
tags: [typescript]
---

![ts](https://user-gold-cdn.xitu.io/2018/8/31/165902737ee8f6ea?w=922&h=470&f=png&s=234130)

> typescript 概览

<!--more-->

### 基础类型

```js
let isDone: boolean = false

let age: number = 2

let yourname: string = 'who'

let ary: number[] = [1, 2, 3]

let ary2: Array<string> = ['1']

let moreAry: any[] = ['', 2, true]

let isUn: void = null
```

### 联合类型

可以是 多种类型的一种

```js
function data(arg: string | number) {
  return `arg is ${typeof arg === 'string' ? 'string' : 'number'}`
}

console.log(data(1), data('username'))
```

### 接口 

> 对值所具有的结构进行类型检查

* 必须包含一个label属性且类型为string

```
interface args {
  label: string
}

function explainUser(user: args) {
  return user['label']
}

explainUser({label: '123'})
```

比如编写一个 获取数据的函数 getData

```javascript
async function getData(url: string, data: object) {
  return xxx
}
```
需要传递两个参数 url data

我们将其调整为 interface 形式

```javascript
interface dataType {
  url: string,
  data: object
}

async function getData(dataValue: dataType) {xxx}
```

* 类型检查器不会去检查属性的顺序，只要相应的属性存在并且类型也是对的就可以

* 可选属性

添加一个 ? 表示这个属性是可选的 

```javascript
interface dataType {
  url: string,
  data: object,
  uid?: number
}
```

### 函数

> 函数类型包含两部分：参数类型和返回值类型

TypeScript能够根据返回语句自动推断出返回值类型，因此我们通常省略它

返回值类型是函数类型的必要部分，如果函数没有返回任何值，你也必须指定返回值类型为void而不能留空

* 根据当前环境参数 返回config 

```javascript
function (env: string, number: number): object {
  return config[env][number]
}
```

* 可选参数必须跟在必须参数后面

```javascript
function (env: string, number: number, uid?: number) {
  xxx
}
```

这里加了一个可选参数 UID, uid 放到必选参数后面

* 在所有必须参数后面的带默认初始化的参数都是可选的

```javascript
function (env: string, number: number, uid = 137722) {
  xxx
}
```

这里的参数UID带有默认参数 所以它是可选的

### 类型断言

```js
<类型>值 

值 as 类型
```

比如我们需要在还没有确定类型的时候读取其值

```js
function getData (classify: string, page: number | string ): boolean {
  if (page.length > 1) {

  } else {

  }
}
```
这里肯定会有报错

所以需要处理为

```
if ((<string>page).length )
```

### 内置对象

JavaScript 中有很多内置对象，它们可以直接在 TypeScript 中当做定义好了的类型。

* 


### 元组


### 类 


TypeScript 可以使用三种访问修饰符（Access Modifiers），分别是 public、private 和 protected。

* public 修饰的属性或方法是公有的，可以在任何地方被访问到，默认所有的属性和方法都是 public 的
* private 修饰的属性或方法是私有的，不能在声明它的类的外部访问
* protected 修饰的属性或方法是受保护的，它和 private 类似，区别是它在子类中也是允许被访问的


### 泛型


### Promise

经常需要去构建promise处理方法

```javascript
const info = (uid: number): Promise<string> => {
  return new Promise((resolve, reject) => {
    resolve(`${uid} is success`)
  })
}
```

如果返回值不确定 可以写为 

```javascript
const info = (uid: number): Promise<any> => {
  return new Promise((resolve, reject) => {
    xxx
  })
}
```

### react中ts的应用


