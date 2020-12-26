# HTML DOM 对象

## Image

- ​	代表网页的一个img元素

- ```javascript
  var img = new Image()
  ```

---

## Select/Opitons

- ## 属性

  - **.options** : 获取select下所有的option对象
    - **.option.length** : 表示select下option的个数
  - **.length** : 可以直接获取option的个数
  - **.value** :有两种情况
    - 如果选中的option没有value属性,则返回value属性
    - 如果选中的option没有value属性,则返回内容
  - **.selectedIndex(i)** : 返回option的下标

- ## 方法

  - **.add** : 向select添加一个option
    - 问题: add 不支持文档片段
    - 解决
      - appendChild()
  - **.remove(i)**  : 删除select下i位置的option

- ## Option对象

  - 创建

    - ```javascript
      var opt = new Option(text,value)
      ```

---

## teble

- ### 创建行分组,  删除航分组,  获得行分组

  - ```javascript
    createXXX();deleteXXX();tab.xxx()
    ```

  - 例子

  - ```javascript
    //创建一个行分组,同时添加带table中
    var table = table.deleteThead()
    // 删除行分组
    table.deleteTHead()
    // 获取行分组
    tab.THead
    ```

- ### 创建行分组: 创建 , 删除, 获取行

  - ```javascript
    .inserRow(i) ; .deleteRow(i) ; .rows
    ```

  - 简写:

    - inserRow 表示末行追加

  - 示例

    - ```javascript
      // 添加
      /*
      	返回tr的目的是,可以继续想tr中添加他td
      */
      var tr = thead.inserRow()
      
      // 删除
      thead.deleteRow(i)
      // 获取
      var trs = thead.rows
      ```

- ### 添加td  删除  获取

  - ```javascript
    .insertCell(i);    deleteCell(i);   cells  
    ```

  - **强调**

    - **insertCell**只能用来添加td

  - 示例

  - ```javascript
    var td = tr.insertCell()
    ```

- ### 删除行

  - ```javascript
    // i : tr 在行分组内的下标
    tbody.deleteRow(i)
    
    // i : 在整个表中的下标
    table.deleteRow(i) 
    /*
    	因为每个tr都有tr.rowindex记录了tr在整个表中的下标
    	通常使用: "table.deleteRow(tr.rowIndex)"
    */
    ```

---

## Form/Element

- **from** 对象代表网页的一个form元素

- ### 获取

  - ```javascript
    var form = document.forms[i/id/name]
    ```

- ### 属性

  - ```javascript
     /*
     	.element  获得form中所有表单元素的集合
     		获得表单元素 : form.elements[i/id/name]
     			简写: 如果表单元素有name属性
     				可直接: var elem = form.name
     	表单元素: 方法
     				elem.focus() // 让elem获取焦点
     				elem.blur() // 让elem失去焦点
     	.length == element.length // 获取form表单元素的个数
     */
    ```

- ### 方法

  - ```javascript
    .submit() // 手动提交
    ```

- 事件

  - ```javascript
    onsubmit()
    ```