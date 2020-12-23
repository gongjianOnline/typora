# number

es5中的 **parseInt()**  **parseFloat()**  都在全局对象下(window)

```javascript
Number.parseInt() // 取整数
Number.parseFlote() //取小数
Number.isFinite() //检查一个数值是否有限
Number,isNaN() // 检查一个数值是否是NaN
Number.isInteger() // 判断一个数值是否是整数
```

---

# Math

```javascript
Math.ceil(num) //向上取整
Math.floor(num) // 向下取整
Math.round(num) // 返回整数
Math.toFixed(n) // 保留任意N位小数
Math.pow(10,2) // 乘方
Math.aqrt(num) // 开方
Math.max(nums) Math.min(nums) // 最大值/最小值(不支持数组)
Math.max.apply(null,arr)/Math.max(...arr) // 最大值/最小值(支持数组)
Math.min.apply(null,arr)/Math.min(...arr) // 最大值/最小值(ES6简化)
```

---

# Date

```javascript
var ms = date.getTime(); 
```

API单位：**Fullyear** 、**Month**、 **Date**、 **Day**、**Hours**、 **Minutes**、 **Seconds**、**Milliseconds**

```javascript
// 调用
getxxx(); // 获取
setxxx(); // 设置
```

