# 浅谈JWT用户信息认证解决方案

登录看似是一个简单的功能，实际上随着业务的增多和项目的扩大，早期的JSP、ASP、PHP时代，常用的解决方案只是服务端写一个session就搞定了，前端调用登录接口即可。但现在更多考虑到单点登录、身份加密、OAuth授权等安全场景。

## SessionID

http本身是无状态协议，服务端和前端通信是相互不认识的，为了能记住每个访问的身份，引入了Session概念，他是一种服务端缓存技术。当用户访问服务端时候，服务端会生成一个SessionId作为唯一标识保存在内存中，并写入用户浏览器的cookie中，当用户下次访问时，会携带这个cookie，服务端根据据SessionId来判断用户身份。

## Cookie

Cookie是前端浏览器的特性。跟Session有相似之处，但一般我们都不用Session作为唯一身份。当用户登录以后，服务端会一个UserId作为用户的唯一值，前端获取登录信息后，可自行写入cookie或者有服务端写入Cookie，下次访问页面时，可根据Cookie信息来识别用户身份，的但是这种Cookie方式不安全

## JWT

JWT 是一种综合技术方案，即能安全保证，又能简单使用，同时具备时效性。是目前主要的技术方案

## OAtuh

OAtuh本身是一种单点登录协议，当存在多个业务系统时，又不能每套系统都有一个独立的登录功能，就要借助OAtuh做登录授权，一次登录可访问全部系统，最常见的为第三方登录如微信、qq授权登录等。

---

## 本章重点介绍 JWT 的用法和基本概念

### 什么是JWT

JWT 是一种跨域认证解决方案，会利用加密和组合生成JWT TOKEN，对数据传输具有较高的安全性、时效性，可以利用集群做好单点登录

### 业务流程

在登录时，前端向服务端发送登录认证，认证通过服务端返回用户基本信息和token签名，前端对token做数据持久化后，在后面所有的业务接口时都在header 中添加 token 发送给服务端， 服务端会每次验证token的时效性，并正常传输数据，否则返回过期信息，前端自动重定向到登录页提示用户重新登录。

### 在Koa项目中使用 JWT 

```shell
npm install jsonwebtoken -S // 用于生成token签名
npm install koa-jwt -S // 用于验证token时效性
```

生成 token，在路由模块中使用

```javascript
const router = require("koa-router")()
const jwt = require("jsonwebtoken")
router.post("/login", async (ctx)=>{
    const {userName,userPwd} = ctx.request.body
    const res = await User.findOne({userName,userPwd})
    const token = jwt.sign({
      data:res._doc,
    },"xxxs",{expiresIn:10})
    console.log(token) //打印token 签证
})
/*
*	jwt.sign({签证的内容},"加密方式，可以为xxx"，{加密时常：10秒 '10h'小时 '10d'天})
*/
```

解密  token 数据，在同上路由模块中

```javascript
router.get("/demo",async (ctx)=>{
   	 jwt.verify(ctx.header.authorization,"xxx",(error,authData)=>{
		if(err){
         	console.log(err)  
         }else{
             console.log(authData)
         }        
         /**
         * error 报错信息
         * authData 解析出来的token数据
         */
      })
})
```

添加 jWT 拦截器， app.js中添加

```javascript
const koajwt = require('koa-jwt')
app.use(async (ctx,next)=>{
    await next().catch((err)=>{
    if(err.status == "401"){ // koajwt中认证失败默认会返回401状态吗
      ctx.status = 200
      ctx.body = {msg:"token认证失败"}
    }else{
      throw err
    }
  })
})
app.use(koajwt({secret:'xxx'}).unless({
  path:[/^\/users\/login/] // 这里是跳过拦截的路由，原则上登录接口是没有token的
}))
```

### 前端请求方式

```javascript
import axios from "axios"
axios({
    method:"post",
    url:'/demo',
    headers:{
        Authorization: "Bearer "+token
    }
}).then((response)=>{
    console.log(response)
})
```







