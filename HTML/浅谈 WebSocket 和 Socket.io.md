# 浅谈 WebSocket 和 Socket.io

## 一、webSocket

### WebSocket是什么

webSockett是一种网络通信协议。它是html5开始提供了一种在单个tcp连接上进行全双工通信的协议。

---

### 为什么需要WebSocket

传统的http协议(无状态的)，每次请求都是由客户端主动发起的。服务端进行处理后返回结果，而服务端很难主动向客户端发送数据;这种客户端是主动方。仅可以满足信息更新不频繁的web应用。对于涉及实时信息的web应用来说，传统的http协议将不能满足；例如即时通信、实时数据、订阅推送等。

- 无状态协议

  - Http是一种不保存状态，既无状态协议，http协议自身不对请求和响应之间的通信状态进行保存。也就是说，在http这个级别，协议对于发送过的请求或响应都不做持久化处理。
  - 简单理解： 客户端发起请求，服务端响应请求后 协议即终端。
  - 缺点
    - 海量数据请求中，网络带宽资源消耗大
    - 优于需要每次发起请求，数据实时性不高

- webSocket

  - 建立tcp长连接通讯模式。一旦连接建立后，后续数据都会以帧序列的形式传输。服务端可以主动向客户端推送信息。
  - 优点
    - 节省网络带宽资源的消耗
    - 实时性优势明显

  ---

  ### webSocket实现

  webSocket也是基于TCP的，需要建立连接，连接成功后相互通信

  0. 创建实例

  ```javascript
  var ws = new WebSocket("ws://localhost:3000")
  ```

  1. 连接成功建立的回调方法

  ```javascript
  ws.onopen = function(){
      console.log("连接成功")
  }
  ```

  2. 连接失败的回调

  ```javascript
  ws.onerror = function(){
      console.log("连接失败")
  }
  ```

  3. 接收消息的回调

  ```javascript
  ws.onmessage = function(data){
      console.log(data)
  }
  ```

  4. 发送消息

  ```javascript
  ws.send("xxx")
  ```

  4. 终端连接
  
  ```javascript
  ws.close()
  ```
  
  
  
  ---
  
  ## 二、Socket.io
  
  Socket.io 不是 webSocket， 他是websocket和轮询机制以及其他实时通信方式封装的通用接口，其目的是为了给各个浏览器提供兼容，所以wevsocket可以说是Socket.io的子集。Socket除了封装了webSocket还封装了下面几个通信机制：
  
  - Adobe Flash Socket
    - 通过Flash嵌入到浏览器，因为不在W3C标准里面，况且Google浏览器宣布不在支持Flash的使用，所以被淘汰
  - AJAX long polling
    - ajax 长轮询，在webSocket之前的解决方案，就是定时想服务端发起请求。但是对服务端造成的压力较大，信息更新不及时
  - AJAX multipart streaming
    - 在XMLHttpRequest对象上使用 multi-part 标志 （部分浏览器支持，例如firefox）,Ajax请求被发送给服务器端并保 持打开状态（挂起状态），每次需要向客户端发送信息，就寻找一个挂起的的http请求响应给客户端，并且所有的响应都会通过统一连接来写入
  - Forever Iframe
    - 置于页面中的隐藏Iframe标签，该标签的src属性指向返回服务器端事件的servlet路径。 每次在事件到达时，servlet写入并刷新一个新的script标签，该标签内部带有JavaScript代码，iframe的内容被附加上这一 script标签，标签中的内容就会得到执行。这种方式的缺点是接和数据都是由浏览器通过HTML标签来处理的，因此没有办法知道连接何时在哪一端已被 断开了，并且Iframe标签在浏览器中将被逐步取消使用。
  - JSONP Polling
    -   JSONP轮询基本上与HTTP轮询一样，不同之处则是JSONP可以发出跨域请求
  
  ### 
  
  
  
   
  
  



