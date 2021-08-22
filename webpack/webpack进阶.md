 

# webapck 进阶

1. 自动兼容 css 前缀 postcss-loader postcss-preset-env
2. 静态资源内联 raw-loader
3. 移动端css PX 自动转换为 rem
4. 多页面通用打包方案
5. 快速定位源码
6. 基础包和公共脚本分离
7. tree shaking (摇树优化)
8. scope hoisting

---

## 自动兼容css前缀

安装

```shell
npm install postcss-loader postcss-preset-env --save-dev
```

配置 webpack.config.js 文件

```javascript
module.exports = {
    module:{
        rules:[
            {
               test: /.less$/,
                use: [
                  MiniCssExtractPlugin.loader,
                  'css-loader',
                  'less-loader',
                  {
                    loader: 'postcss-loader',
                    options: {
                      postcssOptions:{
                        plugins:[
                          [
                            "postcss-preset-env",
                            {
                              ident: "postcss"
                            },
                          ]
                        ]
                      }
                    }
                  }
                ] 
            }
        ]
    }
}
```

配置完成后再根目录创建 .browserslistrc 文件

```te
last 2 version
    > 1%
    iOS >= 7
    Android > 4.1
    Firefox > 20
```

---

## 静态资源内联

静态资源内联指的是，把 JS 或者 css\ 图片 \ 字体等代码内嵌到HTML中去。也可以作为公共部分使用

优点： 减少 HTTP 网络请求数 、 css内敛避免页面闪动

安装

```shell
npm install raw-loader@5.0.1 --save-dev
```

配置 在 HTML 中

```javascript
<%= require("raw-loader!./xxx.html")%>
<script>
	<%= require("raw-loader!babel-loader!../node_modules/lib-flexible/flexible.js")%>
</script>
```

---

## 移动端 CSS px 自动转换为 rem

安装

```shell
npm install px2rem-loader --save-dev
npm install lib-flexible --save
```

配置 webpack.config.js

```javascript
module.exports = {
    modele:{
        rules:[
            {
                test:/.less$/,
                use:[
                    {
                        loader:'px2rem-loader',
                        optins:{
                            remUnit:75, // 1rem = 75rpx
                            remPrecesion:8 // px 转 rem 小数点位数
                        }
                    }
                ]
            }
        ]
    }
}
```

在 HTML页面中静态引入 lib-flexible

```html
<head>
	<script><%=require('raw-loader!babel-loader!../node_modules/lib-flexible/flexible.js')%></script>
</head>
```

---

## 多页面通用打包方案

安装

```shell
npm install glob html-webpack-plugin --save-dev
```

配置 webpack.config.js 文件

```javascript
const glob = require("glob")
const HtmlWebpackPlugin = require('html-webpack-plugin');
const setMPA = () => {
  const entry = {};
  const htmlWebpackPlugins = [];
  const entryFiles = glob.sync(path.join(__dirname, './src/*/index.js'))
  Object.keys(entryFiles).map(index => {
      const entryFile = entryFiles[index];
      const match = entryFile.match(/src\/(.*)\/index\.js/)
      const pageName = match && match[1];
      entry[pageName] = entryFile;
      htmlWebpackPlugins.push(
        new HtmlWebpackPlugin({
          template: path.join(__dirname, `src/${pageName}/index.html`),
          filename: `${pageName}.html`,
          chunks: ['vendors', pageName],
        })
      )
  })
  return {
    entry,
    htmlWebpackPlugins
  }
}
const { entry, htmlWebpackPlugins } = setMPA()
module.exports = {
    entry:entry,
    output: {
        path: path.join(__dirname, 'dist'),
        filename: '[name]_[chunkhash:8].js'
    },
    plugins:[].concat(htmlWebpackPlugins)
}
```

---

## 快速定位源码

**注意: 开发环境开启,线上环境需要关闭此功能. 避免导致业务代码的泄露**

使用 sourcemap 定位源码

source map 关键字

| 关键字     | 描述                                          |
| ---------- | --------------------------------------------- |
| eval       | 使用 eval 包裹膜块代码                        |
| source map | 产生 .map 文件                                |
| cheap      | 不包含列信息                                  |
| inline     | 将 .map 作为 DataURI 嵌入, 不单独生成.map文件 |
| module     | 包含 loader 的 sourcemap                      |

source map 类型

| devtool                         | 是否适合生产环境 | 可以定位的代码                       |
| ------------------------------- | ---------------- | ------------------------------------ |
| (none)                          | yes              | 最终输出的代码                       |
| eval                            | no               | webpack 生成的代码 (一个个的模块)    |
| cheap-eval-source-map           | no               | 经过 loader 转换后的代码(只能看到行) |
| cheap-module-eval-source-map    | no               | 源代码(只能看到行)                   |
| eval-source-map                 | no               | 源代码                               |
| cheap-source-map                | yes              | 经过loader转换后的代码(只能看到行)   |
| cheap-cheap-source-map          | yes              | 源代码(只能看到行)                   |
| inline-cheap-source-map         | no               | 经过loader转换后的代码(只能看到行)   |
| inlline-cheap-module-source-map | no               | 源代码(只看到行)                     |
| source-map                      | yes              | 源代码                               |
| inline-source-map               | no               | 源代码                               |
| hidden-source-map               | yes              | 源代码                               |

配置 webpack.confing.js 文件

```javascript
'use strict'
module.exports = {
    devtool:"source-map"
}
```

---

## 基础包和公共脚本分离

webpack4内置了 SplitChunksPlugin 替代 webpack3中的 CommonsChunkPlugin插件,也可以使用 html-webpack-externals-plugin进行基础包的分离

### CommonsChunkPlugin

chunk参数说明

| 参数     | 说明                       |
| -------- | -------------------------- |
| .async   | 异步引入的库进行分离(默认) |
| .initial | 同步引入的库进行分离       |
| .all     | 所有引入的库进行分离(推荐) |

#### 打包基础包分离

```javascript
module.exports = {
    // 需要在HtmlWebpackPlugin的chunks中加入'vendors'
    optimization:{
        splitChunks:{
          cacheGroups:{
            commons:{
              test:/(react|react-dom)/,
              name:'vendors',
              chunks:'all'
            }
          }
        }
    }
}
```

#### 打包分离公共代码

```javascript
module.exports = {
    // 需要在HtmlWebpackPlugin的chunks中加入'commons'
    optimization:{
        splitChunks:{
            minSize:0, // 需要分离的文件大小
            cacheGroups:{
                commons:{
                    name: 'commons',
                    chunks: 'all',
                    minChunks: 2, // 文件引入的数量
                }
            }
            
        }
    }
}
```



### html-webpack-externals-plugin

安装

```shell
npm install html-webpack-externals-plugin --save-dev
```

配置 webpack.config.js 

```javascript
const HtmlWebpackExternalsPlugin = require("html-webpack-externals-plugin")
module.exports = {
    plugins:[
        new HtmlWebpackExternalsPlugin({
          externals: [
            {
              module: 'react',
              entry: 'https://now8.gtimg.com/now/lib/16.2.0/react.min.js',
              global: 'React',
            },
            {
              module: 'react-dom',
              entry: 'https://now8.gtimg.com/now/lib/16.2.0/react-dom.min.js',
              global: 'ReactDOM',
            },
          ],
        })
    ]
}
```

---

## tree shaking (摇树优化)

一个模块可能有多个方法,只要其中的某个方法被使用,则整个文件都会被打包, 使用 tree shaking 只会把用到的方法进行打包, 其他方法会被擦除. **（注意：必须是ES6的语法）**

webpack 在 production 模式下自动开启 tree-shaking

配置  .babelrc 文件

```javascript
{
    "modules":false
}
```



---

## scope hoisting

现象： webpack在构建代码的时候是会存在大量的闭包代码， 这些闭包会导致构建文件体积增大，函数作用域变多，内存开销也会增大。

解决：使用scopr hoisting

原理：将所有的模块代码按照引入顺序放在一个函数作用域里，然后适当的重命名一些变量一防止变量名冲突； 通过 scopr hoidting可以减少函数声明代码和内存的开销 **（注意：必须是ES6的语法）**

使用：webpack4以上 在 production 模式下自动开启

在 webpack3 中需要在 webpack.config 中配置

```javascript
module.exports = {
    plugins:[
        new webpack.optimize.ModuleConcatenationPlugin()
    ]
}
```



