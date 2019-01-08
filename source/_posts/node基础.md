---
title: node基础
date: 2017-10-25 19:19:30
categories: node
tags: [基础知识]
---

### event

#### 事件发射

on 与 emit

#### emit

emit 一次 相当于向方法池中添加一个函数 写一次加一次 即使是相同函数名 [ fn1 , fn2,fn1,fn4 ,fn1]

once 发射一次之后移除此事件

<!--more-->

removeListen 在发射之前移除 是就近移除

```javascript
removeListen(emitName,fn)
```

removeAllListen 移除所有监听

removeAllListen 有参数则移除参数事件

有最大监听数 10 但是可以自己设置 target.setMaxListeners()

listeners 获取当前函数池子中函数个数

```javascript
let girl = new  Girl()
girl.age='20'
girl.once('smile', function () {
  console.log('i am smiling')
  console.log(this.age)
})
girl.setMaxListeners(5)
girl.emit('smile')
```

### 流 stream

#### 可读流  readStream

flag 打开文件要做什么

start end 前+后面 highWaterMark 最大字节数 默认64k

传送字节数

切换到流动模式

```javascript
rs.on('data')
```

流的方式是异步的，不能用try catch 要用on('err')

不写编码格式的， on.('data') 中读取到 就是 buffer形式

如果设置之后，highwaterMark 一定要大于最小编码的字符长度

pause 暂停 resume 开始

结束

```javascript
rs.on('end',function(){ ... })
```

#### 可写流 writeStream

默认编码utf8 highWaterMark 最大字节数 默认16k

```javascript
ws.write(data)
```

当数据写入核缓存区，该值为true 当数据存放到队列 ，该值为false

当流成功出刷新挂起的缓存区时调用drain

```javascript
ws.on('drain')
ws.end('当写入结束时候你要写入的东西')
```

####  pipe

相当于可读流和可写流的综合：暂停可读流直到可写流赶上再恢复可读流

```javascript
(可读流).pipe(可写流);
```

默认情况下，可读流在结束时会调用end方法  当然你可以设置不这么做

```javascript
var fs = require('fs')
var rs = fs.createReadStream('./2.txt')
var ws = fs.createWriteStream('./3.txt') // 这个目标文件没有回自动创建的

(rs).pipe(ws)
rs.on('end', function () {
 console.log('ending')
})
```

## node路径

__dirname 表示当前文件所在的目录的绝对路径

__filename 表示当前文件的绝对路径

module.filename ==== __filename 等价

process.cwd() 返回运行当前脚本的工作目录的路径

process.chdir() 改变工作目录
