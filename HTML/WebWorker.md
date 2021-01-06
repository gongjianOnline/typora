# WebWorker

## 作用

- 如果在浏览器需要运行耗时的js程序

## 现象

- js执行的过程中,按钮1可见,按钮2在按钮1显示后可见

## 原因

- 浏览器执行js和渲染页面的一个线程

## 解决

### 方案一

- ​	将所有js程序执行放在body最后一个元素上

### 方案二

- ​	创建新的线程，由他来执行耗时的js任务，UI主线程执行后续的HTML渲染

- ```
  <button>1</button>
  <script>
  	var w = new Worker('1.js')
  </script>
  <button>2</button>
  ```

---

## WebWorker --- 缺陷

- 浏览器不允许Worker线程操作DOM和BOM对象

### 原因

- 浏览器只允许UI主线程操作DOM&BOM,若多个Worker线程同时操作DOM结构,页面将混乱

### 解决方案

- ​	Worker线程可以给UI主线发数据

  - worker线程(发送)

    - postMessage(stringMsg)

  - UI主线程(接收)

    - ```javascript
      var w6 = new Worker("1.js")
      w6.onmesssage = function(e){e.data}
      ```

- UI 线程可以给Worker主线发送数据

  - UI主线程(发送)

    - ```javascript
      var w6 = new Worker("1.js")
      w6.postMessage(stringMsg)
      ```

  - Worker线程(接收)

    - ```javascript
      onmessage = function(e){e.data}
      ```