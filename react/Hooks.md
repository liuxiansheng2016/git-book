# Hooks

Hook 是 React16.8 的新特性它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性，

https://zh-hans.react.dev/reference/react/useCallback

为已知的 React 概念提供了更直接的 API:pros,state,context,refs 以及生命周期

#### 原因

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
8. **useTransition：** 它允许你标记一个状态更新为过渡（transitions），这意味着这些更新不会立即触发同步的UI渲染，而是允许React在后台处理这些更新，并在适当的时候进行更新。这有助于提高应用的响应速度，尤其是在执行可能耗费较多时间的状态更新时，比如过滤大型列表或者进行复杂的计算
9. `useActionState` 它主要用于表单提交场景下状态的管理和异步 Action 的处理
10. `useFormStatus`  够访问其所在的 `<form>` 的信息而无需将属性传递到组件内的组件

#### 使用规则

1. 只能在 React 的函数组件中调用 Hook。不要在其他 JavaScript 函数中调用。（还有一个地方可以调用 Hook —— 就是自定义的 Hook 中）
2. 不要在循环，条件或嵌套函数中调用 Hook，必须始终在 React 函数的顶层使用 Hook

## 常见hooks

### 1. 状态管理类 Hooks

* **useState**\
  用于在函数组件中添加本地状态。它返回当前状态和更新状态的函数，适合管理简单数据。
* **useReducer**\
  用于处理复杂状态逻辑或多个子状态之间相互关联的场景。它接收一个 reducer 函数和初始状态，返回当前状态和 dispatch 函数，类似 Redux 的工作方式。

***

### 2. 副作用与 DOM 操作类 Hooks

* **useEffect**\
  用于执行副作用操作，如数据获取、订阅、手动修改 DOM 等。它会在组件渲染后异步执行，并可在依赖变化时重新调用。
* **useLayoutEffect**\
  与 useEffect 类似，但在所有 DOM 变更后同步执行，适用于需要测量 DOM 或立即更新布局的场景。
* **useInsertionEffect**\
  新增的 Hook，专门用于在 DOM 更新前同步插入样式（例如 CSS-in-JS 库），确保样式在浏览器绘制前生效。

***

### 3. 上下文与引用类 Hooks

* **useContext**\
  用于消费 React Context，直接获取上下文中的值，避免层层传递 props。
* **useRef**\
  用于持久化存储可变值或引用 DOM 元素。更新 ref.current 不会触发组件重新渲染，常用于保存跨渲染周期的实例变量。
* **useImperativeHandle**\
  与 forwardRef 配合使用，允许组件自定义暴露给父组件的实例值，控制父组件通过 ref 访问到哪些内部方法或属性。

***

### 4. 性能优化类 Hooks

* **useMemo**\
  用于缓存高开销的计算结果，只有在依赖项变化时才重新计算，避免每次渲染都执行昂贵计算。
* **useCallback**\
  用于缓存函数实例，确保只有在依赖项变化时才创建新函数，常用于传递给子组件以避免不必要的重渲染。

***

### 5. 调试与开发类 Hooks

* **useDebugValue**\
  用于在 React DevTools 中显示自定义 Hook 的调试信息，帮助开发者跟踪 Hook 内部的状态和变化，便于调试复杂逻辑。

***

### 6. 并发与新特性类 Hooks（React 18+）

* **useTransition**\
  允许将某些状态更新标记为“过渡”更新，使得高优先级的 UI 更新保持流畅，适用于实现更平滑的并发渲染。
* **useDeferredValue**\
  延迟更新某个值，降低频繁更新带来的性能压力，使得用户界面在高负载下仍能保持响应性。
* **useId**\
  用于生成唯一标识符，常用于无障碍属性、表单标签关联等需要唯一 id 的场景。
* **useSyncExternalStore**\
  用于在并发渲染环境下安全地读取外部存储的数据，确保数据一致性，是构建全局状态管理或订阅外部数据时的推荐方案。

***

### 7. 新实验性或未来推广的 Hooks（React 19+）

* **use**\
  一个全新的 API，可在组件渲染中直接读取 Promise 或其他异步数据，结合 Suspense 实现数据加载挂起，简化异步数据处理。
* **useOptimistic**\
  用于实现乐观更新，允许在发起异步操作时立即更新界面，并在操作完成后根据结果进行回滚或确认，提升用户体验

## 第三方库常见hooks

* **react-redux**
  * **常用 Hook**：`useSelector`、`useDispatch`、`useStore`
  * **介绍**：用于在函数组件中访问 Redux 全局状态及派发 actions，适合中大型应用的全局状态管理。
* **react-router-dom**
  * **常用 Hook**：`useNavigate`、`useLocation`、`useParams`、`useRoutes`
  * **介绍**：React 官方推荐的路由解决方案，提供编程式导航、路由参数获取和路由配置等功能。
* **react-i18next**
  * **常用 Hook**：`useTranslation`
  * **介绍**：基于 i18next 的国际化解决方案，使用 Hook 来加载翻译资源、切换语言和格式化文本。

### UseState

useState 就是一个 hook.通过在函数组件里调用它来给组件添加一些内部 state.react 会在重复渲染时保留这个 state.useState 会返回一个值：当前状态和一个让你更新它的函数。 可以在事件处理函数中或者其他一些地方调用这个函数，类似与 this.setState，但是不会将新的 state 与旧的 state 合并。

一般来说，在函数退出后变量就会”消失”，而 state 中的变量会被 React 保留。

**类组件：**

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

**使用UseState**

* **直接设置新值**：`setCount(newValue)`，适用于简单的状态更新。
* 使用回调函数更新状态, **基于前一个状态更新**：`setCount(prevCount => prevCount + 1)`，适用于需要依赖于之前状态值的情况。

```
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0); // 初始化状态，可以是一个函数，在初始化时调用一次，使用返回值作为初始化值

  return (
    <div>
      <p>You clicked {count} times</p> {/* 直接使用 count 变量 */}
      <button onClick={() => setCount(count + 1)}>Click me</button> {/* 使用 setCount 更新状态 */}
    </div>
  );
}
```

#### UseEffect

在组件执行过程中执行数据获取，订阅或者手动修改 DOM

useEffect 就是一个 Effect hook,和 class 组件中 conponnetDidMount,DidUpdate 和 willUnmout 具有相同的用途，只是合成了一个 API

#### UseContext

**用途**： useContext 允许你在函数组件中访问 React Context 的值。Context 提供了一种无需通过 props 层层传递就能共享数据的方式，特别适用于跨层级传递数据。 **适用场景**： 当你需要在多个嵌套层级较深的组件之间共享数据时（例如主题、认证状态等）。 避免“prop drilling”问题，即避免必须将 props 逐层传递给子组件。 全局配置或者需要被许多组件访问的数据。 首先创建 Context:

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

#### UseReducer

useReducer 是 React 中的一个 Hook，它为复杂的状态逻辑提供了一种更可预测和结构化的方式来管理状态。 它类似于 Redux 的工作原理，通过一个 reducer 函数来管理状态的变化。

* **useReducer** <mark style="color:red;">适合用于单个组件或局部组件树中管理较复杂的状态逻辑，无需引入额外的状态管理库</mark>。
* **Redux** 则用于全局状态管理，特别是当多个组件需要共享状态、需要中间件支持异步操作或需要调试工具时，Redux 提供了更完善的解决方案。
*   **与`useState`的区别**

    1. **状态更新方式**
       * **`useState`**：通过调用返回的更新函数直接更新状态，适用于简单的状态管理。
       * **`useReducer`**：通过`dispatch`函数发送action来更新状态，action描述了要执行的操作，reducer函数根据action更新状态，适用于复杂的状态管理。
    2. **性能优化**
       * **`useState`**：每次更新状态都会触发组件的重新渲染。
       * **`useReducer`**：可以通过`useMemo`或 `useCallback`对`dispatch`函数进行优化，减少不必要的渲染。
    3. **可读性和可维护性**
       * **`useState`**：对于简单的状态管理，代码简洁易懂。
       * **`useReducer`**：将状态更新逻辑集中在一个reducer函数中，提高了代码的可读性和可维护性，尤其适用于大型项目。

    总之，`useReducer`适用于复杂状态管理和需要高度可维护性的场景，而`useState`适用于简单的状态管理。根据具体需求选择合适的钩子，可以提高应用的性能和开发效率

**基本使用**

useReducer 接受两个参数：

reducer：这是一个函数，接收当前的 state 和一个 action，并返回一个新的 state。 initialState：这是状态的初始值。 它返回一个数组，包含当前的状态和一个分发（dispatch）动作的函数。

```
const [state, dispatch] = useReducer(reducer, initialState);
```

### useReducer

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

state：当前状态。 action：描述发生了什么的对象，至少需要一个 type 属性，但也可以包含其他数据（称为 payload）。

使用场景？：购物车

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

**与 Redux 的比较**

尽管 useReducer 提供了类似 Redux 的 API，但它们有显著的区别：

**范围**：useReducer 适用于局部状态管理，而 Redux 通常用于应用级别的状态管理。&#x20;

**中间件支持**：Redux 支持丰富的中间件生态系统，如异步操作处理、日志记录等，而 useReducer 则没有内置这样的功能。

### useCallback

useCallback 是 React 的一个 hook，用于优化性能。它的作用是缓存一个函数，确保在组件重新渲染时，不会创建新的函数实例。

useCallback 接受两个参数：回调函数和一个依赖数组。当依赖数组中的依赖项发生变化时，才会重新创建回调函数。如果依赖数组为空，则回调函数只会在组件首次渲染时创建一次。

**使用 useCallback 的场景包括**：

1. <mark style="color:red;">将回调函数传递给子组件，避免子组件不必要的重新渲染。</mark>
2. 将回调函数作为 effect 的依赖项，确保 effect 只在特定依赖项发生变化时执行。

<mark style="color:red;">如果我们不使用 useCallback，每次父组件重新渲染时都会创建一个新的 onClick 函数，导致子组件重新渲染</mark>

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

#### 使用 `useCallback` 时，如何避免闭包陷阱（stale closure）？

1\. 正确管理依赖项

2.函数式更新

```
 const incrementCount = useCallback(() => {
    setCount(prevCount => prevCount + 1); // 使用函数式更新，确保基于最新状态
  }, []);
```

3\. 使用 `useRef` 存储最新的值

如果你有需要频繁更新的值，可以使用 `useRef` 来存储最新的值，并在回调函数中引用它。这可以确保回调函数总是使用最新的值。

```
import React, { useState, useCallback, useRef, useEffect } from 'react';

const MyComponent = () => {
  const [count, setCount] = useState(0);
  const countRef = useRef(count);

  useEffect(() => {
    countRef.current = count; // 更新 ref 中的值
  }, [count]);

  const logCount = useCallback(() => {
    console.log('Count:', countRef.current); // 使用 ref 中的最新值
  }, []);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment Count</button>
      <button onClick={logCount}>Log Count</button>
    </div>
  );
};

```

### useMemo

`useMemo` 是 React 中的一个 Hook，用于优化组件性能，避免不必要的计算和渲染。它通过缓存计算结果，在依赖项未发生变化时直接返回缓存值，从而减少重复计算。

**使用方法**

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

**使用场景**

1. **缓存计算结果**：对于复杂的计算，如数据转换、过滤或排序，使用 `useMemo` 可以避免在每次渲染时重复计算。
2. **优化子组件渲染**：当父组件传递昂贵的计算值给子组件时，可以通过 `useMemo` 缓存该值，防止子组件不必要的重新渲染。

**性能优化作用**

1. **减少计算量**：避免在依赖项未变化时重复执行昂贵的计算，提升组件性能。
2. **优化渲染性能**：通过减少不必要的计算，间接减少渲染次数，提高页面响应速度。

**对比useCallback**

1. useMemo **缓存函数调用的结果** 。在这里，它缓存了调用 computeRequirements(product) 的结果。除非 product 发生改变，否则它将不会发生变化。这让你向下传递 requirements 时而无需不必要地重新渲染 ShippingForm。必要时，React 将会调用传入的函数重新计算结果。
2. useCallback **缓存函数本身**。不像 useMemo，它不会调用你传入的函数。相反，它缓存此函数。从而除非 productId 或 referrer 发生改变，handleSubmit 自己将不会发生改变。这让你向下传递 handleSubmit 函数而无需不必要地重新渲染 ShippingForm。直至用户提交表单，你的代码都将不会运行。

#### 是否会一直缓存值

\
`useMemo` 不会一直缓存值，缓存值的有效性取决于依赖项数组。具体情况如下：

**1. 依赖项变化**\
如前面所述，当依赖项数组中的值发生变化时，`useMemo` 会重新计算并更新缓存值。

**2. 组件卸载**\
当组件卸载时，`useMemo` 的缓存会被清除。组件再次挂载时，`useMemo` 会重新进行初始渲染时的操作，即调用回调函数并缓存新的值。

**3. 空依赖项数组**\
如果依赖项数组为空 `[]`，则 `useMemo` 只会在组件首次渲染时计算并缓存值，之后不会再重新计算，除非组件卸载后重新挂载。

### `useRef`&#x20;

const refContainer = useRef(initialValue);

`UseRef` 是一个 Hook，可以在函数组件中使用。<mark style="color:red;">它不仅限于获取 DOM 节点的引用，还可以用来保存任何可变值</mark>，<mark style="color:red;">且在整个组件的生命周期内保持不变</mark>。

<mark style="color:red;">函数组件没有实例</mark>，不能像类组件那样通过实例来存储数据，`useRef` 可以在函数组件的多次渲染之间保存值，并且不会因为组件重新渲染而重置。

直接修改 `useRef` 的 `.current` 属性不会触发组件重新渲染。

#### &#x20;`useRef` 保存非状态值的例子

假设你需要记录一个计时器的启动时间，并在用户点击按钮时显示经过的时间。这个场景下，我们并不需要每次时间变化都触发组件重新渲染，因此使用 `useRef` 来保存开始时间是合适的。

* **存储表单输入值**（适用于不想因输入变更触发重新渲染的场景）。
* **存储定时器 ID**，避免因组件重新渲染导致定时器被重置。
* **存储前一次的 prop 或 state**，用于在 `useEffect` 中对比数据变化。



### **useTransition**

使用 `useTransition` 可以帮助你在状态更新和UI渲染之间创建更流畅的用户体验。当你有一个耗时的状态更新操作时，你可以将其标记为过渡，这样用户界面就不会被阻塞，同时可以显示一个加载指示器，直到过渡完成并准备好进行最终的UI更新。

#### 使用方法

`useTransition` 返回两个值：

* `startTransition`: 一个函数，用于将状态更新标记为过渡。
* `isPending`: 一个布尔值，表示是否有任何过渡正在进行中。

<pre><code><strong>//before
</strong>const [isPending, setIsPending] = useState(false);


// 使用 Actions 中的待定状态
function UpdateName({}) {
  const [name, setName] = useState("");
  const [error, setError] = useState(null);
  const [isPending, startTransition] = useTransition();

  const handleSubmit = () => {
    startTransition(async () => {
      const error = await updateName(name);
      if (error) {
        setError(error);
        return;
      } 
      redirect("/path");
    })
  };

  return (
    &#x3C;div>
      &#x3C;input value={name} onChange={(event) => setName(event.target.value)} />
      &#x3C;button onClick={handleSubmit} disabled={isPending}>
        Update
      &#x3C;/button>
      {error &#x26;&#x26; &#x3C;p>{error}&#x3C;/p>}
    &#x3C;/div>
  );
}
</code></pre>

### useActionState

* `useActionState` 用于包装一个“Action”函数（通常是提交表单时调用的服务器操作），并跟踪该 Action 的执行状态。它在每次表单提交时，会调用传入的 Action 函数，同时返回最新的状态、包装后的提交函数以及一个表示当前是否处于待处理状态（isPending）的标志。
*   **参数和返回值**\
    调用 `useActionState(fn, initialState, permalink?)` 时：

    * 第一个参数 `fn` 是一个 Action 函数，当表单提交时，该函数会被调用，并接收上一次状态作为第一个参数，以及表单数据（FormData）作为后续参数。
    * 第二个参数 `initialState` 指定了初始状态。
    * 第三个可选参数 `permalink` 用于在某些场景下控制表单的导航。

    返回一个数组，包含：

    1. 当前的状态（初始值为 `initialState`，Action 执行后更新为 Action 返回的值）。
    2. 一个包装后的 Action 函数（可直接赋值给 `<form>` 元素的 `action` 属性或按钮的 `formAction` 属性）。
    3. 一个 `isPending` 布尔值，表示当前是否有 Action 正在执行。



    **表单处理**\
    在表单提交时，你可以使用 `useActionState` 来管理提交过程中的各种状态，比如显示提交中状态（pending）、处理提交返回的错误信息或成功提示等。\
    示例代码：

    ```jsx
    import { useActionState } from 'react';

    async function updateName(prevState, formData) {
      // 从 formData 中获取输入值，进行相应处理（如调用 API）
      const newName = formData.get('name');
      // 模拟异步操作
      await new Promise((resolve) => setTimeout(resolve, 1000));
      return newName;
    }

    function UpdateNameForm() {
      const [state, submitAction, isPending] = useActionState(updateName, '');

      return (
        <form action={submitAction}>
          <input name="name" placeholder="Enter new name" />
          <button type="submit" disabled={isPending}>
            {isPending ? 'Updating...' : 'Update'}
          </button>
          <div>{state && `Updated name: ${state}`}</div>
        </form>
      );
    }
    ```

### `useFormStatus`  <a href="#new-hook-useformstatus" id="new-hook-useformstatus"></a>

在设计系统中，常常需要编写设计一类能够访问其所在的 `<form>` 的信息而无需将属性传递到组件内的组件。这可以通过 Context 来实现，但为了使这类常见情况更简单，我们添加了一个新的 Hook&#x20;

`useFormStatus` 返回的是一个对象，这个对象包含多个属性，用来描述当前 `<form>` 的状态。通常，这个对象具有以下几个属性：

* **pending**：布尔值，指示表单是否处于提交状态。当表单正在提交时，`pending` 为 `true`；否则为 `false`。
* **data**：当表单处于提交状态时，这里会包含当前的 `FormData` 对象；当表单未提交时，通常为 `null`。
* **method**：表示表单提交时使用的 HTTP 方法（例如 `'post'` 或 `'get'`）；如果表单未提交，则为 `null`。
* **action**：表示表单的提交目标，可以是一个 URL 字符串或一个函数（用于处理表单提交）；如果表单未提交，则为 `null`。

在非 pending 状态下，除了 `pending` 为 `false` 外，其他属性通常都为 `null`。而当表单提交过程中，这些属性则提供了相关的上下文信息，使你可以根据需要更新 UI（例如禁用提交按钮或显示加载状态）。

```
import {useFormStatus} from 'react-dom';

function DesignButton() {
  const {pending} = useFormStatus();
  return <button type="submit" disabled={pending} />
}
```

####

