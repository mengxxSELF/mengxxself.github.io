---
title: 读书笔记-js高程
date: 2017-09-01 19:12:30
categories: 读书笔记
tags: [阅读, 学习, 对象, 原型]
---

面向对象[Obecjt-Oriented   简称OO]语言有一个标志，那就是【类】的概念，通过类可以创建多个具有任意属性和方法的对象。但是JS中其实是没有

类的概念的，所以js中的对象与其他语言中的类不同。

简单来说，js中的对象是一组无序的名值对，而其中的值是对象或者函数。

### 对象

<!--more-->

###  创建对象

创建一个对象，可以通过new一个实例或者使用对象字面量的方式

```javascript
var person=new Object();
person.name='tom';
// 或者使用对象字面量
var person={
  name='tom',age=20
}
```

虽然以上两个方式都能创建对象，但是如果通过这些方式创建大量对象，会产生大量冗余代码。为此开始使用别的方式来创建对象。

#### 工厂模式

```javascript
function createP(name){
  var per=new Object();
  per.name=name;
  return per;
}
```

工厂模式就是一个函数的封装

#### 构造函数

通过创建自定义构造函数，定义对象类型的属性和方法。

```javascript
function Per(name){
  this.name=name;
}
```

构造函数相对于工厂模式，并没有显示创建对象，直接将方法属性赋值this，无return语句。就相当于系统自动进行了 var  和return 的过程。

按照惯例，构造函数的首字母大写。

要创建一个新实例，必须通过new操作符。这种方式实际会经历以下过程

创建一个新对象 > 将构造函数作用域赋值给新对象 所以this为当前实例 > 执行构造函数中代码 即为新对象添加属性方法 > 返回新对象

#### 原型模式

我们创建的每一个函数都有一个prototype属性 这其实是一个指针 指向创建出当前实例的那个类的原型对象

使用原型对象的好处是，在原型对象上添加的属性和方法是所有实例都可以共享的。

```javascript
function Person();
Person.prototype.say=function (){ console.log('hello') }
```

通过这个方式，以后new出来的实例都可以使用say方法了

### 关于类 原型 实例之间的关系

构造函数也就是类，自带一个prototype属性，这是类的原型对象。

** 每一个原型对象自带一个constructor的属性 指向当前类 **

```javascript
Person.prototype.constructor=Person;
```

当通过构造函数new出一个实例后，** 此实例会自带一个__proto__属性，指向当前实例所属类的原型 **

```javascript
person1.__proto__ = Person.prototype;
```

在ES6中 新增了一个Object.getPrototypeOf() 方法，可以返回 实例的__proto__的值

```javascript
Object.getPrototypeOf(person1) == Person.prototype  // true
```

所以在ES6中 可以使用Object.getPrototypeOf() 获取实例的原型对象

实例在查找某个属性或方法时，会先找私有属性，然后向上查找原型对象 一直最后找到基类Object为止

【虽然可以通过实例访问保存在原型中的值  但是不能通过实例重写原型中的属性或者方法】

```javascript
function Person(){}
Person.prototype.name='tom';
var per1 = new Person();
var per2 = new Person();
per1.name='jack';
console.log(per1.name);// jack
console.log(per2.name);// tom
```

以上例子表示，在实例上添加一个同名属性，** 会阻断向原型上的查找，**  但是不会覆盖原型中属性。

【即使将此同名属性值设为null    也只会在实例中设置该属性 而不会恢复指向原型的链接 不过可以使用delete操作符完全删除实例属性 从而重新访问原型中的属性】

### 属性判断

4.1 hasOwnPrototype  判断私有属性

使用此方法可以判断一个属性是存在于实例中还是原型中   若给定属性存在于实例中 返回true

4.2 in 操作符

无论是私有属性还是共有属性 都可以使用in

【结合两者 写一个判断是公有属性的判断】

```javascript
function common(object,attr){
  return (attr in object) && !object.hasOwPprototype(attr);
}
```
4.3 isPropertyOf

```js
obj1.isPropertyOf(obj2)
```

判断 前者是不是在后者的原型链上

4.4 for in

循环自定义属性,公有加私有

### 数组

数组是值的有序集合。其中每一个值是一个元素。

1 稀疏数组

稀疏数组就是从0开始的不连续索引的数组。如果数组是稀疏的，length属性值大于元素的个数。

注意，当数组直接量中省略值时，不属于稀疏数组。省略的元素在数组中是存在的，其值为undefined。这个数组元素不存在是不同的。

```js

var ary1 = [,,]; // 元素是3个undefined 
var ary2 = new Array(3); // 元素为空 但是数组长度是3
```

2 数组长度

ary.length 数组长度可能小于数组元素个数 ，因为有稀疏数组情况存在

3 数组元素的删除 添加

常用删除方法  pop  shift  常用添加方法  push  unshift

注意，还可以使用delete操作符删除数组元素，但是使用此方法，不会改变数组长度，也不会发生数组塌陷，而是变为稀疏数组

其余删除方式： 使用splice   或者 改变Length

4 数组遍历

1 for循环

在稀疏数组中，有元素不存在的情况，假设不想处理不存在元素

```js
for(var i=0;i<ary.length;i++){
  if(!( ary[i] in ary) ) continue;
  - somethings--
}
```

2 forEach

不兼容

5 数组方法

1 join

2 reverse

3 sort

4 concat

5 slice

6 splice

7 push  pop

8 shift unshift

9 toString

6 新增的数组方法

1 forEach

2 map

3 filter  过滤

4 every some

5 reduce

6 indexOf



7 数组类型

在ES6中，可以使用Array.isArray() 判断是否为数组

8 关于类数组对象

Js数组 有一些特性 是其他对象没有的

length
设置length值可以截断数组
从Array.prototype 继承一些有用的方法
其类属性为‘Array’
所以一般类数组想要使用数组中方法，要先将其转为数组。以arguments为例

```js
 var ary = Array.prototype.slice.call(arguments)
```

这样arguments就可以使用数组中方法了

或者使用

```js
 arguments.__proto__ = Array.prototype
```

### try catch

try catch 是js异常捕获的一种方式。不仅可以用在普通JS也可以用在node环境中。

```js
try{
  可能导致异常的代码
}catch (e){
  异常发生时如何处理
}
```

在try中的代码块发生错误时，会执行catch 中代码块

catch中会接收到一个错误对象 e。此错误对象包含的信息因为浏览器的不同而不同，但是所有的浏览器

都包含一个message属性

所以可以在catch错误中打印错误信息

```js
try{
  // 可能导致异常的代码
}catch (e){
  console.log(e.message)
}
```

关于finally字句问题

finally中代码是无论错误与否都会执行

例子

```js
try{
   return 'success'
}catch (e){
   return 'error'
} finally {
  return 'guess'
}
```

以上代码输出的是 'guess'

如果提供了finally字句，则catch字句就成了可选的。


### window对象

window对象是浏览器的一个实例。

#### 浏览器中的全局对象

window是浏览器中的全局对象。【node中全局对象是global】

因此在全局定义的变量后者方法都是属于window对象的。

但是这个直接写在window上还是有点差别。

window.xxx  可以通过 delete 操作符 删除 但是直接写的不可以

直接写的属性和方法相当于其[[configurable]] 为false，所以不能通过delete操作符删除

#### 属性

1 window.name 输出本窗口包含的框架名字

2 window.screenTop 输出窗口位置信息 [有兼容问题]

3 window.innerHeight 窗口大小  [有兼容问题]

一般获取当前窗口大小使用的是

```js
document.documentElement.clientWidth||document.body.clientWidth
```

#### 方法 [常用的几个]

1 window.open() 打开新页面

2 定时器

setTimeout  setInterval

2 JSON

3 alert()

是提示 不具有确认以及取消功能

4 confirm()

确认或者取消

会返回一个布尔值。为true，表示确认，为false 表示取消

5 prompt()

比confirm 多一个Inout输入框  用户可以填写一些信息   返回的是输入框的值

6 window.resize

####  事件 [常用的几个]

1 window.onscroll

2 window.inhashchange

### ajax

Ajax 技术核心是XMLHttpRequest对象  能够以异步的方式从服务器获取数据 意味着不用刷新页面 就可以进行数据更新

#### XHR 的用法

ajax需要通过四步

```js
var xhr = new XMLHttpRequest();
xhr.open(method,url,async);
xhr.onreadystatechange= function () {};
xhr.send();
```
在调用请求发送后，请求被发送到服务器。

在接受到服务器的响应后 响应数据会自动填充到XHR属性

#### XHR 属性

xhr,responseText   作为响应主体被返回的文本
xhr.responseXML   如果响应内容类型是 ‘text/xml’ 或者 ‘application/xml’  这个属性中将保存数据的XML DOM 文档
xhr.status  HTTP状态码
xhr.statusText  HTTP状态的说明

注意，无论内容类型是什么，响应主体内容都会保存在responseText 属性中 ，但是在对于非XML数据 responseXML 属性为 null

关于 readyState 属性  该属性表示请求/响应过程的当前活动阶段 其属性值有 0 -4 个状态

状态 0  未初始化  尚未调用open()
状态1  启动 已经调用 open()  但未调用send()
状态2  发送  调用send()  接受到响应头
状态3  接受  接受响应体
状态4  接受完毕   接受完毕响应体
通过 onreadystatechange 事件 可以监听到状态的改变

```js
xhr.onreadystatechange= function () {
   if(xhr.readystate==4){......}
};
```
注意这个函数里面没有用到this 主要是因为onreadystatechange 事件处理程序的作用域问题 如果是this对象 在有的浏览器会导致函数执行失败或者导致错误发生

在接受到响应之前还可以通过调用abort() 方法来取消请求

调用这个方法后 XHR 会停止触发事件 而且不允许访问任何与响应有关的对象属性 一般在终止请求之后 还应该对XHR对象进行解应用操作

就是将其设置为null 这样浏览器在空闲的时候 会对其进行内存销毁

#### HTTP 头部信息

每个HTTP请求和响应会带有响应头部信息 XHR对象提供了操作这两种头部[请求头和响应头] 的方法

设置请求头  xhr.setRequestHeader()
获取请求头  xhr.getRequestHeader()

注意，有的浏览器允许重写默认头部信息 有的则禁止重写

* get请求

GET是最常见的请求类型 常用于向服务器查询信息  可以将查询字符串参数追加到URL末尾 用于将信息发送给服务器

get请求经常在查询字符串格式那里出问题

* POST请求

POST请求通过用于向服务器发送数据 数据在请求体中  POST请求体可以包含非常多的数据 而且格式不限

#### 超时设定

XHR有一个timeout属性 表示请求在等待响应多少毫秒后就终止  在给其设置一个数值后 如果规定时间内浏览器未收到响应 则触发timeout事件

```js
var xhr = new XMLHttpRequest();
xhr.open(method,url,async);
xhr.onreadystatechange= function () {
    if(xhr.readyState==4) {
        try{
            if(xhr.status==200){
                console.log(xhr.responseText);
            }
        }catch (e){
            console.log('time out');
        }
    }
};
xhr.timeout=2000;
xhr.ontimeout= function () {
    console.log('Request timeout');
}
xhr.send();
```

设置超时时间为2000毫秒 如果请求在此时间未返回 则自动终止请求 而请求终止时 会触发ontimeout事件

注意，在请求终止的时候 有可能readyState已经是4了 但是请求终止后是无法访问status属性 所以使用了一个异常捕获处理

页面中通过效果来吸引用户注意是很常用的，会显著增强页面可用性。通过jQuery，可以轻松为页面添加简单的视觉效果，以及动画。
