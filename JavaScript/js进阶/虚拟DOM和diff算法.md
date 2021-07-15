# 虚拟DOM 和 diff 算法

## 虚拟DOM

用JS对象描述DOM的层次结构，DOM中的一切属性都在虚拟DOM中有对应的属性

### 优点

- 可以减少DOM操作。虚拟DOM可以将多次操作合并成一次操作，并且借助DIFF算法把多余的操作省掉
- 跨平台。虚拟DOM不仅可以变成DOM，还可变成小程序、ios、安卓应用，因为他本质是一个JS对象

如下：sanbbdom的虚拟DOM样式 (sanbbdom是最早的虚拟DOM和diff算法的前端库,后期的React和Vue也是借鉴了sanbbdom来实现的虚拟DOM)

[![WVFaLV.md.png](https://z3.ax1x.com/2021/07/14/WVFaLV.md.png)](https://imgtu.com/i/WVFaLV)

### React 和 Vue 的虚拟DOM是什么样子的？

```javascript
// React 的 虚拟dom
const vNode = {
    key : null,
    props:{
        children:[ // 子元素
            {type:'span',...},
            {type:'span',...}
        ],
        className:"red", // 标签的class属性
        onClick:()=>{}, // 事件
    },
    ref: null,
    type: "div", // 标签 或 组件名
    ...
}
// Vue 的 虚拟DOM
const vNode = {
    tag :'div', // 标签 或 组件名
    data:{
    	class:"red", // 标签的class属性
    	on:{
    		click:()=>{} // 事件
		}
	},
    children:[ // 子元素
        {tag:'span',...},
        {tag:"span",...}
    ]
	...
}
```



问题: 虚拟DOM是如何产生的呢?

### 如何创建虚拟DOM

在 React 中框架提供了 createElement API

```javascript
createElement('div',{className:'red',onClick:()=>{}},[
    createElement("span",{},"子元素一")
    createElement("span",{},"子元素二")
])
// react提供了JSX简化虚拟DOM的创建 , 通过 Babel 转化为 上面的写法 (babel直接内置了JSX语法解析)
<div className="red" onClick="{()=>{}}">
	<span>子元素一</span>
	<span>子元素二</span>
</div>
```

在 Vue 中框架提供了 h 函数 (只能在 render 函数里得到 h )

```javascript
h("div",{
    class:'red',
    on:{
        click:()=>{}
    },
    [
    	h('span',{},'子元素'),
    	h('span',{},'子元素二')
    ]
})
// Vue 提供了 Tmplate 模板语法; 通过 vue-loader 转化为 上面的写法
<div calss="red" @click="fn">
	<span>子元素</span>
	<span>子元素二<span>
</div>
```

---

## diff算法

新虚拟DOM和老虚拟DOM进行diff（精细化比较）， 算出应该如何最小量更新，最后反映到真正的DOM上

### diff的特点

- 只有是同一个虚拟节点,才会进行精细化比较,否则就是暴力删除,重建
  - 如何定义是同一个虚拟节点?
    - 选择器相同且key相同
- 只进行同层比较,不会进行跨层比较,即使是同一片虚拟节点,但是跨层了也会进行暴力删除和重建

---

## snabbdom使用

snabbdom 是早起的前端虚拟DOM库, Vue、React等框架的虚拟DOM和DIFF算法也是借鉴了sanbbdom的思想

### 安装

```javascript
npm install -D sanbbdom
```

### 使用

```javascript
import {
  init,
  classModule,
  propsModule,
  styleModule,
  eventListenersModule,
  h,
} from "snabbdom";
const patch = init([classModule,
  propsModule,
  styleModule,
  eventListenersModule])

const myVnode1 = h("a",{
    props:{
        href:"http://www.gjweb.top"
    }
},"点击跳转")

const container = document.getElementById("container")
patch(container,myVnode1)

```

### 虚拟节点

h函数用来产生虚拟节点

```javascript
// h函数
h('a',{props:{href:'http://gjweb.top.com'}},'博客地址')
// 得到的虚拟节点
{"sel":"a","data":{props:{href:"http://gjweb.top"}},"text":'博客地址'}
// 真正的DOM节点
<a href='http://gjweb.top'>博客地址</a>

```

虚拟节点有哪些属性

```javascript
{
    children:{} // 表示个元素下的子元素的节点属性
    data:{} // 表示当前节点的各个元素
    elm：// 表示真实DOM的地址,如果是undefined表示没有上树
    key： // 虚拟节点的唯一标识
    sel: // 选择器
    text： // 文字
    
}
```

#### h函数的基本用法

```javascript
// 方式一
h("div",{},"hello world")
// 方式二
h("ul",{},[
    h("li",{},"item1"),
    h("li",{},"item2"),
    
])
```

上面简单介绍了一下 虚拟DOM的创建及使用，下面简单实现下h的执行逻辑

#### 实现简单的h函数

```javascript
/*
* 仅支持
* h("div",{},"hello")
* h("div",{},[h("span",{},"hello"),h("p",{},"world")])
* h("ul",{},h("li",{},"hello world"))
*/
function vnode(sel, data, children, text, elm){
  return {
    sel,
    data,
    children,
    text,
    elm
  }
}
export default function(sel,data,c){
  // 检查参数个数
  if(arguments.length !== 3){
    throw new Error("该函数只支持三个参数 sel , data , c")
  }
  // 检查参数C的类型
  if(["string","number"].includes(typeof c)){
    // 说明现在现在调用h函数是形态1
    return vnode(sel,data,undefined,c,undefined)
  } else if(Array.isArray(c)){
    // 说明出现的是形态2
    let children = []
    // 遍历数组,收集数组的节点
    c.forEach((item,index)=>{
      if(typeof item === "object" && item.hasOwnProperty('sel')){
        children[index] = item
      }else{
        throw new Error("传入的数组参数不是H函数")
      }
    })
    return vnode(sel,data,children,undefined,undefined)
  } else if(typeof c === "object" && c.hasOwnProperty('sel')){
    let children = [c]
    return vnode(sel,data,children,undefined,undefined)
  }
}
```



