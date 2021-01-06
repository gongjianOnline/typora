# webSocket(服务器-客户端)

## HTTP协议

​	属于"请求-响应"模型,一次请求一次响应

## 解决方案:

### 方案一

- 长轮询(Long-Polling) / 心跳请求
- 定时器 + AJAX --- 请求过于频繁,服务器压力大,不够频繁,客户端延迟会变大

### 方案二

- ​	属于"广播--收听",客户端连接到服务器就不在断开,双方就随时向对方发送消息,且是全双方不对等发送;

- ### 原理

  - ws协议的应用程序分为客户端和服务端程序

  - ws客户端应用

  - 主动发起连接请求,保持连接,向对方发送消息,并接受信息;实现语言(php;java;node/html)

  - 使用HTML创建Ws协议客户端应用

    - 连接ws服务器

      - ```javascript
        var socket = new WebScoket('url')
        ```

    - 向服务器发来信息

      - ```javascript
        socket.send(stringMsg);
        ```

    - 接收服务器发来信息

      - ```javascript
        socket.onmessage = function(e){
            console.log(e.data)
        }
        ```

    - 断开到WS服务器连续(可选)

      - ```javascript
        socket.close()
        ```

    ​	

### 	