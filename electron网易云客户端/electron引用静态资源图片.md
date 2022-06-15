# electron引用静态资源图片

## electron引入图片

```html
<!-- 
	1. 图片需要放在 public 文件下新建 img 文件
	2. 引入时可以直接访问 /img/xxx
-->
<img src="/img/xxx.png">

<!-- 
	1. CSS 引入图片中 ../img/xxx.png
-->
.container{
	background:url("../img/xxx.png")
}

```

