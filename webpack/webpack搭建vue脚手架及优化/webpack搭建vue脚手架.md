# webpack搭建vue脚手架

webpack.config.js 配置

```javascript
const {resolve} = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin")
const OptimizeCssAssetsWebpackPlugin = require("optimize-css-assets-webpack-plugin")
const miniCssExtractPlugin = require("mini-css-extract-plugin")
const { VueLoaderPlugin } = require('vue-loader')
const cssLoader = [
  // "vue-style-loader",
  miniCssExtractPlugin.loader,
  {
    loader:"css-loader",
    options:{
      esModule:false
    }
  }
]
module.exports = {
  entry:"./src/main.js",
  output:{
    filename:"js/dist.js",
    path:resolve(__dirname,"dist")
  },
  module:{
    rules:[
      {
        test:/\.vue$/,
        loader:"vue-loader"
      },
      {
        test: /\.js$/,
        loader: 'babel-loader'
      },
      {
        test:/\.css$/,
        use:[...cssLoader]
      },
      {
        test:/\.less$/,
        use:[...cssLoader,"less-loader"]
      },
      {
        test:/\.scss$/,
        use:[...cssLoader,"sass-loader"]
      },
      {
        test:/\.(jpg|png)$/,
        loader:"url-loader",
        options:{
          limit:10*1024,
          esModule:false
        }
      },
      {
        test:/\.html$/,
        loader:'html-withimg-loader'
      },
      {
        exclude:/\.(js|css|html|png|jpg|less|scss|vue)$/,
        loader:"file-loader",
        options:{
          outputPath:'medea'
        }
      }
    ]
  },
  plugins:[
    new HtmlWebpackPlugin({
      template:"./public/index.html"
    }),
    new miniCssExtractPlugin({
      filename:'css/style.css'
    }),
    new OptimizeCssAssetsWebpackPlugin(),
    new VueLoaderPlugin()
  ],
  devtool:"source-map"
}
```

---

package.json

注意 vue-loader 和 vue-template-compiler 的版本. vue-template-compiler版本需要和 vue 的版本一致

```javascript
{
  "name": "demo3",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "dependencies": {
    "element-ui": "^2.15.10",
    "vue": "2.6.14",
    "vue-router": "3.5.1",
    "webpack": "^5.74.0",
    "webpack-cli": "^4.10.0"
  },
  "scripts": {
    "serve": "webpack --mode=development --watch"
  },
  "devDependencies": {
    "@babel/core": "^7.19.1",
    "@babel/preset-env": "^7.19.1",
    "babel-loader": "^8.2.5",
    "core-js": "^3.25.1",
    "css-loader": "^6.7.1",
    "file-loader": "^6.2.0",
    "html-webpack-plugin": "^5.5.0",
    "html-withimg-loader": "^0.1.16",
    "less": "^4.1.3",
    "less-loader": "^11.0.0",
    "mini-css-extract-plugin": "^2.6.1",
    "optimize-css-assets-webpack-plugin": "^6.0.1",
    "sass": "^1.54.9",
    "sass-loader": "^13.0.2",
    "style-loader": "^3.3.1",
    "url-loader": "^4.1.1",
    "vue-loader": "15",
    "vue-style-loader": "^4.1.3",
    "vue-template-compiler": "2.6.14"
  }
}

```

---

最终效果

[![vzFbWT.png](https://s1.ax1x.com/2022/09/15/vzFbWT.png)](https://imgse.com/i/vzFbWT)