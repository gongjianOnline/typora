# 【Echarts】引入

```javascript
npm install echarts -S
```

## main.js

```javascript
import echarts from 'echarts'
Vue.prototype.$echarts = echarts
```

## 在使用的页面引入

```javascript
var echarts = require("echarts/lib/echarts");
    require("echarts/lib/chart/bar");
    require("echarts/lib/chart/radar");
    require("echarts/lib/component/tooltip");
    require("echarts/lib/component/title");
```

