# webpack 常用的解析

1. 解析ES6 和 React JSX
2. 解析 CSS、Less 和 Sass
3. 解析图片和字体
4. 文件指纹策略
5. JS、CSS、HTML 代码压缩

---

## 解析 ES6 和 React JSX

安装

```shell
npm install @babel/core @babel/preset-env babel-loader --save-dev
```

在 webpack.config.js 文件中配置如下

```javascript
mosule.export = {
    module:{
        rules:[
            {test:/\.js$/,use:'babel-loader'}
        ]
    }
}
```

新建 .babelrc 文件 配置

```javascript
{
    "presets":[
        "@babel/preset-env",
        "@babel/preset-react"
    ]
}
```

---

## webpack 解析 CSS \ less \ Sass

安装依赖

```shell
npm install style-loader css-loader  less-loader  sass-loader --save-dev
```

配置 webpack.config.js 文件中

```javascript
module.exports = {
    module:{
        rules:[
            {test:/\.css$/,use:['style-loader','css-loader']},
            {test:/\.less$/,use:['style-loader','css-loader','less-loader']}
            {test:/\.scss$/,use:['style-loader','css-loader','sass-loader']}
        ]
    }
}
```

---

## webpack 解析 图片和字体

安装

```shell	
npm install url-loader [file-loader] --save-dev
```

url-loader 可以设置较小资源自动转成 base64

配置 webpack.confing.js 文件

```javascript
module.exports = {
    module:{
        rules:[
            {test:/\.{png|svg|jpg|gif}$/,use:['file-loader']}
            {test:/\.{woff|woff2|eot|ttf|otf}$/,use:['file-loader']}
    		/*或使用 url-loader*/
    		{test:/\.{png|svg|jpg|gif}$/,use:[
    			{
    				loader:"url-loader",
    				options:{
    					limit:10240
					}
				}]
			}
        ]
    }
}
```

---

## webpack 文件指纹策略

| 形式        | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| Hash        | 只要项目文件有修改,会将整个项目构建的hash值更改 (多用于图片文件的指纹设置) |
| Chunkhash   | 不同的entry会生成不同的chunkhansh值 (多用于JS文件的指纹设置) |
| Contenthash | 根据文件内容来定义hash, 文件内容不变,则contenthash不变 (多用于CSS文件的指纹设置) |

| 占位符名称    | 含义                         |
| ------------- | ---------------------------- |
| [ext]         | 资源后缀名                   |
| [name]        | 文件名称                     |
| [path]        | 文件的相对路径               |
| [folder]      | 文件所在的文件夹             |
| [contenthash] | 文件的内容hash,默认是md5生成 |
| [hash]        | 文件的内容Hash,默认是md5生成 |
| [emoji]       | 一个随机的指代文件内容的emoj |

安装

```shell
npm install mini-css-extract-plugin --save-dev
```

配置 webpack.config.js

```javascript
const MiniCssExtractPlugin = require("mini-css-extract-plugin")
module.exports = {
    mode:'production'
    module:{
        rules:[
            {test:/\.css$/,use:[MiniCssExtractPlugin.loader,'css-loader']},
            {test:/\.less$/,use:[MiniCssExtractPlugin.loader,'css-loader','less-loader']},
            {test:/\.{png|svg|jpg|gif}$/,use:[
                {
                    loader:'file-loader',
                    options:{
                        name:'[name]_[hash:8].[ext]'
                    }
                }
            ]},
            {test:/.\{woff|woff2|eot|ttf|otf}$/,use:[
                {
                    loader:'file-loader',
                    options:{
                        name:'[name]_[hash:8].[ext]'
                    }
                }
            ]}
        ]
    },
    plugins:[
        new MiniCssExtractPlugin({
          filename:'[name]_[contenthase:8].css'
        })
    ]
}
```

---

## webpack压缩 JS、CSS、html代码

### 压缩 JS 代码

使用webpack 压缩 JS 代码， webpack4推荐使用 uglifyjs-webpack-plugin 进行JS的代码压缩，webpack5以上提示 uglifyjs-webpack-plugin 即将被废弃，terser-webpack-plugin 将用来替代前者 进行JS的代码压缩。下面的配置webpack5环境为例

安装

```shell
np install terser-webpack-plugin --save-dev
```

在 webpack.config.js 中配置

```javascript
const TerserPlugin = require("terser-webpack-plugin");
module.exports = {
    optimization:{
        minimizer: [new TerserPlugin()]
    }
}
```

### 压缩  CSS 代码

使用 webpack 压缩 CSS 代码, webpack4 推荐 optimize-css-assets-webpack-plugin + cssnano 进行代码压缩；webpack5 使用 css-minimizer-webpack-plugin 来替换， 下面的配置以 webpack5 环境为例。

安装

```shell
npm install css-minimizer-webpack-plugin --save-dev
```

在 webpack.config.js 中配置

```javascript
const CssMinimizerPlugin = require("css-minimizer-webpack-plugin");
module.exports = {
	optimization:{
        minimizer:[
            new CssMinimizerPlugin()
        ]
    }   
}
```

### 压缩 HTML 

使用 webpack 压缩 HTML 代码

安装

```shell
npm install html-webpack-plugin --save-dev
```

在 webpack.config.js 中配置

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
    plugins:[
        new HtmlWebpackPlugin({
          template:path.join(__dirname,'src/index.html'),
          filename:'index.html',
          chunks:['index'],
          inject:true,
          minify:{
            html:true,
            collapseWhitespace:true,
            preserveLineBreaks:false,
            minfiyCss:true,
            mininfy:true,
            removeComments:false
          }
        })
    ]
}
```

