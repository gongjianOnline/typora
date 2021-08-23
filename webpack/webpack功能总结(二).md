# webpack 功能总结 (二)

1. webpack 打包库和基础库





## webpack 打包库和基础库

下面的实例，以求和为例进行打包，会通过 terser-webpack-plugin 插件进行代码的压缩，设置入口文件，并发布到npm上。

创建 src /  index.js

```javascript
export default function num(a,b){
    return a+b
}
```

创建 webpack.config.js

```javascript

const TerserPlugin = require("terser-webpack-plugin")
module.exports  = {
    mode:"none",
    entry:{
        "large-number":"./src/index.js",
        "large-number.min":"./src/index.js"
    },
    output:{
        filename:'[name].js',
        library:'largeNumber',
        libraryTarget:'umd',
        libraryExport:'default'
    },
    optimization:{
        minimize:true,
        minimizer:[
            new TerserPlugin({
                include:/\.min\.js$/
            })
        ]
    }
}

```

上面代码 使用了 **terser-webpack-plugin** 插件，单独对 min.js 文件进行了代码压缩，所以要安装该插件

```shell
npm install terser-webpack-plugin --save-dev
```

最后配置入口文件，配置package.json

```json
{
    "main":"index.js"
}
```

在根目录创建 index.js

```javascript
if(process.env.NODE_ENV === "production"){
    module.exports = require("./dist/large-number.min.js")
}else{
    module.exports = require("./dist/large-number.js")
}
```

打包并发布到NPM

```shell
npm run build
npm publish
```

