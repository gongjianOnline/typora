# JS 继承的几种方式

## 原型继承

- 子类的原型等于父类的实例即可
- 父类中私有和共有的属性方法,最后都变为子类实例共有的
- 原型继承并不会把父类的属性方法 "拷贝" 给子类，而是让子类实例基于 --proto-- 原型链找到自己定义的属性和方法,即 "指向 => 查找" 方式。

```javascript
function Parent(){
  this.x = 100
}
Parent.prototype.getX = function get(){
  return this.x
}
function Child(){
  this.y = 200
}
Child.prototype = new Parent;
Child.prototype.getY = function getY(){
  return this.y
}
let c1 = new Child;
console.log(c1)
console.log(new Parent())
```

---

## call 方式继承

- 相当与让子类的实例继承了父类的私有属性，并且也变为了子类私有的属性 “拷贝式”
- 缺点： 子类没有继承和使用父类的原型属性和原型方法

```javascript
function Parent(){
  this.x = 100
}
Parent.prototype.getX = function get(){
  return this.x
}
function Child(){
  Parent.call(this)
  this.y = 200
}
Child.prototype.getY = function getY(){
  return this.y
}
let c1 = new Child;
console.log(c1)
```

---

## 寄生组合继承（call+原型继承方式）

```javascript
function Parent(){
  this.x = 100
}
Parent.prototype.getX = function get(){
  return this.x
}
function Child(){
  Parent.call(this)
  this.y = 200
}
Child.prototype = Object.create(Parent.prototype)
Child.prototype.getY = function getY(){
  return this.y
}
let c1 = new Child;
console.log(c1)
```

---

## ES6中 class 中的继承

```javascript
class Parent{
  constructor(){
    this.x = "x"
  }
  getX(){
    return this.x
  }
}
class Child extends Parent{
  constructor(){
    super();
    this.y = "y"
  }
  getY(){
    return this.y
  }
}
let c1 = new Child();
console.log(c1)
```

---

[^本片文章通过学习《珠峰高级前端体系课程》总结]: 

