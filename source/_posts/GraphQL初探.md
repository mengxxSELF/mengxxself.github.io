---
title: GraphQL初探
date: 2019-03-26 14:50:22
tags:
---

### 基本概念

#### 操作类型

gql 有三种操作类型 query、mutation 或 subscription, 用于描述你打算做什么类型的操作

GraphQL 的操作类型可以是 query、mutation 或 subscription，描述客户端希望进行什么样的操作

query 查询：获取数据，比如查找，CRUD 中的 R
mutation 变更：对数据进行变更，比如增加、删除、修改，CRUD 中的 CUD
substription 订阅：当数据发生更改，进行消息推送

#### schema

GraphQL 服务都会定义一套类型，用以描述你可能从那个服务查询到的数据。每当查询到来，服务器就会根据 schema 验证并执行查询

GraphQL schema 中的最基本的组件是对象类型，它就表示你可以从服务上获取到什么类型的对象，以及这个对象有什么字段

比如获取一个用户信息

```js
type Person {
  id: Int!,
  name: String!,
  friends: [String]
}
```

*  Person 是一个普通对象

*  id  name friends 是 Person 类型上的字段, 那么再使用 Person 的时候 只能返回这三个字段值

* String!  这个值这里，添加叹号  表示这个字段是非空的

##### schema 的特殊类型

一个 schema 内有两个特殊类型： Query 与  Mutation

Query 定义了每一个 GraphQL 查询的入口

```js
type Query {
  getName (id: Int) : String
}
```

##### 变量类型

gql 有几种自带的变量类型 

Int String Float Boolean 

ID - -ID 标量类型表示一个唯一标识符，通常用以重新获取对象或者作为缓存中的键

枚举 enum 

```js

enum Teachers {
  MAN,
  WOMAN
}

```

这个就表示 Teachers 的两种类型是 MAN 或者 WOMAN 

##### 接口 Interfaces

接口的存在用于给另外的对象使用 【感觉这个和ts中的接口含义类似】

```js

interface Teachers {
  book: String
}

type LiTeaches implements Teachers {
  book: String,
  school: String
}

```

#### resolver

