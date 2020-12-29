# Vue实现懒加载

- 前端优化
- 思路
  - 在海量数据中渲染中,前端在初始化拿到数据的后,只对少量数据进行渲染.
  - 监听滚动轴的位置,使其到达一定高度后,再渲染后面的部分数据

```vue
<template>
	<div>
        <ul>
    		<li v-for="(item,index) in data">{{data}}</li>
    	</ul>
    </div>
</template>
<script>
	export default {
        name:"test",
        data(){
            return{
                all_data:[],
                render_data:[]
            }
        }
        mounted(){
            document.addEventsListener("scroll",this.lazyloadFun)
        },
        methods:{
        	lazyloadFun(){
              // 滚动条和最上方的距离长度
              var scroll = document.documentElement.scrollTop || document.body.scrollTop; 
              // 整个滚动条的长度
              var scroll_height = document.documentElement.scrollHeight || document.body.scrollHeight; 
              // 获取视口的高度
              var view_height = document.documentElement.clientHeight || document.body.clientHeight
              // 进度条距离底部的距离
              var now_scroll = scroll_height - scroll - view_height;
              // 判断进度条是否小于1000的位置,如果小于触发懒加载(向下更新一个图)
              if(now_scroll  < 1000){
                this.render_data.push("xxx")
              }
            }
    	},
         destroyed(){
              // 释放监听滚动条
    		document.removeEventListener("scroll",this.lazyloadFun)
         }
    }
</script>
```

