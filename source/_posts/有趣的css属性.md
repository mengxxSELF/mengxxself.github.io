---
title: 有趣的css属性
date: 2017-10-26 18:00:26
categories: css
tags: css3
---

<!-- more -->

### 开启惯性滑动

```
-webkit-overflow-scrolling:touch
```

但是这个可能会引起一些别的问题,参见文章[webkit-overflow-scrolling](https://www.cnblogs.com/xiahj/p/8036419.html)

### 图片变为黑白

```css
img.desaturate {
  filter: grayscale(100%);
  -webkit-filter: grayscale(100%);
  -moz-filter: grayscale(100%);
  -ms-filter: grayscale(100%);
  -o-filter: grayscale(100%);
}
```
<!--more-->

主要应用的是 [css3-filter](http://www.w3cplus.com/css3/ten-effects-with-css3-filter)

它可以对图片进行类似于ps的简单效果

### box-sizing

可以设定图片实际宽度

### 文本渐变

```html
<div class="element">
  <p>Lorem ipsum dolor sit … amet.</p>
</div>
```

```css
-webkit-mask-image: -webkit-gradient(linear, left top, left bottom, from(rgba(0,0,0,0)), color-stop(50%, rgba(0,0,0,1)), to(rgba(0,0,0,0)));
```

利用了CSS的遮罩进行处理

也可以使用一个特定的图片进行展示

```css
 -webkit-mask-image: url(mouse.png)
```


###  calc

```css
width: calc(100% - 100px)
```

可以利用calc 实现css中的函数效果

### mask -- css遮罩

```css
&__mask {
  color: #fff;
  mask-image: url('../../img/product.png');
  mask-repeat: no-repeat;
  mask-size: 100% 100%;
}
```
![mask](/img/css/mask.png)
![mask](/img/css/product.png)


[css遮罩](http://www.w3cplus.com/css3/css-masking.html)

### @supports

类似媒体查询 可以将@supports 视为 特性查询

用来查询浏览器是否支持某个CSS3的特性，这个功能除了IE之外，已被其他浏览器所支持

```css
@supports ( display: flex ) {
    .foo { display: flex; }
}

```
