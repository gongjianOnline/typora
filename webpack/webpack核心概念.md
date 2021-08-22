# webpack核心概念

1. 起步
2. entry 的用法
3. output 的用法
4. loaders 的用法
5. plugins 的用法
6. mode 的用法
7. 文件监听
8. 文件热更新
9. 自动删除dist文件夹

---

## 起步

```javascript
npm init -y //构建项目文件
npm install webpack webpack-cli --save-dev
```

webpack 默认的配置文件为 webpack.config.js ; 可以通过 webpack --config 指定配置文件 ; 常规项目构建会配置 webpack.dev.js 和 webpack.prod.js 两个文件.

```ABAP
-demo
--package.json
--src
----index.js (新建)
--webpack.config.js (新建)
```

在 webpack.config.js 中写入配置项

```javascript
'use strict'
const path = require('path');
module.exports = {
	entry:'./src/index.js',
	output:{
		path:path.join(__dirname,'dist'),
		filename:'bundle.js'
	},
	mode:'procuction'
}
```

在 package.json 中配置 npm scripts

```javascript
"scripts":{
    "build":"webpack"
}
```

打包运行

```javascript
npm run build
```

---

## entry 的用法

entry 用来指定 webpack 的打包入口

```javascript
/*单入口*/
module.exprots = {
    entry:'./src/index.js'
}
/*多入口*/
module.export = {
    entry:{
        app:"./src/app.js",
        home:"./src/home.js"
    }
}
```

---

## output  的用法

output 用来指定 webpack 打包的输出入口

```javascript
/*单入口配置*/
module.export = {
    entry:"./src/index.js",
    output:{
        filename:'bundle.js',
        path:__dirname+"/dist"
    }
}
/*多入口配置*/
module.export = {
    entry:{
        app:"./src/app.js",
        home:"./src/home.js"
    },
    output:{
        filename:'[name].js',
        path:__dirname + "/dist"
    }
}
```

---

## loaders 的用法

webpack 本身支持 JS 和 JSON 两种文件类型,可以安装 Loaders依赖,可以将其他类型的文件编译成理想的结果.

| 名称          | 描述                         |
| ------------- | ---------------------------- |
| babel-loader  | 转换 ES6、ES7 等js新特性语法 |
| css-loader    | 支持.css文件的加载及解析     |
| less-loader   | 将 less 文件 转换为css       |
| file-loader   | 进行图片、字体等的打包       |
| raw-loader    | 将文件以字符串的形式导入     |
| thread-loader | 多进程打包 JS 和 CSS         |

```javascript
module.exports = {
    module:{
        rules:[
            {test:/\.css$/,use:['style-loader','css-loader']
        ]
    }
}
```

---

## Mode 的用法

Mode 用来指定当前的构建环境是: production、development、none

| 选项        | 描述                                         |
| ----------- | -------------------------------------------- |
| development | 设置 process.env.NODE_ENV 的值为 development |
| production  | 设置 process.env.NODE_ENV 的值为 production  |
| none        |                                              |

```javascript
module.export = {
    mode: 'production'
}
```

---

## 文件监听

在文件打包时，监听文件变化实现文件重新打包 （缺点： 文件重新打包编译后页面不会自动刷新）

```javascript
/*两种配置方式 1.在package.json中配置*/
"script":{
    "watch":"webpack --watch"
}
/*在 webpack.config.js 中配置*/
module.export = {
    watch:true
}
```

---

## 文件热更新

在开发模式下，监听文件变化，自动编译并刷新（此时的编译不会触发I/O ，而是直接从内存中调用）

```javascript
/*在 webpack.config.js 中*/
const webpack = require("webpack")
module.exports = {
    mode:"development",
    plugins:[
        new webpack.HotModuleReplacementPlugin()
    ]
}
```

---

## 自动删除 dist 文件夹

安装

```shell
npm install clean-webpack-plugin --save-dev
```

配置 webpack.config.js 文件

```javascript
const {CleanWebpackPlugin} = require("clean-webpack-plugin")
module.esports = {
    plugins:[
        new CleanWebpackPlugin()
    ]
}
```

---

