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
