---
title: 安全令牌JWT
date: 2018-07-18 13:45:21
categories: javascript
tags: [JWT]
---



![JWT](https://user-gold-cdn.xitu.io/2018/7/11/164878066f024a5e?w=788&h=216&f=png&s=32480)

> 安全令牌JWT: JSON对象的形式安全传递信息的方法

<!--more-->

前段时间处理一个抽奖H5，测试过程中想到如果有用户抓到抽奖接口，比如 

```
https:xxx/lottery/userinfo
```

如果直接访问抽奖接口，可以直接进行抽奖动作。这里就涉及到处理验证用户身份的问题

之后的解决方式是 判断接口的cookie中是否包含 userInfo 等参数信息

不过还可以通过另外一种方式来处理-- JWT


## 什么是JWT （JSON WEB TOKEN）

JWT是通信双方之间以 JSON对象的形式安全传递信息的方法。

其实可以理解为使用非对称算法来进行前后端校验。

JWT 由三部分组成

### 头部
![head](https://user-gold-cdn.xitu.io/2018/7/11/164871616dfaf51b?w=352&h=180&f=png&s=12349)
* typ 声明类型 

* alg 声明加密的算法 

然后按照此规则将头部信息进行base64编码，构成JWT第一部分

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
```

### payload

payload 就是存放有效信息的地方

![payload](https://user-gold-cdn.xitu.io/2018/7/11/16487199d8b65066?w=322&h=179&f=png&s=14545)

payload 中有一些参数字段是建议使用的 （仅列出几个）

| 参数 | 含义 |
| --- | --- | --- | --- |
| iat |  jwt的签发时间 |
| exp |  jwt的过期时间，这个过期时间必须要大于签发时间 |
| nbf | 定义在什么时间之前，该jwt都是不可用的 |

比如来定义一个payload

```
{
  "exp": Math.floor(Date.now() / 1000) + (60 * 60),
  "name": "John Doe"
}
```

payload 会进行base64编码，构成JWT第二部分

### 签证

![签证](https://user-gold-cdn.xitu.io/2018/7/11/164872339c1506d0?w=356&h=193&f=png&s=22799)

可以看到，签证部分是由三个部分组成的

| 参数 | 含义 |
| --- | --- | --- | --- |
| base64UrlEncode | base64加密后的Header |
| base64UrlEncode | base64加密后的payload |
| your-256-bit-secret | 自定义的加密secret |

secret 相当于私钥，不可泄漏，如果客户端可以拿到secret，就可以自我签发JWT了

```
var encodedString = base64UrlEncode(header) + '.' + base64UrlEncode(payload)
var signature = HMACSHA256(encodedString, 'secret')
```

signature 是JWT的第三部分

将以上三部分拼接起来，就是最后的JWT


## 第三方库

### jsonwebtoken

如果自己在生成jwt,有点复杂。目前已经有很多开发的第三方库来支持JWT。比如 jsonwebtoken

[jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken) 

* sign 用于生成 token

* verify 用于检验token

### koa-jwt

koa-jwt 用于验证接口中是否包含token信息

搭建了一个简易的server 来看下效果

[项目Git地址](https://github.com/mengxxSELF/koa-jwt)

```
app.use(
  jwtKoa({secret: SECRET})
  .unless({
    path: [/\/login/] // 不需要通过jwt验证的请求路径
  })
)
router.get('/login', async (ctx) => {
  let token = jwt.sign({
    name: 'dva'
  }, SECRET)
  console.log(token, 'token')
  ctx.body = {
    token
  }
})

router.get('/try', async (ctx) => {
  let token = ctx.header.authorization
  let result = jwt.verify(token, SECRET)
  ctx.body = {
    result
  }
})

```

* /login 拿到token

```
// {"token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJuYW1lIjoiZHZhIiwiaWF0IjoxNTMxMjgwMDg2fQ.Rh_vAKeytjAL2TbOk-MmXQWFesszjRU3Bzldrx5x17s"}%
```

* 如果不添加token 会被koa-jwt 拦截

![wrong](https://user-gold-cdn.xitu.io/2018/7/11/1648769e313f5e7f?w=701&h=312&f=png&s=28038)

* 添加 token

![token](https://user-gold-cdn.xitu.io/2018/7/11/16487702e4d849aa?w=793&h=404&f=png&s=41332)


### 工作机制

![工作机制](https://user-gold-cdn.xitu.io/2018/7/11/164877a3277653a0?w=919&h=620&f=jpeg&s=67632)

图片来自于文章 [《前后端分离之JWT用户认证》](http://lion1ou.win/2017/01/18/?hmsr=toutiao.io&utm_medium=toutiao.io&utm_source=toutiao.io)

* 登录拿到JWT

* 前端发起请求，Header中挂载JWT


## 项目实战

由于我搭建的这个项目中有这种需要鉴权的接口比较少，所以并没有使用koa-jwt来处理。只是用了 jsonwebtoken

[项目GIT地址](https://github.com/mengxxSELF/test-activity)

### server端

构建两个接口 login , lottery

![server](https://user-gold-cdn.xitu.io/2018/7/9/1647ed64044740a5?w=637&h=178&f=png&s=27705)

* login 用来生成JWT 返回给前端

```
    token = jwt.sign({
        name: 'who',
        exp: Math.floor(Date.now() / 1000) + (60 * 60), // 设置 token 过期时间
      }, SECRET)
```

* lottery 用来验证JWT，验证通过则进行抽奖动作

```
    let token = this.headers.authorization
      // 解码
      let decoded = jwt.verify(token, SECRET)
      // console.log(decoded, 'decoded')
      let {name} = decoded

      if (name != 'who') {
        code = 403
        return
      }
```

### 前端

* 首页点击登录后 经返回的token信息存储起来

![login](https://user-gold-cdn.xitu.io/2018/7/9/1647edb4bbb51e7e?w=484&h=216&f=png&s=15670)

我这里拿到token之后将其写入了localStorage

```
 window.localStorage.setItem('token', token)
```

* 进入抽奖页面进行抽奖，每次请求的时候挂载Authorization 

![Authorization](https://user-gold-cdn.xitu.io/2018/7/9/1647edde5ae69505?w=434&h=204&f=png&s=12755)

```
    axios.get(`/${APP_NAME}/win`, {
      headers: {
        Authorization: token
      }
    })
```

如果传递错误的token 在server端JWT验证的时候就会报错

![error](https://user-gold-cdn.xitu.io/2018/7/11/16487356ff9fd540?w=767&h=360&f=png&s=36543)

![error](https://user-gold-cdn.xitu.io/2018/7/11/1648735fe4613e42?w=468&h=171&f=png&s=37664)


[项目在线地址](101.200.45.254:9097/activity/lottery)


总而言之，如果你的接口需要考虑鉴权问题，可以参考下JWT来处理。

## Other

* [jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken)

* [jwt.io](https://jwt.io/)

* [项目git地址](https://github.com/mengxxSELF/test-activity) 

* [资料1](http://lion1ou.win/2017/01/18/?hmsr=toutiao.io&utm_medium=toutiao.io&utm_source=toutiao.io)

* [资料2](https://en.wikipedia.org/wiki/JSON_Web_Token) 


## 问题回复

这篇文章发布之后，很多同学提出了一些问题，这里一一回复。感谢各位的评论。

### 问题1： 关于base64处理 Header 和 payload

其实JWT在处理Header 和 payload 的时候，只是很简单的进行了Base64编码。 如果拿到某一个token的话，是很容易就将其解码出来的。

```
const base64url = require('base64url')
let header = {
  'typ': 'JWT',
  'alg': 'HS256'
}

let resultH = base64url(JSON.stringify(header))
console.log(resultH, 'resultH')

let payload = {
  name: 'dva',
  exp: 1531410000
}

let result = base64url(JSON.stringify(payload))
console.log(result, 'result')

// 解码payload
let isP = 'eyJuYW1lIjoiZHZhIiwiZXhwIjoxNTMxNDEwMDAwfQ'
let getP = base64url.decode(isP)
console.log(getP, 'getP')
```

所以不建议在payload中存放敏感信息，比如用户手机号，地址信息等

### 问题2 JWT怎么做续签更新

查阅资料后总结，jwt的续签更新目前有以下处理方式，基本的原理就是在某一个时间点，server端发放新的token

* 每次客户端发起新的请求过来，server自动更新token，返回最新的token信息给客户端，客户端拿到token后需要再更新token

比如 在用户点击抽奖，发起请求的时候，server端每次更新一个token（我这里只更新jwt的有效期）

![token](https://user-gold-cdn.xitu.io/2018/7/13/164929b24314b32a?w=1090&h=982&f=png&s=192311)

拿到新的token之后将其返回。下次发起抽奖动作的时候，挂载这个最新的token

但是这种处理方案有缺陷，如果用户两次请求的间隔时间超过了过期时间（比如20分钟），则接口过来的时候 首先会被判断为过期状态，请求终止（之后的代码不被执行，不会被下发新的token了）。用户会被强制退出到登录界面。

* 每次请求过来的时候，不去判断有效期 （当然此请求本身携带的token必须在有效期内，我的意思是不像第一种，判断距离过期还有多久） 直接下发新的token


#### 问题3 处理注销

可以说token比较重要的问题就是注销token。

比如我上面第二个jwt的项目，当用户点击退出登录的时候，仅仅在客户端做了token的删除。

![remove](https://user-gold-cdn.xitu.io/2018/7/12/1648f150f63cc14b?w=942&h=134&f=png&s=34937)

但是实际上这个token还是处于有效期内的。如果用户保存了token值，在点击了退出登录之后，实际还可以使用此token值的。可以理解为伪注销。

传统的方式怎么处理用户的注销行为呢？-- 删除数据库记录。当用户注销登录信息的时候，server会变更数据库信息

但是jwt是没有介入服务器来存储用户状态的。这就比较难处理了。我们希望token能够在用户注销后不可以被继续使用了


* 设置比较短的token 有效期，每次请求过来的时候，重新下发，不断更新token. 

* 使用服务器存储token状态。当用户点击注销，将token置空。


#### 问题4 JWT单点登录（强制退出用户登录,比如修改密码后,希望能让其他客户端登陆的地方全部强制登出）

可以理解为如何让一个token立即失效（有点像上面的问题3）

* jwt + 数据库（比如 redis）+ 白名单  （这种思路是公司同事提出来的，特别感谢~）

每一位用户在设备A登录的时候，将UID和token对应关系存放起来

```
myRedis.set(`${uid}:token`, ${tokenA})
```

用户换设备B登录的时候，将redis中的的token进行更新 

```
myRedis.set(`${uid}:token`, ${tokenB})
```

每次请求发起的时候，server端去验证该UID对应的token信息是否是最新的token， 这样 如果携带的不是redis中的token的话，拒绝请求。前端强制退出登录。

我将这个单点登录的逻辑加入到了项目中。

[登录尝试](http://101.200.45.254:9097/activity/lottery) 

你可以在两台设备使用同一个用户名进行登录，尝试是不是可以将第一台设备的状态登出。

新增逻辑部分：将用户{name, token} 对应关系存放在文件中，每次发送抽奖请求的时候，判断文件最新的token与接口携带的token是否一致。不一致则反馈前端需要退出登录

![退出](https://user-gold-cdn.xitu.io/2018/7/13/16492f55a5f4485b?w=962&h=472&f=png&s=78702)

缺点：需要保留每一位用户的 {user, token} 对应关系

* jwt + 数据库（比如 redis）+ 黑名单

当用户点击退出登录，此token则被放入黑名单（比如存放在redis）。如果有请求此时携带了黑名单中的token，则不予处理

缺点：长此以往黑名单数据量增长



#### 问题5  如何防范Replay Attacks （重放攻击）

> 重放攻击就是攻击者发送一个目的主机已接收过的包，来达到欺骗系统的目的，主要用于身份认证过程

比如用户的token被获取，那么即使用户登出了系统，其他人还可以利用Token模拟正常请求，而服务器端则无法判断这种情况。

还是黑名单思路，每次token更新之后，或者用户登出之后，旧的token被放入黑名单。携带此token的请求一律不予处理


#### 问题6 使用‘每一次发送请求就去更新token的方式’ 如果客户端有并发的请求，如何处理

em，这个和上面的问题5有点矛盾，如果使用变化token的情况处理，那么肯定会有当请求并发状态下，第一个请求在处理完毕拿到新的token，后面的请求携带的token就变成了旧的token，请求会失败

查阅资料后发现，有些人在将token存到黑名单的时候，会同时添加一个“宽限时间” 。当请求中携带了一个黑名单中的过期token，则去判断去“宽限时间”，如果在期宽限之间之内，则予以通过。


不过我个人没想明白，这种处理方式是不是有问题，既然已经被放入黑名单了，那为什么又来一个“宽限时间”。为什么不直接设置一个长一点的有效时间。


以上是对各位的一些回答。欢迎留言讨论。


### 补充文章

* [JSON Web Token - 在Web应用间安全地传递信息](http://blog.leapoahead.com/2015/09/07/user-authentication-with-jwt)

* [jwt 相关问题](https://github.com/bigmeow/JWT/issues/4)

* [有关JWT(Json Web Token)如何解决并发问题的思考](https://zhuanlan.zhihu.com/p/22693223)

* [不要用 JWT 来做 Web 应用的会话管理](https://juejin.im/entry/59748def518825592c4f9ac0)


