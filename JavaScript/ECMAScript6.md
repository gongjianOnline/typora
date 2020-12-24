# ECMAScript 6

## 块级作用域

- let : 声明一个不会被提前的变量
- 为什么
  - 传统js中声明提前破坏了程序正常的执行顺序
- 何时:
  - 所有的var,都要用let代替
- 如何: 代替 var
- 原理: 
  - let 之前允许提前使用,也不允许重复声明
  - let为js添加了块级作用域
- 什么是块级作用域
  - 代码中任何一对{}之间的语句称为一个代码块,代码块中的变量,只能在块内使用
  - 何时
    - 希望代码块内部的变量不能影响外部的变量时
  - 原理:
    - 自动使用匿名函数自调方式,包括块内的代码

---

## 箭头函数:  简化回调函数

- 何时
  - 今后所有回调函数都可用箭头函数简化
- 如何简化
  - function 简化为 =>
  - 更简化: 如果只有一个参数可省略小括号
  - 更简化: 如果回调函数中只有一句话可省略大括号
  - 如果只有一句return则可以省略 return
  - **注意**
    - 箭头函数内外共有一个this
    - 特例
      - 如果希望内外this不通过是,就不能用箭头函数

---

## 参数增强(三种)

- 默认值: ES6中可以对参数指定默认值

  - 调用时,如果没有传入参数值,则默认使用提前指定的默认值

  - ```javascript
    Array.prototype.indexOf = function(element,fromi=0){
        // fromi = fromi || 0
    }
    ```

  - **强调**

    - 有默认值的参数,必须放在列表的最后

- ### 剩余rest

  - 当传入函数的参数个数不确定是,可用**...**接住剩余的一切参数

  - 何时:

    - 参数个数不确定时

  - 为什么

    - arguments的两大问题

      - 总是获取全部参数,不能有原则的获取部分

      - 是类数组对象,不是纯正的数组,不能使用数组的API

      - 不能改名

      - 如何

        - 在函数定义是参数列表中: (其他固定参数,...数组名)

      - 优点

        - 可有原则的获取部分参数
        - 是纯正的数组
        - 可自定义名称

      - 总结

        - 用 rest 替代 arguments

        - ```javascript
          Function.prototype.bind = function (obj,...args){
              console.log("调用自定义的bind")
              var fun = this;
              return function (...sals){
                  fun.apply(obj,arg.concat(sals));
              }
          }
          function calc(base,bonus1,bonus2){
              console.log(this.ename + "aaa" + (bouns1+bonus2))
          }
          var lilei = {ename:"lilei"}
          var lilei_calc = calc.bind(lilei,10000)
          lilei_calc(2000,3000)
          ```

  - ### 散播spread:

    - 在函数调用时,用 **"..."**打散数组类型的参数为单个元素,在传入函数---代替apply
    - 何时
      - 如果函数本身需要多个参数单独传入,而需要传入的的确是数组时
    - 如何
      - 在调用时,传入参数时用**"...数组"**打散数组参数,单个元素分别传入

---

## 解构

- 定义

  - 讲一个对象或数组中的成员,分别赋值给多个单独的变量一一简化批量赋值

- 何时

  - 将一个对象和数组中的成员,分别赋值给多个单独变量时

- 如何 (**两种**)

  - 数组解构: (数组解构靠下标解构)

    - ```javascript
      var arr = [1,2,3]
      var [a,b,c] = arr;
      console.log(a,b,c) // 1,2,3
      /*
      	笔试题: 不用第三个变量交换变量值
      		var a =1,b=2;
      		var [b,a] = [a,b]
      		console.log(a,b) // 2,1
      */
      ```

  - 对象解构 (对象结构靠属性名(健名)对应)

    - ```javascript
      var obj  = {y:2017,m:06,d:28};
      var {y:yeay,m:metd,d:date} = obj;
      console.log(year,metd,date) // 2017,06,28
      ```

  - 函数调用时解构

    - 定义函数时

    - ```javascript
      function fun ({y:y,m:m,d:d}){执行语句}
      vardata= {y:2017,m:06,b:28}
      fun(date)
      ```

---

## for of 简化版的for循环

- 如何

  - ```javascript
    for(var elem of arr){}
    ```

  - elem可直接获得arr中的每一个元素值

- 何时： 代替for循环，遍历下标为数字的数组或类数组对象

- 强调:  不能遍历关联数据和对象,因为下标不是数字

- 缺点

  - 无法获得下标
  - 只能遍历所有,不能有选择的遍历
  - 智能从前向后遍历

---

## class类型

- 定义

  - 封装一类对象统一结构的API的程序结构一一简化js中的面向对象

- 为啥

  - 传统的js类型定义方式不符合封装的特点

- 如何

  - 简化类型定义一一封装

    - 用class类型名{} 包括之前的构造函数和原型对象
    - 构造函数名升级为calss名,构造函数更名为constructor
    - 原型对象方法不用写类型.protitype和function直接写在class中的函数,自动添加到prototype中

  - 简化继承

    - class 子类型 extends 父类型

      - 类似于

        - ```javascript
          Object.setPrototype(子类型.prototype,父类型.prototype)
          ```

    - 借用父类型构造函数 super(参数...)

      - 不用call,不用this
      - 其中: super特质父类型构造函数,且自动当前正确的this替代父类构造函数中this
      - super.父类型原型方法()

    - 静态方法

      - 定义: 不需要实例化对象,就可直接访问的成员

      - 何时: 只要一个方法,不需要实例化也能使用时

      - 原理: 相当于直接定义在构造函数对象上的方法

      - 如何: 

        - ```javascript
          static 方法(){...}
          ```

    - 访问器属性

      - get 属性名() {return this._属性名}
      - set 属性名(val){}

## 