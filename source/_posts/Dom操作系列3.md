---
title: Dom操作系列3
date: 2017-02-06 10:09:46
categories: javascript基础
tags: [笔记迁移, DOM]
---

> 笔记迁移系列，由于原始笔记所在网站即将下线，现将笔记全部迁移，并对原有笔记进行修正或添加

<!-- More -->

####   创建元素

```js
var ele = document.createElement('div')
document.body.appendChild(ele)
```
#### 插入元素

2.1  放在元素末尾   appendChild

```js
document.body.appendChild(ele)
```

2.2  放在旧元素前面   insertBefore


```js
document.body.insertBefore(ele ,oldEle)
oldEle.parentNode.insertBefore(ele,oldEle)
```

父级元素.insertBefore(新元素，旧元素)

#### 克隆元素 cloneNode

```js
oNode.cloneNode()
```

该方法有一个参数，可写可不写：true或者false

如果是true，表示全部克隆，包含所有子元素。

如果是false，表示只克隆目标元素自己

#### 删除元素 removeChild

```js
myBox.removeChild(wrongNode)
```

#### 替换元素  replaceChild

父级元素.replaceChild(新元素，旧元素)

```js
myBox.replaceChild(newNode,oldNode)
```

