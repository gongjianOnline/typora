# JS类型判断

## typeof()

类型检查只支持 基本数据类型 + undefined + function

```javascript
console.log(typeof 1) // number
console.log(typeof true) // boolean
console.log(typeof "str") // string
console.log(typeof undefined) // undefined
console.log(typeof null) // object
console.log(typeof function(){}) // function
console.log(typeof [1,2,3]) // object
console.log(typeof {name:"xxx"}) // object
```



---

## instanceof

类型检查只支持 引用类型

```javascript
console.log( 1 instanceof Number) // false
console.log( 'str' instanceof String) // fasle
console.log( true instanceof Boolean) // false
console.log( [1,2,3] instanceof Array) // true
console.log( function(){} instanceof Function) // true
console.log( {} instanceof Object) // true
console.log( null instanceof Object) // fasle
```



---

## Object.prototype.toString.call()

支持所有类型的检查， 缺点是语法太长了

```javascript
var start = Object.prototype.toString
console.log(start.call(1))
console.log(start.call("111"))
console.log(start.call(true))
console.log(start.call(undefined))
console.log(start.call({}))
console.log(start.call(function(){}))
console.log(start.call([1,2,3]))
console.log(start.call(null))
```

