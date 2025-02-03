React 只是 DOM 的一个抽象层，并不是 Web 应用的完整解决方案。

没有涉及组件中的通信。

设计思想， web 应用是一个状态机，视图与状态一一对应，所有的状态保存在一个对象里面。

**Action**:一个对象，其中 type 是必须的

**Reducer**:是一个纯函数，接受 Action 和当前 State 作为参数，返回一个新的 State.

**Store**
Store.dispatch() 是 view 发出 Action 的唯一方法
Store.getState()
Store.subscribe() 设置监听函数，一旦 state 发生变化，就自动执行这个函数。

执行有副作用的操作，如 API 请求和路由跳转；

调用非纯函数，如 Date.now() 或 Math.random()。

**单一数据源**：整个应用的全局 state 被存储在一棵 object tree 中，并且这个 object tree 只存在于唯一一个 store 中。

**State 是只读的**：唯一改变 state 的方法就是触发 action，action 是一个用于描述已发生事情的普通对象。

**使用纯函数来执行修改**：为了描述 action 如何改变 state tree，你需要编写纯的 reducers。
定义 Action Types 和 Actions
定义常量来表示不同的 action 类型，并创建相应的 action creator 函数。
```
// actionTypes.js
export const INCREMENT = 'INCREMENT';
export const DECREMENT = 'DECREMENT';

// actions.js
import { INCREMENT, DECREMENT } from './actionTypes';

export const increment = () => ({
  type: INCREMENT,
});

export const decrement = () => ({
  type: DECREMENT,
});
```
创建 Reducer
Reducer 根据当前状态和接收到的 action 返回新的状态。
```
import { INCREMENT, DECREMENT } from './actionTypes';

const initialState = {
  count: 0,
};

function counterReducer(state = initialState, action) {
  switch (action.type) {
    case INCREMENT:
      return { ...state, count: state.count + 1 };
    case DECREMENT:
      return { ...state, count: state.count - 1 };
    default:
      return state;
  }
}

export default counterReducer;
```
创建 Store
使用 createStore 来创建 Redux store，并且可以结合 applyMiddleware 使用中间件如 redux-thunk 或 redux-saga。
```
// store.js
import { createStore } from 'redux';
import counterReducer from './reducer';

const store = createStore(counterReducer);

export default store;
```
在 React 中使用 Redux
接下来，在 React 应用中使用 react-redux 提供的 <Provider> 组件将 store 提供给整个应用，并使用 useSelector 和 useDispatch Hooks 来访问状态和分发 actions。

配置 Provider
在应用的入口文件中（例如 index.js 或 App.js），使用 <Provider> 将 store 传递给应用。
```
// index.js or App.js
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import store from './store';
import App from './App';

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```
store的使用
```
// Counter.js
import React, { useEffect } from 'react';
import store from './store';
import { increment, decrement } from './actions';

function Counter() {
  // 使用 getState 获取初始状态
  console.log('Initial State:', store.getState());

  // 订阅状态变化
  useEffect(() => {
    const unsubscribe = store.subscribe(() => {
      console.log('State changed:', store.getState());
    });

    // 清理订阅
    return () => {
      unsubscribe();
    };
  }, []);

  // 分发 action
  const handleIncrement = () => store.dispatch(increment());
  const handleDecrement = () => store.dispatch(decrement());

  return (
    <div>
      <h1>Count: {store.getState().count}</h1>
      <button onClick={handleIncrement}>Increment</button>
      <button onClick={handleDecrement}>Decrement</button>
    </div>
  );
}

export default Counter;

// App.js
import React from 'react';
import Counter from './Counter';

function App() {
  return (
    <div className="App">
      <Counter />
    </div>
  );
}

export default App;
```

### 使用 useSelector 和 useDispatch

useSelector 是一个 React Hook，用于从 Redux Store 中选择状态的一部分。在这里，我们选择 count 的值。
useDispatch 是另一个 React Hook，用于获取 Redux 的 dispatch 函数，以便分发动作。
