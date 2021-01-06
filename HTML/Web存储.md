# Web存储

## 定义

​	在浏览器中存储当前用户专用的数据: 访问历史； 内容定制 ； 样式定制

- 在客户端存储数据可以使用的技术
  - cookie
    - 浏览器兼容性好,不能超过4kb,操作复杂
  - flash
    - 依赖falsh播放器
  - H5 webStorage:
    - 不能超过8MB,操作简单
  - IndexDB
    - 可存储大量数据
    - 但不是标准的技术

---

## session

- 浏览器从打开某个网页的一个页面开始,中间可能打开很多歌页面,指导页面关闭浏览器,整个过程为"浏览器与WEB服务器的一次会话"

---

## webSorage

- WebSorage技术中,浏览器为用户提供两个对象

  - window.sessionStorage

    - 类数组对象: 会话级数据存储,在浏览器进程分得内存中储存一次web会话数据,可供此次会话的页面共同使用,浏览器一旦关闭就消失

    - ### 作用

      - 在同个会话中所有页面之间共享数据

      - ```javascript
        sessionStorage[key]=val; 	//保存一个数据
        sessionStorage.setItem(key,val); 	//保存一个数据
        var val = sessionStorage['key']; 	//读取一个数据
        var val= sessionStorage.getItem('key'); 	//读取一个数据
        sessionStorage.removeItem('key'); 	//删除一个数据
        sessionStorage.length; 	 	//数据个数
        sessionStorage.key(i); 	//获取第i个key
        sessionStorage.clear()   //清空所有session缓存
        ```

  - window.localStorage

    - 本地存储对象(跨会话级存储)

    - 在浏览器能管理(磁盘)中存储用户数据,可以供此次会话和后续会话的页面共同使用;及时浏览器关闭了也不会消失--永久存在

    - ```javascript
      ocalStorage[key]=val; 	//保存一个数据
      localStorage.setitem(key,val); 	//保存一个数据
      var val = localStorage['key']; 	//读取一个数据
      var val= localStorage.getitem('key'); 	//读取一个数据
      localStorage.removeltem('key'); 	//删除一个数据
      localStorage.length; 	 	//数据个数
      localStorage.key(i); 	//获取第i个key
      ```

    - ### 注意

      - locaStorage若数据发生了修改,会触发事件 **window.onstorage**事件,可以监听事件,实现监听localStorage数据改变目的,但是不能监视 **sessionStorage**数据修改

​	