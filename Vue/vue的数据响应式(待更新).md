# vue的数据响应式原理

## Vue数据响应式

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

{n:10}和{n:(...)}发生了什么?

## 了解Es6的 getter 和 setter

```javascript
let obj = {
    姓:"小",
    名:"明",
    姓名(){
        return this.姓 + this.名
    },
    age:18    
}
console.log("需求一"+obj1.姓名());
// 姓名后面的括号能删掉吗? 不能因为他是函数
// 怎么去掉括号

let obj2 = {
    姓:"小",
    名:"明",
    get 姓名(){
        return this.姓 + this.名
    },
    age:18    
}
console.log("需求二" + obj2.姓名)

// 总结: gtter 就是不加括号的函数,仅此而已

// 需求三: 姓名可以被写
let obj3 = {
    姓:"小",
    名:"明",
    get 姓名(){
        return this.姓 + this.名
    },
    set 姓名(xxx){
        this.姓 = xxx[0],
        this.名 = xxx.substring(1)
    }
    age:18    
}
obj3.姓名 = "高媛媛"
console.log(`需求三: 姓${obj3.姓},名${obj3.名}`)
console.log(obj3)
// 总结: setter 就是这样用的,用 .xxx 触发 get 函数
// 确实可以对姓名进行读和写,但是并不存在姓名这个属性,获取和修改是通过get和set完成的

```

## Object.defineProperty()的用法

```javascript
/*向obj3添加虚拟属性*/
let obj3 = {
    姓:"小",
    名:"明",
    get 姓名(){
        return this.姓 + this.名
    },
    set 姓名(xxx){
        this.姓 = xxx[0],
        this.名 = xxx.substring(1)
    }
    age:18    
}
let _xxx = 0;
Object.defineProperty(obj3,"xxx",{
    get(){
        return  _xxx
    },
    set(value){
        _xxx = value
    } 
})
```





