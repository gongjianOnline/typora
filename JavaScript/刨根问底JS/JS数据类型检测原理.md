# JS  数据类型检查原理

## 数据类型检测的方法

- typeof : 返回一个字符包含对应的数字类型
  - typeof  只能检测原始的数据类型
  - typeof null 会返回 object
  - typeof new Number(0) 会返回 object

```javascript
let arr = [];
typeof arr // object
typeof "123" // string 
typeof new Number(0) // object
typeof null // object
typeof undefined // undefined
```

---

- [实例] instanceof [构造函数]
  - 弊端
    - 本身的作用的是用来检查当前实例是否属于这个类，临时充当数据类型检查会出现诸多问题
    - instanceof  只能检查引用类型
  - 运行原理
    - 首先按照 构造函数**[Symbol.hasInstance]([实例] )**，如果存在这个属性方法，这个方法的返回值就是最后的检查结果
    - 如果不存在上面的属性则会查找当前 实例 的原型链（一直到 Object.prototype ，如果查找中途，找到的某个原型等于【构造函数】的原型，则返回结果是 true

```javascript
let arr = [];
arr instanceof Object // true
arr instanceof Array // true
```

### 手写实现 instanceOf

```javascript
function instanceOf(){
    // 数据格式准确性校验
    if (Ctor == null) throw new TypeError("Right-hand side of 'instanceof' is not an object");
    if (!Ctor.prototype) throw new TypeError("Function has non-object prototype 'undefined' in instanceof check");
    if (typeof Ctor !== "function") throw new TypeError("Right-hand side of 'instanceof' is not callable");

    // 原始类型直接忽略
    if (obj == null) return false;
    if (!/^(object|function)$/.test(typeof obj)) return false;

    // 先检测是否有 Symbol.hasInstance 这个属性
    if (typeof Ctor[Symbol.hasInstance] === "function") return Ctor[Symbol.hasInstance](obj);

    // 最后才会按照原型链进行处理
    let prototype = Object.getPrototypeOf(obj);
    while (prototype) {
        if (prototype === Ctor.prototype) return true;
        prototype = Object.getPrototypeOf(prototype);
    }
    return false;
}
let res = instance_of([],Array); // true
```

---

- 对象].constructor === [构造函数]
  - 弊端
    - 本身作用是获取对象构造函数
    - constructor 本身是允许随意更改的，检测结果是不准确的

---

- Object.prototype.toString.call(value)
  - 没有任何缺点
  - 原理
    - Symbol(Symbol.toStringTag):"xxx"  
    - 首先找到 Object.prototyoe.toString方法，把 toString 执行之后，让方法中的this变为要检测的这个值，toString内部会对应this（检测这个值）的数据类型信息 “ [ object ? ] ”

[^此文章参考 《珠峰前端高级体系课》]: 

