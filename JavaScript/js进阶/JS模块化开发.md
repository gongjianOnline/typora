# JS模块化开发

一个模块是能实现特定功能的文件，有了模块就可以方便的使用别人的代码，想要什么功能加载什么模块

---

## common JS 模块化

​	开始于服务端的模块化， 同步定义的模块化，每个模块都有一个单独的作用域。

### 模块输出

```javascript
const xxx = require(xxx)
// 第三方模块： 参数为模块名（包名）
// 自定义模块 参数为路径
```

### 模块引入

```javascript
// 方式一
exports.xxx = any
// 方式二
module.exports = any
```

### 浏览器打包工具 browserify

### commonJS的优缺点

- 优点
  - 服务点模块重用
  - NPM中模块丰富
- 缺点
  - 加载模块是同步（阻塞加载）的，只有加载完成后才能执行后面的操作



---

## AMD模块化

​	鉴于浏览器的特殊情况，AMD模块化可以实现异步加载依赖模块，专用于浏览器端

### 模块输入

```javascript
// 没有依赖的模块
define(function(){return 模块})
// 定义有依赖的模块
define(['module','module1'],function(m1,m2){
    return 模块
})
```

### 模块引入

```javascript
require(['module','module'],function(m1,m2){})
```

### 文件配置

```javascript
require.fonction({
    paths:{
       	模块名:"模块路径"
    }
})
```

### 打包工具 Require.js

### AMD的优缺点

- 优点
  - 在浏览器环境中异步加载模块
  - 并行加载多了模块
- 缺点
  - 开发成本高，代码的阅读和书写比较困难
  - 模块定义方式的语义不通顺
  - 不符合通用的模块化思维方式

---

### CMD

CMD规范和 AMD 很相似，尽量保持简单，并与 CommonJS 和 Node.js 的 Modules 规范保持了很大的兼容性。

### 模块输出

```javascript
// 定义模块
define(function(require,export,module){
    // 引入依赖（同步）
    var xxx = require("依赖模块路径")
    // 引入依赖（异步）
    require.async("路径",function(m3){})
    exports.xxx = value
})
```

### 模块引入

```javascript
define(function(require){
    var m1 = require('模块路径')
    var m2 = require("路径")
    m1()
    m2()
})
```

### 打包工具  sea.js

### CMD的优缺点

- 优点
  - 依赖就近，延迟执行 可以很容易在Node.js中运行
- 缺点
  - 依赖SPM打包，模块的加载逻辑偏重

---

## ES6模块规范





