# 手写bind

## 兼容版本

```javascript
var slice = Array.prototype.slice;
function bind(asThis){
    var args = slice.call(arguments,1);
    var fn = this;
    return function(){
        var agrs2 = slice.call(arguments,0);
        return fn.apply(asThis,args.concat(agrs2))
    }
}
module.exports =  bind;

if(! Function.prototype.bind){
    Function.prototype.bind = bind;
}

```

## 简介写法

```javascript
Function.prototype.Mybind = function(){
    var self = this;
    var agr = Array.from(arguents)
    var that = agr.shift()
    return function(){
        return self.applay(that,agr)
    }
}
```



---

## ES6版本

```javascript
function bind(asThis,...args){
    const fn = this;
    return function(...args2){
        return fn.call(asThis,...args,...args2)
    }
}
module.exporte = bind;
if(!Function.prototype.bind){
   	Function.prototype.bind = bind
}
```

---

