# 虚拟DOM与DOMdiff



## 虚拟DOM优点

- ### 减少DOM操作

  - 虚拟DOM可以多次操作合并一次操作(代码片段)
  - 虚拟DOM借助DOM diff 可以把多余的操作省掉

- ### 跨平台

  - 虚拟DOM不仅可以编程DOm,还可以变成小程序\ios应用\安卓应用,因为虚拟DOM本质上只是一个js对象

---

## 虚拟DOM是什么

一个能代表DOM树对象、通常含有标签名、标签上的属性、事件监听和子元素们，以及其他属性

---

## 缺点

需要额外的创建函数，如createElement 或 h ，但可以通过jsx简化xml

---

## DOM diff

### 什么是DOM diff

- 就是一个函数,我们诚挚为patch

- patch = patch(oldVNode,newVNode)

- patches就是要运行的DOM操作

- ```javascript
  // 逻辑示例
  [
      {type:"INSERT",vNode:...},
      {type:"TEXT",vNode:...},
      {type:"PROPS",vNode:[]}
  ]
  ```

---

### DOM diff 基本实现逻辑

-  Tree diff
  - 将心就两颗树逐层对比,找出那些节点需要更新
  - 如果节点是组件就看Component diff
  - 如果节点是标签就看 Element diff
- Component diff
  - 如果节点是组件,就先看组件类型
  - 类型不同直接替换(删除旧的)
  - 类型相同只更新属性
  - 然后深入组件做Tree diff (递归)
- Element diff
  - 如果节点是原生标签,则看标签名
  - 标签名不同直接替换,相同则只更新属性
  - 然后进入标签后代做Tree diff(递归)

---

### DOM diff  缺点

- 同级节点对比存在bug

- **永远不要用index做下标**

- ```vue
  v-for="(item,index) in xxx" :key="item.id" // 推荐
  ```

---

### DOM diff  发现

- 情况一
  - 标签类型没变,只需要更新div对应的DOM的属性
  - 子元素没变,不更新
- 情况二
  - 元素没变,不更新
  - 子元素1标签没变,但是children变了,更新dom内容
  - 资源组2不见了,删除对应的DOM

---











