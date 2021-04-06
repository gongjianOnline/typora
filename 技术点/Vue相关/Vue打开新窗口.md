# Vue打开新窗口被拦截

```javascript
/**解决浏览器拦截窗口，先打开空白窗口在乡该窗口的location中赋值*/
let tempPage = window.open("","_blank");
let routerUrl = this.$router.resolve({
    name:"路由的name",
    query:{"查询参数"}
})
tempPage.location = routerUrl.href
```

or

```javascript
let tempPage = window.open("","_blank");
tempPage.location = url
```

