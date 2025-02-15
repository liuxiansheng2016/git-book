Hook 是 React16.8 的新特性它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性，

为已知的 React 概念提供了更直接的 API:pros,state,context,refs 以及生命周期

### 原因
1. 复杂组件变得难以理解
2. 难以理解的 class

随着 React Hooks 的引入，函数组件可以拥有类似于类组件的生命周期方法。主要使用的 Hooks 包括：

1. **useState()**：用于添加组件的局部状态。
2. **useEffect()**：用于执行副作用操作，如数据获取、设置订阅等。可以模拟类组件中的 componentDidMount、componentDidUpdate 和 componentWillUnmount。
3. **useContext()**：用于消费上下文中的值。
4. **useReducer()**：用于处理更复杂的状态逻辑。
5. **useCallback()**：返回一个 memoized 回调函数。
6. **useMemo()**：返回一个 memoized 值。
7. **useRef()**：返回一个可变的引用对象。

### 使用规则
1. 只能在 React 的函数组件中调用 Hook。不要在其他 JavaScript 函数中调用。（还有一个地方可以调用 Hook —— 就是自定义的 Hook 中）
2. 不要在循环，条件或嵌套函数中调用 Hook，必须始终在 React 函数的顶层使用 Hook

### UseState
useState 就是一个 hook.通过在函数组件里调用它来给组件添加一些内部 state.react 会在重复渲染时保留这个 state.useState 会返回一个值：当前状态和一个让你更新它的函数。
可以在事件处理函数中或者其他一些地方调用这个函数，类似与 this.setState，但是不会将新的 state 与旧的 state 合并。

一般来说，在函数退出后变量就会”消失”，而 state 中的变量会被 React 保留。

#### 类组件：
```
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 }; // 初始化状态
  }

  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Click me
        </button>
      </div>
    );
  }
}
```
#### 使用UseState
```
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0); // 初始化状态

  return (
    <div>
      <p>You clicked {count} times</p> {/* 直接使用 count 变量 */}
      <button onClick={() => setCount(count + 1)}>Click me</button> {/* 使用 setCount 更新状态 */}
    </div>
  );
}
```

### UseEffect
在组件执行过程中执行数据获取，订阅或者手动修改 DOM

useEffect 就是一个 Effect hook,和 class 组件中 conponnetDidMount,DidUpdate 和 willUnmout 具有相同的用途，只是合成了一个 API

### UseContext

**用途**：
useContext 允许你在函数组件中访问 React Context 的值。Context 提供了一种无需通过 props 层层传递就能共享数据的方式，特别适用于跨层级传递数据。
**适用场景**：
当你需要在多个嵌套层级较深的组件之间共享数据时（例如主题、认证状态等）。
避免“prop drilling”问题，即避免必须将 props 逐层传递给子组件。
全局配置或者需要被许多组件访问的数据。
首先创建 Context:
```
import React from 'react';

const ThemeContext = React.createContext('light'); // 默认主题为 'light'
export default ThemeContext;
```
然后，在父组件中提供 Context 值:
```
import React, { useState } from 'react';
import ThemeContext from './ThemeContext';

export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');

  const toggleTheme = () => {
    setTheme(prevTheme => prevTheme === 'light' ? 'dark' : 'light');
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}
```
最后，在任何子组件中消费 Context:
```
import React, { useContext } from 'react';
import ThemeContext from './ThemeContext';

function ThemedButton() {
  const { theme, toggleTheme } = useContext(ThemeContext);

  return (
    <button onClick={toggleTheme}>
      当前主题：{theme}
    </button>
  );
}
```


### UseReducer
useReducer 是 React 中的一个 Hook，它为复杂的状态逻辑提供了一种更可预测和结构化的方式来管理状态。
它类似于 Redux 的工作原理，通过一个 reducer 函数来管理状态的变化。相比于 useState，useReducer 更适合处理涉及多个子值的状态逻辑，或者下一个状态依赖于前一个状态的情况。

#### 基本使用
useReducer 接受两个参数：

reducer：这是一个函数，接收当前的 state 和一个 action，并返回一个新的 state。
initialState：这是状态的初始值。
它返回一个数组，包含当前的状态和一个分发（dispatch）动作的函数。
```
const [state, dispatch] = useReducer(reducer, initialState);
```
#### Reducer 函数
Reducer 函数通常遵循以下模式：
```
function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      throw new Error();
  }
}
```
state：当前状态。
action：描述发生了什么的对象，至少需要一个 type 属性，但也可以包含其他数据（称为 payload）。
```
const ACTION_TYPES = {
  INCREMENT: 'INCREMENT',
  DECREMENT: 'DECREMENT',
};
function counterReducer(state, action) {
  switch(action.type) {
    case ACTION_TYPES.INCREMENT:
      return { count: state.count + 1 };
    case ACTION_TYPES.DECREMENT:
      return { count: state.count - 1 };
    default:
      throw new Error(`Unknown action type: ${action.type}`);
  }
}

import React, { useReducer } from 'react';

export default function Counter() {
  const [state, dispatch] = useReducer(counterReducer, { count: 0 });

  return (
    <>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: ACTION_TYPES.INCREMENT })}>
        Increment
      </button>
      <button onClick={() => dispatch({ type: ACTION_TYPES.DECREMENT })}>
        Decrement
      </button>
    </>
  );
}
```
#### 与 Redux 的比较
尽管 useReducer 提供了类似 Redux 的 API，但它们有显著的区别：

**范围**：useReducer 适用于局部状态管理，而 Redux 通常用于应用级别的状态管理。
**中间件支持**：Redux 支持丰富的中间件生态系统，如异步操作处理、日志记录等，而 useReducer 则没有内置这样的功能。

### useCallback
useCallback 是 React 的一个 hook，用于优化性能。它的作用是缓存一个函数，确保在组件重新渲染时，不会创建新的函数实例。

useCallback 接受两个参数：回调函数和一个依赖数组。当依赖数组中的依赖项发生变化时，才会重新创建回调函数。如果依赖数组为空，则回调函数只会在组件首次渲染时创建一次。

**使用 useCallback 的场景包括**：

1. 将回调函数传递给子组件，避免子组件不必要的重新渲染。

2. 将回调函数作为 effect 的依赖项，确保 effect 只在特定依赖项发生变化时执行。

如果我们不使用 useCallback，每次父组件重新渲染时都会创建一个新的 onClick 函数，导致子组件重新渲染

示例代码：

```
import React, { useState, useCallback } from 'react';

// 子组件
function Child({ onClick }) {
  return (
    <button onClick={onClick}>
      Click me!
    </button>
  );
}

// 父组件
function Parent() {
  const [count, setCount] = useState(0);

  // 修改依赖项数组，添加 count
  const handleClick = useCallback(() => {
    console.log('Button clicked');
    setCount(prevCount => prevCount + 1); // 使用函数形式更新状态以避免闭包问题
  }, []); // 因为我们使用了函数形式更新状态，这里可以为空数组

  return (
    <div>
      <p>Count: {count}</p>
      <Child onClick={handleClick} />
    </div>
  );
}

export default Parent;
```
在上面的例子中，handleClick 函数只会在组件首次渲染时创建一次，不会因为组件重新渲染而创建新的实例。

### useMemo
`useMemo` 是 React 中的一个 Hook，用于优化组件性能，避免不必要的计算和渲染。它通过缓存计算结果，在依赖项未发生变化时直接返回缓存值，从而减少重复计算。

#### 使用方法

`useMemo` 接收两个参数：

1. **计算函数**：返回需要缓存的值。
2. **依赖数组**：包含计算函数依赖的变量。只有当依赖项发生变化时，`useMemo` 才会重新执行计算函数。

```javascript
import React, { useMemo } from 'eact';

function MyComponent({ data }) {
  const memoizedValue = useMemo(() => {
    // 执行计算或逻辑
    return expensiveCalculation(data);
  }, [data]);

  return <div>{memoizedValue}</div>;
}

import { useMemo, useCallback } from 'react';

function ProductPage({ productId, referrer }) {
  const product = useData('/product/' + productId);

  const requirements = useMemo(() => { //调用函数并缓存结果
    return computeRequirements(product);
  }, [product]);

  const handleSubmit = useCallback((orderDetails) => { // 缓存函数本身
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  }, [productId, referrer]);

  return (
    <div className={theme}>
      <ShippingForm requirements={requirements} onSubmit={handleSubmit} />
    </div>
  );
}
```

#### 使用场景

1. **缓存计算结果**：对于复杂的计算，如数据转换、过滤或排序，使用 `useMemo` 可以避免在每次渲染时重复计算。
2. **优化子组件渲染**：当父组件传递昂贵的计算值给子组件时，可以通过 `useMemo` 缓存该值，防止子组件不必要的重新渲染。

#### 性能优化作用

1. **减少计算量**：避免在依赖项未变化时重复执行昂贵的计算，提升组件性能。
2. **优化渲染性能**：通过减少不必要的计算，间接减少渲染次数，提高页面响应速度。

#### 对比useCallback
1. useMemo **缓存函数调用的结果** 。在这里，它缓存了调用 computeRequirements(product) 的结果。除非 product 发生改变，否则它将不会发生变化。这让你向下传递 requirements 时而无需不必要地重新渲染 ShippingForm。必要时，React 将会调用传入的函数重新计算结果。
2. useCallback **缓存函数本身**。不像 useMemo，它不会调用你传入的函数。相反，它缓存此函数。从而除非 productId 或 referrer 发生改变，handleSubmit 自己将不会发生改变。这让你向下传递 handleSubmit 函数而无需不必要地重新渲染 ShippingForm。直至用户提交表单，你的代码都将不会运行。

### useRef
const refContainer = useRef(initialValue);

useRef 返回一个可变的 ref 对象，其 .current 属性被初始化为传入的参数（initialValue）。返回的 ref 对象在组件的整个生命周期内保持不变。
