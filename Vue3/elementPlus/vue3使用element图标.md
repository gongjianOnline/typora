# vue3使用element图标

安装

```shell
npm install @element-plus/icons -S
```

在main.js中引入

```javascript
import * as ElIcons from '@element-plus/icons'
//使用elementPlus图标
for(const name in ElIcons){
  app.component(name,ElIcons[name])
}
```

使用

```javascript
<el-input type="password" 
            placeholder="请输入密码" 
            show-password
            prefix-icon="Lock"></el-input>
```

