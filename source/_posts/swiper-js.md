---
title: swiper.js
date: 2017-10-07 14:06:31
categories: 第三方插件使用
tags: [react, javascript]
---

swiper 是一个很好的处理滑动的第三方插件

[github地址](https://github.com/nolimits4web/swiper/)

[API](http://www.swiper.com.cn/api/index.html)

<!-- more -->

### 属性

| 属性 | 含义 |
| --- | --- | --- | --- |
| fullScreen | 是否支持全屏展示 |
| animate | 是否开启动画 |
| speed | 切换速度 |
| loop | 是否开启循环播放 |
| direction | 切换方向 |
| autoplay | 每一屏幕展示时间 |
| autoplayDisableOnInteraction | 是否继续保持用户行为 |
| initialSlide | 默认在开始的时候展示第几个实例 |


* autoplayDisableOnInteraction

关于这个属性 就是当你开启了手势滑动或者键盘切换的时候 如果不设置此属性值为 FALSE 那么当你手动滑动之后 自动切换功能将会停止 也就是只能通过 手动触发滑动事件了

```javascript
autoplayDisableOnInteraction: false
```

此属性值设置为FALSE 表示可以当用户行为结束之后 继续自动切换效果

### 事件

#### onSliderMove

在滑动屏幕的时候

注意这个事件在通过键盘滑动切换的时候 并不会触发

#### onSlideChangeEnd 屏幕切换结束


### 方法

#### slideTo

指定跳转到某一页实例

```javascript
mySwiper.slideTo(9, 75, runCallbacks)
```

#### removeAllSlides

移除所有实例

```javascript
mySwiper.removeAllSlides(); //移除全部
```

此方法将所有的实例删除 可以查看DOM树信息 会发现所有切换实例被移除

### Other

#### DOM 元素解析

查看页面元素发现 Swiper 将DOM树解析为包含 swiper-slide-prev swiper-slide-active swiper-slide-next 这三个比较重要的部分

包含 swiper-slide-active 类名的 表示处于激活状态的屏幕


#### onSlideChangeStart VS  onSlideChangeEnd

项目中一个swiper的切换banner之后获取新数据的需求 最开始的时候是把getData写到 onSlideChangeEnd 中

但是发现 如果快速滑动页面 swiper会丢失 onSlideChangeEnd 中注册的函数 不再执行

GIT中也有人反应这个bug

[onSlideChangeEnd丢失](https://github.com/nolimits4web/Swiper/issues/1403)

然后我把方法注入 onSlideChangeStart 中 发现可以正确得到结果 不会丢失


### v4 vs v3

v4 在一些安卓机和部分iOS中 无法使用，建议使用v3