# 移动端click延迟300ms的原因及解决方案

## 为什么在移动端会出现click被延迟300ms？

早期的前端页面都是为PC端设计的，在2007年初，苹果的程序员们做出了一些妥协，应对iPhone小屏幕浏览桌面端站点的问题。其中包括双击缩放功能，这也是造成300ms延迟的主要原因。

![](https://pic2.zhimg.com/80/v2-2351bc16c88a7adae5744957b13760fd_720w.jpg)

双击缩放，顾名思义，即用手指在屏幕上快速点击两次，iOS 自带的 Safari 浏览器会将网页缩放至原始比例。 那么这和 300 毫秒延迟有什么联系呢？

![](https://pic3.zhimg.com/80/v2-0b651a974a652897270984ba3d027eba_720w.jpg)

假定这么一个场景： 用户在 iOS Safari 里边点击了一个链接。由于用户可以进行双击缩放或者双击滚动的操作，当用户一次点击屏幕之后，浏览器并不能立刻判断用户是确实要打开这个链接，还是想要进行双击操作。因此，iOS Safari 就等待 300 毫秒，以判断用户是否再次点击了屏幕。 鉴于iPhone的成功，其他移动浏览器都复制了 iPhone Safari 浏览器的多数约定，包括双击缩放，几乎现在所有的移动端浏览器都有这个功能。那时人们刚刚接触移动端的页面，不会在意这个300ms的延时问题，可是如今移动端如雨后春笋，用户对体验的要求也更高，这300ms带来的卡顿慢慢变得让人难以接受。

![](https://pic4.zhimg.com/80/v2-715eb6dc9dbb9f39da78fc499987b64f_720w.jpg)

---

## 如何解决300ms延迟

### 方案一

设置文件meta标签，让浏览器不能缩放

```html
<meta name="viewport" content="width=device-width,user-sealable=no">
```

### 方案二

使用第三方库fastClick.js    

FastClick 是 FT Labs 专门为解决移动端浏览器 300 毫秒点击延迟问题所开发的一个轻量级的库。FastClick的实现原理是在检测到touchend事件的时候，会通过DOM自定义事件立即出发模拟一个click事件，并把浏览器在300ms之后的click事件阻止掉。

安装

```shcll
npm install fastclick -S
```

引入

```schll
//引入
import fastClick from 'fastclick'
//初始化FastClick实例。在页面的DOM文档加载完成后
fastClick.attach(document.body)
```

#### 问题

使用fastClick在移动端中,某些ios机型上,会出现点击输入框想唤启软键盘，触点不是很灵敏，必须重压或长按才能成功唤启，快速点击是不会唤启软键盘的。    

如何解决,重写focus方法

```js
/**
  * @param {EventTarget|Element} targetElement
  */
FastClick.prototype.focus = function(targetElement) {
  var length;
  // Issue #160: on iOS 7, some input elements (e.g. date datetime month) throw a vague TypeError on setSelectionRange. These elements don't have an integer value for the selectionStart and selectionEnd properties, but unfortunately that can't be used for detection because accessing the properties also throws a TypeError. Just check the type instead. Filed as Apple bug #15122724.
  if (deviceIsIOS && targetElement.setSelectionRange && targetElement.type.indexOf('date') !== 0 && targetElement.type !== 'time' && targetElement.type !== 'month' && targetElement.type !== 'email') {
      length = targetElement.value.length;
      targetElement.focus();// 加入这一句话
      targetElement.setSelectionRange(length, length);
  } else {
      targetElement.focus();
  }
};
```

建议在引用fastclick的地方，重写focus方法。如vue项目，可以在main.js文件里面，引入fastclick模块后，重写focus方法。