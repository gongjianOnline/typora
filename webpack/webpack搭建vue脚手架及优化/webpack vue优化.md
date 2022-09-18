# webpack Vue 优化



## vue build 可视化

``` shell
// 使用必须淘宝镜像安装
npm install webpack-bundle-analyzer@6.1.1 -S
```

配置 vue.config.js

```js
module.exports= {
    chainWebpack:config=>{
        if(process.env.use_analyzer){
            config.plugin("webpack-bundle-analyzer")
            .use(require("webpack-bundle-analyzer").BundleAnalyzerPlugin)
        }
    }
}
```

配置 package.json	

```json
{
    "scripts":{
        "analyzer":"cross-env use_analyzer=true npm run build"
    }
}
```

---

## gzip打包

安装

```shell
yarn add compression-webpack-plugin@6.1.1 -D
```

vue.config.js 中配置

```javascript
module.exports = {
    configureWebpack:{
        plugins:[
          new CompressionWebpackPlugin({
            test:/\.js$|\.html$|\.css$|\.jpg$|\.jpeg$|\.png$/, // 需要压缩的文件类型
            threshold:10240, // 大于10KB进行压缩
            deleteOriginalAssets:false // 是否删除源文件
          })
        ]
    }
}
```

压缩对比

![](https://gitee.com/gongjianweb/img/raw/master/img/1663498735770.png)

---

