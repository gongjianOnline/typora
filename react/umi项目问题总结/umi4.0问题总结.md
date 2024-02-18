# umi4.0 问题总结

## 使用 dva

Umi Max 集成了 dva 工具，安装 umi 集成工具

```shell
pnpm install @umijs/plugins --save-dev
```

创建 `models / index.ts` 思路和 paina 类似，每一个文件需要给与单独的命名空间

```ts
export default {
  namespace: 'menuModel',
  state: {
    menuIndex:localStorage.getItem('menuIndex') || '1', /*状态值*/
  },
  reducers: {
    menuIndexConfig(state:any, params:any ) { /*改变 state 属性的值*/
      return { ...state,menuIndex:params.menuIndex};
    },
  },
};
```

在组件使用，函数式中采用高阶函数调用

```react
import {connect} from "umi"
/*此时的 menuModel 为 dva 文件的命名空间名称*/
const MenuComponent:React.FC<any> = ({dispatch, menuModel}) => {
    const handleClick = ()=>{
        dispatch({type:"menuModel/menuIndexConfig",payload:{menuIndex:2}})
    }
	return (
    	<div>
        	<span>{menuModel.menuIndex}</span>
        	<button onClick="{()=>handleClick()}">测试</button>
        </div>
    )    
})

export default connect(({menuModel})=>({
  menuModel
}))(MenuComponent)
```

---

## 路由创建



## 编程式导航



## 使用 ant-design 





