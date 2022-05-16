# 彻底掌握基于HTTP网络层的前端性能优化

## 从输入URL地址到看到页面，中间经历了什么

1. URL 解析
2. 检查缓存【强缓存、协商缓存（针对于资源文件请求）& 本地存储（针对于数据请求）】
3. DNS 服务器解析【域名解析：根据域名解析出服务器的外网IP】
4. TCP 三次握手 【建立客户端和服务器之间的网络链接通道】
5. 基于 HTTP / HTTPS 等传输协议，实现客户端和服务器之间的信息通信
6. TCP四次挥手【把建立好的网络通道释放掉】
7. 客户端渲染 【呈现出页面和效果】

---

## URL解析【识别URI】

- URI 统一资源标识符
  - URL： 统一资源定位符
  - URN：统一资源名称

```javascript
http://user:pass@www.baidu.com:80/st/index.html?xxx=xxx&xxx=xxx#video
```

- 传输协议：http / https / ftp ... 
  - HTTP 超文本传出协议 { 除了传输文本【例如：字符串】还可以传出其他的信息【例如：文件流、二进制或这Buffer格式、BASE64格式】 }
  - HTTPS = HTTP+SSL 更安全的HTTP{ 传输的内容经过加密处理 }，一般涉及支付类的产品都采用这种协议
  - FTP 文件传出协议，一般用于直接基于一些 FTP工具，把开发文件部署到服务器上
- 用户名密码： user:pass (不常用)
- 域名：www.baidu.com
  - 顶级域名：baidu.com
  - 一级域名： www.baidu.com
  - 二级域名：video.baidu.com
  - 三级域名：test.video.baidu.com
  - 多级域名...
  - 购买的是顶级域名，后期可以分配三级或多级域名
- 端口号：80
  - 端口就是用来区分一台服务器上多个项目的
    - 取值范围 0-65535
    - 端口号：http-> 80, https -> 443 , ftp -> 21
- 请求资源的路径名称：/st/index.html
  - 基于路径找到客户端需要的资源文件
  - 看到的URL可能是重写后的 【看到的地址在文件目中不存在】
    - 比如：ajax数据请的接口地址 /api/list
    - 比如：URL重写【http://item.jd.com/123456.html】(动态地址：不利于SEO优化)
- 问号参数信息： ?xxx=xxx&xxx=xxx
- HASH（哈希）：#video
  - 锚点定位
  - HASH路由

### URL 编译问题

- encodeURI & decodeURI 编译空格和中文，一般编译整个URL中的信息
- encodeURIComponent & decodeURICOmponent ：编译空格和中文以及一些特殊符号，所以一般只是用来编译传递的信息值的，而不是整个URL【放置URL解析不了或者传递信息的乱码等问题】

```javascript
let url = `http://www.baidu.com/index.html?user=${encodeURIComponent("管理  员")}`
console.log(url)

let url2 = "http://www.baidu.com/index.html?user=管理 员"
console.log(eencodeURI(url2))
```



---

## 检查缓存【强缓存、协商缓存（针对于资源文件请求）& 本地存储（针对于数据请求）】

### 一、强缓存、协商缓存（针对于资源文件请求）

无论强缓存还是协商缓存都是服务器段设置的，客户端浏览器自己会根据返回的一些信息，进行相关处理，无需前端单独设置

缓存处理时基于 HTTP 网络层进行优化的一个非常重要的手段【针对的资源文件请求】

#### 强缓存： Expires(HTTP1.0) / Cache-Control(HTTP1.1)---状态码 200

- 问题：本地缓存了文件，但是服务对应的资源文件更新了，我们如何保证或的时最新的内容

  - 请求文件的时候设置时间戳

    - ```javascript
      <link href="xxx.css?123456456">
      ```

    - 如果服务器资源有更新，再次发请求，保证时间戳不一样，就会拉取新的资源

  - 文件的HASH名

    - 借助Webpack等构建工具，对每个文件内容生成HASH

**永远不要对 HTML 文件做强缓存**

# 图片展位

#### 协商缓存：Last-Modified (HTTP1.0) / Etag (HTTP1.1)----状态码304

- Last-Modified：记录服务器资源文件最后一次更新的时间
- ETag：只要服务器资源文件改变，会生成一个不同的标识

**当强缓存失效（或者不存在）【html可以做协商缓存】，会校验协商缓存【前提时设置了协商缓存】**

**协商缓存每一次都会向服务器校验资源是否更新**

# 图片占位

### 二、本地存储

#### 客户端存储数据的方案有那些

- cookie
- webStorage
- IndexDB
- Cache（被 W3C 废了）
- Manifest 离线存储（浏览器性能欠佳）

#### LocalStorage VS cookie

本地存储的数据是有同源访问限制的，之允许读取本源下存储的内容

- cookie 之允许一个源下最多4KB内容；localStorage可以5MB
- cookie 可以收路径条件限制，可以设置过期时间；localStorage需要手动清除数据
- cookie 不稳定，开启无痕模式将不能存储cookie信息；localStorage 不受影响
- cookie 兼容低版本浏览器
- cookie 会默认把本地cookie信息基于请求发送给服务器





