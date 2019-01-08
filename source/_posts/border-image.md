---
title: border-image
date: 2017-12-05 11:03:44
categories: css
tags: [css3, border-iamge]
---

有处理到一个CSS问题 内容列表区域展示部分 边框是图片形式的~ css画不出来~~

<!--more-->

使用的border-iamge处理边框图片的问题

最终效果如下~

![border](/img/borderImage/border1.png)

关于border-image这里不多做解释  可参考文章

* [张鑫旭css3-border-image](http://www.zhangxinxu.com/wordpress/2010/01/css3-border-image/)

* [CSS魔法堂：重拾Border之——图片作边框](https://www.cnblogs.com/fsjohnhuang/p/5449717.html)

这里只几点使用中注意的问题

#### round 与 repeat

您从万科地产买了个99.5m*99.5m的毛坯房，地面要贴瓷砖，都是1m*1m的正方形瓷砖。
  如果是“平铺”，对不起，这1m边长的瓷砖不行，要处理！怎么处理法？很简单，每个瓷砖压成0.995m*0.995m的，这样就可以了，所以，平铺就是以完整的单元铺满整个区域。

  如果是重复，就直接把这1m*1m的瓷砖从一个角落一个一个的放置，放到头放不下了怎么办？直接把瓷砖从中间“咔”掉，于是最后会在房子的边角看到很多半截的瓷砖

 --- 来自[张鑫旭css3-border-image](http://www.zhangxinxu.com/wordpress/2010/01/css3-border-image/)


#### border-iamge 与 border-radius 冲突

项目设计稿中 边框是有圆角的 因此我又添加了圆角属性


```javascript
background: green;
border: 5px solid #fff;
border-image-source:url('../../img/border2.png');
/* 以九宫格形式做切片 */
border-image-slice:20;
border-image-repeat: round;

/* 添加圆角属性 */
border-radius: 20px;
```

但是页面中并没有出现圆角效果

问题参见 [css3 怎么实现border-image圆角的问题](https://segmentfault.com/q/1010000006613100)

核心在于 当使用border-iamge时候 会压制 border的样式  如果border-iamge 出错  就会展示border的样式

然后测试一下  写一个错误的URL图片路径

![border](/img/borderImage/border2.png)

当border-image 失效的时候  border style 就可以看到了

[MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-image)

> 使用 border-image 时，其将会替换掉 border-style 属性所设置的边框样式。虽然规范要求使用 border-image 时边框样式必须存在，但一些浏览器可能没有实现这一点。

> 特别注意，若 border-image-source（此值可用border-image-source或border-image简写设置) 的值为 none 或者图片不能显示，则将应用 border-style。

其实我觉得 border-style 其实是实现了的 但是被border-image 的效果遮挡住了 我们可以拿一个透明图片来测试一下




那么怎么实现圆角效果呢？

* 利用九宫格原理  将资源图片设置为圆角

开始时候我们的资源图片是这个样子的

![border](/img/borderImage/border4.png)

是一个60 x 60的图片源

这经过九宫格处理之后  每一个格子就是 20 x 20的

那么此时分布在元素border的四个角上的 对应着 九宫格的四个角

所以我们只要把资源图片的四个角位置设置为有弧度的  那么经过九宫格处理后 页面就可以看到圆角效果了

所以将资源图片更改为

![border](/img/borderImage/border3.png)

然后刷新页面

![border](/img/borderImage/border5.png)

这里看到有一个圆角效果了 但是后面的多余元素颜色也漏出来了 所以再添加一个 border-radius 就可以了

* 盒子的overflow

将页面结构更改为

```html
<section>
  <div>  国际圣诞节 </div>
</section>
```

然后里面div的样式不要变动

在section中做处理

```css3
border-radius: 10px;
overflow: hidden;
```

![border](/img/borderImage/border6.png)

这样也可以走到罗马
