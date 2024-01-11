# electron打包部署和开发问题填坑集合

## 填坑一 ： electron打包运行为空白

vue项目构建完成默认为 History ，此时的路由模式在本地运行时没问题的，打包后客户端会出现空白现象；原因是匹配不到路径，将将路由模式改为 **hash** 模式既可

router.js

```js
// 导入 router
import { createRouter, createWebHistory,createWebHashHistory } from 'vue-router'
// 导入路由各页面配置
import routes from './routes'
// 配置router对象
const router = createRouter({
    history:createWebHashHistory(), // 更改为hash模式
    routes
})
export default router
```

---

## 填坑二： 页面数据资源共享

场景一： 在同一个渲染线程下

- 推荐使用Vuex 或者 Pinia
- eventBus通信方式在 electron 中运行时无效的

场景二：在不同渲染线程中数据共享

- 需要借助 主线程 实现（有单独的文章简介实现步骤）

---

## 填坑三：跨域问题

可以直接配置vue中的proxy代理解决，方法同平时vue项目的配置相同

在 vite.config.js 中

```javascript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

// https://vitejs.dev/config/
export default defineConfig({
  base:"./",
  plugins: [vue()],
  server:{
    proxy:{
      "/http":{
        target: 'http://localhost:3001',
        changeOrigin: true,
        rewrite:(path) => path.replace(/^\/http/,'')
      }
    }
  }
})

```

区分生产和开发环境 main.js

```javascript
import axios from "axios"
app.config.globalProperties.$axios = axios;
if(process.env.NODE_ENV === "development"){
  app.config.globalProperties.$http = "/http";
}else{
  app.config.globalProperties.$http = "http://localhost:3001";
}
```

vue3调用全局上下文

```vue
<script>
imoprt {getCurrentInstance} from "vue"
export default{
    setup(){
        const {proxy} = getCurrentInstance();
        proxy.$axios ---> 指向全局的 $axios
        proxy.$http ---> 指向全局的 $http
    }
}
</script>
```

### 使用 electron-vite 构建的跨域问题

在 electron.vite.config.ts

```ts
import { resolve } from 'path'
import { defineConfig, externalizeDepsPlugin } from 'electron-vite'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  main: { /*主进程配置*/
    plugins: [externalizeDepsPlugin()]
  },
  preload: { /*中间层配置*/
    plugins: [externalizeDepsPlugin()]
  },
  renderer: { /*渲染层配置*/
    resolve: {
      alias: {
        '@renderer': resolve('src/renderer/src')
      }
    },
    plugins: [vue()],
    server:{ 
      proxy:{ /*本地服务代理配置*/
        // 接口地址代理
        '/api': {
          target: '',
          secure: false,
          changeOrigin: true,
          rewrite: path => path.replace(/^\/api/, '')
        },
      }
    }
  }
})

```

---

## 填坑四: 打包时因网络导致拉取不到部分git项目导致的打包失败

通常报错 winCodeSign \ nsis \ nsis-resources 拉去失败

解决方法：手动下载相关包放到指定目录中，下载地址查看报错信息中的 github 地址

```shel
C:\Users\YOU_USER_NAME\AppData\Local\electron-builder\Cache\nsis\nsis-3.0.4.1
C:\Users\YOU_USER_NAME\AppData\Local\electron-builder\Cache\nsis\nsis-resources-3.4.1
C:\Users\15031\AppData\Local\electron-builder\Cache\winCodeSign\winCodeSign-2.6.0
```

**当完成一项配置后需要手动删除项目中的 out 和 dist 目录，重新执行打包命令**

