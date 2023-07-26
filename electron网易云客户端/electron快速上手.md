# electron快速上手

## 快速上手

当前版本为官网最新版本 V25.3.0

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
  mainWindow.loadFile(path.resolve(__dirname,"index.html"));
  // 控制窗口缩放比例 1：1
  mainWindow.setAspectRatio(1);
  // 自动打开开发者工具
  mainWindow.webContents.openDevTools();
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

## 主进程主动向渲染进程通信

main.js

```js
const mainWindow = new BrowserWindow({
   	...
    /**指定预加载脚本，用于主线程和渲染线程通信 */
    webPreferences:{
      preload:path.resolve(__dirname,'preload.js'),
      nodeIntegration:true, // 同意 预处理进程使用 node 的模块
    }
 })
 mainWindow.loadFile(path.resolve(__dirname,"index.html"))
/*mainWindow 表示当前窗口的对象,通过 webContents 可以获取渲染进程中的对象(获取通信的方式也是通过预处理器来传递的,所有 webContents 也可以调用到 preload 中的内容)*/
mainWindow.webContents.send("toRender",100);
```

preload.js

```js
const { contextBridge,ipcRenderer } = require("electron");
/**
 * 创建供渲染进程访问的函数 
 * 使用渲染进程可以直接通过 window 获取 api 里面的函数方法
 * */
contextBridge.exposeInMainWorld('api',{
  toRender:(callback)=>{
    ipcRenderer.on('test', callback)
  }
})
```

render.js

```js
  const testFun = (event,value)=>{
    testContainer.innerHTML = Number(testContainer.innerHTML) + value;
  }
  window.api.toRender(testFun)
```

### webContents 问题

#### webContents 和 fromWebContents 有啥区别 

​	webContent 是 electron 中的一个类，用于控制和操作渲染进程的内容，常用来与渲染进程通信、导航、执行 JS 代码、注入 css等。可以通过`BrowserWindow`实例的`webContents ` 对象来访问当前窗口对象，例如 `mainWindow.webContents` 将返回 `mainWindow` 的窗口对象

​	formWebContents 是 BrowserWindow 的静态方法, 接受一个 `webContents` 对象为参数, 返回与 `webContents` 对象关联的 `BrowserWindow` 实例。 例如 `BrowserWindow.fromWebContents(webContents)`将返回与给定`webContents`对象关联的`BrowserWindow`实例。这个方法通常用于在主进程中查找与特定`webContents`对象相关联的窗口。

​	总结 `fromWebContents` 方法用于在主进程中查找指定的 `webContents` 对象相关的窗口；webContents 用于控制操作渲染进程对象。



---

## 双向通信

### 基础版

主进程 main.js 

```js
const createWindow = ()=>{
  const mainWindow = new BrowserWindow({
   ...
    webPreferences:{
      preload:path.resolve(__dirname,'preload.js'),
      nodeIntegration:true, // 同意 预处理进程使用 node 的模块
    }
  })

  mainWindow.loadFile(path.relative(__dirname,"index.html"))
}
app.whenReady().then(()=>{
  createWindow()
})

ipcMain.on("mainData",(event,value)=>{
  console.log("mainData",value);
  /*获取当前窗口示例，向渲染进程通信*/
  BrowserWindow.fromWebContents(event.sender).send("toRender","向渲染进程返回消息")
})

```

预处理器 preload.js

```js
const {ipcRenderer,contextBridge} = require("electron");
/**
 * 创建供渲染进程访问的函数 
 * 使用渲染进程可以直接通过 window 获取 api 里面的函数方法
 * */
contextBridge.exposeInMainWorld("api",{
  getMainData:()=>{
    ipcRenderer.send("mainData","向主进程发送信息")
  }
})
/*接受主进程的信息*/
ipcRenderer.on("toRender",(event,value)=>{
  console.log(value)
})
```

### invoke双向通信

main.js

```js
ipcMain.handle("test",(event,value)=>{
    console.log(value);
    rteurn "invoke 主进程向渲染进程通信"
})
```

preload.js

```js
contextBridge.exposeInMainWorld("api",{
    setTest:(value)=>{
        return ipcRenderer.invoke("test",value); // invoke 返回 promise 对象
    }
})
```

render.js

```js
 const result = await windos.api.setTest("渲染进程向主进程通信")
 console.log(result)
```

---

## 进程隔离与沙盒模式

### 进程隔离

在 electron 中预处理器会默认隔离主进程和渲染进程，例如

```js
contextBridge.exposeInMainWorld("api",{
    test:()=>{
        console.log("this is test")
    }
})
```

如上面代码 在 preload 预处理器文件中需要这样注册后才能在 渲染进程中 通过 window.api 进行调用， 如果想在 preload 文件中直接通过增加 window 示例的方式，需要接触默认隔离，**解除隔离后渲染进程 和 预处理进程 都可以使用 node 的全部模块**

配置方式如下：main.js 文件

```js
const createWindow = ()=>{
  const mainWindow = new BrowserWindow({
   ...
    webPreferences:{
      preload:path.resolve(__dirname,'preload.js'),
      nodeIntegration:true, // 同意 预处理进程使用 node 的模块
      contextIsolation:false, // 管理隔离状态
    }
  })
  mainWindow.loadFile(path.relative(__dirname,"index.html"))
}
```

如上配置后 preload 预处理文件可以直接对 window 实例添加方法，共渲染进程使用，代码如下

```js
// preload.js
window.api = {
    test:()=>{
        console.log("this is test")
    }
}

// render.js 渲染进程
window.api.test();
// main.js 主进程也可通过 global 对象调用
global.api.test()
```

### 沙盒模式

开启沙河模式后 只有 预处理文件 （preload.js）可以用使用 node 的全部模块（相比接触进程隔离更加安全）

配置窗口对象

```js
const createWindow = ()=>{
  const mainWindow = new BrowserWindow({
    ...
    webPreferences:{
      preload:path.resolve(__dirname,'preload.js'),
      nodeIntegration:true,
      contextIsolation:false, // 开启沙盒模式
    }
  })
  ...
}
```

---

## 动态窗口尺寸

```js
mainWidow.center() // 窗口居中
mainWidow.setBounds({
    width:
    height:
    x:
    y:
},true) // 开启动画
```

---

## 菜单管理

创建 menu.js 统一管理菜单

**值得注意的在 mac 平台中 第一个菜单为软件本身的示例，需要单独兼容**

```js
const {Menu} = require("electron");
const createMenu = ()=>{
  const menu = [
    {
      label: 'File', // 一级菜单名称
      submenu: [ // 子菜单对象
        { 
          label: 'New',  // 二级菜单名称
          accelerator: 'CmdOrCtrl+N',  // 快捷键
          click: () => { /* 处理点击事件 */ }  // 自定义事件
        },
        { 
          label: 'Open', 
          accelerator: 'CmdOrCtrl+O', 
          click: () => { /* 处理点击事件 */ } 
        },
        { 
          type: 'separator'  // 分割线
        },
        { 
          label: 'Quit', 
          accelerator: 'CmdOrCtrl+Q', 
          click: () => { /* 处理点击事件 */ } 
        }
      ]
    },
    // 其他菜单项...
  ];
  const applicationMenu = Menu.buildFromTemplate(menu);
  Menu.setApplicationMenu(applicationMenu);
}

module.exports = createMenu;
```

在 main.js 中引用创建菜单

```js
const {app,BrowserWindow, ipcMain} = require("electron");
const createMenu = require("menu.js")
app.whenReady().then(()=>{
    ...
    createMenu() // 创建菜单
})
```

---

## 右键菜单

在预加载进程中监听 dom 的右键事件

```js
/*preload.js*/
const {ipcRenderer} = require("electron");
// 全局监听右键点击事件
window.addEventListener("contextmenu",()=>{
  console.log("右键测试");
  ipcRenderer.send("handelContextmenu")
})
```

在主进程中通信中创建右键菜单

```js
/**创建右键菜单 */
const {ipcMain, Menu, BrowserWindow} = require('electron')

ipcMain.on("handelContextmenu",(event)=>{
  const template = [
    {
      label:"退出",
      click:()=>console.log("退出应用")
    }
    { type: 'separator' }, //菜单分割线
  ]
  const menu = Menu.buildFromTemplate(template)
  menu.popup(BrowserWindow.fromWebContents(event.sender))
})
```

---

## dialog 弹窗

**在只能在主进程中使用, 详细配置见官方文档**

### [信息窗](https://www.electronjs.org/zh/docs/latest/api/dialog#dialogshowmessageboxbrowserwindow-options)

```js
const {dialog} = require("electron")
/*
* result 返回一个对象 
*  response 表示 buttons 中点击的下标
*  checkboxChecked 表示 checkboxLabel 属性是否被勾选
*/
const result = await dialog.showMessageBox({
	title:'提示',
	detail:'确认要退出吗',
	buttons:["取消","确定"],
    cancelId:0, // 按下 esc 时走 取消 选项
    checkboxLabel:"记住我的选择"
})
```

### [错误弹窗](https://www.electronjs.org/zh/docs/latest/api/dialog#dialogshowerrorboxtitle-content)

```js
dialog.showErrorBox("温馨提示","下次将不在提示退出弹窗")
```

### [选择文件](https://www.electronjs.org/zh/docs/latest/api/dialog#dialogshowopendialogbrowserwindow-options)

```js
ipcMain.handle("checkFile",async (event)=>{
  /*选择文件，返回选中文件的绝对路径*/
  const {filePaths} = await dialog.showOpenDialog({
    title:"选择文件",
    filters:[{name:"files"}],
    properties:["openFile","multiSelections"]
  })
  return filePaths
})
```

### [保存文件](https://www.electronjs.org/zh/docs/latest/api/dialog#dialogshowsavedialogbrowserwindow-options)

```js
/*返回保存文件的路径*/
const {filePath} = await dialog.showSaveDialog({
    title:'保存文件'
})
/*使用 node fs 模块将一个文件内容写入到另一个文件中*/
fs.readFile(fliePath,'utf8',(err,data)=>{
    if(err){throw err};
    // 写入目标文件
    fs.writeFile(filePath,data,"utf8",(err)=>{
      if(err){throw err}
      dialog.showMessageBox({
        title:'提示信息',
        detail:"文件成功写入"
      })
    })
  })
```

---

## 托盘图标设置

规范性: mac 系统用中图标为黑白色, windows 系统可以为彩色; 尺寸: 32*32 144dpi

```js
/*模块抽离 使用 createTray.js*/
const {Menu,tray} = require("electron");
cons {resolve} = require("path");
const createTray = ()=>{
  // 设置托盘图标的图片 需要放在 electron 项目的 resources 文件下面
  const tray = new Tray(resolve(__dirname,"../../resources/icon.png"));
  // 设置托盘图标右键的菜单，菜单属性同 Menu 配置一致
  const contextMenu = Menu.buildFromTemplate([{ label: '退出', role: 'quit' }])
  // 鼠标悬停到托盘图标时的提示信息
  tray.setToolTip('hello electron')
  tray.setContextMenu(contextMenu)
}
module.exports = {createTray}
```

在 mian.js 中调用

```js
const {app} = require("electron");
const {crateTray} = require("createTray.js")

app.whenReady().then(()=>{
    createTray()
})
```

---

## 状态栏图标设置

```js
/*windos中在 BrowserWindow 示例中配置*/
new BrowserWindow({
    skipTaskbar:flase,// 隐藏托盘图标
})
/*在 mac 在 whenReady 中配置*/
app.whenReady().then(()=>{
    app.dock.hide() // 隐藏托盘图标
})
```

---

