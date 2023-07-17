# electron快速上手

## 快速上手

```shell
yarn init 
yarn add nodemon electron -D
```

配置 package.json 

```json
{
	  "name": "demo",
      "version": "1.0.0",
      "description": "",
      "main": "main.js", // electron 主进程文件
      "scripts": {
        "dev": "nodemon --exec electron ."
      },
      "keywords": [],
      "author": "",
      "license": "ISC",
      "dependencies": {
        "electron": "^25.3.0",
        "nodemon": "^3.0.1"
      }
}
```

创建 main.js

```js
const {app,BrowserWindow} = require("electron");
const path = require("path");
const createWindows = ()=>{
  const mainWindow = new BrowserWindow({
    width:200,
    height:200,
    alwaysOnTop:false, // 是否置顶
    x:0, // 窗口的x轴位置
    y:100, // 窗口的y轴位置
    frame:false, // 隐藏标题栏
    transparent:true, // 背景透明
  })
  // 网页套壳
  // mainWindow.loadURL("http://www.gjweb.top")
  // 解析项目文件
  mainWindow.loadFile(path.resolve(__dirname,"index.html"))

  // 自动打开开发者工具
  mainWindow.webContents.openDevTools()
}
app.whenReady().then(()=>{
  createWindows(); // 创建窗口
})
```

创建 index.html 视图

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <!-- 安全策略 -- 只有自身可以调用主进程对系统进行操作 技术栈CSP -->
  <meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self'" />
  <meta charset="UTF-8">
  <title>客户端</title>
  <style>
    html{
      /* 点击DOM可拖动 */
      -webkit-app-region:drag 
    }
    textarea{
      /* 文本域禁止拖动 */
      -webkit-app-region:no-drag
    }
  </style>
</head>
<body>
    <h1>hello electron</h1>
</body>
    <!--创建渲染进程-->
	<script src="./render.js"></script> 
</html>
```

创建渲染进行 render.js

```js
console.log("渲染线程已创建")
```

总结: electron 中 main.js 作为主线程主要对系统相关操作(只能有一个主线程), index.html 页面(可以有多个页面分到不同的窗口和路由), render.js 渲染线程可以操作浏览器对象(可以有多个渲染进程) 

---

## 主进程通信

主要思路: 主进程（main.js） ---> 预加载处理器（ preload.js）---> 渲染进程 （render.js） 反向同理

考虑安全原因 preload.js 只能使用部分的 node 和 electron API； 需要在 main.js 中配置给相应的权限。

主进程 和 渲染进程 也可直接通信（处于安全考虑不推荐）

### 配置 main.js 

```js
const { ipcMain, ipcRenderer } = require("electron");
const mainWindow = new BrowserWindow({
   	...
    /**指定预加载脚本，用于主线程和渲染线程通信 */
    webPreferences:{
      preload:path.resolve(__dirname,'preload.js'),
      nodeIntegration:true, // 同意 预处理进程使用 node 的模块
    }
  })
 
 /**主进程 和 预加载文件通信 */
ipcMain.on("saveData",()=>{
  console.log("触发了saveData");
})
```

### 配置预处理器中 preload.js

```js
const { ipcMain, ipcRenderer } = require("electron");
const { contextBridge } = require("electron");
ipcRenderer.send("saveData") // 预处理器主动像主进程通信

/**
 * 创建供渲染进程访问的函数 
 * 使用渲染进程可以直接通过 window 获取 api 里面的函数方法
 * */
contextBridge.exposeInMainWorld('api',{
  getSaveData:()=>{
    ipcRenderer.send("saveData")
  }
})

/*预处理器操作渲染进程进行dom操作*/
window.addEventListener('DOMContentLoaded',()=>{
  for(const app of ['chrome','electron','node']){
    console.log(app);
    const el = document.querySelector(`#${app}`)
    el.innerHTML = `${app}  版本号 ${process.versions[app]}`
  } 
})
```

### 在渲染进程中 render.js

```js
console.log(window); // window示例可以打印出 api 对象的方法
window.api.getSaveData() // 通过预处理器通知主进程
```

总结： preload 预处理器进程是 主进程 和 渲染进程 的桥梁， preload 既可以操作部分的 node API 也可以调用 DOM 内容。

---

