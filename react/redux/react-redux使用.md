# react-redux 使用

上面介绍 Redux 的时候被up主形容为xiang, 而 react-redux 在一定程度上简化了 redux 的操作, 优化了用户体验，但不过是xiang上添花，本质还是xiang，后来官方对 react-redux 的功能又做了进一步的改进，推出的 react Hook 可以进完全替代 react-redux 的功能，还是推荐想用函数开发的小伙伴请拥抱vue3

## react-redux核心组成

- Provider
  - Prov4der 包裹在跟组件最外层，使所有的子组
  - \4o;/ntext 传递给所有组件
- Connect
  - Provider 内部组件如果想使用 state 中的数据, 就必须被connect加强 (需要柯力化调用组件)
  - connect 本质上就是简化了获取 store 中的state
  - **需要接受两个参数（将要传给子组件传递的属性，将要给子组件传的回调函数）**

---

## react-redux 基本使用

安装

```shell
npm install react-redux redux 
```

创建 redux 文件夹用于存放状态 redux/reducer/index 模块化设置 reducer

```javascript
const reducer = ((
	state = {
        text:"hello world"
    },
    action
)=>{
	let newState  = {...state}
    let {type,text} = action
    switch (type) {
      	case "setText":
          newState.text = text
          return newState
        default:
          return state
     }    
})
export default reducer
```

创建 redux / store.js 用于整合 reducer

```javascript
import {createStore,combineReducers} from "redux"
import textReducer from "./reducer/index"
const reducer = combineReducers({textReducer})
const store = createStore(reducer)
export default store
```

在组件中使用 APP 组件引入

```jsx
import {Provider} from "react-redux"
import store from "./redux/store.js"
import ChildA from "./childA"
import ChildB from "./childB"
const App = ()=>{
    return(
    	<Provider store={store}>
            <div>
            	<ChildA/>
                <ChildB/>
            </div>
        </Provider>
    )
}
export default Index
```

在 ChildA 中修改 text 的值

```javascript
imoprt {connect} from "react-redux"
const ChildA = (props)=>{
    const handelClick = ()=>{
        console.log(props)
        props.setText()
    }
    return (
    	<div>
        	<h2>this is ChildA</h2>
        	<button onClick={handelClick}>Click<button/>
        </div>
    )
}
const setText = {
    setText（）{
    	return {
    		type:"setText",
    		text:"fuck react"
		}
	}
}
export default connect(null,setText)(childA)
```

在 childB 中拿到 text 的值

```jsx
import {connect} from "react-redux"
const ChildB = (props)=>{
    return (
    	<div>
        	<h2>this is ChildB</h2>
            <h3>this is react-redux {props.text}</h3>
        </div>
    )
}
export default connect((state)=>{
    return {
        text:state.textReducer.text
    }
})(ChildB)
```

