---
title: react组件Video
date: 2018-09-06 15:54:19
categories: 轮子
tags: [react, video]
---


![video](https://user-gold-cdn.xitu.io/2018/9/6/165ade59d09be085?w=1412&h=616&f=png&s=1455389)

> 写一个很简单的video组件

<!--more-->

之前的一篇文章讨论了 video元素 的使用

[video 和 audio在移动端的表现](https://mengxxself.github.io/2017/10/24/video%E5%92%8Caudio%E5%9C%A8%E7%A7%BB%E5%8A%A8%E7%AB%AF%E7%9A%84%E8%A1%A8%E7%8E%B0/)

可以看到在iOS和Android两种系统对于video控件的支持很不一样，在展示控制组件的时候，两者差别很大

![vs](https://user-gold-cdn.xitu.io/2018/9/6/165adfb2a41884ee?w=667&h=487&f=png&s=295192)

左侧为iOS, 其控件要比Android多

ISO有提供一个全屏播放的按钮，就是左上角的那个，当点击会进入下图右侧状态。当在点击关闭，会恢复到左侧状态

![vs](https://user-gold-cdn.xitu.io/2018/9/6/165adf8dd1ae98c6?w=612&h=465&f=png&s=267705)

然后产品同学表示，希望可以实现设备同步

![do](https://user-gold-cdn.xitu.io/2018/9/6/165ae0086461647f?w=646&h=447&f=png&s=192550)

之后向其解释了video的展示样式是基于设备浏览器的，无法进行控制，比如关闭iOS的全屏按钮，于是就这么处理了

后面提测之后看这里的代码，因为需求不复杂，决定写个小组件，实现 播放，暂停，进度展示 这三个功能

## 组件结构

1 video 

2 底部控制条

```javascript
<div>
  <video/>
  <controller/>
</div>
```

video元素 不添加 controls 不展示本身的控件

## 事件控制

### onloadedmetadata

在获取视频总时长的时候，一直拿到的空值

```javascript
componentDidMount () {
  const target = this.video
  const time = target.duration || 0
  ...
}
```

怀疑是不是因为这个 target 对象 获取的是空, 然后在添加了console，在控制台是可以看到的，查阅资料有人说这个因为视频资源数据没有被加载

![load](https://user-gold-cdn.xitu.io/2018/9/6/165ae0e042d85c34?w=795&h=202&f=png&s=121115)

| 事件 | 触发机制 |
| --- | --- |
| loadeddata | 当当前帧的数据已加载，但没有足够的数据来播放指定音频/视频的下一帧时，会发生 loadeddata 事件|
| loadedmetadata | 当指定的音频/视频的元数据已加载时，会发生 loadedmetadata 事件 |

音频/视频的元数据包括：时长、尺寸（仅视频）以及文本轨道

所以调整为

```javascript
componentDidMount () {
  const target = this.video
  
  // 获取视频总时长
  target.onloadedmetadata = () => {
    this.setState({ time: target.duration })
  }
}    
```

### ontimeupdate

| 事件 | 触发机制 |
| --- | --- |
| timeupdate | 在音频/视频（audio/video）的播放位置发生改变时触发 |

```javascript
target.ontimeupdate = () => {
  const { currentTime, duration } = target
  this.setState({ move: currentTime, width: currentTime / duration })
}
```

这个事件用于处理展示视频进度

## 控制栏

调整之后，iOS  Android 在微信中的表现

![control](https://user-gold-cdn.xitu.io/2018/9/6/165ae7a26a512986?w=301&h=433&f=png&s=143203)

只有进度条，播放按钮

但是进入浏览器环境，发现还是有那个控制组件, 虽然并没有使用controls属性，也会显示控件

后面查询文章，说在浏览器中无法处理这个问题

有的人想的办法是，将视频设置的大一点，把自带的控件放在屏幕之外，这样用户就看不到了

## poster

video组件有设置 点击之后立即播放

```javascript
// 播放视频
export const videoPlay = () => {
  const video = document.querySelector('video')
  video && video.play()
}
```

但是这个执行的时候，能看到黑屏展示

![poster](https://user-gold-cdn.xitu.io/2018/9/6/165aeb4c78796f9b?w=307&h=475&f=png&s=70669)

给video元素添加一个poster属性, 这样会使用封面图填充缓冲时间

> poster 属性指定视频下载时显示的图像，或者在用户点击播放按钮前显示的图像

## other

[项目Git地址](https://github.com/mengxxSELF/Video)

![progress](https://user-gold-cdn.xitu.io/2018/9/6/165ae0fe2d742de5?w=504&h=340&f=png&s=103725)

* [html5--移动端视频video的android兼容，去除播放控件、全屏等](https://segmentfault.com/a/1190000006857675) 