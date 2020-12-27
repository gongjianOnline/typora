# BOM模型

## history

- 封装当前窗口打开后成功访问过的URL的历史记录站

- history.go(n)

  - ```javascript
    //前进
    history.go(1)
    // 后退
    history.go(-1) // 如果失效就go(-2)
    // 刷新
    history.go(0)
    ```

---

## location

- 封装当前窗口正在打开的url

- ### 属性

  - ```javascript
    .href // 保存完整的url
    .protocol // 协议
    .host(.hostname和.prot) // 主机名
    .pathname // 相对路径
    .hash // 锚点
    .search?xxx=xxx  //参数
    ```

- ### 方法

  - ```javascript
    // 在新窗口打开新的url
    locationg.assign("url")
    location.href = "url"
    location = "url"
    
    // 在当前创窗口打开禁止后退
    location.replace("url")
    
    // 重新加载当前页面
    location.reload(false / true)
    ```

---

## navigator

- 封装浏览器配置信息的对象 (没有标准)

- **.cookieEnabled** 判断是否启用cookie

  - 什么是:
    - 在客户端持久保存用户私密数据的文件
  - 为什么
    - 内存中的数据(变量,数组,对象)都是临时的
  - 何时用
    - 只要希望在客户端永久保存数据时
  - 缺点
    - cookie数据储存在浏览器里
    - 内存在4kb左右
    - 数据已被篡改和泄露

- **.plugins** 封装了浏览器安装的所有插件信息

  - 查找浏览器是否安装

  - ```javascript
    function fun(name){
        return navigator.pulgins[name] !== nudefined
    }
    ```

- **.userAgent** : 包含浏览器名称.内核和版本号的字符串

---

## screen

- 获得当前屏幕大小

- 获得屏幕完整分辨率 **screen.width / height**

- ### 笔试题

  - 专门用于判断客户端先显示设备的类型
  - 大屏 lg > 1200
  - 中屏 md pc > 992
  - 小屏 sm pad > 768
  - 超小屏 xs phone < 768

---

## event

- ### 事件

  - 事件处理函数
    - 当事件发生时自动调用的函数
  - 何时
    - 当时间发生时,自动调用一个函数,都要在时间发生前,将函数绑定到事件属性上

- ## addEventListener

  - ```javascript
    // 绑定事件
    document.addEventlistener("eventName",function)
    // 解绑事件
    document.removeEventListener("eventName",function)
    ```

- ## 事件模型

  - DOM三个阶段

    - #### 事件捕获

      - 有外向内,记录各级父元素上绑定的相同事件

        - ```javascript
          /*
          	captur 表示是否在捕捉阶段提前触发,默认是false
          */
          addEventListener('eventName',function,captur)
          ```

      - 目标触发

        - 首选触发目标元素上的事件处理函数

      - 冒泡

        - 按照捕获的顺序的反响,由内向外,一次触发各父级元素绑定的事件处理函数

    - ### 事件对象

      - 当时间发生时,自动创建的,封装所有事件信息的对象

      - 何时

        - 操控事件或者事件相关数据都要用事件对象

      - 获取

        - 事件对象默认作为事件处理函数的第一个参数传入

        - ```javascript
          function 处理函数(e){
              // 事件发生时自动获取事件对象
          }
          ```

    - ### 取消冒泡

      - ```javascript
        e.stopPropagation()
        ```

    - ### 利用冒泡

      - 优化
        - 尽量少的添加事件监听
      - 原因
        - 浏览器在事件发生时,会遍历所有注册的事件,事件监听越多,遍历的越慢,网页响应速度也就较慢
      - 解决
        - 如果多个子元素绑定相同的事件时,只需要在父元素集中绑定一次,所有子元素公用即可
      - 难题
        - 如何获得目标元素
        - this 指向冒泡的元素
        - e,targrt 始终指向目标元素

    - ### 取消事件

      - 阻止默认行为

      - 何时

        - 当事件发生后,不希望事件再执行浏览器默认行为时

        - ```javascript
          e.preventDefault()
          ```