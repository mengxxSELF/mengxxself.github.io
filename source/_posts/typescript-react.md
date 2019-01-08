---
title: typescript-react
date: 2018-09-28 20:36:14
tags: [typescript]
---

> 项目ts版做半重构

<!--more-->

### 储备知识

#### 基本类型

#### 接口

> TypeScript的核心原则之一是对值所具有的结构进行类型检查

在TypeScript里，接口的作用就是为这些类型命名和为你的代码或第三方代码定义契约。



定义属性

```js
interface props {
  readonly name: string
  habit? : string
}
```

定义一个函数所需的形状

```js
interface fnBox {
  (len:number, cont: number): number[]    
}
let pushAry:fnBox
pushAry = function (len: number, cont: number) {
  return new Array(len).fill(cont)    
}
```
如果这里故意写错cont的类型，会有什么提示

![inetrface](https://user-gold-cdn.xitu.io/2018/9/28/1661ee46975a3f58?w=925&h=353&f=png&s=269858)

可以定义无返回值的函数

```js
interface props {
  once(): void 
  twice: () => void 
}
```

类 && 接口

implements 可以使类 具有接口的属性功能

```js
interface int2 {
  once(): void,
  touchMore: (count: number) => void
}

class Btn implements int2 {
  once () {
    console.log('once')
  }
  touchMore (count: number) {
    console.log('touchMore')
  }
}

const btns = new Btn
btns.once()
btns.touchMore(9)
```


### 配置 tsconfig.json

1 使用webpack搭建项目的时候，有配置alias参数

```js
'utils': path.resolve(__dirname, '../utils')
```

这样在组件中引入的时候，文件路径可以简化

```js
import { log } from 'utils/statistics'
```

不过在使用ts之后，会发现组件中有警告信息，表示找不到此模块

就需要另外在tsconfig中添加配置参数

```js
    "baseUrl": "./",
    "paths": {
      "components": ["./components/*"],
      "utils": ["./utils/*"],
    }
```

2  noEmitOnError: true

当编译出错，则不输出编译后的文件


### 全局变量 window

在访问页面的时候，server会反馈一些基本的用户信息，比如用户名，设备版本号等，将这些全部挂载在CONFIG变量中了

```html5
<script type="text/javascript">
  window.CONFIG = JSON.parse(decodeURIComponent('{{feConfig}}'))
</script>
const {uid, version} = window.CONFIG // 然后在组件中可以直接获取
```

但是在加入TS之后，会提示window中不存在属性CONFIG

![window](https://user-gold-cdn.xitu.io/2018/9/28/1661f5da919ed31b?w=888&h=167&f=png&s=200358)

这是因为 ts 不允许从未

处理方案

1 

```js
 declare global {
   interface Window { CONFIG: any }
 }
 const {uid} = window.CONFIG
```

2 使用类型断言

```js
const { apk } = (window as any).CONFIG
```


#### 泛型

```js
import * as React from 'react'

class App extends React.Component<props, state>{
    -- do something --
}
```

#### 定义

> 泛型是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性

比如写一个根据指定内容填充到指定长度的数组生成函数 getAry

```js
function getAry (len: number, cont: string | number): string[] | number[] {
  return new Array(len).fill(cont)
}
```

参数

| 参数 | 含义 | 类型 |
| --- | --- | --- |
| len | 指定长度 | number |
| cont | 指定内容 | string 或 number （使用联合类型处理）|

由于不确定参数cont的输入类型，所以返回值使用了联合类型处理

这种需要 返回值类型和输入参数类型保持一致的情况，可以使用泛型T来处理

泛型是类型变量，一种用来表示类型的特殊变量

* 使用方式

在函数名后添加<>

```
function createFn<T>
function createFn<string> // 明确指定 T 是 string 类型的
```

其实泛型函数的类型与非泛型函数的类型没什么不同，只是多了一个类型参数在最前面

* 可以指定默认类型

TypeScript 2.3 以后，我们可以为泛型中的类型参数指定默认类型。当使用泛型时没有在代码中直接指定类型参数，从实际值参数中也无法推测出时，这个默认类型就会起作用

```js
function beConfusion<T = boolean> (name: string, isDone: boolean) {...}
```

#### 泛型接口

将上面那个函数使用接口来定义

```js
interface dealFn {
  <T>(len: number, cont: T): T[]    
}
let getAry: dealFn
getAry = function <T> (len: number, cont: T): T[] {
 --do something--    
}
```

#### 泛型类

泛型类使用<>括起泛型类型，跟在类名后面

```js
class DealAry<T> {
  value: T
  constructor (value: T) {
    this.value = value
  }
  deal () {
    console.log(this.value)
  }
}
```
#### other 

* 其余的一些泛型变量

虽然可以自己定义泛型变量结构，但是一般会使用已定义好的泛型

Promise<T>

```js
async taobaoGoods =  function (ids: number[]): Promise<string> {
  return new Promise <string>((reslove, reject) => {
    try{
      reslove('success')
    } catch {
      reject('failture')
    } 
  }) 
}
```

* 可以有多个泛型变量

```js
class 
```

#### React.component 类的泛型变量

打开 node_modules/@types/react 可以看到 component 类

![component](https://user-gold-cdn.xitu.io/2018/9/28/1661f1b1d5cf6f97?w=848&h=425&f=png&s=355775)

简单概括就是

```js
class Component<P = {} , S = {} > {
  -- other --
  readonly props: Readonly<{ children?: ReactNode }> & Readonly<P>
  state: Readonly<S>
  -- other --
}
```

可以看到 props 和 state 两个对象都是只读的

所以我们在写React组件的时候，要调整为

```js
interface BannerProps {
  showUpdate(): void,
  clickOnce: (type: string, id: number) => void
}

interface BannerState {
  once: boolean
}

export default class Banner extends React.Component<BannerProps, BannerState> {
  --do something--
} 

```

### 类型断言

获取一个元素的某个属性

```react
componentDidMount () {
  new Swiper(findDomNode(this), Object.assign({}, options, {
    onInit: () => {...}  
  }))    
}
```

ts提示有错误



> 类型断言（Type Assertion）可以用来手动指定一个值的类型

```js
<类型>值 或者 值 as 类型
```

** 在jsx中必须使用 第二种方式去处理  **

在不确定类型的时候就访问其中一个类型的属性或方法，可以使用类型断言

```js
function getType (arg: string | boolean): boolean {
  if ( arg.length ) return true
  return false
}
```
这种其实会报错的，如果输入是布尔值，则没有length

借助类型断言调整一下

```js
function getType (arg: string | boolean): boolean {
  if ( (arg as string).length  ) return true
  //  if ( (<string>arg).length ) return true
  return false
}
```

断言成一个联合类型中不存在的类型是不允许的

```js
 if ( (arg as array).length  ) return true // 报错
```

所以上面组件里 需要调整为

```js
componentDidMount () {
  new Swiper(findDomNode(this), (Object as any).assign({}, options, {
    onInit: () => {...}  
  }))    
}
```

### 掉坑记录

#### 获取DOM的style属性

```js
componentDidMount () {
    const target = document.body.children 
    const cont = target[0].style.height
}
```
需要调整为

```js
componentDidMount () {
    const target = document.body.children 
    const cont = target[0]['style'].height
}
```

#### ::写法

```js
<Banner once={::this.update} />
```
这个主要是借助了爸bable 的  transform-function-bind 

ts不支持这种写法 必须要调整为

```js
<Banner once={() => this.update} />
```