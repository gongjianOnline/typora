# react-redux 使用

上面介绍 Redux 的时候被up主形容为xiang, 而 react-redux 在一定程度上简化了 redux 的操作, 优化了用户体验，但不过是xiang上添花，本质还是xiang，后来官方对 react-redux 的功能又做了进一步的改进，推出的 react Hook 可以进完全替代 react-redux 的功能，还是推荐想用函数开发的小伙伴请拥抱vue3

## react-redux核心组成

- Provider
  - Provider 包裹在跟组件最外层，使所有的子组件都可以拿到 State
  - Provider 接受 Store 作为 Props，然后通过 context 传递给所有组件
- Connect
  - Provider 内部组件如果想使用 state 中的数据, 就必须被connect加强 (需要柯力化调用组件)
  - connect 本质上就是简化了获取 store 中的state

---

## react-redux 基本使用

安装

```shell
npm install react-redux redux 
```

在 src 目录中创建 reducer / index.js

```javascript
const initState = {
  count:0
}
exports.reducer = (state=initState,action)=>{
  switch (action.type) {
    case "add_action":
      return {
        count:state.count+1
      }  
    default:
      return state
  }
}
```

在 src 目录中创建 store / index.js

```javascript
import {legacy_createStore as createStore} from "redux"
import {reducer} from "../reducer/index"

const store = createStore(reducer)
export default store
```

创建 ComSend 组件,作为发送方

```javascript
import React from "react"
import {connect} from "react-redux"

class ComSend extends React.Component{
  handelClick = ()=>{
    this.props.sendAction()
  }
  render(){
    return (
      <button onClick={()=>this.handelClick()}>+</button>
    )
  }
}

const mapDisPatchToProps = (dispatch)=>{
  return {
    sendAction:()=>{
      dispatch({
        type:"add_action"
      })
    }
  }
}

export default connect(null,mapDisPatchToProps)(ComSend)
```

创建 ComGet 组件，作为接收方

```javascript
1import React from "react"
import {connect} from "react-redux"
class ComGet extends React.Component{
  render(){
    console.log(this.props)
    return (
      <div>
        {this.props.count}
      </div>
    )
  }
}

const 就khuy    NHJM K	下 000000000K0=] (state)=>{
  return state
}
export default connect(mapStateToProps)(ComGet)
```

在 app.js 中引入

```javascript
import React from "react";
import {Provider} from "react-redux"
import store from "./store/index"
import ComSend from "./pages/ComSend/index"
import ComGet from "./pages/ComGet/index"

function App() {
  return (
    <Provider store={store}>
      <div className="App">
        <h1>this is app page</h1>
        <ComSend></ComSend>
        <ComGet></ComGet>
      </div>
    </Provider>
  );
}
export default App;
```

