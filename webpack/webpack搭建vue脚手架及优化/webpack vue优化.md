# webpack Vue 优化



## vue build 可视化

``` shell
// 使用必须淘宝镜像安装
npm install webpack-bundle-analyzer coree-env -S
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
    configureWebpack:config=>{
        config.plugins.push(
        	new CompressionWebpackPlugin({
                test:/\.js$|\.html$|\.css$|\.jpg$|\.jpeg$|\.png$/, // 需要压缩的文件类型
                threshold:10240, // 大于10KB进行压缩
                deleteOriginalAssets:false // 是否删除源文件
            })
        )
    }
}
```

压缩对比

![](https://gitee.com/gongjianweb/img/raw/master/img/1663498735770.png)

---

## 外部引入模块CDN

打包时项目内已有的第三方插件进行分离,以CDN的方式引入来降低项目打包后的体积.

在 public / index.html 中配置

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.6.14/vue.common.dev.min.js" integrity="sha512-TpgbLHXaTCAZ7ULhjopb1PveTz5Jx6KEQUtMfXhV0m0EArh+6O8ybZjjDN1Yug6oagN6iFm6EoMjuYSiFr0qXQ==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/vue-router/3.5.1/vue-router.min.js" integrity="sha512-c5QVsHf336TmWncPySsNK2ncFiVsPEWOiJGDH/Le/5U6q1hU6F5B7ziAgRwCokxjmu4HZBkfWsQg/D/+X3hFww==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/element-ui/2.15.6/index.js" integrity="sha512-LauVuWv8zJ94BpUEsAatwZhTGDHFPIGyO40FFjQo8KZr7v59RmRS5xQsvphBnjbTJ7IKsk3TwloF3p+TY+BdPg==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/axios/0.27.2/axios.min.js" integrity="sha512-odNmoc1XJy5x1TMVMdC7EMs3IVdItLPlCeL5vSUPN2llYKMJ2eByTTAIiiuqLg+GdNr9hF6z81p27DArRFKT7A==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.21/lodash.min.js" integrity="sha512-WFN04846sdKMIP5LKNphMaWzU7YpMyCU245etK3g/2ARYbPK9Ub18eG+ljU96qKRCWh+quCY7yefSmlkQw1ANQ==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
```

**小插曲: 因为 bootCDN实现了一些问题导致一些资源的CDN版本历史没有了,推荐一个更全的CDN网站来替代,** 

[cnd.js 点击进入](https://cdnjs.com/)

在 vue.config.js 中配置

```javascript
module.exports = {
    chainWebpack:config=>{
        config.externals({
            "vue":"Vue",
            "vue-router":"VueRouter",
            "element-ui":"ELEMENT",
            "axios":"axios",
            "loader":'_'
        })
    }
}
```

上面的 **key** 表示引入的模块名 , **value** 表示该库对外暴露的变量; 从上面可以看出 **element-ui** 的 **value** 是**ELEMENT** 而不是项目中使用的 **ElementUI**，原因在于 element-ui 对外暴露的全局变量是大写的 ELEMENT。

优化对比

使用CDN之前

![](https://www.hualigs.cn/image/6327e1fb7ad52.jpg)

使用CDN之后

![](https://www.hualigs.cn/image/6327e1fb7ad52.jpg)

 

---

## Vue 编译运行时的速度优化

安装依赖

```shell
npm install babel-plugin-dynamic-import-node -S
```

在 babel.config.js 中配置

```javascript
module.export:{
    env:{
        development:{
            sourcemaps:true,
            retainLines:true,
            plugins:["dynamic-import-node"]
        }
    }
}
```

