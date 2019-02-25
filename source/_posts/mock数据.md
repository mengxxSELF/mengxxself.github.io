---
title: mock数据
date: 2018-12-26 10:43:02
categories: 第三方插件使用
tags: [node, mockjs]
---

![mock](https://user-gold-cdn.xitu.io/2018/12/27/167ee440eb91144c?w=1160&h=634&f=png&s=383086)

> 前端可通过 [Mockjs](http://mockjs.com/) 模拟数据 独立与后端开发 

<!-- More -->

### 安装

```js
cnpm i mockjs -D
import mock from 'mockjs'
```

### 语法

Mock.mock(template)

mock 接受一个模板，将根据定义的模板生成对应格式的数据

#### 数据模板

template 可以是一个数据对象 -- 属性名、生成规则、属性值

```js
'name|rule': value
```

比如生成一个长度为 3 的数组

```js
let data = Mock.mock({
  'id|3': [1]
})
// { id: [ 1, 1, 1 ] }
```

* 'name|min-max': value

| value格式 | 作用                                                         |
| --------- | ------------------------------------------------------------ |
| string    | 重复string生成结果，重复次数区间[min, max]                   |
| number    | 生成整数, 区间[min, max]。 也就是这里的 value 并没有什么意义 |
| object    | 随机选取 object 中 min 到 max 个属性 生成新对象              |
| array     | 重复属性值 array 生成一个新数组，重复次数区间[min, max]      |

```js
  'name|1-3': '中文' // name: '中文中文中文' 
  'id|1-3': 18 // id: 3 
```

* 'name|count': values

| value格式 | 作用                                                    |
| --------- | ------------------------------------------------------- |
| string    | 重复string生成结果，重复次数 count                      |
| object    | 从对象 object 中随机选取 count 个属性                   |
| array     | 重复属性值 array 生成一个新数组，重复次数区间[min, max] |


```js
  'users|2': {
    name: 'tome',
    age: 20,
    habit: 'listen'
  }
  //  users: { age: 20, habit: 'listen' } 
  //  users: { name: 'tome', habit: 'listen' }
```

* other

'name|1': boolean 

随机生成一个布尔值, true/false 的概率各为50%

#### 数据占位符 @占位符

template 可以是一个占位符

Mock.Random 是一个工具类，用于生成各种随机数据。Mock.Random 的方法在数据模板中称为“占位符”，引用格式为 @占位符(参数 [, 参数])

这里简单列举几个常用的占位符

比如生成 长度为5 的字符 

```js
Mock.mock('@string(5)') 
Mock.mock('@csentence(5)') // 中文
```

生成 图片

```js
Random.image('200x100', '#50B347', '#FFF', 'Mock.js')
```

[具体点击查看文档](http://mockjs.com/examples.html#DPD)

#### 应用

获取文章列表数据

比如

![lists](https://user-gold-cdn.xitu.io/2018/12/27/167eea5edf07e162?w=926&h=348&f=png&s=81273)

主要的参数为 id img title

```js
let getArticles = (tabId = 1) => {
  let data = Mock.mock({
    'articles|10': [
      {
        'id|+1': 0,
        img: Random.image('200x100', '#f1f1f1', '#FFF', 'ARTICLE BANNER'),
        title: tabId + '@csentence(3, 10)'
      }
    ]
  })
  return data
}
```

[点击git查看](https://github.com/mengxxSELF/vue-shop/blob/master/Mock/index.js)

### 参考文章

* [Mock.js - 模拟数据生成器](https://juejin.im/entry/564b293c60b259caed3e7e2c)
