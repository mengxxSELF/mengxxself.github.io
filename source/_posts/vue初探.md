---
title: vue初探
date: 2018-12-28 17:59:55
tags: [vue]
---

![vue](https://user-gold-cdn.xitu.io/2018/12/28/167f487da679727d?w=1060&h=392&f=png&s=62416)

> vue 初探

<!-- More -->

### 安装

vue的文件编译差不多也是react那一套 大概在loader会有不同 因为重点在熟悉Vue 而不是webpack 没有做单独的配置 直接使用vue-cli 生成

```js
cnpm i vue-cli 
```

### router 的使用

```js
import Vue from 'vue';
import Router from 'vue-router';
Vue.use(Router); //挂载属性
//创建路由对象并配置路由规则
let router = new Router({
    routes: [
        //定义路由对象
        { path: '/', component: Tabs },
        { path: '/article', component: Article },
    ]
});


// 在创建 vue实例的时候 要传入router
new Vue({
    el: '#app',
    //让vue知道我们的路由规则
    router: router, //可以简写router
})
```

#### 使用router进行参数传递

```js
<router-link :to="{name:'detail',params:{ id: id, title: title }, query :{author}}">detail</router-link>
```

子页面做解析

```js
  <h3> {{$route.params.title}} </h3>
```

有一个奇怪的问题，就是进入子页面刷新，子页面数据就没有了，

为了确定这个现象，在组件的 mounted 生命周期 log 一下 参数

```js
  mounted: function () {
    console.log(this.$route.params.title)
  } 
```

初次进入是有的，但是刷新的话的确是没有的。 目前没有看懂为什么会这样

#### 解析router

| 属性 | 含义 |
| ----------------- | ----------------- |
| $route.path | 对应当前路由的路径 |
| $route.params | key/value 对象 |
| $route.query | 传参对象 |
| $route.hash | 当前路由的 hash 值 (不带 #) |
| $route.fullPath | 完成解析后的 URL，包含查询参数和 hash 的完整路径 |
| $route.matched | 数组格式，包含当前匹配的路径中所包含的所有片段所对应的配置参数对象  |
| $route.name | 当前路径名字 |

#### 404

vue有一个有意思的功能 页面404

直接在路由页面中添加

```js
    {
      path: '*',
      component: Error
    }
```

当用户访问的路径不存在时，就会展示这个组件


### vue 生命周期

vue 几个常用的生命周期函数

![vue](https://user-gold-cdn.xitu.io/2018/12/28/167f48987e3b7b2a?w=516&h=758&f=png&s=86242))

相对于react 来说， vue的生命周期函数要多一些，而且有的看起来，性质差不多，比如 create 和 mounted 无法从字面感受有什么区别

在组件里打几个log看一下

![fn](https://user-gold-cdn.xitu.io/2018/12/28/167f49d880b7e2da?w=716&h=614&f=png&s=91842)


beforecreated：el 和 data 并未初始化 

created:完成了 data 数据的初始化，el没有

beforeMount：完成了 el 和 data 初始化 

mounted ：完成挂载


### 组件

#### 引入子组件

```js
components: [
  Table,
  Model
]
```

#### 组件之间的数据传递

* 子级组件使用父级组件的方法

父级通过 v-on 绑定

```
<Form v-if="isShow" v-on:hideForm="hideModal" ></Form>
```

子级通过 $emit 触发

```js
<el-button @click="$emit('hideForm')">取消</el-button>
```

#### UI库

vue的比较常用的UI库 有[element](http://element-cn.eleme.io/#/zh-CN)  

#### 样式

* 引入第三方css

提供了三种方式

1 入口js文件引入

```js
import 'swiper/dist/css/swiper.css'
```

2 写到 html 模板文件中

3 组件中引入

```js
@import 'swiper/dist/css/swiper.css'
```

