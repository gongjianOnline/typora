# 浅谈OAuth2.0

## 什么是OAuth2.0？

OAuth2.0 是一种授权机制，数据的所有者，告知系统同意授权第三方应用进入系统，获取数据，系统从而产生一个进入令牌（token）用来替代密码，供第三方使用

使用场景：常用于获取第三方中的用户资源； 第三方登录

---

## 令牌  VS 密码

| 令牌                   | 密码                   |
| ---------------------- | ---------------------- |
| 存在周期短，不可以修改 | 可长期存在，可以修改   |
| 可被管理者撤销         | 一般不允许撤销         |
| 有利于权限的划分       | 可进行较完整的权限操作 |



---

## OAuth2.0 的四种授权方式

无论哪一种授权方式，第三方应用申请令牌之前，都必须先到系统备案，说明自己的身份，然后会拿到两个身份识别：客户端ID（client ID）和 客户端密钥（client secret），这是为了防止令牌被滥用，没有备案过的第三方应用，是不会拿到令牌的。

![](https://i0.hdslb.com/bfs/album/9e6babc7ebeb9ec1398df8f6fae90c6674b4c425.png)

---



### 一、授权码授权

指的是第三方应用先申请一个授权码,然后在用授权码获取令牌

这种方式是最常见的授权方式，安全性也最高，它使用那些前后端分离的web应用，授权码通过前端传送，令牌则存储在后端，而写所有资源服务器的通信都在后端完成，从而可一避免令牌泄露。

第一步, A 网站提供一个连接,用户点击后就会跳到 B 网站,授权用户数据给 A 网站使用.下面就是 A 网站跳转 B 网站的一个示意连接

```javascript
https://b.com/oauth/authorize?
  response_type=code&
  client_id=CLIENT_ID&
  redirect_uri=CALLBACK_URL&
  scope=read
```

上面 URL 中，`response_type`参数表示要求返回授权码（`code`），`client_id`参数让 B 知道是谁在请求，`redirect_uri`参数是 B 接受或拒绝请求后的跳转网址，`scope`参数表示要求的授权范围（这里是只读）。

![](https://i0.hdslb.com/bfs/album/de7331e12186e2644f83f061ef68b4b1bf90966f.png)

第二步，用户跳转后，B 网站会要求用户登录，然后询问是否同意给予 A 网站授权。用户表示同意，这时 B 网站就会跳回`redirect_uri`参数指定的网址。跳转时，会传回一个授权码，就像下面这样。

```javascript
https://a.com/callback?code=AUTHORIZATION_CODE
```

上面 URL 中，`code`参数就是授权码。

![](https://i0.hdslb.com/bfs/album/d93bdb0903745c46e19136b5a3fc5387485698da.png)

第三步，A 网站拿到授权码以后，就可以在后端，向 B 网站请求令牌。

```javascript
https://b.com/oauth/token?
 client_id=CLIENT_ID&
 client_secret=CLIENT_SECRET&
 grant_type=authorization_code&
 code=AUTHORIZATION_CODE&
 redirect_uri=CALLBACK_URL
```

上面 URL 中，`client_id`参数和`client_secret`参数用来让 B 确认 A 的身份（`client_secret`参数是保密的，因此只能在后端发请求），`grant_type`参数的值是`AUTHORIZATION_CODE`，表示采用的授权方式是授权码，`code`参数是上一步拿到的授权码，`redirect_uri`参数是令牌颁发后的回调网址。

![](https://i0.hdslb.com/bfs/album/057a2a854fe6920d446a513e563c8d5437732076.png)

第四步，B 网站收到请求以后，就会颁发令牌。具体做法是向`redirect_uri`指定的网址，发送一段 JSON 数据。

```json
{    
  "access_token":"ACCESS_TOKEN",
  "token_type":"bearer",
  "expires_in":2592000,
  "refresh_token":"REFRESH_TOKEN",
  "scope":"read",
  "uid":100101,
  "info":{...}
}
```

上面 JSON 数据中，`access_token`字段就是令牌，A 网站在后端拿到了。

![](https://i0.hdslb.com/bfs/album/ad9a12488d98ef9a4230550ec2cb7b0cf1c1bd7f.png)

---

### 二、隐藏式授权

有些WEB是纯前端应用，没有后端，授权码方式将不再适用， 隐藏式授权，允许直接向前端颁发令牌。这种方式没有授权码这个中间步骤。

第一步，A 网站提供一个链接，要求用户跳转到 B 网站，授权用户数据给 A 网站使用。

```javascript
https://b.com/oauth/authorize?
  response_type=token&
  client_id=CLIENT_ID&
  redirect_uri=CALLBACK_URL&
  scope=read
```

上面 URL 中，`response_type`参数为`token`，表示要求直接返回令牌。

第二步，用户跳转到 B 网站，登录后同意给予 A 网站授权。这时，B 网站就会跳回`redirect_uri`参数指定的跳转网址，并且把令牌作为 URL 参数，传给 A 网站。

```javascript
https://a.com/callback#token=ACCESS_TOKEN
```

上面 URL 中，`token`参数就是令牌，A 网站因此直接在前端拿到令牌。

注意，令牌的位置是 URL 锚点（fragment），而不是查询字符串（querystring），这是因为 OAuth 2.0 允许跳转网址是 HTTP 协议，因此存在"中间人攻击"的风险，而浏览器跳转时，锚点不会发到服务器，就减少了泄漏令牌的风险。

![](https://i0.hdslb.com/bfs/album/0edfbe7ffbc39484a10fd8066af8a1e31d678225.png)

隐藏式直接把令牌传递给前端这种方式，只能用于一些安全要求不高的场景，并且令牌的有效性很短，通常是会话（session）周期内有效。

---

### 三、密码式

如果高度信任第三方应用，也可以把用户名和密码，直接告诉应用，该应用使用账户密码，申请令牌

第一步，A 网站要求用户提供 B 网站的用户名和密码。拿到以后，A 就直接向 B 请求令牌。

```javascript
https://oauth.b.com/token?
  grant_type=password&
  username=USERNAME&
  password=PASSWORD&
  client_id=CLIENT_ID
```

上面 URL 中，`grant_type`参数是授权方式，这里的`password`表示"密码式"，`username`和`password`是 B 的用户名和密码。

第二步，B 网站验证身份通过后，直接给出令牌。注意，这时不需要跳转，而是把令牌放在 JSON 数据里面，作为 HTTP 回应，A 因此拿到令牌。

![](https://i0.hdslb.com/bfs/album/23a536ba9a58d97484328f8fd5247f107284e386.png)

密码式授权需要用户给出自己的用户名和密码，因此风险会加大，所以只适用于该应用是用户高度信任的应用。

---

### 四、凭证式

适用于没有前端的命令行应用，即在命令行中请求令牌。

第一步，A 应用在命令行向 B 发出请求。

```javascript
https://oauth.b.com/token?
  grant_type=client_credentials&
  client_id=CLIENT_ID&
  client_secret=CLIENT_SECRET
```

上面 URL 中，`grant_type`参数等于`client_credentials`表示采用凭证式，`client_id`和`client_secret`用来让 B 确认 A 的身份。

第二步，B 网站验证通过以后，直接返回令牌。

![4.1](C:\Users\Administrator\Desktop\auth\4.1.png)

凭证式给出的令牌，只针对第三方应用，而不针对用户，可能存在多个用户共享一个令牌的情况。

---

## 令牌的使用

A 网站获取到令牌后，就可以调用B网站的API。此时每个发到API的请求，都必须带有令牌，具体做法是请求头中，加入`Authorization`字段，令牌就放在这个字段里面。

```javascript
curl -H "Authorization: Bearer ACCESS_TOKEN" \
"https://api.b.com"
```

上面命令中，`ACCESS_TOKEN`就是拿到的令牌。

---

## 更新令牌

更新令牌不需要用户二次登录授权，OAuth 2.0 允许用户自动更新令牌。

B 网站颁发令牌的时候，一次性颁发两个令牌，一个用于获取数据，另一个用于获取新的令牌（refresh token 字段）。令牌到期前，用户使用 refresh token 发一个请求，去更新令牌。

```javascript
https://b.com/oauth/token?
  grant_type=refresh_token&
  client_id=CLIENT_ID&
  client_secret=CLIENT_SECRET&
  refresh_token=REFRESH_TOKEN
```

上面 URL 中，`grant_type`参数为`refresh_token`表示要求更新令牌，`client_id`参数和`client_secret`参数用于确认身份，`refresh_token`参数就是用于更新令牌的令牌。

B 网站验证通过以后，就会颁发新的令牌。

