# Object扩展

## 属性的简洁表示法

```javascript
{
    //简洁表示法
    let a = "hello word"
    let obj = {
        a
    }
    console.log(obj) //hello word
}
```

## 属性名表达式

```javascript
{
    //属性名表达式
    let foo =  "test"
    let obj = {
        [foo]:foo,
        //[key] ===> 取得是变量赋值的内容
    }
    console.log(obj.test)
}
```

## 方面的name属性

```javascript
{
    //方法的name属性
    let obj2 = {
        run(){
            return 'hello word'
        }
    }
    console.log(obj2.run.name) //run
    //对象的方法的名称是什么
}
```

---

## Object.js()

​	用于做比较等同于 === \ ==

---

## Object.assign()

​	用于对象的合并,将源对象(source)的所有可枚举属性,复制到对象(target)

```javascript
{
    //Object.assign()
     let target = {a:1}
     let source1 = {b:2}
     let source2 = {c:3}
     Object.assign(target,source1,source2)
     console.log(target) //{a:1,b:2,c:3}
}
```

---

## Object.getOwnPropertyDescriptor

​	可以获取改属性的描述对象;

​	返回该对象本身的配置信息

---

## Object.setPrototyOf

用来设置一个对象的prototype对象,返回参数对象本身

---

## Object.getPrototyOf() 

​	用来读取一个对象的去原型对象

```javascript
{
    /**接收两个参数,第一参数为自身的属性,第二参数向自身属性的原型上添加新的原型对象*/
    let proto = {};
    let obj = {x:10};
    Object.setPrototypeOf(obj,proto)
    proto.y = 20;
    proto.z = 40;
    console.log(obj)
    console.log(Object.getPrototypeOf(obj))
}
```

---

## Object.keys() / Object.value / Object.entries()

​	统一遍历方法

- Object.keys() // 遍历健名

- Object.value() // 遍历健值

- Object.entries() //遍历健名和健值

  ```javascript
  {
      //遍历
      let obj ={
          a:1,
          b:2,
          c:3
      }
      for(let key of Object.keys(obj)){
          console.log(key) "a,b,c"
      }
      for(let key of Object.values(obj)){
          console.log(key) "1,2,3"
      }
      for(let key of Object.entries(obj)){
          console.log(key) 
      }
  }
  ```

  

