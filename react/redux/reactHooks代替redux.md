# reactHooks 代替 Redux

官方退出的 reactHooks 可以完美的实现 redux 的功能, 使用方法更加便捷, 仿佛又看到了React的未来

## 使用步骤

1. 将数据及重在一个store对象上
2. 将所有操作集中在reducer中
3. 创建一个Context
4. 创建对数据的读写API
5. 将第四部的内容放到第三步的Context中
6. 用 Context.Provider 将 Context 提供给所有组件
7. 各组件用 useContext 获取读写API

---

## 上代码

新建 Context / index.js 文件模块化步骤三

```javascript
import {createContext} from "react"
const Context = createContext(null)
export default Context
```

在APP.js 中

```javascript
import React, { useReducer } from "react";
import Context from "./Context/index"
import Coma from "./pages/Coma"
import Comb from "./pages/Comb"

const initState = {
  user:"null",
  books:""
}

const obj = {
  user: (state, action) => {
    return { ...state, user: action.user };
  },
  books:(state, action) => {
    return { ...state, books: action.books };
  },
}

const reducer = (state,action)=>{
  let fn = obj[action.type];
  if(fn){
    return fn(state,action)
  }else{
    console.error("wrong type");
  }
}

function App() {
  const [state,dispatch] = useReducer(reducer,initState)

  const handelAddUser = ()=>{
    dispatch({type:"user",user:"yoyo"})
  }
  const handelAddBooks = ()=>{
    dispatch({type:"books",books:"东方甄选"})
  }

  return (
    <Context.Provider value={{state,dispatch}}>
      <Coma></Coma>
      <Comb></Comb>
      <div>
        <button onClick={handelAddUser}>添加作者</button>
        <button onClick={handelAddBooks}>添加书籍</button>
      </div>
    </Context.Provider>
  );
}

export default App;

```

创建 pages / Coma.js

```javascript
// eslint-disable-next-line
import React, { useContext } from "react"
import Context from "../Context/index"
function Coma(){
  // eslint-disable-next-line
  const {state,dispatch} = useContext(Context)
  return (
    <div>
      <h1>this is Coma </h1>
      <div>name: {state.user}</div>
      <hr />
    </div>
  )
}
export default Coma
```

创建 pages / Comb.js

```javascript
import React, { useContext } from "react"
import Context from "../Context/index"
function Comb(){
  // eslint-disable-next-line
  const {state,dispatch} = useContext(Context)

  return (
    <div>
      <h1>this is Comb </h1>
      <div>书籍：{state.books}</div>
      <br />
    </div>
  )
}

export default Comb
```

