# electron 数据持久化 electron-store

安装

```shell
npm install electron-store
```

封装持久化 storeIPC 文件

```ts
import {ipcMain} from "electron"
import Store from  "electron-store"
const store = new Store();


/**设置store */
ipcMain.on("setStore",(_,key,value)=>{
  store.set(key, value)
})

/**获取store */
ipcMain.handle("getStore",(_,key)=>{
  return store.get(key)
})

export const setStore = (key:string,value:any)=>{
  store.set(key, value)
}

export const getStore = (key:string)=>{
  return store.get(key)
}
```

支持视图层 和 应用层的数据持久化