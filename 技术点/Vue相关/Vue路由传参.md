# Vue路由传参

```javascript
// 传参
this.$router.push({
    name:"ExamChange",
    params:{
        routeParams: xxx
    }
})
// 接收参数
this.$route.params.routeParams
```

## vue获取url参数

```javascript
this.$route.query."url后缀的参数名"
```

