---
title: video.js的API
date: 2017-11-01 10:30:43
categories: 第三方插件使用
tags: [react-native]
---

视频第三方插件库 video.js 的API文档并不清晰  查阅的时候比较麻烦  自己整理了一篇API

[官网](http://videojs.com/)

[npm](https://www.npmjs.com/package/video.js)

[Github](https://github.com/videojs/video.js)

<!--more-->

### 标签属性

* data-setup

### 属性

*

### 方法

* videojs(id, optionsopt, readyopt)

* ready

* play

* pause

* on 事件监听

* off

* one

* log 信息打印

```javascript
this.on('ended', function() {
  videojs.log('您的视频已经播放完毕')
})
```
* duration

* currentTime

获取视频播放进度

```javascript
myPlayer.currentTime()
```

设置视频播放进度

```javascript
myPlayer.currentTime(100)
```
* width

* height

* size

* enterFullScreen 全屏

* exitFullScreen 退出全屏

* removeEvent 事件移除

### 事件

* ended 播放事件结束

* fullscreenchange 全屏状态变动

* play

* pause
