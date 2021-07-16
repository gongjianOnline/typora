# 手写Promise

Promise是一个对象，保存着未来将要结束的事件，他有两个特征：

- 对象的状态不受内部影响，Promise对象代表一个异步操作，有三种状态，pending进行中，fulfilled已成功，rejected以失败，只有异步操作的结果，才可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。
- 一旦状态改变，就不会在变，promise对象状态改变只有两种可能，从pending改到fulfilled 或者 从pending 改到 rejected ，只要这两种情况发生，状态就凝固了，不会在改变，这个时候就称为定型resolved

## 手写实现promise

遵循 Promise  A+ 规范

https://juejin.cn/post/6844903649852784647

```javascript
class Promise2{
  state= "pending"
  callbacks = []
  
  constructor(fn:any){
    if(typeof fn !== "function"){
      throw new Error("promise 接收的是一个函数")
    }
    fn(this.resolve.bind(this),this.reject.bind(this))
  }

  resolve(value:any){
    setTimeout(()=>{
      if(this.state !== "pending"){return}
      this.state = "fulfilled"
      this.callbacks.forEach((handle)=>{
        if(typeof handle[0] === "function"){
          // @ts-ignore
          handle[0].call(undefined,value)
        }
      })
    },0)
  }
  reject(reason:any){
    setTimeout(()=>{
      if(this.state !== "pending"){return}
      this.state = "rejected"
      this.callbacks.forEach((handle)=>{
        if(typeof handle[1] === "function"){
          // @ts-ignore
          handle[1](reason)
        }
      })
    },0)
  }

  // @ts-ignore
  then(succeed?:any,fali?:any){
    // @ts-ignore
    const hande = []
    if(typeof succeed === "function"){
      hande[0] = succeed
    }
    if(typeof fali === "function"){
      hande[1] = fali
    }
    // @ts-ignore
    this.callbacks.push(hande)
  }

}
export default Promise2
```

