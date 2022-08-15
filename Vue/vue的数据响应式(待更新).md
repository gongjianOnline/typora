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

使用:

object.definePropertry(obj,prop,descriptor)

- obj  要更改的对象
- prop 要更改的对象中的key
- descriptor 描述
  - 公共属性
    - configurable 表示是否可删除(值为false时不可删也不可改),默认为false
    - enumerable 表示是否可枚举, 默认是false
  - 数据描述符
    - wirtable 是否可以修改,默认为false
    - value 默认值,默认为 undefined
  - getter / setter
    - get 获取时触发的监听
    - set 修改时触发的监听
    - **get 和 set 不能同数据描述符同时出现**

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

---

## 代理和监听

### 需求一

用 Object.defineProperty 定义 n 

```javascript

let data1 = {}
Object.definProperty(data1,'n',{
    value:0
})
console.log(`需求一:${data1.n}`) // 0
```

上面的代码,不是把简单问题复杂化了吗? 

---

### 需求二

n 不能小于 0;即 data2.n = -1 应该无效,但 data2.n = 1 有效

```javascript
let data2 = {};
data2._n = 0; // 又来偷偷的存储n的值,默认是一个0;
Object.defineProperty(data2,'n',{
    get(){
        return this._n // 这里的this就是data2,当前的对象
    },
    set(value){
        if(value<0) return;
        this._n = value
    }
})
console.log(`需求二: ${data2.n}`)
data2.n = -1;
console.log(`需求二:${data2.n} 设置为 -1 失败`)
data2.n = 1;
console.log(`需求二: ${data2.n} 设置为 1 成功`)

```

- 缺点
  - 如果直接使用data2._n,是防不住的

---

### 需求三

​	使用代理

```javascript
let data3 = proxy({data:{n:0}});
function proxy({data}){ 
	const obj  = {
        // 这里的 'n' 写死了,理论上遍历data所有key,这里做了简化
       Object.definProperty(obj,'n',{
        	get(){
            	return data.n
        	},
            set(value){
                if(value < 0) return;
                data.n = value
            }
       })
       return obj // obj 就是代理
    }    
}
console.log(`需求三: ${data3.n}`)
data3.n = -1;
console.log(`需求三: ${data3.n} , 设置为 -1 失效`)
data3.n = 1;
console.log(`需求三: ${data3.n} , 设置为 1 成功`)
```

- 缺点

  - 如果括号里不是匿名对象,就无法判断

  - ```javascript
    let myData = {n:0}
    letdata4 = proxy({data: myData})
    console.log(`缺点: ${data4.n}`)
    myData.n = -1;
    console.log(`缺点: ${data4.n}, 设置为 -1 失败吗 !?`)
    ```

---

### 需求五

就算用户擅自修改myData,也要拦截

```javascript
let  myData5 = {n:0};
let data^ = proxy2({data:myData5})
function  proxy2(data){
    let valye = data.n
    Object.defineProperty(data,'n',{
        get(){
            return value
        },
        set(newValue){
            if(newValue < 0)return
            value = newValue
        }
    })
    // 就加上上面几句,这几句会监听data
    const obj = {};
    Object.definProperty(obj,'n',{
        get(){
            return data.n
        },
        set(value){
            if(value < 0) return
            data.n = value
        }
    })
}
```

---

## 总结

### Object.defineProperty

- 可以给对象添加属性value
- 可以给对象添加getter、setter
- getter、setter 用于对属性的读写进行监控

### 啥是代理（设计模式）

- 对myData对象的属性读写，全权由另一个对象vm负责
- 那么vm就是mydata的代理（类比房东租房）
- 比如myData.n不用,偏要用vm.n来操作myData.n

### Vm = new Vue({data:myData})

- 会让vm编程myData 的代理(proxy)
- 会让myData的所有属性进行监控
- 为什么要监控,为了防止myData的属性变了,vm不知道
- vm知道了又如何? 知道属性变了就可以调用render(data)呀!
- UI = render(data)

---













