---
title: moment.js使用
date: 2018-09-04 14:38:07
categories: 第三方插件使用
tags: [javascript]
---

![moment](https://user-gold-cdn.xitu.io/2018/9/4/165a3684f26f1a8f?w=1442&h=652&f=png&s=380462)

> moment.js 使用笔记 

<!--more-->

## 获取当前时间

```javascript
let now = moment()
let date = now.date() // 或者 now.dates()  这个是当月哪一日
let hour = now.hour() // 或者 now.hours()
let minute = now.minute() // 或者 now.minutes()
```

## 时间日期进行格式化

```javascript
let day = moment().format('YYMMDD')
```

输出什么样的格式可以之间在这里定义

```javascript
YYMMDD  170315
YY-MM-DD 17-03-15
YYMMDD 20170315
YYMMDD HH-mm-ss 170315 20:23:21
```

### 注意大小写是有区别的

HH 这种形式的获取的是24小时的时间 

hh是12小时制的 

```javascript
YYMMDD hh-mm-ss 170315 04:23:21
```

## 获取相对时间

有的时候项目考虑到时区问题或者数据展示  比如在每一天的00：00-05：00进行数据展示 展示的是昨天的数据

此时就需要一个时间参数来表示昨天

```javascript
let today = moment()
let yesterday = moment().subtract(1,'day')
```

获取第二天

```javascript
let tomorrow = moment().add(1,'days')
```

## 时间戳转化

时间转为时间戳

```javascript
moment('2018-09-01 22:00').unix()
```

获取当前时间的时间戳

```javascript
moment().format('X')  
moment().format('x') 
moment().valueOf()
```

时间戳转为时间

```javascript
moment.unix(时间戳).format('YYYYMMDD')
```

## 是否处于某个时间段内

只有当用户注册时间在2017-3-10之间注册的才能参加

```javascript
let time = moment(user.loginTime).isBefore('2017-3-10 00:00:00')
```
是否相同

```javascript
moment().isSame('2010-10-20'); // true
```

是否之后

```javascript
moment().isAfter('2010-10-19 12:30:00'); // true
```

## 注意有坑

* 当前是第几周

```javascript
  const week = moment().isoWeeks()
```

注意 文档里还有一个 获取周的API是 moment().week()

这两个API是有区别的，下面的 week 那个的一周 是从 周末到周六 算一周，和平时理解的一周不同

## QUESTION

* 今日

```
  const yesterday = moment().subtract(1, 'days').format('YYYYMMDD')
  const day = moment().format('YYYYMMDD')
```

* 本周

```
  // 年份
  const year = moment().format('YYYY')
  // 当前周是第几周
  const week = moment().isoWeeks()

  const start = moment().isoWeekday(1).format('YYYYMMDD')
  const end = moment().isoWeekday(7).format('YYYYMMDD')
```

* 本月

```
  const month = moment().format('YYYYMM')

  const start = moment().startOf('month').format('YYYYMMDD')
  const end = moment().endOf('month').format('YYYYMMDD')
```