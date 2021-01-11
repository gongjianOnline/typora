# 基本操作（cli3）

## 安装

```javascript
vue add vuex
```

## 自动生成store.js文件

```javascript
import Vue from "vue"
import Vuex from "vuex"
//挂载插件
Vue.use(Vuex)
export default new Vuex.Store({
    state:{
        //共享数据
        count:1
    },
    mutations:{
        increase(state){
            state.count += 1 
        }
    },
    getters:{ //对state中的数据进行加工处理
        moeny:(state)=>{
            return state.count + "$"
        }
    },
    actions:{
        increaseAsync({commit,state},payload){
            setTimeout(()=>{
                commit("increase")
            },1000)
        }
    }
})
```

## 在组件更改

```javascript
//组件一
<template>
    <div>vuex获取{{$store.state.count}}</div>
    <div>vuex获取{{$store.getters.moeny}}</div>
</template>

//组件二
<template>
    <div>
        <button @click="inc">编辑</button>
        <button @click="incAsync">异步编辑</button>
    </div>
</template>
<script>
    export default {
        name:'',
        data(){
            return{}
        },
        methods:{
            inc(){
                this.$store.commit("increase")
            },
            incAsync(){
                this.$store.dispatch('increaseAsync')
            }
        }
    }
</script>
```

