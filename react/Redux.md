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

由于我们在每个需要访问 Redux store 的组件中直接导入并使用了 store（例如通过 import store from './store';），
因此无需使用 <Provider> 来将 store 注入到 React 组件树中。这种方法适用于小规模的应用或仅需在少数几个组件中直接管理状态的情况。
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

### 使用 useSelector 和 useDispatch

useSelector 是一个 React Hook，用于从 Redux Store 中选择状态的一部分。在这里，我们选择 count 的值。
useDispatch 是另一个 React Hook，用于获取 Redux 的 dispatch 函数，以便分发动作。

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

**编写 Reducer**
编写 reducer 函数，它接收当前状态和一个 action，并根据 action 的类型返回新的状态。
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

在应用的入口文件中（例如 index.js 或 App.js），使用 <Provider> 将 store 提供给整个应用。
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
**使用 useSelector 和 useDispatch**

现在，在组件内部使用 useSelector 来读取状态，使用 useDispatch 来分发 actions。
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

**connect 函数**

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

**使用 connect 连接 UI 组件**
现在，我们创建一个纯 UI 组件，并使用 connect 将其与 Redux store 连接起来。

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

1. **定义多个 reducer**：
   每个 reducer 负责管理 state 的一部分。

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

2. **使用 combineReducers**：
   通过 `combineReducers` 将多个 reducer 组合成一个根 reducer。

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

3. **创建 store**：
   使用组合后的根 reducer 创建 store。

```javascript
// store.js
import { createStore } from 'redux';
import rootReducer from './rootReducer';

const store = createStore(rootReducer);

export default store;
```

4. **在组件中使用 state**：
   使用 `useSelector` 或 `connect` 获取 state。

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

### useReducer

在React中，`useReducer`是一个用于状态管理的钩子，它提供了一种更结构化的方式来管理复杂状态逻辑。

#### 使用方法
`useReducer`接收一个reducer函数和初始状态作为参数，并返回当前状态和一个`dispatch`函数。通过调用`dispatch`函数并传入一个action对象，可以触发状态的更新。

```jsx
const initialState = {
  tasks: [
    { id: '1', name: 'Buy groceries', description: 'Milk, Bread, Eggs', completed: false },
    { id: '2', name: 'Do laundry', description: 'Separate colors', completed: true },
  ],
};

function taskReducer(state, action) {
  switch (action.type) {
    case 'ADD_TASK':
      return {
        ...state,
        tasks: [
          ...state.tasks,
          { id: String(Date.now()), name: action.name, description: action.description, completed: false },
        ],
      };
    case 'TOGGLE_TASK':
      return {
        ...state,
        tasks: state.tasks.map(task =>
          task.id === action.id ? { ...task, completed: !task.completed } : task
        ),
      };
    case 'DELETE_TASK':
      return {
        ...state,
        tasks: state.tasks.filter(task => task.id !== action.id),
      };
    default:
      throw new Error(`Unknown action type: ${action.type}`);
  }
}

import React, { useReducer } from 'react';

function TaskManager() {
  const [state, dispatch] = useReducer(taskReducer, initialState);

  const handleAddTask = (name, description) => {
    dispatch({ type: 'ADD_TASK', name, description });
  };

  const handleToggleTask = (id) => {
    dispatch({ type: 'TOGGLE_TASK', id });
  };

  const handleDeleteTask = (id) => {
    dispatch({ type: 'DELETE_TASK', id });
  };

  return (
    <div>
      {/* 这里可以放置表单和其他UI元素 */}
      <button onClick={() => handleAddTask('New Task', 'A new task description')}>Add New Task</button>
      <ul>
        {state.tasks.map(task => (
          <li key={task.id}>
            <span style={{ textDecoration: task.completed ? 'line-through' : 'none' }}>
              {task.name} - {task.description}
            </span>
            <button onClick={() => handleToggleTask(task.id)}>
              {task.completed ? 'Mark Incomplete' : 'Mark Complete'}
            </button>
            <button onClick={() => handleDeleteTask(task.id)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

#### 使用场景
- **复杂状态管理**：当组件的状态包含多个子值，或者状态的更新逻辑较为复杂时，使用`useReducer`可以使状态管理更加清晰和易于维护。
- **状态逻辑复用**：`useReducer`的reducer函数可以被多个组件复用，提高代码的可重用性。
- **异步操作**：处理异步操作时，可以通过`dispatch`函数在不同阶段更新状态，使状态更新的流程更加可控。

#### 与`useState`的区别
1. **状态更新方式**
   - **`useState`**：通过调用返回的更新函数直接更新状态，适用于简单的状态管理。
   - **`useReducer`**：通过`dispatch`函数发送action来更新状态，action描述了要执行的操作，reducer函数根据action更新状态，适用于复杂的状态管理。

2. **性能优化**
   - **`useState`**：每次更新状态都会触发组件的重新渲染。
   - **`useReducer`**：可以通过`useMemo`或` useCallback`对`dispatch`函数进行优化，减少不必要的渲染。

3. **可读性和可维护性**
   - **`useState`**：对于简单的状态管理，代码简洁易懂。
   - **`useReducer`**：将状态更新逻辑集中在一个reducer函数中，提高了代码的可读性和可维护性，尤其适用于大型项目。

总之，`useReducer`适用于复杂状态管理和需要高度可维护性的场景，而`useState`适用于简单的状态管理。根据具体需求选择合适的钩子，可以提高应用的性能和开发效率。


### Redux-Thunk
Redux-Thunk 是一个用于 Redux 的中间件，它允许你在 action creators 中返回一个函数（thunk）而不是普通的 action 对象。这使得你可以在这些函数中执行异步操作，并在合适的时机通过 dispatch 函数发送普通的 action 对象来更新 Redux store 1。

为什么需要 Redux-Thunk？
默认情况下，Redux 只能处理同步数据流。这意味着当你需要执行如 API 调用之类的异步操作时，你需要一种机制来处理这种异步行为。这就是 Redux-Thunk 发挥作用的地方。通过使用 Redux-Thunk，你可以将异步逻辑封装到 action creators 中，并且可以根据异步操作的结果来派发不同的 actions 

我们将创建一个简单的 React 应用程序，该应用程序使用 Redux 和 Redux-Thunk 来处理异步操作。这个应用将展示如何获取用户数据，并根据不同的状态显示加载中、成功或失败的信息。

### 1. 安装依赖

首先，确保你已经安装了必要的依赖：

```bash
npm install redux react-redux redux-thunk axios
```

这里我们还引入了 `axios`，这是一个流行的 HTTP 客户端库，用于发起网络请求。

### 2. 创建 Reducer 和 Action Creators

#### Reducer

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

#### Action Creator

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

### 3. 设置 Store

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

### 4. 在 React 组件中使用

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

### 5. 渲染组件

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
