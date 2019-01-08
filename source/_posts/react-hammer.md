---
title: react-hammer
date: 2017-08-27 10:02:58
categories: 第三方插件使用
tags: react
---

react-hammer是一个帮助实现移动端拖拽效果的插件

[npm](https://www.npmjs.com/package/react-hammerjs)

[Github](https://github.com/JedWatson/react-hammerjs)

<!-- more -->

其实React-hammer是基于[hammer.js](http://hammerjs.github.io/) 构建而成的一个更适合在React中使用的触摸插件

## 使用

```html
let Hammer = require('react-hammerjs')

<Hammer onTap={handleTap} options={options}><div>Tap Me</div></Hammer>

```
## 六种手势支持

react-hammer.js 支持六种手势操作

| 事件 | 手势 |  
| --- | --- | --- | --- |
| pan | 单手指滑动 |
| tap | 单手指点触 |
| doubleTap | 单手指双击 |
| pinch | 两个手指进行缩放动作 |
| press | 单手指下压 |
| rotate | 双手指旋转 |
| swipe | 单手指滑动 |

默认状态下 是无法进行pinch 与 rotate 操作的 需要手动将其设置为TRUE

> As a default, the pinch and rotate events are disabled in hammer.js, as they would make actions on an element "blocking".

```html
let options = {
  recognizers: {
    pinch: { enable: true },
    rotate: { enable: true }
  }
}

<Hammer options={options} >
  <p>{text}</p>
</Hammer>
```

这样才能进行缩放以及旋转动作

## 事件对象

每一个事件的回调函数中都有一个事件对象 包含以下属性 (展示部分常用属性)

| 事件对象 | 含义 |  
| --- | --- | --- | --- |
| type | 事件类型 |
| deltaX | X轴方向移动 |
| deltaY | Y轴方向移动 |
| distance | 移动距离 |
| direction | 移动的方向 |
| srcEvent | 事件来源 |
| rotation | 多点触摸时已经完成的旋转（deg） |
| eventType | 事件类型，匹配INPUT常量 |



## pan 单手滑动

| 事件 | 含义 |  
| --- | --- | --- | --- |
| pan | 单手指滑动 整个滑动周期 |
| panstart | 开始滑动 |
| panmove | 滑动 |
| panend | 滑动结束 |
| pancancel | 滑动取消 |


默认只能进行水平方向的滑动  ** 无法处理垂直方向的滑动  **    因此需要手动添加属性  direction

npm 中这样说道

> If you provide the prop direction the pan and swipe events will support Hammer.DIRECTION_(NONE/LEFT/RIGHT/UP/DOWN/HORIZONTAL/VERTICAL/ALL).

因此我就这么写的

```html
<Hammer direction={DIRECTION_ALL}>
 ...
</Hammer>
```
然而并没有效果 之后查看issue 发现实际是这么处理

```html
<Hammer direction='DIRECTION_ALL'>
 ...
</Hammer>
```

** 关于 pancancel **

个人感觉应该是在快速拖动导致手势丢失的时候 会触发此事件发生 然而测试中并没有发现有什么用 因为一直会被 panend 被捕捉而不是 pancancel

### 拖拽

此函数会一直被执行 从整个滑动开始 正在滑动 滑动结束  

元素跟随手指滑动变动位置的原理是 获取元素初始位置 + 手指滑动距离 然后在手指抬起瞬间 更新元素初始位置

```javascript
let eleLeft = startX + deltaX
```

**  结束手指滑动时候 更新元素位置 **

之前一直是在 Panend 中处理这个问题 但是发现实际页面拿到的left数据  一旦发生一次滑动结束 再次滑动的时候  里面的值就是undefined  

调试很久之后 将事件处理到 pan 事件中

通过监听事件对象的 eventType === 4 来判断词此时是要结束滑动 然后更新元素初始位置

**  有一个坑  **

当手指离开屏幕 也就是结束滑动的时候    

```javascript
// 不触发方向运动 也就是手指离开的时候
if (direction === 1) {
  end = translateX
  topEnd = translateY
}
```

不属于任何一个判断条件  但是此时也要处理 左 上 的值  否则也会发现下次出现undefined

** 建议不要使用定位变动元素位置 **

最开始通过position将元素进行定位 在滑动过程中更改left top 坐标点  但是这种处理方式中 元素滑动并不流畅 体验性很差

可以通过transform来移动元素

```javascript
transform: `translateX(${translateX}px) translateY(${translateY}px)`,
```

### 缩放

| 事件 | 含义 |  
| --- | --- | --- | --- |
| pinch | 两个手指缩放 整个缩放周期 |
| pinchstart | 开始滑动 |
| pinchIn | 缩小 |
| pinchOut | 放大 |
| PinchEnd | 缩放取消 |
| PinchCancel | 滑动取消 |

缩放监听的是两个手指捏合的动作

元素缩小

```javascript
pinchIn () {
  let width = this.state.width - 1
  let height = this.state.height - 1
}
```

元素放大

```javascript
pinchOut () {
  let width = this.state.width + 1
  let height = this.state.height + 1
}
```

由于手指捏合速度很快 所以这里直接将元素进行宽度与长度的增减

** 最好不要将元素大小变化的处理放入 onPinch 事件 防止手势丢失导致元素一直放大或者缩小 **

### 旋转 rotate

hammer.js官网中提供的 [旋转案例](http://hammerjs.github.io/#try-it) 中  通过判断两个手指之间的角度差 来更改元素旋转角


```javascript
onRotate (ev) {
  transform.angle = initAngle + ev.rotation
}
```

但是这样是有一个问题如果最开始两个手指之间不在同一个水平线上 同样检测到两个手指之间的差值 元素会马上发生转动

所以这个属性不能准确读取到手指旋转角度

测试中发现 函数中事件对象 ev 有一个 srcEvent 参数  它也是一个对象 里面也有一个 rotation 属性 这个真正的表示了每一次手指旋转的角度值

** 此处有坑 只在iOS中有此属性 **

所以更改为使用此属性值来进行元素的旋转效果

```javascript
handleRotate (ev) {
  let {startRotate} = this.state
  let {deltaTime, srcEvent} = ev
  // 点触事件  时间太短不去触发旋事件
  if (deltaTime < 100) return

  let {rotation = ''} = srcEvent || {} // 每次手指旋转的角度
  let end
  if (rotation) {
    // 如果存在则表示是在iOS中 rotation 从srcEvent 中获取 表示每一次旋转的角度
    end = startRotate + rotation
  }
}
```

### 按压 press

| 事件 | 含义 |  
| --- | --- | --- | --- |
| press | 按压周期 |
| pressUp | 按压结束 手指抬起 |

* 旋转的补充方案

由于在安卓机中没有拿到每一次旋转角度 因此使用plan B 通过添加两个按钮 当点击按钮或者长按按钮 可以进行图片的旋转

按压过程 通过开启定时器 来不断旋转目标元素

```javascript
timer = setInterval(() => {
  old--
  this.setState({rotateArg: old})
}, 25)
```

在按压结束的时候 清除定期器

```javascript
handlePressUp () {
  timer = window.clearInterval(timer)
}
```

** bug 处理  手势并发 导致不停旋转 **

后来发现 如果在点击旋转按钮的时候 同时触发了别的手势 比如滑动 那么一旦手指离开屏幕 会发现元素会一直转个不停

这是因为定时器的清除操作只写在了 PressUp 这个手势中 而多手势的时候 有时候会读不到此事件

修正措施： 在每一种手势结束的时候 都清除一次定时器 比如 tapEnd 、 pinchEnd等
