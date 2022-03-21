# vite+vue3配置接口代理

在接口联调的时候，配置接口代理，按照网上的主流代理配置代码如下: vite.config.js

```javascript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
export default defineConfig({
  server:{
    port:8080,
    host:"localhost",
    proxy:{
      "/api":{
        target: 'http://localhost:3000',
      }
    }
  },
  plugins: [vue()]
})

```

这样配置后会发现，接口会报404错误；但是同等配置在vue2.0上面配置代理就是好的，经过不断尝试后发现，vite配置的接口代理，默认**不会替换 /api** 这一层，也就说我们本想请求 "/user",但代理后会变成 "/api/user"; 在vue2.0中我们可以pathRewrite:{"^/api":""}。此种方法在vite中是无效的，下面直接改为支持vite的代理方式

```javascript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
// https://vitejs.dev/config/
export default defineConfig({
  server:{
    port:8080,
    host:"localhost",
    proxy:{
      "/api":{
        target: 'http://localhost:3000',
        changeOrigin: true,
        rewrite:(path) => path.replace(/^\/api/,'') 
      }
    }
  },
  plugins: [vue()]
})
```



