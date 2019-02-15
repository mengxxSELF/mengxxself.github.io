---
title: Dom操作系列1
date: 2017-02-05 11:56:52
categories: javascript基础
tags: [笔记迁移, DOM]
---

> 笔记迁移系列，由于原始笔记所在网站即将下线，现将笔记全部迁移，并对原有笔记进行修正或添加

<!-- More -->

DOM : document object modal 文档对象模型

### 获取DOM元素的方法

1.1  通过元素id

```js
document.getElementById('idName')
```

得到的是id名为idName的DOM元素

假使有重复的ID，那么这种方法获取的是第一个

标准浏览器区分id的大小写，但是IE6,7中不区分大小写

1.2 直接使用id名   idName

此方法可以使用但不推荐

1.3   通过元素标签名

```js
document.getElementsByTagName()
```

得到的是类数组

1.4 通过元素class名

```js
document.getElementsByClassName()
```

得到的是类数组

注意： DOM操作的是类数组中具体元素

1.5 通过name  [只对于form表单而言]

```js
document.getElementsByName()
```

1.6 获取body

```js
document.body()
```

1.7 获取html

```js
document.documentElement()
```


1.7 

```js
document.querySelector() 
```

获取的是第一个元素

支持css选择器  ‘#idName’  '.className' 标签等

1.7 

```js
document.querySelectorAll() 
```

获取的是一组元素

支持css选择器  ‘#idName’  '.className' 标签等

###  获取可视窗口的信息

2.1  宽度 clientWidth  高度 clientHeight

```js
let viewWidth = document.documentElement.clientWidth || document.body.clientWidth
```

###  获取相关节点

节点包括  1 元素节点 2 文本节点[空格 标签  回车] 3 注释节点  4 document节点

nodeType 1 3 8 9

nodeName  大写的元素标签  #Text #comment #document

nodeValue  null  文本内容 注释内容  null

3.1 获取所有子节点 childNodes

```js
document.body.childNodes
```

3.2  获取所有子元素节点   children

```js
document.body.children
```

3.3  获取父级元素节点  parentNode

```js
document.body.parentNode
```

3.4  获取哥哥节点  previousSibling  可能会得到文本节点

```js
document.body.previousSibling
```

3.5  获取弟弟节点  nextSibling  可能会得到文本节点

```js
document.body.nextSibling
```

3.6 第一个子节点  firstChild 可能会得到文本节点

3.7 最后一个子节点  lastChild 可能会得到文本节点







