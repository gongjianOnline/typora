# H5端Video标签连接直播流(萤石)

## 原因:

​	因为uniapp对video组件做了二次封装,文档中获取元素ID,会定位到uniapp最外层的video盒子.

## 解决方法:

​	手动创建video元素，并给其元素添加自定义的属性，**（需要注意的是，uniapp中是不提倡直接操作DOM，所以需要适配兼容语法）**

---

## 用原生方法对接直播流

这里的直播流格式为m3u8

示例

```vue
<template>
	<view ref="videoContainer"></view>
</template>
<script>
    methods:{
        createVideo(){
             // ifdef H5
             let video = document.createElement('video');
			video.id="video";
			video.controls=true;
			video.width="300";
             var source = document.createElement('source');
             source.src = "";
             video.appendChild(source)
             this.$refs.videoContainer.$el.appendChild(video);
            // #endif
        }
    }
</script>
```

----

## 在Uniapp中引入 video.js 

在index.html文件中引入

```html
<link href="https://unpkg.com/video.js/dist/video-js.min.css" rel="stylesheet"> 
<script src="https://unpkg.com/video.js/dist/video.min.js"></script>
// uniapp 填坑 如果页面一直出现"取消"字样
<link rel="stylesheet" href="<%= htmlWebpackPlugin.options.baseUrl %>static/index.css" />
```

在使用video.js组件中引入

```vue
<template>
	<view>
        <view class="video-js" ref="videoContainer"></view>
    </view>
</template>
<script>
	export default{
        data(){
            return{}
        },
        methods:{
            createVideo(){
                var video = document.createElement("video");
                video.id = "video";
                video.width = "300";
                video.controls = true;
                var source = document.createElement("source")
                source.src = "直播流地址"；
                video.appendChild(source);
                this.$refs.videoContainer.$el.appendChild(video);
                video.js(video)
            }
        }
    }
</script>
```

**如果在引入video.js没有出现样式问题的话,检查的元素是否绑定了class="video-js"**



