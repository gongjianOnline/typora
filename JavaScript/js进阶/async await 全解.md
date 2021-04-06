# async await 全解

1. 微任务和宏任务
2. promise的API
3. promise的使用场景
4. async/await

## 宏任务(macrotask )和微任务(microtask )

在挂起任务时,js引擎会将所有任务按照类别分到两个队列中,首先在 macrotask 的队列（这个队列也被叫做 task queue）中取出第一个任务，执行完毕后取出 microtask 队列中的所有任务顺序执行；之后再取 macrotask 任务，周而复始，直至两个队列的任务都取完。

- 先宏在微
- 其实一开始没有两个任务队列
- 为了让Promise回调更早执行,强行插入了一个队列
- 如果没有微任务,函数直接用seTtimeout

## promise的API

### 常规用法

```
    let text = new Promise((resolve,reject)=>{
        resolve("成功时返回")
        reject("失败时返回")
    })
```

### Promise.resolve(resolve)

制造一个成功(或失败)

### Promise.reject(reject)

制造一个失败

### promise.all(数组)

等待全部成功,或者有一个失败

### promise.race(数组)

等待第几个状态改变

### promise.allSettled(数组)

等待全部状态改变,目前处于stage-4(并未规范)
手写allSettled

```
    let ppromise1 =()=>{return  new Promise((resolve,reject)=>{
        setTimeout(()=>{reject("第一扇门关了")},3000)
    })}

    let ppromise2 =()=>{ return new Promise((resolve,reject)=>{
        setTimeout(()=>{reject("第二扇门关了")},4000)
    })}

    let ppromise3 =()=>{ return new Promise((resolve,reject)=>{
        setTimeout(()=>{resolve("第三扇门开了")},5000)
    })}

    // Promise.all([ppromise1(),ppromise2(),ppromise3()]).then(null,(reason)=>{console.log(reason)}) //返回失败的对象会终止
    //冗余的代码
    // Promise.all([ppromise1().then(()=>({status:'ok'}),()=>({status:"not ok"})),
    // ppromise2().then(()=>({status:'ok'}),()=>({status:"not ok"})),
    // ppromise3().then(()=>({status:'ok'}),()=>({status:"not ok"}))]).then((result)=>{console.log(result)})
    // console.log("12.")
    //上面的优化
    let x = (paomise)=>{return paomise.then(()=>({status:'ok'}),()=>({status:"not ok"}))};
    Promise.all([x(ppromise1()),x(ppromise2()),x(ppromise3())]).then((reason)=>{console.log(reason)})
```

## promise的使用场景

1. 多次处理一个结果时
2. 串行
   1. 把任务放进队列中,完成一个在做下一个
3. 并行
   1. Promise.all([p1,p2])
   2. promise.allSettled
   3. 手写allSettled

## async/await

基本用法

```
    const fn = async ()=>{
        const temp = await makePromise()
        return temp + 1
    }
```

await 错误处理

```
    const response = await axios.get('./xxx').then(null,errorHandler)
    console.log(response)
```

await的传染性

```
    console.log(1)
    await console.log(2)
    console.log(3)
```

此时的consoel.log(3)变成了异步任务

await的天然串行,在for里面是串行;在froEach里面是并行