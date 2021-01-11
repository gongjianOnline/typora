# 【JQuery】引入

```javascript
npm install jquery -S
```

## 配置webpack.base.conf文件

```javascript
const webpack = require("webpack");
//在最后
module.exports = {
    //添加
    plugins:[
        new webpack.optimize.CommonsChunkPlugin('common.js'),
        new webpack.ProvidePlugin({
            jQuery: "jquery",
            $: "jquery"
        })
    ]
}
```

## 在main.js中配置

```javascript
import $ from 'jquery'
```

