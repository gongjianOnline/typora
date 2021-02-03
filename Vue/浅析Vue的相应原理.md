# 浅谈Vue响应式原理
1. 了解es6中的getter / setter 访问器属性
2. 了解Object.defineproperty()
3. 

## 一、es6中的getter和setter
在了解getter和setter之前看看Vue的响应式发什么变化?
```javascript
    const  myData = {
        n : 0
    }
    console.log(myData) // {n:10}
    const vm = new Vue({
        data: myData,
        template:`
    		<div>{{n}}</div>
    	`
    }).$mount('#app')
    setTimeout(()=>{
        myData.n += 10;
        console.log(myData) // {n:(...)}
        console.log(vm)
    },3000)
```
问题：{n:0} {n:(...)} ?

```javascript
    let obj = {
        name:'韩梅梅',
        get 姓名(){
            console.log("访问了姓名属性")
            return this.name 
        },
        set 姓名(xxx){
            console.log("修改了姓名属性")
            this.name = xxx
        }
    }
    
    console.log(obj.姓名)
    obj.姓名 = "李雷"
    console.log(obj)
```
打印obj之后会发现,历史总是那么惊人的相似.

---

## 二、了解Object.defineproperty()
- 定义
  - Object.defineProperty() 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象。

```javascript
var obj = {};
Object.defineProperty("obj",'a',{
    value:3, // 和get不能互存
    get(){
        console.log("访问了a属性")
    },
    set(){
        console.log("设置了a属性")
    }
})
console.log(obj.a)
obj.a = 10
```

- 问题
  - 为什么要这么定义
    - definProperty可以定义一些隐藏属性

### 赋值操作

```javascript
var obj = {};
var temp;
Object.defineProperty("obj",'a',{
    get(){
        console.log("访问了a属性")
        return temp
    },
    set(newValue){
        console.log("设置了a属性",newValue)
        temp = newValue
    }
})
console.log(obj.a)
obj.a = 10
/*
	以上代码添加temp来周转get和set
	Q : 每一次创建一个对象属性都要设一个周转变量吗?
	A : 封装函数 利用闭包
*/
```

优化封装

```javascript
var obj = {};
function defineReactive(data,key,value){
    if(arguments.length == 2){
        value = obj[key]
        return
    }
    // value 作为中间变量
    Object.defineProperty(data,key,{
        get(){
            console.log("访问了a属性")
            return value
        },
        set(newValue){
            console.log("设置了a属性",newValue)
            value = newValue
        }
    })
}
defineReactive(obj,key,10)
console.log(obj.a)
obj.a = 11
console.log(obj.a)
/*************************************************************************************************************/
// 模块化封装
export default function defineReactive(data,key,value){
    if(arguments.length == 2){
        val = obj[key]
    }
    // value 作为中间变量
    Object.defineProperty(data,key,{
        get(){
            console.log("访问了a属性")
            return value
        },
        set(newValue){
            console.log("设置了a属性",newValue)
            value = newValue
        }
    })
}

```

## 递归(循环)侦测对象

解决的问题

```javascript
var obj  = {
    a:{
        m:{
        	n:{}
    	}
    },
    b:4
}
/*以上代码常规的defineProperty是监听不到里层对象的变化*/
```

```javascript
// Observer
/**将一个正常的object转换为每个层级的属性都是响应式(可以被侦测)的object*/
export default class Observer{
    constructor(){
        
    }
}
```

在文件中index.js引入

```javascript
import defineReactive from "./defineReactive.js";
import Observer from "./Observer.js"
// 创建ovserve函数,注意函数名字没有r
function observe(value){
    // 如果value不是对象,什么都不做
    if(typeof value !== "object"){
        return
    }
    // 定义ob，用于存储Observer的实例;
    var ob;
    if(typeof value.__ob__ !== "undefined"){
       ob = value.__ob__;
    }else{
        ob = new Observer(value)
    }
    return ob
}
```

Observer流程图

[![ynnZR0.png](https://s3.ax1x.com/2021/02/02/ynnZR0.png)](https://imgchr.com/i/ynnZR0)



