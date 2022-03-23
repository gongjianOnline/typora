# 使用Vue3全局变量在组件中无法获取变量

在Vue3官方文档中说 使用 **globalProperties** 和  **getCurrentInstance** 在全局中注册和使用变量，其语法等同 Vue2 中的 **Vue.prototype.xxx**

在 main.js 中注册全局变量

```javascript
import { createApp } from 'vue'
import axios from "axios"
const app = createApp(App)
app.config.globalProperties.$axios = axios
```

在其他组件使用 axios, **注意有些文档中会将 proxy 写成 ctx ，但是亲测 ctx 中无法取得全局变量* ** ，此时的 proxy 相当于 this.$axios

```javascript
import { getCurrentInstance } from "vue"
export default {
    name:"component",
    setup(){
        const {proxy} = getCurrentInstance()
        const gitData = async ()=>{
            let response = await proxy.$axios({
                method:"get",
                url:""
            })
            console.log(proxy)
        }
    }
}
```