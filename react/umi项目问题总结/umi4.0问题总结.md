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

---

## umi4 路由传参

### 动态路由传参

`.umirc.ts` 配置路由传参

```ts
export default defineConfig({
    routes:[
        { path: "/:id", component: "login"},
    ]
})
```

在组件中获取参数

```react
import React from "react";
import {useParams} from "umi"

const Home:React.FC<any> = ()=>{
    const params  = useParams();
    /**
    * {
    *   "id": ""
    * }
    */
    return ( <div>hello world</div> )
}
```

**动态路由必须要携带参数**

### 查询字符串传参

路由传参示例

```react
import React from "react"
import {history} from "umi"
const pageA:React.FC<any> = ()=>{
    const handleClick = ()=>{
        history.push("/pageB?index=100")
    }
    return (<div onCLick={()=>handleClick()}>to PageB</div>)
}
```

接收查询字符串传参

```react
import React,{useEffect} from "react"
import {useSearchParams} from "umi"

const PageB:React.FC<any> = ()=>{
    const [searchParams, setSearchParams] = useSearchParams();
	
    useEffect(()=>{
        searchParams.get('index')  /*100*/
        setSearchParams({xxx:hello}) /*此时的地址变成了 /pageB?xxx=hello  */
    },[])
    return (<div>this is Page</div>)
}
```

---

## 路由鉴权

路由鉴权 `wrappers`，在 `umirc.ts` 中配置指定的路由进行鉴权

```ts
export default defineConfig({
    ...
    routes:[
        { path: "/", component: "home", wrappers: [
            '@/wrappers/auth',
        ]},
    ]
})
```

在 `src / wrappers / auth.tsx` 中可以书写鉴权逻辑

```tsx
/**登录鉴权 */
import { Navigate } from 'umi';

export const checkAuth = ()=>{
  const userInfo = localStorage.getItem("userInfo");
  if (userInfo) {
    return redirectToHome();
  } else {
    return redirectToLogin();
  }
}

export const redirectToLogin = ()=>{
  return <Navigate to="/login" />;
}

export const redirectToHome = ()=>{
  return <Navigate to="/home/userInfo" />;
}
```

上面代码中判断当进入到 `/home` 页面检查用户是否时登录状态，登录状态存在进入 `/home/userInfo` 否则进入 `/login` 页面， 这种路由鉴权的方式如果时在 `/logoin` 页面就判断用户登录状态实现免登录功能时，由于路由鉴权在路由进入后会一直校验跳转到本页面，就i会出现死循环的情况，下面推荐使用**函数式API做路由登录鉴权**

#### 登录鉴权

修改 ``src / wrappers / auth.tsx` ` 如下

```tsx
export const checkAuth = ()=>{
  const userInfo = localStorage.getItem("userInfo");
  if (userInfo) {
    return true;
  } else {
    return false;
  }
}
```

在要校验的函数组件中，通过 hook 的 useEffect 在组件初始化的时候校验登录状态，已 `login` 页面为例

```react
import React,{useEffect} from "react"
import { checkAuth } from "@/wrappers/auth";
import {history} from "umi"
const Login:React.FC<any> = ()=>{
    useEffect(()=>{
        if(!checkAuth()){
            /*表示登录状态已存在,直接跳转到home页面*/	
            history.replace("/home")
        }
    },[])
    return ( <div>this is loginPage</div> )
}
```

---

## 页面鉴权

页面同样采用登录鉴权的逻辑，不同的是页面鉴权需要高阶函数的方式去调用

示例，判断用户是否有权限访问该模块，实现鉴权逻辑 `src/wrappers/pageAuth.tsx`

```tsx
/**页面权限鉴权 */
import AuthComponent from "@/pages/auth";

export const PageAuth = (Component:React.FC)=>()=>{
  const userInfo = JSON.parse(localStorage.getItem("userInfo") || "");
  if ((userInfo as any).role !== "admin") {
    return <AuthComponent />
  }else{
    return <Component />;
  }
}
```

在上面代码中，如果用户不是管理员则使用鉴权组件提示用户无权访问，鉴权组件可以是一个普通的函数页面这里不在实现。

使用方法

```react
import React from "react"
import { PageAuth } from "@/wrappers/pageAuth";
const Home:React.FC<any> = ()=>{
    return ( <div>this is home</div> )
}
export default PageAuth(Home)
```

实现效果当用户角色是 admin 的时候进入 Home 组件，否则进入 AuthComponent 组件

