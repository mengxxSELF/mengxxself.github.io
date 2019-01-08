---
title: rn-swiper
date: 2017-10-04 17:40:17
categories: 第三方插件使用
tags: [react-native]
---

RN 提供的react-native-swipe是一个swiper 滑动组件

[GitHub地址](https://github.com/leecade/react-native-swiper)

[npm地址](https://preview.npmjs.com/package/react-native-swiper)

<!--more-->

#### 安装

```javascript
npm i react-native-swiper --save
```

#### 基本使用

```html
render () {
 return (
     <Swiper style={styles.wrapper} showsButtons={true}>
        <View style={styles.slide1}>
          <Text style={styles.text}>Hello Swiper</Text>
        </View>
        <View style={styles.slide2}>
          <Text style={styles.text}>Beautiful</Text>
        </View>
        <View style={styles.slide3}>
          <Text style={styles.text}>And simple</Text>
        </View>
      </Swiper>
 )
}
```

#### 参数解析

| 参数 | 说明 |
| --- | --- | --- | --- | --- |
| index  | 默认展示的面板的索引
| loop  | 开启循环滑动
| horizontal  | 水平滑动  设置FALSE 则垂直滑动
| autoplay | 开启自动循环
| showButtons | 展示按钮

其实是和一款js插件Swiper效果类似
