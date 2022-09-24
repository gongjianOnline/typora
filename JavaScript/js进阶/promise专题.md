# promise 专题

## promise 基本概念

- promise 是一个构造函数, 必须传一个 executor 执行器
- executor 会立刻执行,并传入 resolve 和 reject 参数
- promise 三种状态: fulfilled成功  reject 失败 pending等待
- 可以通过 resolve 和 reject 来改变状态, 同时调用对应的回调
  - 一个 promise 实例状态改变后,不能再重新发生新的变化
- 当 executor 有异常时,也会触发 promise 的失败 (reject)

通过上方的描述 我们来构建最基本的代码框架

```javascript
const FULFILLED = "FULFILLED"
const REJECTED = "REJECTED"
const PENDING = "PENDING"

class Promise {
    constructor(executor){
        this.state = PENDING; // promise 的状态，默认是 PENDING
        this.value = null;  // 成功状态的接受参数
        this.reason = null; // 失败后状态的参数
        this.onFulfilledCallbasks = []; // 异步状态下成功的回调函数集合
        this.onRejectedCallbacks = [];  // 异步状态下失败的回调函数几个
        const resolve = (value)=>{ // 成功后调用的函数
            if(this.state === PENDING){ // 防止一次promise触发多个状态
                this.state = FULFILLED; // 将状态改为成功
                this.value = value; //将成功的内容赋值给 value 变量，供给 then 调用
                this.onFUliflledCallbasks.forEach(item=>{item()}) // j执行异步的回调队列
            }
        }
        const reject = (reason)=>{ // 失败后调用的函数
             if(this.state === PENDING){
                this.state = REJECTED; // 将状态改为失败
                this.reason = reason; // 将失败的内容赋值给 reason 变量,供给 then 调用
                 this.onRejectedCallbasks.forEach(item=>{item()} //执行异步回调的队列
            }
        }
        try{
            exectuor(resolve,reject) // exectuor会立即执行,传入resolve,reject两个参数
        }catch(err){
            reject(err) // 如果 exectuor发生异常则直接 reject
        }
    }
        
    then(onFulfilled,onRejected){ // 接收一个成功 , 失败的回调
        let promise2 = new Promise((resolve,reject)=>{ 
            if(this.state === FULFILLED){ // 如果吃成功状态,则调用resolve
                let x = onFulfilled(this.value)
                resolve(x)
            }
            if(this.state === REJECTED){ // 如果是失败状态,则调用reject
                let x = onRejected(this.reason)
                reject(x)
            }
            if(this.state === PENDING){
                this.onFulfilledCallbacks.push(()=>{ // 异步的成功回调
                    let x = onFulfilled(this.value)
                	resolve(x)
                })
                this.onRejectedCallbacks,push(()=>{ // 异步的失败回调
                    let x = onRejected(this.reason)
                	reject(x)
                })
            }
        })
    }
}
export default Promise

```

---

## then 链的特点

- 当 then 中成功和失败的回调返回一个 promise 时,内部会解析和这个 promise, 并且将结果传递给外层的下一个 then
- 下一次 then 的成功和失败,取决于当前的 promise 状态
- 如果成功和失败返回的不是一个 promise ,则直接给下一个 then 的成功
- 如果成功和失败的回调出现异常,则直接传递给下一个 then 的失败

### resolvePromise 实现逻辑

判断 then 的输入方式，需要单独封装一个 resolvePromise 函数进行链路判断和引导

规范

resolvePromise共接收四个参数 promise , x , resolve , reject

- 如果 x 和 promise2 引用的是同一个对象就会造成死循环, 需要 reject 直接抛出异常
- 判断 x 是否为 promise
  - 缩小范围 (x==="object" && x !== null) || (x === "function")
  - 查看 x 有没有 .then 属性 (规范中: 取出 x.then 可能会发生异常, 如果 then 是 function 则就认定他是 promise)
- 如果 x 是 promise 则监听报错
  - try 优化, 防止 x.then 的返回是同一个 promise, 必须调用 resolvePromise
  - 防止多一次调用多个状态, 加入 called 认证, 只要通过一个状态,这个值就会认为 true

```javascript
function resolvePromise (promise2,x,resolve,reject){
    // 判断 x 和 promise 不能引入同一个 promise 对象
    if(promise2 === x){reject(new TypeError("chaining cycle detected for promise"))}
    // 判断 x 是不是 promise 对象
    if((typeof x === "object" && x !== null) || (typeof x === "function")){
        let called = false; // 防止出现一个回调执行多个状态
        try{
            let then = x.then;
            if(typeof then === "function"){
                then.call(
                	x,
                    (y)=>{
                       	if(called){return}
                        called = true;
                        resolvePromise(promise2,y,resolve,reject)
                    },
                    (err)=>{
                        if(called){return}
                        called = true;
                        reject(err)
                    }
                )
            }else{
                resolve(x)
            }
        }catch(err){
            if(called){return}
            called = true;
            reject(err)
        }
    }else{
        resolve(x)
    }
}

class promise {
    ....
    then(onFulfilled,onRejected){ // 接收一个成功 , 失败的回调
        let promise2 = new Promise((resolve,reject)=>{ 
            if(this.state === FULFILLED){ // 如果吃成功状态,则调用resolve
                try{
                    let x = onFulfilled(this.value)
                    resolvePromise(promise2,x,resolve,reject)
                }catch(error){
                 	reject(error)
                }
            }
            if(this.state === REJECTED){ // 如果是失败状态,则调用reject
                try{
                    let x = onRejected(this.reason)
                    resolvePromise(promise2,x,resolve,reject)
                }catch(error){
                    reject(error)
                }
            }
            if(this.state === PENDING){
                this.onFulfilledCallbacks.push(()=>{ // 异步的成功回调
                    try{
                        let x = onFulfilled(this.value)
                		resolvePromise(promise2,x,resolve,reject)
                    }catch(error){
                       	reject(error) 
                    }
                })
                this.onRejectedCallbacks,push(()=>{ // 异步的失败回调
                    try{
                        let x = onFulfilled(this.value)
                		resolvePromise(promise2,x,resolve,reject)
                    }catch(error){
                     	reject(error)   
                    }
                })
            }
        })
    }
}
```

---

### then 的优化

```javascript
class Promise{
    ...
    then(onFulfilled,onRejected){
        onFulilled = typeof onFulfilled === "function"?onFulflled : v=>v;
        onRejected = typeof onRejected === "function"? onRejected : e=>{throw e}
    }
}
```

