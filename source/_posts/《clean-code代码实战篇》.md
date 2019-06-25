---
title: 《clean-code代码实战篇》
date: 2019-05-31 11:09:59
categories: 读书笔记
tags: [阅读]
---


<head> 
    <script defer src="https://use.fontawesome.com/releases/v5.0.13/js/all.js"></script> 
    <script defer src="https://use.fontawesome.com/releases/v5.0.13/js/v4-shims.js"></script> 
</head> 
<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.0.13/css/all.css">


> <i class='fas fa-quote-left fa-3x fa-pull-left'></i>  Later equals never  -- 《Clean code》

分类     | 内容
-------- | ---
变量 | 命名
函数 | 单一职责、拆分、参数、其他
其他 | 注释、团队风格

<!-- More -->

### 变量

变量的命名体现了代码的表现力，通过清晰的命名，就能展示代码本身的功能

1.1 尽量具有描述意义的名称，遵循一般规律，比如类名大多是名词，方法大多是动词开头，例如获取 get  变更 update 等等

```js
const get_user_base_info = function * (){...}
const get_anchor_base_data = function * (){...}
```

1.2 要名副其实的名称，比如用户列表，要用userList， 不是 listData 

1.3 要做有意义的区分， 比如 不要同时出现，user， userdata, userinfo 这三个看上去其实并没有什么区别

```js
const get_user_base_info = function * (){...}
const get_user_info = function * (){...}
```

1.4 使用可读的变量名称， 有人将单词的首字母拼接在一起，那基本除了作者之外就没人读懂

```js
const yyyymmdstr = moment().format('YYYY/MM/DD')
```

1.5 不添加不必要的上下文  比如 book 对象有一个 color 属性 那不要写为 book.bookColor 

```js
Book.Color 
```

1.6 使用可搜索的名称

```js
const isExist = anchorUids.includes(3)
```

### 函数

#### 短小

函数第一规则是短小，拆分为短小的函数有利于理解、复用，管理维护起来相对简单

一般函数拆分可以按照两种维度来做

* 横向 — 同类型处理，比如对于表的操作，对于数据的操作，拆分到不同方法中

* 纵向 — 按照业务流程，从接口的参数处理 ，到查询数据库，返回结果


刚好有看到一篇文章实在讨论函数保持多少行是合理的[多长的函数才合理](https://mp.weixin.qq.com/s/sEVG6z0A8MeltVPlGLu6JA) 

其实我感觉这个要看具体的开发场景，没有特定的规范。不过要尽量拆分，保持函数简练，便于管理维护

#### 单一职责

判断函数是否保持单一性

1 这个函数能不能用一句话去解释

2 主观判断一下这个函数有没有可被拆分的必要

before

```js
function* add_record({ ... }) {
  const sql1 = '.....'
  yield insert_users.mysql(sql1)
  const sql2 = '.....'
  yield update_anchor.mysql(sql2)
}
```

after 

```js
function* add_record({ ... }) {
  yield [
    insert_users({ ... }),
    update_anchor({ ... }),
  ]
}
```

#### 参数

1 参数是布尔值

表示这个函数肯定不止做一件事，违反了单一原则，所以最好直接拆分两个函数，doForTrue , doForFalse

```js
if (arg) {
  doForTrue()
} else {
  doForFalse()
}
```

2 参数超过3个

推荐构建 参数 object  — 过长的参数不易管理

```js
function add_anchor(name, label, age, icon) {
  // ...
}
```

3 有参vs无参

比如我们写的sleep函数，其中的时间是传参还是直接写到函数里面


#### 副作用

如果一个函数做了除接受一个值然后返回一个值或多个值之外的任何事情， 它将会可能就会产生副作用

#### 函数命名

关于命名，上面已经提到了一部分，这里针对于函数的命名再说一句，函数越短小，功能越单一，那么更容易起一个好名字

#### 不要写重复的逻辑

注重代码的拆分封装，不要做重复的事情。其实去看项目，大部分由于重复的处理类似的逻辑变得庞大不好管理

可以参考后台广告上传组件，存在大量功能类似的代码，导致组件代码量非常大，维护起来很困难

### 移除僵尸代码

这种情况在比较久的项目中会有一些，就是被注释了但是没有被删除的代码。

那可能不确定为什么添加注释的人没有删除，是不是会有什么用。不要犹豫，直接删除

不要害怕删除僵尸代码，工具会帮你记得它， 比如组内使用的 gitlib 可以查看提交历史

###  异常

把一段代码用 try/catch 包裹起来  意味着你想到这里可能会出错。 尽量不要写 console.log 而应该
放到合适的位置

server端常见的这个错误，有的时候会直接写 cosnole.log(e) 

```js
function * get_anchor () {
  try {
    ......
  } catche (e) {
    cosnole.log(e) 
  }
}
```

### 注释

由于现在的打包工具已经非常优秀了， 所以还是推荐写注释的

但是要注意以下问题

#### 注释错位， 要注释的变量与变量位置并不紧密

```js

// 注释1 这里的变量 isAnchor 用于判断主播身份

// 中间有几行别的代码 .....

isAnchor

```

#### 变更代码逻辑的时候没有变更注释

```js

// 注释1 需要判断状态是不是 3
if ([2, 6, 8].includes(status)) {
  ...
}

```

#### 带有个人情绪色彩的注释，参见之前B站代码泄漏后的注释


### 团队风格

#### 代码缩进

目前团队风格为2格缩进

#### 驼峰 vs 下划线

新后台统一为驼峰命名法则

#### 隔离

概念隔离，不同的的概念/逻辑 代码实用空行隔离

相关靠近：对于关系紧密的代码，尽量写在一起

### 补充

目前团队使用到是内部开发的eslint相关配置, 基于 [airbnb](https://github.com/airbnb/javascript) 规范

* [基本版](https://www.npmjs.com/package/eslint-config-blued)

* [TS版](https://www.npmjs.com/package/eslint-config-blued-typescript)
  
* [React版](https://www.npmjs.com/package/eslint-config-blued-react)

