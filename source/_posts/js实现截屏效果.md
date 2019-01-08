---
title: js实现截屏效果
date: 2017-08-26 23:00:18
categories: 第三方插件使用
tags: javascript
---

项目中需要一个截屏的效果 因此尝试了两个不错的第三方截屏插件

## html2canvas

<!-- more -->

[官网](http://html2canvas.hertzen.com/index.html)

[Github](https://github.com/niklasvh/html2canvas)

基本使用

```javascript
html2canvas(document.getElementById('view'), {
  onrendered: (canvas) => {
    image = canvas.toDataURL('image/png')
  },
  width: 300,
  height: 300
})
```

这里返回的是一个canvas元素  你可以通过canvas的方法获base64的图片格式 或者 Blob的形式

* base64

```javascript
let baseimage = canvas.toDataURL('image/png')
```

* Blob

```javascript
canvas.toBlob((blob) => {

})
```

这里就可以拿到图片Blob的形式

但是这里也有一个坑  此Blob无法在iOS中实现  所以如果需要Blob 最好不要使用这种方式

使用base64 转化为 Blob  的函数方法

```javascript
function convertBase64UrlToBlob(urlData){
  var bytes = window.atob(urlData.split(',')[1])   //去掉url的头，并转换为byte
  //处理异常,将ascii码小于0的转换为大于0
  var ab = new ArrayBuffer(bytes.length)
  var ia = new Uint8Array(ab)
  for (var i = 0; i < bytes.length; i++) {
    ia[i] = bytes.charCodeAt(i)
  }
  return new Blob( [ab] , {type : 'image/png'})
}
```
注意这里的参数  是不需要带有base64格式头部那里的部分 最开始没有注意到这里 会发现在控制台报错

```html
failed to execute 'atob' on 'window': the string to be decoded is not correctly encoded.
```

这种方法是可以兼容安卓以及iOS系统的


将指定元素的部分以图片形式展示

## dom-to-image

此插件最大的坑是 ** Safari is not supported **

[Github](https://github.com/tsayen/dom-to-image)

```javascript
domtoimage.toPng(document.getElementById('view'))
.then((dataUrl) => {
  imgSrc = dataUrl
})
.catch(function (error) {
  console.log('oops, something went wrong!', error)
})
```
