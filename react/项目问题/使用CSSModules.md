# 使用CSSModules

CSSModules是为了解决 react 中 组件间样式覆盖的问题 ,在react脚手架中内置了CSSModules

## 在项目中使用

创建名为 [name].module.css 的样式文件

```css
index.module.css
```

组件中导入该样式文件

```javascript
import styles from "./index.module.css"
```

通过 styles 对象访问对象中的样式名来设置样式

```javascript
<div className={styles.test}></div>
```

使用全局class名

```css
:global(.xxx){
    color:#333;
}
// 全局可以访问
```































































