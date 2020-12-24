# JSON对象

## JSON.stringify()

将一个值转换为json格式

注意: 改API对数组无效

**第二个参数可以是一个函数**

````javascript
function f(key,value){
    if(typeof value === "number"){
        value = 2 * value;
    }
    return value;
}
JSON.stringify({a:1,b:2}) //{a:2,b:4}
````

---

## toJSON

- 用法
  - 如果参数对象有自定义的toJSON方法,那么
  - JSON.stringify会使用这个方法的返回值作为参数,二忽略原对象的其他属性

```javascript
var user = {
   name:"三",
   last:"章",
   toJSON:function(){
      return {name:this.last+this.name}
   }     
}
JSON.stringify(user )
```

---

## JSON.parse()

- 用法
  - 将JSON字符串转换对应的值

```javascript
var xxx = JSON.parse("{'name':'张三'}")
console.log(xxx.name)
```

**替代js原生中eval()方法**