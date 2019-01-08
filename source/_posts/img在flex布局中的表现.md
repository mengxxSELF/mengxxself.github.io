---
title: img在flex布局中的表现
date: 2018-12-21 21:28:35
categories: css
tags: [css3, flex]
---

![flex](https://user-gold-cdn.xitu.io/2018/12/21/167d0f7313c08452?w=1112&h=440&f=png&s=125964)

> 奇奇怪怪

<!-- More -->

最近在进行css布局调试的时候，发现一些flex布局的有趣现象

html 结构如下

```html
<div>
  <p> something </p>
  <img src="xxx" alt="">
</div>
```

css 为flex 布局

```css
div {
  background: #ccc;
  width: 400px;
  padding: 20px;
  display: flex;
}
p {
  padding: 10px;
  background: #fff; 
}
```

效果为

![page](https://user-gold-cdn.xitu.io/2018/12/21/167d040d4bfee932?w=1216&h=1008&f=png&s=1910237)

灰色块为div部分

这里的图片可以看到是超出了div盒子

我的预期是图片可以被缩小，不会超出盒子

然后不用图片，换成普通元素

```html
<div>
  <p> something </p>
  <section> xxxx </section>
</div>  
```

```css
section {
  padding: 10px;
  background: #000;
  margin-left: 10px;
  color: #fff;
}
```

![ele](https://user-gold-cdn.xitu.io/2018/12/21/167d0481ffa27437?w=1246&h=516&f=png&s=587504)

可以看到 右侧的section并没有发生像图片那种超出盒子的现象

而且即使给 右侧元素添加一个超大的width值，其样式并不会变化

也就是对于图片没有做像文字这种折行处理，也没有按照预想的进行缩小处理

要想实现这种图片占满剩余空间的效果，需要给图片添加指定宽度

```css
img { width: 100%; }
```

为什么出现这种问题呢？

* 对于有默认大小的元素

图片本身是有大小的，不像 div p span 这些标签。那用input 尝试一下

![input](https://user-gold-cdn.xitu.io/2018/12/21/167d0d4c9a16d445?w=844&h=530&f=png&s=393204)

和图片的表现一样

* width && min-width

一个元素 同时设定width 和 min-width 值 其真正展示的效果是按照这里面的最大值

```html
<div class='width'> width or min-width </div>
```
指定这个元素的 min-width 高于 width

```css
.width { width: 100px; min-width: 300px; color : #fff; }
```

最后呈现出来的元素是 300px 的效果

![300](https://user-gold-cdn.xitu.io/2018/12/21/167d0e1dbcf96eea?w=910&h=452&f=png&s=293021)


图片本身有宽度，就相当于有一个 min-width 值呢

来模拟一个 div

```html
<div class='min'>
  <p> a b c </p>
</div>
```

子元素 同时设置 width 和 min-width

```js
div { display: flex; }
.basis p {
  width: 100%; 
  min-width: 300px;
}
```

![min](https://user-gold-cdn.xitu.io/2018/12/21/167d0f1124e38309?w=914&h=350&f=png&s=247627)

也是超出了盒子大小


#### 参考文章

* [深入理解 flex 布局以及计算](https://www.w3cplus.com/css3/flexbox-layout-and-calculation.html)

