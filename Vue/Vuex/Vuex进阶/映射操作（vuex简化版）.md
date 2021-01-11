# 映射操作（vuex简化版）

## 映射操作引入

```javascript
import {mapState,mapgetters,mapMutations,mapActions} from 'vuex'
```

## 使用

```
//store.js原型
import Vue from "vue"
import Vuex from "vuex"
//挂载插件
Vue.use(Vuex)
export default new Vuex.Store({
    state:{
        //共享数据包
        count:1
    },
    mutations:{ 
        increase(state){
            state.count += 1 
        }
    },
    getters:{ //对state中的数据进行加工处理
        money:(state)=>{
            return state.count + "$"
        }
    },
    actions:{ //对异步操作使用
        increaseAsync({commit,state},payload){
            setTimeout(()=>{
                //延迟一秒钟调用mutations中的increase方法
                commit("increase")
            },1000)
        }
    }
})
```

```javascript
//vue使用
<template>
    <div>
        <div>vuex获取:{{count}}</div>
        <div>vuex加工函数获取:{{money}}</div>
        <button @click="inc">increase</button>
        <button @click="incAc">incAsync</button>
    </div>
</template>
<script>
import {mapState,mapgetters,mapMutations,mapActions} from "vuex"
export default{
    data(){
        return{
            
        }
    },
    computed:{
        //映射state\getters方法返回一个函数对象({key:value}),利用解构函数获取key值,并直接赋值到HTML上
        ...mapState(['count']),
        ...mapGetters(['money'])
    },
    methods:{
        //映射mutatuins方法,利用解构方法获取vuex中的increase方法
        ...mapMutations(['increase']),
        //映射mapActions方法,利用解构方法获取vuex中的increaseAsunc方法.
        ...mapActions(['increaseAsunc']),
        inc(){
            //通过映射方法用this直接调用increase方法
            this.increase()
        },
        incAc(){
            this.increaseAsync()
        }
    }
}
</script>

```

