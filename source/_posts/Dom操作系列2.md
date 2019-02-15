---
title: Dom操作系列2
date: 2017-02-05 17:10:53
categories: javascript基础
tags: [笔记迁移, DOM]
---

> 笔记迁移系列，由于原始笔记所在网站即将下线，现将笔记全部迁移，并对原有笔记进行修正或添加

<!-- More -->

#### 获取某一个元素的所有子元素节点

1.1  使用 obj.children

1.2  第一种方法不兼容IE,自己写一个

```js
function node(obj){
    var nodes = obj.childNodes ;
    var end =[];
    for(var i=0;i<nodes.length;i++){
        var item = nodes[i];
        if(item.nodeType==1){
            end.push(item)
        }
    }
    return end;
}
console.log(node(document.querySelector('#box')))
```

#### 获取某一个元素的上一个兄弟元素节点

```js
function pre(obj){
    var preNode = obj.previousSibling;
    while(preNode&&preNode.nodeType!=1){
        preNode = preNode.previousSibling;
    }
    return preNode;
}
console.log(pre(document.querySelector('ul')))
```

#### attribute

```js
obj.setAttribute(属性名,属性值)
obj.getAttribute(属性名)
obj.removeAttribute(属性名)
```

注意：setAttribute 不可设置class

box.setAttribute('class','showing')  是无效的

可以通过点加属性名来设置一个属性，这两种方法，怎么设置的就怎么获取。

注意，增加class属性只能通过点的形式

obj.seAttribute('class')无效