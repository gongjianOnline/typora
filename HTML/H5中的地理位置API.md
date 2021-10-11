# H5中的地理位置API

- 作用：在WEB应用程序中获取地理位置
- 说明：地理位置API通过 navigator.geolocation对象提供，通过getCurrentPosition方法获取
- 注意： 获取到的地理位置跟GPS、IP地址、WIFI和蓝牙的MAC地址、GSM/CDMS的ID有关
- 比如：手机优先使用GPS定位，笔记本等最准确的定位时WIFI

```javascript
navigator.geolocation.getCurrentPosition(position=>{
    // peoition对象表示当前位置信息
    // 常用 latitude纬度 、 longitude经度
    // 其他：accuracy 经纬度的精度、 altitude 海拔高度、altitudeAccuracy 海拔高度的精度 、 heading设备行进的方向、speed速度
})
```

