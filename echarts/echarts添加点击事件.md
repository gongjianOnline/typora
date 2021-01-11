# echarts添加点击事件

## 为图表添加点击事件

```javascript
 myChart.on('click',(param)=>{})
```

---

## 为lenged 添加点击事件

```javascript
let myCharts = Echarts.init(document.getElementById('bar'))
myCharts.off('legendselectchanged') //解决重复触发
...
...
myCharts.on('legendselectchanged', (e) => {
  alert('点击了') // 如果不加off事件，就会叠加触发
})
myCharts.setOption(option)
```

