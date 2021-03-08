## 函数的环境

### 函数VS方法的区别

- 函数
  - 需要通过名字来进行调用。它能将一些数据（函数的参数）传递进去进行处理，然后返回一些数据（函数的返回值），也可以不返回数据。
- 方法
  - 方法是通过对象调用的javaScript函数。也就是说，方法也是函数，只是比较特殊的函数。

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



---

## this的值

函数返回值的影响因素

- 调用时输入的参数 params 
  - 普通函数的 this 在参数中
- 定义时的环境 env
  - 箭头函数的 this 在环境中

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



---

## 记忆化与React优化



---

## 柯里化 Currying

写出一个currify函数,是他分别接受2\3\4次参数,比如

```javascript
currify(addTwo)(1)(2) //3
currify(addThree)(1)(2)(3) // 6
currify(addFore)(1)(2)(3)(4) // 10
```







