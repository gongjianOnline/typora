# electron项目模板& 进程间通信

## 项目构建

技术选型：electronV19.0.4  / vue3.0 / vite 

项目构建模板： [github下载](https://github.com/gongjianOnline/electron-vite/tree/Template)

运行

```shell
// 运行
yarn electron:serve
// 打包
yarn electron:build
```

---

## electron 进程通信

### 主进程同渲染进程之间通信  ipcRenderer  & ipcMain

index.vue 渲染进程

```vue
<template>
	<button @click="handelClick">向主进程发送消息</button>
</template>
<script>
const {ipcRenderer} = require("electron")
export default {
    name:"index",
    setup(){
        const handelClick = ()=>{
            ipcRenderer.send("message","天王盖地虎")
        }
        ipcRenderer.on("mainMessage",(event,arg)=>{
            console.log(arg) // "宝塔镇河妖" 
        })
        return{
            handelClick
        }
    }
}
</script>	
```

electron / main.js 主进程

```vue
const {ipcMain} = require("electron");
ipcMain.on("message",(event,arg)=>{
	console.log(arg) // "天王盖地虎"
	event.sender.send("mainMessage","宝塔镇河妖")
})
```

### 渲染线程同渲染线程通信  webContents 

- **在electron中 每一个新的窗口都是一个渲染进程**
- **渲染线程之间使用VUEX的数据管理工具是相互独立的，即A窗口操作Vuex数据 B窗口监听和获取都是无效的**

A.vue 渲染进程

```vue
<template>
	<button @click="handelClick">向B线程通信</button>
</template>
<script>
const {ipcRenderer} = require("electron")
export default {
    name:"index",
    setup(){
        const handelClick = ()=>{
            ipcRenderer.send("message","天王盖地虎")
        }
        return{
            handelClick
        }
    }
}
</script>
```

main.js 主进程

```javascript
const {app,BrowserWindow,ipcMain} = require("electron");
ipcMain.on("message",(event,data)=>{
    /** 这里的 mainWindow 是主窗口的实例（B.vue）*/
    mainWindow.webContents.send("mainMessage",data)
})
```

B.vue 渲染线程

```vue
<template></template>
<script>
const {ipcRenderer} = require("electron")
export default {
    name:"index",
    setup(){
        ipcRenderer.on("mainMessage",(event,arg)=>{
         	console.log(arg) // 天王盖地虎
    	})
    }
}
</script>
```

