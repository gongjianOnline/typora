# 浅谈HTTP缓存

HTTP换内存应该算是前端开发中常接触的缓存机制之一,他又课细分为强制缓存与协商缓存,二者最大的区别在于判断缓存命中时,浏览器是否需要服务器进行询问以协商缓存的相关信息,而进行判断是否需要相应内容进行重新请求.下面就来具体看HTTP缓存的具体机制及缓存的决策策略.

## 强制缓存 （200内存缓存)

无需和浏览器通信,直接读取缓存内容

请求头信息

```http
access-control-allow-origin:*
age:734978
content-length:40830
content-type:image/joeg
cache-control:max-age=31536000
expires: Web,14 Fed 2021 12:23:42 GMT
```

强制缓存相关的两个字段是expires和cache-control

- expires
  - 是在HTTP1.0协议中声明的用来控制缓存失效日期的时间戳字段,  他由服务端指定后通过响应头告知浏览器进行缓存
  - 之后浏览器在发起相的资源请求时，便会对比expires与本地的时间戳
  - 缺点
    - 会受到本地时间的影响，如果本地时间更改为缓存时间一样则会一直读缓存
- cache-control
  - 为了解决expires的缺点，新增了cache-control字段
  - 以秒为时间单位
  - 可扩展性较强

### cache-control 中的属性



#### no-cache 和 no-store

- no-cache
  - 表示使用协商缓存
- no-store
  - 表示不适用任何缓存（默认）

#### private 和 public

- private 
  - 表示既被浏览器缓存，也可以服务器缓存
- public
  - 表示只能被浏览器缓存（默认）

#### max-age 和 s-maxage

- max-age
  - 表示告诉浏览器和服务器设置缓存多长时间（秒）
- s-maxage
  - 表示告诉浏览器缓存多长时间（秒）仅在public时有效



---

## 协商缓存 （304）

协商缓存就是使用本地缓存之前，需要想服务端发起一次GET请求，与之协商当前浏览器保存的本地缓存是否过期

请求头

```http
Request URL: htp://127.0.0.1:3000/1.jpg
Request Method:GET

last-modified: Thu,29 Apr 2021:03:0:28MT
cache-control:no-cache
```

当文件第一次请求时服务端会返回 last-modified 字段，表示缓存的时间； 当第二次请求是响应头会带上 IF-Modified-Since 字段 和服务端时间进行对比，如果一直则读取缓存，状态码变成304

#### last-modified的不足

-  如果文件进行了编辑，但实质上文件内容并没有改变，是时间戳会更新。 从而导致网络资源的浪费，延长用户等待的时间

### 基于Etag的协商缓存

为了弥补通过时间戳判断的不足，增加了Etag的头信息，及实体标签    

其内容主要是服务器为不同资源进行了哈希运算所生成的一个字符串，类似于文件指纹

响应头

```http
Content-Type:image/jpeg
Etag:"xxxxxxxxxxxxx"
```

再次请求头

```http
If-node-match:"xxxxxxxxxxx"
```

注意： 如果 last-modified 和 Etag 同时存在是 Etag的优先级最高

#### ETag的不足

- 对文件加密计算会付出额外的计算开销，如果文件过大，数量过多且修改频繁，会一定程度上影响服务器性能



----

## 缓存决策

![](https://cdn.nlark.com/yuque/0/2021/jpeg/152778/1618975657916-6796bcca-6e22-4715-8ffc-bc7a1d30e05b.jpeg?x-oss-process=image%2Fresize%2Cw_451)