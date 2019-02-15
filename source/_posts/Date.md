---
title: Date
date: 2017-02-06 17:35:39
categories: javascript基础
tags: [笔记迁移, Date]
---

> 笔记迁移系列，由于原始笔记所在网站即将下线，现将笔记全部迁移，并对原有笔记进行修正或添加

<!-- More -->

JS中的日期对象Date()

#### 方法

```js
let time= new Date();

console.log(time.getTime()) // 得到现在到1970的毫秒数
console.log(time) // Sun Dec 04 2016 12:41:09 GMT+0800 (中国标准时间)
console.log(time.getFullYear()) 2016
console.log(time.getMonth()) 11[0-11]
console.log(time.getDate()) 4

console.log(time.getDay()) // 星期0-6

console.log(time.getHours())
console.log(time.getSeconds())
console.log(time.getMinutes())
```

#### 常见使用

写一个倒计时

```js
function time(){
  let time = new Date();
  let nowD = time.getTime(); //  当前 距离1970之间的毫秒差
  let objTime = new Date('2016/12/4 16:51:34');// 考试时间
  let objTimeM = objTime.getTime(); //考试  距离1970之间的毫秒差

  let diff = objTimeM -nowD ;  // 之间相差的毫秒数
  let Mdiff = parseInt(diff/1000) ; // 转为秒
  let Hdiff = parseInt(Mdiff/3600); // 小时
  let endS = Mdiff%3600; //计算小时之后剩余的秒数
  let MSdiff = parseInt(endS/60); // 计算有几个分钟
  let endMS = endS%60; //计算分钟之后剩余的秒数

  let show = ad(Hdiff) +':'+ad(MSdiff) +':'+ad(endMS) ;
  document.querySelector('#box').innerHTML = show;

}
setInterval(time ,1000)
```
