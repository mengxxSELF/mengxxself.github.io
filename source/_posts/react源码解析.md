---
title: react源码解析
date: 2018-12-25 15:22:26
categories: react
tags: [react]
---

![react](https://user-gold-cdn.xitu.io/2018/12/27/167ee4749d7dfa68?w=1380&h=440&f=png&s=207589)

> react 源码分析第一章节

<!-- More -->

写一个简单的react组件

```html
<div className="App">
  react box
</div>
```

我们在babel中看一下，代码将会被编译为

```js
"use strict";

React.createElement(
  "div",
  { className: "App" },
  "react box"
);
```

添加一个子元素

```js
<div className="App">
  react box
  <p className='p' data-age='1123'> i am p </p>
</div>
```

babel的转义结果

```js
"use strict";

React.createElement(
  "div",
  { className: "App" },
  "react box",
  React.createElement(
    "p",
    { className: 'p', 'data-age': '1123' },
    " i am p "
  )
);
```

我们在组件中log一下这个结果是什么

```js
import React from 'react'

const end = React.createElement(
  "div",
  { className: "App" },
  "react box",
  React.createElement(
    "p",
    { className: 'p', 'data-age': '1123' },
    " i am p "
  )
);
console.log(end, 'end')

```

![log](https://user-gold-cdn.xitu.io/2018/12/25/167e476a9210d239?w=854&h=370&f=png&s=180957)

简单概括为

```js
props: {
  children: [
    子元素A, 子元素B
  ],
  className: '本元素的类名',
  本元素的其他属性名: '本元素对应的其他属性值',
},
type: '本元素的标签名'
```

这个 ReactElement 类型的js对象就是React组件了

也就是 react 将这种对象最后 render 到html文件中的

看下流程图

## createElement 方法

看下react 的 createElement 源码

源码

```js
function createElement(type, config, children) {
  ....
  return ReactElement(...)
}

function ReactElement(...) {
  var element = {
    $$typeof: REACT_ELEMENT_TYPE,

    type: type,
    key: key,
    ref: ref,
    props: props,

    _owner: owner
  }
  ...
  return element
}

```


使用方式

```js
// React.createElement(
//   type,
//   [props],
//   [...children]
// )
React.createElement(
  div,
  {className: 'ele'},
  null
)
```

* 实现 createElement 方法

```js
class React {
  constructor (type, props) {
    this.type = type
    this.props = props
  }
}

class createElement {
  constructor(type, props, ...content) {
    // 因为 props 和 content 都会变为 React 对象的 props 属性值
    let allProps = Object.assign({}, props, { children: content})
    return new React(type, allProps)
  }
}

let ele = new createElement('div', { className: 'ele' }, 'i am div', new createElement('p', {}, 'i am p'))
console.log(ele)
```

