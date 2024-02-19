# umi4.0 项目构建问题总结

## 布局框架

umi4.0 版本中更新了布局方式,单独创建了 `layout/index.tsx` 文件为默认布局页面     

页面使用 `Outlet ` 组件作为子页面的表示子页面的视口，类似与 `router-view`

示例 

```react
import React from 'react';
import { Outlet } from 'umi'

const Page:React.FC<any> = ()=>{
    return (
    	<div>
        	<header>顶部</header>
            <main> <Outlet/> </main>
        </div>
    )
}
```

## 使用 dva

Umi Max 集成了 dva 工具，安装 umi 集成工具

```shell
pnpm install @umijs/plugins --save-dev
```

创建 `models / index.ts` 思路和 paina 类似，每一个文件需要给与单独的命名空间

```ts
export default {
  namespace: 'menuModel',
  state: {
    menuIndex:localStorage.getItem('menuIndex') || '1', /*状态值*/
  },
  reducers: {
    menuIndexConfig(state:any, params:any ) { /*改变 state 属性的值*/
      return { ...state,menuIndex:params.menuIndex};
    },
  },
};
```

在组件使用，函数式中采用高阶函数调用

```react
import {connect} from "umi"
/*此时的 menuModel 为 dva 文件的命名空间名称*/
const MenuComponent:React.FC<any> = ({dispatch, menuModel}) => {
    const handleClick = ()=>{
        dispatch({type:"menuModel/menuIndexConfig",payload:{menuIndex:2}})
    }
	return (
    	<div>
        	<span>{menuModel.menuIndex}</span>
        	<button onClick="{()=>handleClick()}">测试</button>
        </div>
    )    
})

export default connect(({menuModel})=>({
  menuModel
}))(MenuComponent)
```

---

## 路由创建

umi4.0 路由搭建 类似于 vue-router 路由模式, 丢弃了早期 react 的标签式路由, 采用对象式, 

示例 `umirc.ts` 中配置路由

```ts
export default defineConfig({
    routes: [
        { path: "/", component: "login"},
        { 
          path: "/layout", 
          component: "@/layout/index", /*布局页面路由声明*/
          routes: [
            {path:"/layout/",component:"user"},/*/layout/ 表示默认显示的子路由*/
            {path:"/layout/node",component:"nodeList"},
          ]
        },
      ]
})
```

---

## 使用 ant-design 

### 安装

```shell
pnpm i @umijs/plugins -D
pnpm i antd axios @ant-design/pro-layout -S
```

### 调用

```react
import React from "react";
import { Input,Button,Checkbox,message } from 'antd';

const Home:React.FC<any> = ()=>{
   return (
       <div>
       		<Button>andButton</Button>
       </div>
   )
}

```

---

## 部署 history 模式

### 问题一: umi4 打包后页面空白,并且部分静态资源404

在 `.umirc.ts` 中配置

```ts
export default defineConfig({
    base:"/myUmiProject", /*基础地址，适用于部署在非根目录，要和服务端目录名称保持一致*/
    publicPath::process.env.NODE_ENV === 'production' ? './' : '/', /*用于解决部分静态资源404问题*/
})
```

在 `nginx.conf` 中配置

```nginx
http {
    ...
    server {
        ...
        location /myUmiProject/ {
            try_files $uri $uri/ /index.html;
		}
        ...
    }
   
}
```

配置完成重载 `nginx` ，运行  `nginx -s reolad`  ; 此时打包后的项目路径应该放在 `nginx/html/myUmiProject` 目录中

### 问题二：umi4 报错找不到 favicon 图标 

在 umi4 中项目并没有 favicon 图标资源，需要单独在 `.umirc.ts` 配置

```ts
export default defineConfig({
    ...
    favicons:[ /*可引用在线图标作为标签页的icon*/
        "https://img.alicdn.com/tfs/TB1YHEpwUT1gK0jSZFhXXaAtVXa-28-27.svg" /*该图标地址为 umi 官方图标
        */
    ],
})
```



