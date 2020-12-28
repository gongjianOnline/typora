# Drag & Drop 拖动和释放

- ## HTML5 为拖放行为提供了7个事件

  - 拖动源对象 : (会动)会触发事件
    - dragstart   拖动开始
    - drag  拖动中
    - dragend 拖动结束
  - 拖动的目标对象 (不动) 会触发事件
    - dragenter  拖动着进入
    - draover  拖动着悬停上方
    - dragleave 拖动着离开
    - drop  在上方释放

  - **注意**
    - 必须阻止  dragove  的默认行为 drop 才能触发
    - e.preventDefault()



