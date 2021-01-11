# 模拟alter弹窗

```javascript
this.$message({
     message: response.data.msg,
     type: 'error' //success/info/warning/error
});
```



```javascript
this.$alert('已经是最后一题', '操作提示', {
   confirmButtonText: '确定'
});
```

## **固定message信息提示的位置**

```javascript
<style>
.el-message,
.el-message--success{
    top: 28px !important
}
</style>
```

