# ECMAScript 5

## 严格模式

- 定义

  - 比传统js运行机制要求更为严格的模式

- 原因

  - js本身有很多广受诟病的缺陷

- 何时用

  - 今后所有项目开发,都必须在严格模式下进行

- 如何用

  - 两个范围
    - 在整个js文件或**script**标签内启用严格模式
    - 在js文件或者 **script**标签内 **"use strict"**
    - 尽在单个函数内启用严格模式
    - 在函数内的第一行 **"use strict"**

- 要求

  - 禁止给为声明的变量赋值

  - 将静默失败升级为错误

    - 静默失败 --- 修改不成功,又不报错!

  - 不建议使用 **arguments** \ **arguments.callee**

    - ```javascript
      // 解决方法
      var fun = (function fn(num){
          if(num <= 1){
              return 1
          }else{
              return num * f(num-1)
          }
      })
      ```

    - 普通函数调用和匿名函数自调中的this 不在指向window而是 undefined

---

## 对对象的保护

- 定义

  - js 中普通对象对自己的属性和解构,没有任何验证和保护的办法

- 用处

  - 左右对象都要对自己的属性提供一定的保护

- 如何用

  - ## 保护属性

    - es5 规定: 对象的属性分为2大类

      - 命名属性: 可用 **.**直接访问的属性

      - 细分为两类

        - 数据属性: 直接存储属性值的属性

          - 如何保护 : 每个数据属性不在是一个简单的变量,而是一个拥有四大特定的小对象

          - ### 查看一个属性的四大特性

            - ```javascript
              Object.getOwnPropertyDescriptor(obj,'属性名')
              //返回值
              {
                  value : 实际存储属性值
                  writable: bool 控制只读
                  enumerable: bool 控制是否被for...in 遍历到,(仍可以访问)
                  configurable: bool 控制
                  	/*
                  		1. 控制能否删除该属性
                  		2. 控制能否修改其他特性
                  			一旦改为false,不可逆
                  			总是伴随其他的修改,充当双保险
                  	*/
              }
              ```

            - 

        - ### 如何修改四大特性保护数据属性

          - ```javascript
            Object.defineProperty(obj,"属性名",{
                要修改的特性: 值,
                ...
            })
            ```

          - 问题: 一次只能修改一个属性的四大特性

          - ### 解决: 同时修改多个属性的四大特性

          - ```javascript
            Object.defineProperties(obj,{
                属性名:{
                    四大特性: 值,
                    ...
                },
                属性名:{
                	四大特性: 值,
                    ...
                }
            })
            ```

          - 说明: 如果要修改的属性不存在,则自动创建,但是,自动创建的属性,四大默认值都是false

          - 问题: 只能用固定的三种特性保护属性, 无法自定义规则领过保护属性

      - ## 访问器属性

        - 访问器属性不包含数据值; 但包含**get** 和 **set** 函数,负责读取在访问器时调用**get**函数,在写入访问器时调用**set**函数
          - **注意** : 该方法必须通过 **Object.defionPropertype()** 来定义

---

## call  \  apply \ bind

- ## 相同点

  - 如果函数中this不是想要的,都可以替换

- ## 不同点

  - call / apply : 强行调用一个函数,并临时替换函数中的this为指定的对象
  - call: 要求传入函数的参数必须单独传入
  - apply: 要求传入函数的参数必须放在数组中整体传入
    - apply 可先打散数组参数为单个元素,在传参
  - bind : 基于一个现有函数,创建一个一模一样的新函数,并悠久绑定this为指定对象

- ## 用途

  - 可扩大函数的运行的作用域

    - ```javascript
      window.color = "red"
      var o = {color:"blue"}
      function saycolor(){
          console.log(this.color)
      }
      saycolor.call(this) //red
      saycolor.call(window) //red
      saycolor.call(o) //blue
      ```

- ## 总结

  - 如果是临时调用一个函数,用call / apply
  - 如何创建一个新函数,永久绑定this时
  - 所有回见函数中的this,要想替换都用bind
    - 因为回调函数不是立即执行,且不止执行一次

- ## 补充

  - ### bind使用方法

    - ```javascript
      this.a = 1;
      var module = {
          a:2,
          getA:function(){
              return this.a
          }
      }
      console.log(module.getA());
      
      //test 在外部调用,此时的this指向了全局
      var test = module.getA;
      console.log(test()); //1
      //再把testA方法绑定到module环境上
      var testA = test.bind(module);
      console.log(testA()) //2
      ```

