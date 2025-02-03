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
