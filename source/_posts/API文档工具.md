---
title: API文档工具
date: 2018-01-27 23:40:23
categories: tools
tags: [tools, apidoc]
---

有点项目是和同事一起开发的 前后端分离的时候 比较需要注意的就是API文档 今天找了一份API文档生成工具

<!--more-->

### apidoc

[官网](http://apidocjs.com/)


[CN官网](http://apidoc.tools/)


使用注意点

#### 在package.json中添加相应参数


  "description": "apiDoc basic example",
  "apidoc": {
    "title": "Custom apiDoc browser title",
    "url" : "https://api.github.com/v1" // 这个是你的项目URL路径
  }


#### 代码中注释的书写格式

api.doc是根据特定的代码书写格式来生成API文档的

注意在js中注释是这种格式的


```javascript

/**
 * @api {get} /user/:id Request User information
 * @apiName GetUser
 * @apiGroup User
 *
 * @apiParam {Number} id Users unique ID.
 *
 * @apiSuccess {String} firstname Firstname of the User.
 * @apiSuccess {String} lastname  Lastname of the User.
 */

```

* @apiSampleRequest http://localhost:8000/getLyric?queen  这个将会在页面嵌入一个类似nodemon发送请求 的功能 非常棒



#### 文档生成命令

```javascript
apidoc -f 'index.js' -i routes/   -o apidoc/
```

![apiDoc](/img/api/apidoc.png)


#### 缺点

无法开启文档更新监控功能 每一次修改需要重新执行命令