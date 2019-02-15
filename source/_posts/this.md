---
title: this
date: 2017-11-06 11:13:20
categories: javascript基础
tags: [笔记迁移]
---

> 笔记迁移系列，由于原始笔记所在网站即将下线，现将笔记全部迁移，并对原有笔记进行修正或添加

<!-- More -->

#### this

1.1 当元素身上的事件被触发时执行的函数，该函数中this指向当前元素

1.2 函数被调用时，看前面是否有点。有点的this就指向点前面的那个，没有就是window

1.3 自执行函数的this 为 window

#### 应用

2.1  知识点： 预解释  自执行函数this  函数返回值

```js
var name='who';
var age=500;
name=(function(name,age){
    arguments[0]='who';
    age=age||this.age;
    console.log(name,age); 
})(name);
console.log(name,age)  
```

2.2

```js
var num = 10;
var obj = {
    num: 20,
    fn: (function (num) {
        this.num *= 2;
        num += 10;
        return function () {
            this.num *= 3;
            num += 1;
            console.log(num);
        }
    })(num)
};
var fn = obj.fn;
fn();
obj.fn();
console.log(window.num, obj.num);
```


