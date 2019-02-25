---
title: 发布订阅模式与redux
date: 2019-02-22 15:14:41
categories: 开发者模式
tags: [redux]
---

> redux 

<!-- More -->

### 发布订阅模式

发布订阅模式比较简单

```js
class Target {
  constructor () {
    this.list = {}
  }
  // 订阅
  listen (type, fn) {
    if (this.list[type]) {
      this.list[type].push(fn)
    } 
    this.list[type] = [fn]
  }
  // 发布
  trigger (type, ...arg) {
    this.list[type].forEach(element => {
      element(...arg)
    })
  }
}

const tar = new Target()

tar.listen('sing', (name) => {
  console.log('you are sing', name)
})

tar.trigger('sing', 'xiaohun')
```

### 观察者模式

观察者和发布订阅有点类似

```js
// 被观察者
class Subject {
  constructor () {
    this.data = []
  }

  attch (tar) {
    this.data.push(tar)
  }

  going () {
    this.data.forEach(ele => {
      ele.rang()
    })
  }
}

//  观察者
class Observer {
  constructor (name) {
    this.name = name
  }
  rang () {
    console.log('rang, i am', this.name)
  }
}

const target = new Subject()
const person1 = new Observer('tom')
const person2 = new Observer('tom2')

target.attch(person1)
target.attch(person2)

target.going()
```


### redux 中的观察者

redux中有使用到 观察者模式, 来分析一下他的简单实现

#### 使用

* 创建对象

```js
const store = createStore(reducer)
```

* store.getState()

* store.subscribe()

* store.dispatch()

#### 实现

* createStore 

接收参数 reducer 返回一个对象

```js
function createStore (reducer) {
  -- something --
  return { getState, subscribe, dispatch }
}
```
* getState 

返回 state 

```js
const getState = () => {
  return state
}
```

* subscribe

订阅, 接受函数, 用于在 dispatch的时候执行

```js
const subscribe = (cb) => {
  listens.push(cb)
}
```
* dispatch

分发Action, 参数是 action  1 去执行 reducer -》 更新 state 2 执行之前的订阅数组

```js
const dispatch = (action) => {
  state = reducer(state, action)
  listens.forEach(item => {
    item()
  })
}
```

果然redux 和 react 没有什么关系， 完全可以脱离react使用
