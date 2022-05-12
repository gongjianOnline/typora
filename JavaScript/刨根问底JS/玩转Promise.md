# 玩转Promise

## promise的基本内容

```javascript
let a = new Promise([executor])
```

- [executor]: 可以执行函数

  - new Promise 的时候，在Promise内部会立即把 [ executor ] 函数执行
  - 函数中一般用来管理一个异步编程代码
  - 同时给 [ executor ] 函数传递两个 [ 函数类型 ] : resolve / reject

- a 是 Promise 类的实例

  - 内置私有属性
    - [[PromiseState]] 实例状态：pending准备状态  fulfilled/resolved成功状态  reject失败状态
    - [[PromiseResult]] 实例的值
  - 公共属性方法（Promise.prototype）
    - then
    - catch
    - finally

---

## promise中callback（executor）执行过程

```javascript
let a = new Promise((resolve,reject)=>{
    ....
})
```

- 在[executor]执行resolve/reject都是为了改变promise实例的状态和值[结果]，一旦状态被改变成 fulfilled/rejected 则不能再改为其他的状态。
  - resolve("ok"); [[PromiseState]]:fulfilled [[PromiseResult]]:"ok"
  - reject("NO"); [[PromiseState]]:rejected [[PromiseResult]]:"NO"
- 如果[executor]函数执行执行报错.则
  - [[PromiseState]]:rejected
  - [[PromiseResult]]: 报错原因
  - **Promise内部做异常信息捕获[try/catch]**

---

## promise中then的基本使用

```javascript
let  a = new Promise((resolve,reject)=>{
    resolve("ok")
})
a.then((result)=>{
    console.log("成功=》",result)
},(reason)=>{
    console.log("失败=>",reason)
})
```

上面代码 实例状态的改变可以控制 then 方法时，存放两了方法（成功或失败），并执行其中的一个方法

- a.then(onfulfilledCallback.onrejectedCallback)
  - 状态成功执行的是：onfulfilledCallback 
  - 状态失败执行的是：onrejectedCallback
  - **并且把[[PromiseResult]]的值传递给方法**

---

## promise中then的执行过程

```javascript
let p= new Promise((resolve,reject)=>{
  console.log(1)
  resolve("ok")
  console.log(2)
})
console.log(p)
p.then((resolve)=>{
  console.log("成功->",resolve)
})
console.log(3)
/*控制台一次输出： 1 2 promise实例 3 "成功->ok"*/
```

有上一章节介绍，promise.then 有两个回调方法

- 首先把传递进来的 onfulfilledCallback和onrejectedCallback存储起来[存储在一个容器中:因为可以基于then给其存放好多个回调函数]
- 其次去验证当前实例的状态
- 如果实例状态 pending 则不做任何处理
- 如果已经变为 fulfilled/rejected，则会通知对应的回调函数执行[但是不是立即执行，而是把其放在 EventQueue中的微任务队列中]。

**“Promise本身不是异步的，是用来管理异步的，但是then方法是异步的[微任务]”**

### 相关面试题

```javascript
let p = new Promise((resolve,reject)=>{
  console.log(1)
  setTimeout(()=>{
    resolve("ok")
    console.log(p)
    console.log(4)
  },0) 
  console.log(2)
})
console.log(p)
p.then((result)=>{
  console.log("成功->",result);
})
console.log(3)
/*控制台结果 1 2 Promise 3 Promise 4 "成功->ok"*/
```

---

## Promise的then链

```javascript
let p = new Promise((resolve,reject)=>{
  resolve("OK")
})
let p2 = p.then((result)=>{
  console.log("p1成功->",result)
},(reason)=>{
  console.log("p1失败->",reason)
})
console.log(p2)
/*P2是一个新的Promise实例*/
```

- 执行then方法会返回一些全新的promise实例P2
  - p2的状态和值是咋改变的?
    - 无论执行的是基数p1.then存放的onfulfilledCallback/onrejectedCallback两个方法中的哪一个
    - 只要方法执行不报错
      - 如果方法中返回一个全新的Promise实例.则"全新的Promise实例"的成功和失败决定P2的成功和失败
      - 如果不是返promise呢? 则[[PromiseState]]:fulfiled [[PromiseResult]]:返回值
    - 如果方法执行报错: p2的[[PromiseState]]:rejected [[PromiseResult]]:报错原因

---

## promise中then的顺延

```javascript
new Promise((resolve,reject)=>{
  reject("NO")
}).then().then((result)=>{
  console.log("2成功->",result)
},(reason)=>{
  console.log("2失败",reason)
}).then((result)=>{
  console.log("3成功->",result)
},(reason)=>{
  console.log("3失败",reason)
})
```

如果 onfulfilledCallback / onrejectedCallback 不传递,则状态和结果那会"顺延/穿透"到下一个同等状态应该执行的回调函数上[内部其实是自己补充了一些实现效果的默认函数]

----

## Promise中catch的使用

- catch 只处理状态为失败下做的事情
  - 利用promise的顺延机制,可以直接链尾添加catch方法来获取整个promise的失败处理.

```javascript
 Promise.prototype.catch = function(onrejectedCallback){
 	return this.then(null,onrejectedCallback) 
 }
```

使用

```javascript
new Promise((resolve,reject)=>{
  reject("NO")
}).then((result)=>{
  console.log("2成功->",result)
}).then((result)=>{
  console.log("3成功->",result)
}).catch((reason)=>{
  console.log("失败-->",reason)
})
```

[^本片文章借鉴《珠峰前端高级体系课程》，如有侵权行为，请联系作者删除]: 

