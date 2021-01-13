# web性能优化

## 当输入url之后浏览器做了什么?

- DNS 查询得到 公网IP
  - DNS (用有由运营商提供)
  - 公共DNS 114.114.114.114
- 向IP发起请求
  - http请求
    - tcp连接
    - 每一个请求都是一个tcp连接(包括 js,css,html)
- 服务器查询数据库
- 返回数据
- 展示HTML,渲染css,加载js

---

## DNS服务优化

- 花钱
- 配置hosts

---

## 请求性能优化(keep-alive)连接复用

- 后端配置tcp的keep-alive

- ```javascript
  Content-Encoding: keep-alive
  ```

---

## SQL优化

---

## 提高服务器带宽

花钱

---

## 压缩gzip算法

```javascript
// headers中
Content-Encoding: gzip // 后台开始压缩算法,浏览器以压缩包的形式下载后自动解压缩
// 如搜索nginx gzip
```

---

## 优化css

- 重复的选择器和属性统一合并
- **理论上可行(实际开发中可忽略不计)**
- 先css优化后js

---

## 前端懒加载

参考大厂 京东 淘宝 天猫

---

## 前端预加载

- 在请求时提前加载下一页
- 缺点
  - 浪费带宽

---

## 缓存

**DNS缓存 、 浏览器自动缓存机制 、HTTP缓存（html、css、js、img） **--后端配置

```java
cache-control: max-age = 时间
```

### 弱缓存

当浏览器重复请求一个文件时,浏览器会向服务端发送一个版本号,服务器匹配版本号如果相同,则返回304,

304没有下载内容

---

## 请求优化

- 每个域名同时最多发起10接口请求

- 解决

  - 添加域名(CDN)

  - ```javascript
    例如:
    // 域名a 专门发送HTML请求
    // 域名B 专门发起css请求
    // 域名C 咱们发起js请求
    ```

---

## cookie-free

- cookie携带参数请求,每次请求都会带给服务器
  - cookie最大4KB
- 通过增加域名,将带有参数的cookie传一个域名,不需要cookie的接口放在另一个域名下.

---







