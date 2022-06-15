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

## 天坑三：跨域问题

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

