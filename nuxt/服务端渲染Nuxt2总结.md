# 服务端渲染Nuxt2总结

## 创建项目

```shell
yarn create nuxt-app <project-name>
```

---

## 引入第三方UI

以 Element UI 为例

```shell
yarn add element-ui -S
```

在 plugins / element-ui.js

```js
import Vue from "vue"
import Element from "element-ui"
Vue.use(Element)
```

配置 nuxt.config.js 

```js
plugin:[
    "@/plugins/elemen-ui.js"
],
css:[
    "element-ui/lib/theme-chalk/index.css"
    /*这里的css可以是从 static 文件中引入的外部css*/
]
```

---

## 引入 axios 

```shell
yarn add @nuxtjs/axios
```

在 nuxt.config.js 配置

```js
modules: [
    "@nuxtjs/axios",
],
axios: {
    proxy: true,
    prefix: '/api',/*url拼接的前缀,部署时候可以改成线上的地址*/
    credentials: false
},
proxy: {
    '/api': {
      // 目标接口域
      target: 'http://127.0.0.1:3004',
      // 全局配置是否跨域
      changeOrigin: true,
      pathRewrite: {
        // 单个配置是否跨域
        // changeOrigin: true
        // 把 '/api' 替换成 '/'，具体需要替换为 '' 还是 '/' 看自己习惯
        '^/api': ''
      }
    }
},
build: {
  vendor: ['axios']
},
```

使用

```js
this.$axios({
    methods:'post',
    url:"",
    params:{},
    data:{}
}).then((response)=>{})
```

## 引入 day.js

```shell
yarn add @nuxtjs/dayjs
```

配置 nuxt.config.js

```js
  modules: [
    '@nuxtjs/dayjs'
  ],
  dayjs: {
    locales: ['en', 'ja'],
    defaultLocale: 'en'
  }
```

使用

```js
this.$dayjs().format('YYYY-MM-DD')
```

---

## 静态路由与动态路由

### 路由跳转

```vue
<NuxtLink to="path"></NuxtLink>
```

### 静态路由

在 page 文件中创建的目录结构默认为静态路由的路径

### 动态路由

使用 下划线 + 名称 创建的文件为动态路由；例如 ：_id.vue `pages/search/_id.vue`

### 获取动态路由的参数

```vue
<template>
	<div>{{id}}</div>
</template>
<script>
export default {
    /*nuxt的生命周期*/
    asyncData({params}){
        const id = params.id;
        return {id}
    },
    /*或者在生命周期中*/
    mounted(){
        console.log(this.$route.params.id)
    }
    
}
</script>
```

---

## 布局

在 layouts 文件中创建 default.vue 和 error.vue 分别用于默认的布局和异常页面跳转的布局页面

创建 default.vue

```vue
<template>
	<div>
    	<header>头部模块</header>
        <main> <Nuxt/> </main>
        <footer>尾部模块</footer>
    </div>
</template>
```

使用 <Nuxt/> 模块嵌套 page 中的页面

### 自定义布局文件

在 layouts 中创建 new_layout.vue 文件

在 pages 文件中 index.vue 中配置

```vue
<script>
export default {
	layout:"new_layout"
}
</script>
```

---

## 部署

## 静态部署

运行 `yarn generate` 打包生成 dist 文件夹，会生成静态资源，可直接在 ngixn 部署

## 动态部署

运行 `yarn build` 创建 .next 文件

```javascript
/*将一下文件夹上传到服务器*/
.next
static
nuxt.config.js
package.json
```

在项目目录中运行 `npm install` 安装项目中所需依赖项，运行 `npm start` ,可访问该项目

### PM2 托管

```shll
pm2 start npm --name "项目别名" -- run start 
```

### 通过ip访问

```json
"start": "nuxt start --hostname 0.0.0.0 --port 8080", /*端口号 8080 ，映射ip --hostname*/
```

