# axios的二次封装及接口管理

## 安装

```javascript
npm install axios qs
```

---

## 封装 axios

创建 axios.js 文件 对 axios进行封装

```javascript
import axios from "axios"

// 根据环境变量区分接口的默认地址
switch (process.env.NODE_ENV) {
  case "development":
    axios.defaults.baseURL = "开发环境的地址";
    break
  case "production":
    axios.defaults.baseURL = "生产环境的地址";
    break
}

// 设置超市时间和跨域是允许携带凭证
axios.defaults.timeout = 50000;
axios.defaults.withCredentials = true;

// 设置请求拦截器
axios.interceptors.request.use((config)=>{
  return config
},(error)=>{
  return Promise.reject(error)
})

// 设置响应拦截器
axios.interceptors.response.use((response)=>{
  return  response.data
},(error)=>{
  if(error.response){
    switch(error.response.status){
      case 401:
        alert("没有权限")
        break
      case 403:
        alert("服务器拒绝执行")
        break
      case 404:
        alert("找不到页面")
        break   
    }
  }else{
    // 服务器连接结果没有返回
    if(!window.navigator.onLine){
      alert("断网处理,可以跳转到断网页面")
      return
    }
    return Promise.reject(error)
  }
})
export default axios;
```

---

## 封装请求方式

创建 http.js 文件 封装各种请求的方式及数据格式

```javascript
import axios from "./axios"
import qs from "qs"

export function post(url, data, error) {
  return new Promise((resolve, reject) => {
    axios({
      method: "post",
      url: url,
      params:data,
    }).then((response)=>{
      resolve(response)
    },(err)=>{
      err = error?error:err;
      alert(err)
    })
  })
}

export function get(url,data,error){
  return new Promise((resolve,reject)=>{
    axios({
      methods:"get",
      url:url,
      params:data,
    }).then((response)=>{
      resolve(response)
    },(err)=>{
      err = error?error:err
      alert(err)
    })
  })
}

export function FormDataPost(url,data,error){
  return new Promise((resolve,reject)=>{
    axios({
      method:"post",
      url:url,
      data:data,
      headers:{"Content-Type":"application/x-www-from-urlencoded"},
      transformRequest:(data)=>{
        qs.stringify(data)
      }
    }).then((response)=>{
      resolve(response)
    },(err)=>{ 
      err = error?error:err
      alert(err)
    })
  })
}

```

---

## 建立统一的请求入口

创建 api.js 文件,建立统一的请求入口

```javascript
// 统一数据请求入口
import vote from "./vote"
import xxx from "./xxx"
export default {
  vote,
  xxx
}
```

---

## 按需拆分接口文件

创建 vote.js (文件名称可以根据业务进行分类和命名)

```javascript
import {post,get,FormDataPost} from "./http"
function  login(){
  return post('/login',{name:'admin',pwd:"admin"})
}

function logout(){
  return get("/logout",)
}

export default {
  login,
  logout
}
```

---

## 在 main.js 将统一数据请求入口引入

```javascript
import api from "../src/api/api"
Vue.prototype.$api = api
```

---

## 模板中使用

```javascript
login(){
    this.$api.vote.login().then((response)=>{console.log(response)})
}

logout(){
    this.$api.vote.logout().then((response)=>{console.log(response)})
}

```



