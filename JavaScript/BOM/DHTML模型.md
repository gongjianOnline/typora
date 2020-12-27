# DHML模型

- ### window

  - 代替global充当全局变量
  - 封装所有的**BOM ** 和  **DOM**  的  **API**

- ### history

  - 封装当前窗口打开后的历史记录栈

- ### location

  - 封装了当前窗口正在打开的url对象

- ### navigator

  - 封装浏览器的配置信息

- ### document

  - DOM元素

- ### screen

  - 封装了显示设备的信息

- ### event

  - 封装了事件对象

---

## window对象

- ### 打开和关闭窗口

  - ```
    window.open() / close()
    ```

- ### 打开超链接的方式

  - 替换当前窗口,可后退

    - ```javascript
      // HTML
      <a href="url" target="_self"
      // js
      window.open('url','_self')
      ```

  - 替换当前窗口不可后退

    - ```javascript
      // HTML不支持
      // js
      location.replace('url')
      
      ```

  - 在新窗口打开,可打开多个

    - ```javascript
      // HTML 
      <a href="url" target="name">
      //js
      window.open("url","_blank")
      ```

  - 在新窗口打开,只能打开一个

    - ```javascript
      // HTML
      <a href="url" target="name">
      //js
      window.open('url',"name")
      ```

- ### 窗口大小和窗口位置

  - ### 窗口大小2组

    - 完整大小

      - ```java
        outerWidth / outerHeight
        ```

    - 文档显示区域大小

      - ```javascript
        innerWidth / innerHeight
        ```

---

## 定时器

- ### 周期性定时器

  - 让浏览器反复执行一项任务

  - #### 何时用

    - 反复执行一项任务

  - #### 如何

    - ```javascript
      // 任务函数: 定时器自动调用一个函数
      function task(){...}
      // 启动定时器
      var timer = setInterval(task,ms(毫秒))
      // 停止定时器
      	// 手动停止
      clearInterval(trmer)
         	// 自动停止
      /*
      	在任务函数中判断临界值
      	如果达到临界值,就自动调用手动停止的API
      */
      ```

- ### 一次性定时器

  - 让浏览器等待一段时间后,自动执行一次任务

  - ```javascript
    // 任务函数: task
    // 启动
    var timer = setTimeout(task,ms(毫秒))
    // 停止
    clearTimeout(timer) // 关掉定时器
    ```


