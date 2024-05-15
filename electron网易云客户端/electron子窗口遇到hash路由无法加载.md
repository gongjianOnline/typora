# electron 子窗口遇到 hash 路由无法加载

场景描述：在开发桌面桌面端应用时会结合 vue 或者 react 等框架，如果在单页面应用中指定子窗口页面路由在生产环境下时无法加载。

经过验证发现 electron 对框架中路由的 `History` 模式无法解析，只能使用 `hash` 模式

示例 

创建子窗口，在开发环境和生产环境指定路由

```ts
import { is } from '@electron-toolkit/utils';
import { BrowserWindow } from 'electron'
import path from 'path';
import url from "url";
const createNewWindow = ()=>{
  let newWindow = new BrowserWindow({
    width: 400,
    height: 300,
    frame:false, // 隐藏标题栏
    webPreferences: {
      nodeIntegration: true
    }
  });
  if (is.dev && process.env['ELECTRON_RENDERER_URL']) {
    /*开发环境访问地址 localhost:prot/#/repair*/
    newWindow.loadURL(`${process.env['ELECTRON_RENDERER_URL']}/#/repair`) 
  } else {
    /**生产环境中指定路由，绝对路径的方式*/
    const newUrl = url.format({
      pathname: path.join(__dirname, '../renderer', 'index.html'),
      protocol: 'file:',
      slashes: true,
      hash: 'repair'
    });
    newWindow.loadURL(newUrl);
  }
  // newWindow.webContents.openDevTools(); // 可选，打开开发者工具
}

export  default createNewWindow;
```

