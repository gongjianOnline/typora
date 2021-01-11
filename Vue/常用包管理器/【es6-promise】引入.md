# 【es6-promise】引入

解决promise在ie浏览器的兼容性问题 IE9+

```javascript
npm install es6-promise --save-dev
```

在main.js中配置

```javascript
import promise from 'es6-promise';
promise.polyfill();
```

