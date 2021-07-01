# 手写sleep函数

sleep 顾名思义 睡眠函数，他的作用是让下面的代码延迟一会在执行，效果等同于setTimeout函数，但是 setTimeout 在逐步延迟的使用中会出现回调地狱的情况。代码如下

```javascript
setTimeout(()=>{
  console.log("第一次打印")
  setTimeout(()=>{
      console.log("第二次打印")
      setTimeOut(()=>{
          console.log("第三次打印")
      },2000)
  },2000)
},2000)
```



在其他语言中都提供了 sleep() 函数，但是JS里面没有该方法，下面我们将用两种方式手写sleep函数 

## Promise方式实现

```javascript
function sleep(ms){
    return new Promise((resvole,reject)=>{
        setTimeout(resvole,ms)
    })
}
sleep(5000).then(()=>{
    console.log("打印第一次")
    sleep(5000).then(()=>{
        console.log("打印第二次")
    	sleep(5000).then(()=>{
            console.log("打印第三次")
        })
    })
})

```

## async/await方式实现

```javascript
function sleep(ms){
    retrun new Promise((resvole,reject)=>{
        setTimeout(resvole,reject)
    })
}
async function fun(){
    await sleep(5000)
    console.log("第一次打印")
    await sleep(5000)
    console.log("第二次打印")
    await sleep(5000)
    console.log("第三次打印")
}
fun()
```

