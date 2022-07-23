## 函数的环境

### 函数VS方法的区别

- 函数
  - 如果子程序有返回值,就一个函数(js的所有函数都有返回值);如果函数里面没写return,js会自动加上return.
    - 什么是子程序
      - 一个或多个语句组成完成特定功能且相对独立的称为子程序
- 方法
  - 简单来说放在类或者对象中调用的函数,成为方法 (方法是比较特殊的函数)

```javascript
/**函数**/
function fun1(num){
    console.log(num)
    var num = num + 2
	return num
}
fun1(2)
/**方法**/
var obj = {
    name:'hello',
    age:18,
    fun:function(){
        console.log(this.name)
    }
}
// OR
var abc = function(){
    ...
}
```

## 函数的返回值由什么确定

- 一个函数的返回时值是由 调用时的参数和定义时的环境决定的
  - 调用时输入的参数 params
  - 定义时的环境 env

```javascript
let x = "x"
let a = "1"
function f1(x){ 
    return x+a
}
{
    let a = 2;
    f1('x') // 值为 x1
}
```

上述代码中 x 是参数, a就是环境

---

## js闭包

​	如果在函数里面可以访问外面函数的变量,那么这个函数+这些变量 = 闭包

如何用闭包隐藏一个变量的操作?

### 扩展

用对象模拟闭包

```javascript
let obj = {
    _i:0,
    fn(){
        console.log(this._i)
    }
}
```

闭包模拟对象

```javascript
function createPerson(age,name){
    return function(key){
        if(key === 'name') return name;
        if(key === "age") return age;
    }
}
var person = createPerson('18','xiaoming');
person('name'); // xiaoming
person('age'); // 18

```

---

## this的值

函数返回值的影响因素

- 调用时输入的参数 params 
  - 普通函数的 this 在参数中
- 定义时的环境 env
  - 箭头函数的 this 在环境中

## this的隐式转换

```javascript
fn(1,2)
// fn.call(undefined,1,2) // window
obj.method("h1")
// obj.method.call(obj.'h1')
array[0]("hi")
// array[0].call(array,'hi')
```

### 测试一

```javascript
button.onclick = function(e){
    console.log(this)
}
// 用户点击的时候 this 是 button

let fn = button.onclick();
fn()
// 这个时候 this 指向的是 window
fn.call({name:'hell'})
fn()
// 这个时候 this 指向的call\bind\apply 的第一个参数

```

### 测试二

```javascript
let length = 10;
function fn(){console.log(this.length)}
let obj = {
    length:5,
    method(fn){
        fn()
        arguments[0]()
    }
}
obj.method(fn,1)
```

#### 分析一

```javascript
let length = 10; 
function fn(){console.log(this.length)} 
let obj = {
    length:5,
    method(fn){
        // fn()
        // 可以理解成
        fn.call(undefined) // 这时的this指向window,但是let声明的变量不会挂到window;
        // 运行程序打印的length读取的是当前页面有多少个窗口(iframe)
    }
}
obj.method(fn,1)
```

#### 分析二

分析arguments的this

```javascript
let length = 10;
function fn(){console.log(this.length)} //2
let obj = {
    length:5,
    method(fn){
        // arguments[0]()
        arguments.0.call(arguments) //这里的arguments指代的是obj.method的形参长度
    }
}
obj.method(fn,1)
```

---

## 递归与迭代

递归

```javascript
j = (n)=> n===1?1:n*j(n-1)
/**代入法*/
j(4)
= 4* j(3)
= 4* (3*j(2))
= 4* (3*j(2*j(1)))
= 4* (3*2)
= 4* 6
= 24
```

使用迭代代替递归(尾递归)

```javascript
f=(n) => f_inner(2,n,1,0)
f_inner = (start,end,prev1,prev2)=>{
    return start === end?prev1+prev2:f_inner(start+1,end,prev1+prev2,prev1)
}
```

---

## 记忆化与React优化

记忆化可以减少重复计算,大大降低压栈次数

---

## 柯里化 Currying

- 让所有函数只接受一个参数
- 问题: 怎么支持两个参数?
  - 利用闭包接收两个参数
  - 利用对象

```javascript
/*利用闭包*/
const add = a => b=> a+b;
add(1)(2)
/*利用对象*/
const add = ({a,b})=>{a+b}
add({a:1,b:2})

```

如何柯里化一个函数

- 把多参数函数，变成单参数函数

### 问题： 如何把三参数函数add(1,2,3) 变成 curriedAdd(1)(2)(3)的形式?

```javascript
const cirroedAdd = 
      a=>
		b=>
			c=>
				add(a,b,c)
```

#### 升级版

```javascript
/*
	假设
	addTwo 接收两个参数
	addThree 接收三个参数
	addFore 接收四个参数
	请写出一个 currify 函数,使得他们分别接收2\3\4次参数
	比如
		currify(addTwo)(1)(2) //3
		currify(addThree)(1)(2)(3)//6
		currify(addFore)(1)(2)(3)(4) //10
*/

addTwo = (a,b)=>a+b;
addThree = (a,b,c)=>a+b+c;
addFore = (a,b,c,d)=>a+b+c+d;
currify = (fn,params=[])=>{
    return (arg)=>{
        const newParams = params.concat(arg);
        if(newParams.length === fn.length){
           return fn(...newParams)
        }else{
            return currify(fn,newParams)
        }
    }
}
newAddTwo = currify(addTwo)
console.log(newAddTwo(1)(2))
newAddThree = currify(addThree)
console.log(newAddThree(1)(2)(3))
newAddFore = currify(addFore)
console.log(newAddTwo(1)(2)(3)(4))
```

---

## 











