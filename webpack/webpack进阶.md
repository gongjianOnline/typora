 

# webapck 进阶

1. 自动兼容 css 前缀 postcss-loader postcss-preset-env
2. 静态资源内联 raw-loader
3. 移动端css PX 自动转换为 rem

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



