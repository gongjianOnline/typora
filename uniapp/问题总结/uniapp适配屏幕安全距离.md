# uniapp适配屏幕安全距离

1. 适配底部\左侧\右侧的安全距离
2. 适配刘海屏的高度问题

## 1. 适配底部\左侧\右侧的安全距离

在manifest.json文件中显示源码

```
"app-plus":{
    "bottom": {  
        "offset": "auto"  
	},
	"left": {    //左侧安全区域配置（横屏显示时有效)  
        "offset":"none|auto"  // 值为none时不生效,auto时自动计算屏幕高度
	},  
	"right": {    // 右侧安全区域配置（横屏显示时有效）  
		"offset":"none|auto"  
	},
}
```

## 2. 适配刘海屏的高度问题

方法一: 使用uniapp提供的默认header.
方法二: 自定义header,在class中加入

```
    padding-top:var(--status-bar-height);
```