# 手写EventBus

EventBus 又叫做 事件总线传值 ,本质是观察者模式的实现，

观察者模式就是 定义了对象间的一种一对多的关系，让多个观察这对象同事监听某一个主题对象，当一个对象发生改变时，所有依赖于他的对象将得到通知

他在 Node 中的使用，on 是用来监听事件 ， emit 是用来触发事件 ， 一旦emit触发了事件，on就会被监听到，从而触发回调。

```javascript
const EventEmitter = require('events');
class MyEmitter extends EventEmitter {}
const myEmitter = new MyEmitter();
myEmitter.on('嗨', (str) => {
 console.log(str);
});
myEmitter.emit('嗨','你好');
```

---

## 手写实现

```javascript
class EventBus {
    constructor(){
        this.eventList = {}
    }
    on(eventName,eventCallBack){
        if(this.eventList[eventName]){
           this.eventList[eventName].push(eventCallBack)
        }else{
            this.eventList[eventName] = [eventCallBack]
        }
    }
    emit(eventName,...params){
        if(this.eventList[eventName]){
           this.eventList[eventName].forEach((item,index)=>{
               item.apply(this,params)
           })
        }
    }
}

// 使用
let event = new EventBus()
event.on("hello",(params)=>{
    console.log("监听到了"，params)
})
event.emit("hello","eventBus")
```

