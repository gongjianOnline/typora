# class基本语法

格式

```javascript
class Xxx{
    constructor(){
        
    }
    fun1()
    fun2()
}
let xxx = new Xxx()
```

```javascript
class Tabs {
    constructor(name){
        this.name = name
    }
    run(){
        return "hello word"
    }
}
let lab = new Tabs("猴子")
console.log(lab)
```

---

## class继承性

```javascript
//继承
class 子类 extends 父类 {
    constructor(){
        super()
    }
}
/*****/
{
    // class继承
    class Parent {
        constructor(){
            this.name = "zhangsan"
        }
    }
    class Child extends Parent {
        constructor(){
            super()
            this.age = "18"
        }
    }
    let child = new Child();
    console.log(child.age,child.name)
}
```

