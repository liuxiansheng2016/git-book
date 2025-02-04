https://zh-hans.legacy.reactjs.org/docs/context.html

### Context
创建 Context：使用 React.createContext 创建一个上下文对象。

提供 Context：使用 Context.Provider 将状态提供给子组件。

消费 Context：使用 Context.Consumer 或 useContext Hook 在子组件中消费状态。

#### 1. 创建 Context
首先，我们需要创建一个上下文对象。这可以通过 React.createContext() 方法来实现。
```
// CountContext.js
import React from 'react';

const CountContext = React.createContext();

export const CountProvider = ({ children }) => {
  const [count, setCount] = React.useState(0);

  const increment = () => setCount(prevCount => prevCount + 1);
  const decrement = () => setCount(prevCount => prevCount - 1);

  return (
    <CountContext.Provider value={{ count, increment, decrement }}>
      {children}
    </CountContext.Provider>
  );
};

export default CountContext;
```
在这个例子中，我们不仅创建了 CountContext，还定义了一个 CountProvider 组件。这个组件使用 useState 钩子来维护计数器的状态，并提供了两个函数 increment 和 decrement 来更新状态。

#### 2. 提供 Context
接下来，在应用的根组件中使用 CountProvider 来包裹整个应用，这样所有子组件都可以访问到提供的状态。
```
// index.js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import { CountProvider } from './CountContext';

ReactDOM.render(
  <CountProvider>
    <App />
  </CountProvider>,
  document.getElementById('root')
);
```
#### 3. 消费 Context
现在，我们可以开始在子组件中消费这个上下文。有两种方式可以做到这一点：使用 Context.Consumer 或者 useContext Hook。

**使用 Context.Consumer
```
// CounterConsumer.js
import React from 'react';
import CountContext from './CountContext';

function CounterConsumer() {
  return (
    <CountContext.Consumer>
      {({ count, increment, decrement }) => (
        <div>
          <h1>Count: {count}</h1>
          <button onClick={increment}>Increment</button>
          <button onClick={decrement}>Decrement</button>
        </div>
      )}
    </CountContext.Consumer>
  );
}

export default CounterConsumer;
```
**使用 useContext Hook**
```
// CounterHook.js
import React, { useContext } from 'react';
import CountContext from './CountContext';

function CounterHook() {
  const { count, increment, decrement } = useContext(CountContext);

  return (
    <div>
      <h1>Count: {count}</h1>
      <button onClick={increment}>Increment</button>
      <button onClick={decrement}>Decrement</button>
    </div>
  );
}

export default CounterHook;
```
#### 4. 在主应用中使用这些组件
最后，在主应用组件中使用这两个组件中的任何一个（或两者）来显示和操作计数器。
```
// App.js
import React from 'react';
import CounterConsumer from './CounterConsumer';
import CounterHook from './CounterHook';

function App() {
  return (
    <div className="App">
      <h2>Using Context.Consumer:</h2>
      <CounterConsumer />
      
      <hr />
      
      <h2>Using useContext Hook:</h2>
      <CounterHook />
    </div>
  );
}

export default App;
```

Consumer：每次 Context 值变化时，Consumer 组件会重新渲染。如果组件树很深，可能会导致性能问题。

useContext：React 会优化 useContext 的性能，只有当 Context 值变化时，使用 useContext 的组件才会重新渲染。

### 在类组件中使用 contextType
接下来，在类组件中使用 contextType 来访问上下文。你需要将 contextType 设置为对应的 Context 对象，并且可以通过 this.context 访问上下文中的值。
```
// CounterClassComponent.js
import React from 'react';
import { CountContext } from './CountContext';

class CounterClassComponent extends React.Component {
  // 使用 static contextType 指定要使用的 Context
  static contextType = CountContext;

  render() {
    // 通过 this.context 访问 Context 中的数据和方法
    const { count, increment, decrement } = this.context;
    
    return (
      <div>
        <h1>Count (Class Component): {count}</h1>
        <button onClick={increment}>Increment</button>
        <button onClick={decrement}>Decrement</button>
      </div>
    );
  }
}

export default CounterClassComponent;
```

### Redux 和 context 对比

context 优点

**简单易用**：Context API 是 React 的一部分，不需要额外安装依赖，使用起来非常直观。

**轻量级**：没有复杂的配置，适合小型到中型项目的状态管理。

响应式更新：当 Context 中的值发生变化时，所有订阅了该 Context 的组件都会自动重新渲染。

context 缺点：

**深度嵌套组件性能问题**：如果组件树很深，且频繁更新 Context 中的值，会导致大量不必要的重新渲染。

**缺乏中间件支持**：不像 Redux，Context API 没有内置的中间件机制，例如异步操作处理、日志记录等。

**状态管理不够规范**：对于大型应用，容易导致状态管理混乱，难以维护。

Redux 优点

**集中式状态管理**：所有状态都存储在一个中心化的 store 中，便于管理和调试。

**强大的生态系统**：有许多成熟的中间件和工具支持，如 redux-thunk、redux-saga、redux-logger 等。

**可预测的状态变化**：通过纯函数（reducers）来管理状态变化，确保状态的可预测性。

Redux 缺点

**学习曲线较陡**：需要理解许多概念，如 actions、reducers、store 等。

**配置复杂**：需要设置 store、middleware、reducers 等，对于小型项目可能显得过于繁琐。

**性能开销**：虽然有性能优化手段（如 reselect），但在某些情况下，频繁的状态更新仍可能导致性能问题。

Context：简单易用，适合小型应用和局部状态管理。 （父子组件之间共享状态）

Redux：复杂但强大，适合大型应用和全局状态管理。
