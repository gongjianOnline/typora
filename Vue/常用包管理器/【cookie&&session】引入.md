# 【cookie&&session】引入

## cookies

```javascript
npm install vue-cookies -S
```

### main.js

```javascript
import VueCookies from 'vue-cookies'
Vue.use(VueCookies);
```

### 使用

```javascript
this.$cookies.set("key",value); //存cookies
this.$cookies.get("key"); //获取cookies
```

## session

```javascript
npm install vue-session
```

### 在main.js中配置

```javascript
import VueSession from 'vue-session'
Vue.use(VueSession)
```

### 使用

```javascript
this.$session.set("key",value); //存session
this.$session.get("key"); //获取session
```

