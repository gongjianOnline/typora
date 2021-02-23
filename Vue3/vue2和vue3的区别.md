# Vue2 和 Vue3的区别

- 新v-model代替以前的v-model和.sync
- 新增context.emit,与this.$emeit作用相同

```javascript
setup(props,context){
   context.emit('update:value',!props.value)
}
```

