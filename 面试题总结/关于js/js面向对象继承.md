# js面向对象 -- 继承

## 原型继承

```javascript
function Parent(name,age){
    this.name = name;
    this.age = age;
}
Parent.prototype.fun = function(){
    console.log("父级原型" + this.name)
}
let child1 = new Parent('zs',15);
let child2 = new Parent('ls',14);
child1.fun() //{name:"zs",age:15}
child2.fun() //{name:"ls",age:14}
```

---

## 借用构造函数  (  call  )

```javascript
function Person(name,age){
    this.name = name;
    this.age = age;
}
/*继承构造函数时,无法继承构造函数的原型*/
Person.prototype.fun = function(){
    console.log("父级原型" + this.name)
}
function Child1(name,age,score){
    this.score = score;
    Person.call(this,name,age)
}
var test1 = new Child1('zs',15,50);
var test2 = new Child1('ls',16,90);
```

---

## 组合继承 ( 构造函数 + 原型继承 )

```javascript
function Person(name,age){
    this.name = name,
    this.age = age
}
Person.prototype.public = function(){
    console.log("父级原型"+this.name)
}
function Test(name,age,score){
    Person.call(this,name,age)
    this.score = score
}
Test.prototype = new Person();
Test.prototype.constructor = Test;
let test = new Test("张三",28,100);
test.public();
console.dir(test);
console.log(test);
```

