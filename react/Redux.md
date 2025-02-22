# Redux

React 只是 DOM 的一个抽象层，并不是 Web 应用的完整解决方案。

没有涉及组件中的通信。

设计思想， web 应用是一个状态机，视图与状态一一对应，所有的状态保存在一个对象里面。

### 结构

**Action**:一个对象，其中 type 是必须的

**Reducer**:是一个纯函数，接受 Action 和当前 State 作为参数，返回一个新的 State.

**Store**&#x20;

1. Store.dispatch() 是 view 发出 Action 的唯一方法

&#x20; 2\.  Store.getState()&#x20;

3. Store.subscribe() 设置监听函数，一旦 state 发生变化，就自动执行这个函数。

执行有副作用的操作，如 API 请求和路由跳转；

调用非纯函数，如 Date.now() 或 Math.random()。

### 特点

**单一数据源**：整个应用的全局 state 被存储在一棵 object tree 中，并且这个 object tree 只存在于唯一一个 store 中。

**State 是只读的**：唯一改变 state 的方法就是触发 action，action 是一个用于描述已发生事情的普通对象。

**使用纯函数来执行修改**：为了描述 action 如何改变 state tree，你需要编写纯的 reducers。

**定义 Action Types 和 Actions**

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

**创建 Reducer**

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

**创建 Store**

使用 createStore 来创建 Redux store，并且可以结合 applyMiddleware 使用中间件如 redux-thunk 或 redux-saga。

```
// store.js
import { createStore } from 'redux';
import counterReducer from './reducer';

const store = createStore(counterReducer);

export default store;
```

**在 React 组件中使用 Redux Store**

Counter 组件

```
// Counter.js
import React, { Component } from 'react';
import store from './store';
import { increment, decrement } from './actions';

class Counter extends Component {
  constructor(props) {
    super(props);
    this.state = {
      count: store.getState().count,
    };
  }

  componentDidMount() {
    // 订阅状态变化
    this.unsubscribe = store.subscribe(() => {
      this.setState({
        count: store.getState().count,
      });
    });
  }

  componentWillUnmount() {
    // 清理订阅
    if (this.unsubscribe) {
      this.unsubscribe();
    }
  }

  handleIncrement = () => {
    store.dispatch(increment());
  };

  handleDecrement = () => {
    store.dispatch(decrement());
  };

  render() {
    return (
      <div>
        <h1>Count: {this.state.count}</h1>
        <button onClick={this.handleIncrement}>Increment</button>
        <button onClick={this.handleDecrement}>Decrement</button>
      </div>
    );
  }
}

export default Counter;
```

**在主应用组件中使用 Counter 组件**

```
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

由于我们<mark style="color:red;">在每个需要访问 Redux store 的组件中直接导入并使用了 store（例如通过 import store from './store';）， 因此无需使用 来将 store 注入到 React 组件树中。这种方法适用于小规模的应用或仅需在少数几个组件中直接管理状态的情况。</mark>

```
// index.js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```



**定义 Action Types**

首先，定义一些常量来表示不同的 action 类型。

```
// actionTypes.js
export const INCREMENT = 'INCREMENT';
export const DECREMENT = 'DECREMENT';
```

**创建 Actions**

接下来，创建 action creator 函数，这些函数返回带有特定 type 属性的对象。

```
// actions.js
import { INCREMENT, DECREMENT } from './actionTypes';

export const increment = () => ({
  type: INCREMENT,
});

export const decrement = () => ({
  type: DECREMENT,
});
```

**编写 Reducer** 编写 reducer 函数，它接收当前状态和一个 action，并根据 action 的类型返回新的状态。

```
// reducer.js
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

**创建 Store**

使用 createStore 函数创建 Redux store，并传入之前定义的 reducer。

```
// store.js
import { createStore } from 'redux';
import counterReducer from './reducer';

const store = createStore(counterReducer);

export default store;
```

**配置 Provider**

在应用的入口文件中（例如 index.js 或 App.js），使用 将 store 提供给整个应用。

<mark style="color:red;">`<Provider store={store}>`</mark> <mark style="color:red;"></mark><mark style="color:red;">将 Redux store 通过 React Context 传递给整个组件树，使得任何嵌套的组件都能通过 react-redux 提供的 Hook（如 useSelector、useDispatch）或 HOC（如 connect）访问到 store。</mark>

```
// index.js
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

### **使用 useSelector 和 useDispatch**

现在，在组件内部使用 useSelector 来读取状态，使用 useDispatch 来分发 actions。

useSelector 是一个 React Hook，用于从 Redux Store 中选择状态的一部分。在这里，我们选择 count 的值。 useDispatch 是另一个 React Hook，用于获取 Redux 的 dispatch 函数，以便分发动作。

```
// Counter.js
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { increment, decrement } from './actions';

function Counter() {
  // 获取状态
  const count = useSelector((state) => state.count);
  
  // 获取 dispatch 方法
  const dispatch = useDispatch();

  return (
    <div>
      <h1>Count: {count}</h1>
      <button onClick={() => dispatch(increment())}>Increment</button>
      <button onClick={() => dispatch(decrement())}>Decrement</button>
    </div>
  );
}

export default Counter;
```

**在主应用中使用 Counter 组件**

```
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

### 使用 useStore

在 react-redux 中，useStore 是一个 Hook，它返回由 \<Provider> 传入的 Redux store 实例。通常，开发者更常用 useSelector 和 useDispatch 来访问状态和派发 action，但在某些场景下，你可能需要直接访问整个 store（例如，调用 store.getState() 或 store.subscribe()）

* 使用 useStore 可以让你直接访问 store，但一般不建议在组件内直接调用 store.dispatch 或 getState，因为这可能导致无法利用 react-redux 的订阅优化。
* 大部分场景下建议使用 useSelector 和 useDispatch 来分别获取状态和派发 action。

```jsx
import React from 'react';
import { useStore } from 'react-redux';

function MyComponent() {
  // 获取 Redux store 实例
  const store = useStore();
  // 获取当前 state
  const state = store.getState();

  return (
    <div>
      <h2>当前 Redux 状态：</h2>
      <pre>{JSON.stringify(state, null, 2)}</pre>
    </div>
  );
}

export default MyComponent;
```

### **connect 函数**

connect 是 react-redux 提供的一个高阶组件（HOC），它用于将 React 组件与 Redux store 连接起来。通过 connect，你可以让 UI 组件订阅 Redux store 的更新，并且能够 dispatch actions 来更新 store。

**mapStateToProps**

mapStateToProps 是一个函数，用来指定哪些 state 需要映射到当前组件的 props 中。这样，当 store 的状态发生变化时，相应的 props 也会更新，从而触发组件重新渲染。

**mapDispatchToProps**

mapDispatchToProps 可以是一个函数或对象，用于定义哪些 action creators 应该被绑定到组件的 props 上。如果是函数，则允许你手动 dispatch actions；如果是对象，则会自动绑定每个 action creator 到 dispatch 方法上。

**定义 Action Types 和 Actions**

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

**编写 Reducer**

```
// reducer.js
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

**创建 Store**

```
// store.js
import { createStore } from 'redux';
import counterReducer from './reducer';

const store = createStore(counterReducer);

export default store;
```

**使用 connect 连接 UI 组件** 现在，我们创建一个纯 UI 组件，并使用 connect 将其与 Redux store 连接起来。

```
// CounterUI.js
import React from 'react';

function CounterUI({ count, onIncrement, onDecrement }) {
  return (
    <div>
      <h1>Count: {count}</h1>
      <button onClick={onIncrement}>Increment</button>
      <button onClick={onDecrement}>Decrement</button>
    </div>
  );
}

export default CounterUI;
```

**然后，使用 connect 将这个 UI 组件转换为容器组件**

```
// connectCounter.js
import { connect } from 'react-redux';
import { increment, decrement } from './actions';
import CounterUI from './CounterUI';

// 将 state 映射到 props
const mapStateToProps = (state) => ({
  count: state.count,
});

// 将 action 映射到 props
const mapDispatchToProps = {
  onIncrement: increment,
  onDecrement: decrement,
};

// 使用 connect 连接 UI 组件和 Redux store
export default connect(mapStateToProps, mapDispatchToProps)(CounterUI);
```

Redux 可以通过组合多个 reducer 来管理多个 state。以下是实现步骤：

1. **定义多个 reducer**： 每个 reducer 负责管理 state 的一部分。

```javascript
// userReducer.js
const initialUserState = {
  name: '',
  age: 0,
};

function userReducer(state = initialUserState, action) {
  switch (action.type) {
    case 'SET_NAME':
      return { ...state, name: action.payload };
    case 'SET_AGE':
      return { ...state, age: action.payload };
    default:
      return state;
  }
}

export default userReducer;

// postReducer.js
const initialPostState = {
  posts: [],
};

function postReducer(state = initialPostState, action) {
  switch (action.type) {
    case 'ADD_POST':
      return { ...state, posts: [...state.posts, action.payload] };
    default:
      return state;
  }
}

export default postReducer;
```

2. **使用 combineReducers**： 通过 `combineReducers` 将多个 reducer 组合成一个根 reducer。

代码组织与模块化

在大型应用中，状态管理逻辑会变得非常复杂。如果将所有的状态处理逻辑都写在一个 reducer 函数中，代码会变得冗长且难以维护。使用 `combineReducers` 可以将不同的状态处理逻辑拆分成多个小的 reducer 函数，每个 reducer 只负责管理状态树中的一个特定部分，使代码结构更加清晰，易于理解和维护

```javascript
// rootReducer.js
import { combineReducers } from 'redux';
import userReducer from './userReducer';
import postReducer from './postReducer';

const rootReducer = combineReducers({
  user: userReducer,
  posts: postReducer,
});

export default rootReducer;
```

3. **创建 store**： 使用组合后的根 reducer 创建 store。

```javascript
// store.js
import { createStore } from 'redux';
import rootReducer from './rootReducer';

const store = createStore(rootReducer);

export default store;
```

4. **在组件中使用 state**： 使用 `useSelector` 或 `connect` 获取 state。

```javascript
// UserProfile.js
import React from 'react';
import { useSelector } from 'react-redux';

function UserProfile() {
  const name = useSelector((state) => state.user.name);
  const age = useSelector((state) => state.user.age);

  return (
    <div>
      <h1>User Profile</h1>
      <p>Name: {name}</p>
      <p>Age: {age}</p>
    </div>
  );
}

export default UserProfile;

// PostList.js
import React from 'react';
import { useSelector } from 'react-redux';

function PostList() {
  const posts = useSelector((state) => state.posts.posts);

  return (
    <div>
      <h1>Posts</h1>
      <ul>
        {posts.map((post, index) => (
          <li key={index}>{post}</li>
        ))}
      </ul>
    </div>
  );
}

export default PostList;
```

通过这种方式，Redux 可以轻松管理多个 state，每个 reducer 负责管理 state 的一部分，并将它们组合成一个根 reducer。



### Redux-Thunk

Redux-Thunk 是一个用于 Redux 的中间件，它允许你在 action creators 中返回一个函数（thunk）而不是普通的 action 对象。这使得你可以在这些函数中执行异步操作，并在合适的时机通过 dispatch 函数发送普通的 action 对象来更新 Redux store 1。

为什么需要 Redux-Thunk？ 默认情况下，Redux 只能处理同步数据流。这意味着当你需要执行如 API 调用之类的异步操作时，你需要一种机制来处理这种异步行为。这就是 Redux-Thunk 发挥作用的地方。通过使用 Redux-Thunk，你可以将异步逻辑封装到 action creators 中，并且可以根据异步操作的结果来派发不同的 actions

我们将创建一个简单的 React 应用程序，该应用程序使用 Redux 和 Redux-Thunk 来处理异步操作。这个应用将展示如何获取用户数据，并根据不同的状态显示加载中、成功或失败的信息。

#### 1. 安装依赖

首先，确保你已经安装了必要的依赖：

```bash
npm install redux react-redux redux-thunk axios
```

这里我们还引入了 `axios`，这是一个流行的 HTTP 客户端库，用于发起网络请求。

#### 2. 创建 Reducer 和 Action Creators

**Reducer**

定义一个 reducer 来管理用户数据的状态：

```javascript
// reducers/userReducer.js
const initialState = {
  loading: false,
  user: null,
  error: null,
};

const userReducer = (state = initialState, action) => {
  switch (action.type) {
    case 'FETCH_USER_REQUEST':
      return { ...state, loading: true };
    case 'FETCH_USER_SUCCESS':
      return { ...state, loading: false, user: action.payload, error: null };
    case 'FETCH_USER_FAILURE':
      return { ...state, loading: false, error: action.error };
    default:
      return state;
  }
};

export default userReducer;
```

**Action Creator**

接下来是 action creator，它会使用 `redux-thunk` 中间件来处理异步操作：

```javascript
// actions/userActions.js
import axios from 'axios';

export const fetchUser = (userId) => {
  return async (dispatch) => {
    dispatch({ type: 'FETCH_USER_REQUEST' });
    try {
      const response = await axios.get(`https://jsonplaceholder.typicode.com/users/${userId}`);
      dispatch({ type: 'FETCH_USER_SUCCESS', payload: response.data });
    } catch (error) {
      dispatch({ type: 'FETCH_USER_FAILURE', error: error.message });
    }
  };
};
```

#### 3. 设置 Store

现在设置 Redux store 并应用 `redux-thunk` 中间件：

```javascript
// store.js
import { createStore, applyMiddleware, combineReducers } from 'redux';
import thunk from 'redux-thunk';
import userReducer from './reducers/userReducer';

const rootReducer = combineReducers({
  user: userReducer,
});

const store = createStore(rootReducer, applyMiddleware(thunk));

export default store;
```

#### 4. 在 React 组件中使用

在 React 组件中连接 Redux store 并使用上面定义的 action creator：

```javascript
// components/User.js
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { fetchUser } from '../actions/userActions';

const User = ({ userId }) => {
  const dispatch = useDispatch();
  const { loading, user, error } = useSelector(state => state.user);

  const handleFetchUser = () => {
    dispatch(fetchUser(userId));
  };

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;
  if (!user) return <button onClick={handleFetchUser}>Load User</button>;

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
      <button onClick={handleFetchUser}>Reload User</button>
    </div>
  );
};

export default User;
```

#### 5. 渲染组件

最后，在你的主应用文件中渲染这个组件：

```javascript
// App.js
import React from 'react';
import { Provider } from 'react-redux';
import store from './store';
import User from './components/User';

function App() {
  return (
    <Provider store={store}>
      <div className="App">
        <User userId={1} />
      </div>
    </Provider>
  );
}

export default App;
```

这样你就完成了一个完整的例子，展示了如何使用 Redux 和 Redux-Thunk 来处理异步操作 。在这个例子中，我们通过点击按钮来触发异步请求，然后根据请求的结果更新 UI。

#### 在React中，处理异步操作并不强制要求使用中间件

在React中，处理异步操作并不强制要求使用中间件。实际上，你可以在组件的生命周期方法或者`useEffect`钩子内直接调用异步方法。然而，是否需要使用中间件取决于你的应用架构以及你如何管理状态和副作用。

**在生命周期方法或`useEffect`中调用异步方法**

在类组件中，你可以利用生命周期方法如`componentDidMount`来发起异步请求。比如：

```jsx
class MyComponent extends React.Component {
  componentDidMount() {
    fetch('api/data')
      .then(response => response.json())
      .then(data => this.setState({ data }));
  }

  render() {
    // 渲染逻辑...
  }
}
```

而在函数组件中，你可以使用`useEffect`钩子来实现同样的功能：

```jsx
function MyComponent() {
  const [data, setData] = useState(null);

  useEffect(() => {
    async function fetchData() {
      const response = await fetch('api/data');
      const json = await response.json();
      setData(json);
    }

    fetchData();
  }, []); // 空数组意味着只在组件挂载时执行

  return (
    // 渲染逻辑...
  );
}
```

**使用中间件的原因**

虽然上述方式可以直接进行异步操作，但在某些情况下，使用中间件（如Redux中的中间件）有其独特的优势，尤其是在你需要管理全局状态或处理复杂的副作用时。以下是几个使用中间件的理由：

* **状态管理**：如果你的应用需要维护一个全局状态树，并且这个状态会被多个组件共享，那么使用Redux这样的状态管理库是合适的。而为了处理异步操作，你需要使用像`redux-thunk`或`redux-saga`这样的中间件。
* **副作用集中管理**：通过中间件，可以将所有的副作用逻辑集中在一处，这样有助于保持组件的纯净性，使得组件专注于UI展示而非业务逻辑。
* **调试与追踪**：中间件可以提供日志记录、错误处理等功能，方便开发者追踪和调试异步操作的过程。
* **复杂控制流**：对于复杂的异步流程控制（例如取消请求、重试机制等），使用专门的中间件（如`redux-saga`）可能比手动编写效果更好。

### Redux Toolkit 的主要功能

Redux Toolkit 是 Redux 官方推荐的工具包，旨在简化 Redux 的使用。它提供了一组工具和最佳实践，帮助开发者更容易地编写 Redux 逻辑。Redux Toolkit 解决了 Redux 的一些常见问题，如样板代码过多、配置复杂等。

1. **`configureStore`**：简化了 store 的配置，自动添加了 Redux DevTools 和 thunk 中间件。
2. **`createSlice`**：结合了 reducer 和 action 创建逻辑，减少了样板代码。
3. **`createAsyncThunk`**：简化了处理异步逻辑（如数据获取）的方式。
4. **`createEntityAdapter`**：提供了一种标准化的方式来处理标准化数据（如列表）。
5. **`createReducer` 和 `createAction`**：简化了 reducer 和 action 的创建。

#### 安装 Redux Toolkit

你可以通过 npm 或 yarn 安装 Redux Toolkit：

```sh
npm install @reduxjs/toolkit
```

或

```sh
yarn add @reduxjs/toolkit
```

#### 示例

以下是一个使用 Redux Toolkit 的简单示例：

**1. 配置 Store**

```javascript


import { configureStore } from '@reduxjs/toolkit';
import userReducer from './reducers/userReducer';

const store = configureStore({
  reducer: {
    user: userReducer,
  },
});

export default store;
```

**2. 创建 Slice**

```javascript


import { createSlice } from '@reduxjs/toolkit';

const initialState = { loading: false, users: [], error: null };

const userSlice = createSlice({
  name: 'user',
  initialState,
  reducers: {
    fetchUsersRequest(state) {
      state.loading = true;
      state.error = null;
    },
    fetchUsersSuccess(state, action) {
      state.loading = false;
      state.users = action.payload;
      state.error = null;
    },
    fetchUsersFailure(state, action) {
      state.loading = false;
      state.users = [];
      state.error = action.payload;
    },
  },
});

export const { fetchUsersRequest, fetchUsersSuccess, fetchUsersFailure } = userSlice.actions;

export default userSlice.reducer;
```

**3. 创建异步操作**

```javascript


import { fetchUsersRequest, fetchUsersSuccess, fetchUsersFailure } from '../reducers/userReducer';

export const fetchUsers = () => {
  return async (dispatch) => {
    dispatch(fetchUsersRequest());
    try {
      const response = await fetch('https://jsonplaceholder.typicode.com/users');
      const users = await response.json();
      dispatch(fetchUsersSuccess(users));
    } catch (error) {
      dispatch(fetchUsersFailure(error.message));
    }
  };
};
```

**4. 使用 Redux Store**

```javascript


import React from 'react';
import { Provider } from 'react-redux';
import store from './store';
import UserList from './components/UserList';

function App() {
  return (
    <Provider store={store}>
      <div>
        <h1>Redux Toolkit Example</h1>
        <UserList />
      </div>
    </Provider>
  );
}

export default App;
```

**5. 创建组件**

```javascript


import React, { useEffect } from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { fetchUsers } from '../actions/userActions';

const UserList = () => {
  const users = useSelector(state => state.user.users);
  const loading = useSelector(state => state.user.loading);
  const error = useSelector(state => state.user.error);
  const dispatch = useDispatch();

  useEffect(() => {
    dispatch(fetchUsers());
  }, [dispatch]);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;

  return (
    <div>
      <h1>User List</h1>
      <ul>
        {users.map(user => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  );
};

export default UserList;
```

通过使用 Redux Toolkit，可以显著减少 Redux 代码的样板代码，使代码更加简洁和易于维护。



### `createAsyncThunk`&#x20;

是 Redux Toolkit 提供的一个实用函数，用于简化 Redux 中异步操作的处理。它帮助开发者更方便地处理异步逻辑，如网络请求，<mark style="color:red;">并自动管理异步操作的不同阶段（如加载中、成功、失败）对应的 action</mark>。以下从基本概念、工作原理、使用方法、应用场景等方面详细介绍。

#### 基本概念

在 Redux 里处理异步操作时，通常需要手动管理多个 action 来表示异步操作的不同状态，例如开始请求、请求成功、请求失败。`createAsyncThunk` 可以自动生成这些 action，减少模板代码，让异步操作的处理更加简洁和可维护。

#### 工作原理

`createAsyncThunk` 接收两个参数：

* **动作类型前缀**：是一个字符串，用于标识这个异步操作，`createAsyncThunk` 会基于这个前缀自动生成三个 action type，分别对应异步操作的三个阶段：
  * `pending`：表示异步操作正在进行中。
  * `fulfilled`：表示异步操作成功完成。
  * `rejected`：表示异步操作失败。
* **payload 创建函数**：是一个异步函数，通常用于执行异步操作，如发送网络请求。这个函数接收两个参数，第一个是传递给异步操作的参数，第二个是包含 `dispatch` 和 `getState` 等有用方法的对象。

#### 使用方法

**1. 安装 Redux Toolkit**

```bash
npm install @reduxjs/toolkit
```

**2. 创建异步 thunk**

```javascript
import { createAsyncThunk } from '@reduxjs/toolkit';
import axios from 'axios';

// 创建一个异步 thunk
export const fetchUsers = createAsyncThunk(
    'users/fetchUsers', // 动作类型前缀
    async () => {
        const response = await axios.get('https://jsonplaceholder.typicode.com/users');
        return response.data;
    }
);
```

**3. 在 reducer 中处理异步 thunk 的 action**

```javascript
import { createSlice } from '@reduxjs/toolkit';
import { fetchUsers } from './asyncThunks';

const usersSlice = createSlice({
    name: 'users',
    initialState: {
        data: [],
        loading: false,
        error: null
    },
    extraReducers: (builder) => {
        // 处理 pending 状态
        builder.addCase(fetchUsers.pending, (state) => {
            state.loading = true;
            state.error = null;
        });
        // 处理 fulfilled 状态
        builder.addCase(fetchUsers.fulfilled, (state, action) => {
            state.loading = false;
            state.data = action.payload;
        });
        // 处理 rejected 状态
        builder.addCase(fetchUsers.rejected, (state, action) => {
            state.loading = false;
            state.error = action.error.message;
        });
    }
});

export default usersSlice.reducer;
```

**4. 在组件中使用异步 thunk**

```jsx
import React, { useEffect } from'react';
import { useDispatch, useSelector } from'react-redux';
import { fetchUsers } from './asyncThunks';

const UsersList = () => {
    const dispatch = useDispatch();
    const { data, loading, error } = useSelector((state) => state.users);

    useEffect(() => {
        dispatch(fetchUsers());
    }, [dispatch]);

    if (loading) {
        return <p>Loading...</p>;
    }

    if (error) {
        return <p>Error: {error}</p>;
    }

    return (
        <ul>
            {data.map((user) => (
                <li key={user.id}>{user.name}</li>
            ))}
        </ul>
    );
};

export default UsersList;
```

#### 应用场景

* **数据获取**：从服务器获取数据，如获取用户列表、商品列表等。
* **数据提交**：向服务器提交数据，如用户注册、登录、提交表单等。
* **数据更新和删除**：对服务器上的数据进行更新或删除操作，并同步更新本地状态。

通过 `createAsyncThunk`，开发者可以更高效地处理 Redux 中的异步操作，减少样板代码，提高代码的可读性和可维护性。
