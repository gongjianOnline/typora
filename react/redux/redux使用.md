# Redux使用

## Redux 和 React 的关系

redux 的功能和 vuex 相同,都是作为中央状态管理仓库, 但用户体验上和 vuex 真的是大相径庭, 其原因在于 vuex 高度集成在 vue 中,可以说是为vuex 量身打造的状态管理系统, 而 redux 作为一个库,可以支持在 React \ Angular \ jquery 还有原生JS中, 并不是为 react 私人定制的, 习惯了vuex的小伙伴 在看 redux的流程如同翔一样, 再次提倡想体验函数式开发的小伙伴请拥抱 vue3

---

## Redux的组成

- Action

  - 把数据从应用传到store的载体，是 store 的唯一来源，可以通过 store.dispatch() 将 action传递给 store

  - 特点

    - 本质是一个对象
- 必须包含一个 type 属性来表示要执行的动作
    - type 一般为一个字符串
- 只是描述了事情将要发生,并没有去更新 state
- Reducer
  - 本质是一个函数,用来响应 action, 经过处理后,把 state 发送给 Store 
  - 注意
    - 必须有 return 返回值, store才能接收到数据
    - 接收两个参数, 第一个是初始化的 state (需要给默认值), 第二个参数是 action

- Store
  - 把 action 和 reducer 联系到一起的对象
  - 职责
    - 维持应用的 state
    - getState() 获取 state
    - dispatch() 发送 action
    - subscribe() 注册监听
    - subscribe() 返回值来注销监听

---

## redux基本用法

安装

```shell
npm install redux
```



在 src 目录创建 action / index.js  文件

```javascript
const sendAction = ()=>{
  return {
    type:"send_action",
    value:"我是一个action"
  }
}
module.exports = {
  sendAction
}
```

在 src 目录创建 reducer / index.js 文件

```javascript
const initState = { value: "默认值" };
const reducer = (state = initState, action) => {
  console.log('reducer',state,action)
  switch (action.type) {
    case "send_action":
      return Object.assign({}, state, action);
    default:
      return state;
  }
};
module.exports = {
  reducer,
};

```

在 src 目录创建 store / index.js 文件

```javascript
import {createStore} from "redux";
import {reducer} from "../reducer/index"
const store = createStore(reducer)
export default store
```

创建 home 组件

```javascript
import React from "react";
import store from "../store/index"
import {sendAction} from "../action/index"
export default class Home extends React.Component {
  handleClick = ()=>{
    const action = sendAction()
    store.dispatch(action)
  }
componentDidMount(){
  store.subscribe(()=>{
    console.log("subscribe",store.getState())
    this.setState({})
  })
}
  render() {
    return (
      <div>
        <h1>this is home</h1>
        <button onClick={()=>this.handleClick()}>发送一个action</button>
        <div>{store.getState().value}</div>
      </div>
    );
  }
}

```

---

## redux 使用 redux-thunk 中间件

只是让 dispatch 支持函数的形式

安装

```shell
yarn add redux-thunk -S
```



模拟一个 函数文件 fun.js

```javascript
const xxxfun = ()=>{
  return (dispatch)=>{
    new Promise((resolve)=>{
      resolve("不知道这个玩意能干点啥")
    }).then(response=>{
      dispatch({
        type:'setText',
        content:response
      })
    })
  }
}
export default xxxfun
```

声明一个 reducer 文件夹 reducer / test.js

```javascript
const reducer = ((
  state={
    text:'xxx'
  },
  action
)=>{
  let newState = {...state};
  let {type,content} = action
  switch (type) {
    case "setTest":
      newState.text = content
      return newState

    default:
      return state
  }
})
export default reducer
```

在 store.js 中整合

```javascript
import {createStore,combineReducers, applyMiddleware} from "redux"
import reducerText from "./test.js"
import reduxThunk from "redux-thunk"
const reducer = combineReducers({reducerText})
const store = createStore(reducer,applyMiddleware(reduxThunk))
export default store
```

在组件中使用

```javascript
import store from "./redux/store"
import xxxFun from "./redux/test.js"
const ChildA = ()=>{
  const handelClick = ()=>{
    store.dispatch(xxxFun())
  }
  return (
    <div>
      <h2>this is childA</h2>
      <button onClick={handelClick}>handelClick</button>
    </div>
  )
}
export default ChildA
```

---

## redux 使用 redux-promise 中间件

使用方式和 redux-thunk 一样, 唯一的区别是 dispatch 可以接收 promise 对象

安装

```shell
yarn add redux-promise -S
```

在 store 文件中配置

```javascript
import {createStore,combineReducers, applyMiddleware} from "redux"
import reducerText from "./reducer/test"
import reduxThunk from "redux-thunk"
const reducer = combineReducers({reducerText})
const store = createStore(reducer,applyMiddleware(reduxPromise))
export default store
```

## redux 持久化

安装

```shell
yarn add redux-persist -S
```

使用查看官方文档 CRM 学习法

