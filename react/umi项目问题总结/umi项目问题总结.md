# umi项目问题总结

## 起步

```shell
yarn create @umijs/umi-app
// or
npx @umijs/create-umi-app
```

## umi 约定式路由

umi 提供了约定式理由写法即创建文件自动转化为路由

在 src / pages 中创建文件 umi 会自动转换为页面的路由, 默认为  index.tsx 为首; 默认 404.tsx 为报错页面

### 路由嵌套

![](https://pic1.imgdb.cn/item/6365ba0f16f2c2beb1d4c808.png)

如上图所示在 pages 文件夹上面 创建 _layout.tsx 用于布局文件

```tsx
import React from "react"
const Home:React.FC<any> = (props)=>{
  return  (
    <div>
      {props.children} // 这里的 children 指的是页面的子路由
    </div>
  )
}
export default Home
```

创建 index.tsx 作为 /home 路由的默认入口,其他子路由可以任务命名, 在浏览器访问 localhost:8000/home/xxx

---

## umi 编程式导航

需要引入 umi 内置的 history 对象进行编程式导航的跳转

```tsx
import {history} from "umi"
const App = ()=>{
    const handelClick = ()=>{
        history.push('/xxx')
    }
    return (
    	<button onClick={handelClick}>click</button>
    )
}
```

---

## dva 状态管理

创建 models / user.js 文件 注意 **文件名就是命名空间**

```js
export default {
  namespace: 'user',
  state: {
    userName: userInfo ? userInfo.username : ''
  },
  reducers: {
    userConfig(state, { payload }) {
      return { ...state, ...payload };
    },
  },
};
```

在组件中监听或修改 user 中的状态

```tsx
import React from "react"
import { connect, useDispatch } from 'dva';
const Component:React.FC<any> = (props)=>{
    const distPath = useDispatch()
    handelClick(){
        distPath({
            type:'user/userConfig',
            payload:{
                userName:"admin"
            }
        })
    }
    return (
    	<div>
            <h2>this is {props.userName}</h2>
        	<button onClick={()=>handelClick()}>修改username</button>
        </div>
    )
}
export defalut connect((state:any)=>state.user)(Component)
```

---

## 使用 styled-components 

### 安装

```shell
yarn add styled-components -S
```

### 使用

```tsx
import styled from "styled-components"
const Wrapper = styled.div`
	css
`
const Wrapper = styled(组件名称)`
	css
`
const APP = ()=>{
    return (
        <Wrapper></Wrapper>
    )
}
export defalut APP
```

---

## umi 反向代理

在 .umirc.js 文件中配置

```js
proxy:{
    "/api":{
        target:"http://www.baidu.com",
        pathRewrite:{"^/api":""},
        changeOrigin:true
    }
}
```

---

## umi 中使用阿里图标 SVG

在搜了全网的关于引入阿里图表的方式，大部分都使用的字体图标。 但是觉得这种方式并不太优雅。下面是用umi 中引入阿里 SVG 的用例

1. 将要是用的图标从官网下载SVG到本地
2. 在 umi 项目的根目录 创建 public 文件夹 用于存放静态资源文件
3. 在 .umirc.ts 中配置页面加载 js 文件

```javascript
headScripts: [
    {
      src: '/js/iconfont.js', // 这里的路径不需要包含 public 文件
    },
 ],
```

4. 将 icon 封装成组件

```tsx
import React from 'react';
import styled from 'styled-components';

const SVG = styled.svg`
  width: 2rem;
  height: 2rem;
  overflow: hidden;
  color: #fff;
`;
interface Props {
  iconName: String;
  [propsName: string]: any;
}

const Icon: React.FC<Props> = (props) => {
  return (
    <SVG aria-hidden="true">
      <use xlinkHref={`#${props.iconName}`}></use>
    </SVG>
  );
};
export default Icon;

```

---

## umi修改浏览器 title 和 ICON

在 .umirc.ts 文件中添加配置

```tsx
title:'龚箭图床',
links:[
    {rel:'icon',href:""}
],
```

---

## umi 更改路由模式

在 .umirc.ts 文件中添加配置

```tsx
history:{
    type:'hash'
},
```

