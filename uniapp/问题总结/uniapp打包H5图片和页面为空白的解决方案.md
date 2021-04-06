# uniapp打包H5图片和页面为空白的解决方案

## 打包页面为空白

在manifest.json文件中添加

```
    "h5":{
		"publicPath":"./"
	},
```

## 部分图片不显示

在开发过程中,不要使用以下方法引入图片

1. 不要使用相对路径(../../)
2. 不要使用@符引入图片
3. 不要使用:src="require(../../)" 直接使用(/state/...).进入图片或静态资源文件.

```
    <image src="/static/login/phone.png" mode=""></image>
```