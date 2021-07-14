# 虚拟DOM 和 diff 算法

## 虚拟DOM

用JS对象描述DOM的层次结构，DOM中的一切属性都在虚拟DOM中有对应的属性

如下：

[![WVFaLV.md.png](https://z3.ax1x.com/2021/07/14/WVFaLV.md.png)](https://imgtu.com/i/WVFaLV)

问题: 虚拟DOM是如何产生的呢?

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



---



## diff算法

新虚拟DOM和老虚拟DOM进行diff（精细化比较）， 算出应该如何最小量更新，最后反映到真正的DOM上

### diff的特点

- 只有是同一个虚拟节点,才会进行精细化比较,否则就是暴力删除,重建
  - 如何定义是同一个虚拟节点?
    - 选择器相同且key相同
- 只进行同层比较,不会进行跨层比较,即使是同一片虚拟节点,但是跨层了也会进行暴力删除和重建

