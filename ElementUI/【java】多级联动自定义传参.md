# 【java】多级联动自定义传参

```javascript
<el-cascader
    :options="options"
    :props="props"
    @change="handleChange">
</el-cascader>
```

```javascript
//多级联动
    options: [{
          value: 'zhinan',
          label: '指南',
          children: [{
            value: 'shejiyuanze',
            label: '设计原则',
            children: [{
              value: 'yizhi',
              label: '一致'
            }, {
              value: 'fankui',
              label: '反馈'
            }, {
              value: 'xiaolv',
              label: '效率'
            }, {
              value: 'kekong',
              label: '可控'
            }]
        }]
    }],
    selectedOptions: [],
    //将所有需要的字段都改成
    props:{
        label:"label",
        value:"label",
        children:'children'
    }
```

## **methods**

```javascript
//多级联动
    handleChange(label) {
        console.log(label);
        this.selectedOptions = label;
        console.log("得到的联动Value");
        console.log(this.selectedOptions);
    }
```

