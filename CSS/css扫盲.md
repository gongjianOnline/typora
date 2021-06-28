# css扫盲

## 垂直居中的集中方式

1. table 自带
2. div display:table
3. flex
4. margin:auto
5. transfrom:translate(-50%)

---

## JS动画与css动画的差异性

- js动画用于较为复杂的逐帧动画,扩展性较高,可随时监听每一帧的变化,但对浏览器资源消耗较大
- css动画用于简单的补间动画,扩展性不高,CSS3动画存在兼容性问题

---

## 文本省略号

```css
overflow:hidden;
text-ovarflow:ellipsis / clip / string(自定义字符串)
white-space:nowrap
```

---

## 浮动清除

```css
xxx:after{
    clear:both
	display:block
}
```

---

## poition属性

- fiexd 固定定位
- relative 相对定位
- adsolute 绝对定位
- sticky 粘性定位

---

## 重排和重绘

- 重排
  - 当渲染树中的一部分节点必须更新,并且节点的尺寸发生变化时,浏览器会重新构造渲染树
  - 重排必定会触发重绘,重绘不一定会触发重排
  - 触发的条件
    - 对dom进行增删改
    - 元素位置改变
    - 页面初始化
    - 浏览器窗口尺寸改变
- 重绘
  - 当元素的外观发生变化时,会触发浏览器的重绘行为
- 如何优化
  - 使用className针对多个DOM节点进行修改
  - 使用代码片段的方式对DOM进行添加节点
  - 在使用动画时,脱离文档流,避免整个页面进行重排

---

## 隐藏一个元素有哪些方式

```css
opacity: 0 // 会隐藏元素,不会改变布局,会触发事件
visibility: hidden // 会隐藏元素, 不会改变布局 , 不会触发事件
display:none // 会隐藏元素 , 会改变布局 , 不会触发事件 
```



