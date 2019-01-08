---
title: 'canvas,blob,file,base64格式的转换'
date: 2017-08-30 20:28:00
categories: javascript
tags: [file, canvas]
---

## canvas

### canvas -> DataURL

这个可以使用 canvas本身提供的方法来处理

```javascript
canvas.toDataURL('image/png')
```
<!-- more -->

### canvas -> blob

这个可以使用 canvas本身提供的方法来处理

```javascript
canvas.toBlob((blob) => {

})
```

不过这个方法在iOS中不被支持

也可以借助一些第三方插件

[JavaScript-Canvas-to-Blob](https://github.com/blueimp/JavaScript-Canvas-to-Blob)

## base64

### base64 -> blob

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

**  这里传入的参数 urlData 是base64格式的  但是是不带有头部的 因此在函数内部做了处理  **

* window.btoa() 编码一个可能在传输过程中出现问题的数据

* window.atob() 将使用window.btoa方法的数据进行解码

这两个方法的支持情况还不错

![btoa&atob](/img/btoa.png)

### base64 -> file

将图片转化为file格式 其实是模拟了一个form表单的提交 但是这里需要将base64先转化为Blob格式

```javascript
function base64ToFile (base64) {
  let form = new FormData()
  formData.append('photo', Base64UrlToBlob(base64))
  formData.append('uid', uid)
  $.ajax({
    url: 'upload',
    type: 'POST',
    data: formData,
    processData: false,  // 不处理数据
    contentType: false   // 不设置内容类型
  }).done(({photoUrl}) => {
    // 得到截图上传到七牛的URL
    console.log(photoUrl)
    resolve({photoUrl})
  }).fail(() => {
    console.log('error')
  })
}
```

这里使用的append方法是FormData对象中的方法

> 通过FormData对象可以组装一组用 XMLHttpRequest发送请求的键/值对。它可以更灵活方便的发送表单数据，因为可以独立于表单使用。如果你把表单的编码类型设置为multipart/form-data ，则通过FormData传输的数据格式和表单通过submit() 方法传输的数据格式相同


## DataURL

### DataURL 展示图片

将DataURL 图片绘制在canvas中

```javascript
let img = new Image()
img.onload = () => {
  canvas.drawImage(img)
}
img.src = dataurl
```

## File

### File -> dataUrl
### File -> Blob

两种方式全部借助 FileReader 对象的方法  参见本站文章  [《关于文件的那点事》](https://mengxxself.github.io/2017/08/25/%E5%85%B3%E4%BA%8E%E6%96%87%E4%BB%B6%E7%9A%84%E9%82%A3%E7%82%B9%E4%BA%8B/)
