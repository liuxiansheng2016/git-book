# UseEffect

### UseEffect

在组件执行过程中执行数据获取，订阅或者手动修改 DOM

useEffect 就是一个 Effect hook,和 class 组件中 conponnetDidMount,DidUpdate 和 willUnmout 具有相同的用途，只是合成了一个 API



### 为什么 `useEffect` 依赖项中不能直接放对象或者数组

React 在检测依赖变化时，`useEffect` 依赖项会使用**浅比较（shallow comparison）**。对于对象或数组来说，即使内容相同，每次渲染时都会创建新的引用，导致 `useEffect` 触发不必要的重新执行。

依赖数组的作用就是控制 `useEffect` 何时执行，避免不必要的副作用触发。比如，<mark style="color:red;">如果</mark> <mark style="color:red;"></mark><mark style="color:red;">`useEffect`</mark> <mark style="color:red;"></mark><mark style="color:red;">里有一个网络请求，每次组件渲染都会执行，那就会造成性能浪费</mark>



### <mark style="color:red;">`useEffect`</mark> <mark style="color:red;"></mark><mark style="color:red;">执行的基本规则：</mark>

#### **依赖数组有值**

1. <mark style="color:red;">首次渲染后执行</mark>
2. <mark style="color:red;">依赖项更新时执行</mark>

#### <mark style="color:red;">**空依赖项数组**</mark>

当提供一个空的依赖项数组 `[]` 时，`useEffect` 将只在组件首次渲染执行

#### `useEffect`（无依赖数组）

```
useEffect(() => {
  console.log("🔄 组件渲染后都会执行");
});
```

* 每次组件渲染（包括初次渲染和每次状态更新后）都会执行。
* 没有提供依赖数组，相当于监听所有的状态和 props 变化。



| 依赖情况                      | 触发时机               | 适用场景                     | 可能的问题                            |
| ------------------------- | ------------------ | ------------------------ | -------------------------------- |
| **无依赖数组**                 | **每次渲染后执行**        | 监听组件的**每次渲染**            | 可能会导致**性能问题**或**无限循环**           |
| **空依赖数组 `[]`**            | **仅首次渲染时执行**       | 只在**组件挂载时执行**一次，如**初始化** | 可能会**捕获旧的状态**                    |
| **依赖数组有值 `[dependency]`** | **首次渲染 + 依赖变化时执行** | **监听状态/props 变化**        | 依赖项如果是**对象/函数**，可能导致**不必要的重新执行** |

***

### 清理函数作用

1. **防止内存泄漏**：当组件卸载时，清理函数可以取消不再需要的订阅、定时器或网络请求，从而释放资源，避免不必要的内存占用。
2. **处理异步操作**：如果组件在异步操作完成前被卸载，清理函数可以中止这些操作，防止尝试更新已卸载组件的状态而导致错误。
3. **优化性能**：当依赖项更新触发新的副作用执行之前，清理函数可以先撤销之前的副作用，确保应用状态的一致性和性能。
4. **确保状态一致性**：通过清理函数可以撤销那些可能影响全局状态或浏览器级别的副作用，比如移除事件监听器等，保证应用状态的一致性。

### <mark style="color:red;">清理函数执行时机</mark>

* <mark style="color:red;">**组件卸载时**</mark><mark style="color:red;">（类似</mark> <mark style="color:red;"></mark><mark style="color:red;">`componentWillUnmount`</mark><mark style="color:red;">）。</mark>
* <mark style="color:red;">**依赖更新时**</mark><mark style="color:red;">，新的</mark> <mark style="color:red;"></mark><mark style="color:red;">`useEffect`</mark> <mark style="color:red;"></mark><mark style="color:red;">运行之前会先执行上一次</mark> <mark style="color:red;"></mark><mark style="color:red;">`useEffect`</mark> <mark style="color:red;"></mark><mark style="color:red;">的清理函数</mark>。

````
```javascript
import React, { useEffect, useState } from 'react';
import { BrowserRouter as Router, Route, Link, Routes } from 'react-router-dom';

function UnmountExample() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log(`useEffect: Setting up effect with count = ${count}`);

    // 返回一个清理函数
    return () => {
      console.log(`Cleanup: Clearing effect with count = ${count}`);
    };
  }, []); // 依赖于count的变化

  return (
    <div>
      <div>This is an example component.</div>
      <p>Current Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
function App() {
  return (
    <Router>
      <div>
        <nav>
          <ul>
            <li><Link to="/">Home</Link></li>
            <li><Link to="/example">Example Component</Link></li>
          </ul>
        </nav>

        <Routes>
          <Route path="/example" element={<UnmountExample />} />
          <Route path="/" element={<div>Home Page</div>} />
        </Routes>
      </div>
    </Router>
  );
}

export default App;
```
````

### 如何在 useEffect 里正确管理异步操作，防止内存泄漏？

在 `useEffect` 中正确管理异步操作，可以通过使用 `useEffect` 的清理函数来防止内存泄漏。

在这个示例中，通过设置 `isMounted` 标志位，可以确保只有在组件挂载时更新状态，防止组件卸载后进行状态更新导致的内存泄漏。

```
const MyComponent = () => {
  const [data, setData] = useState(null);

  useEffect(() => {
    let isMounted = true;

    const fetchData = async () => {
      try {
        const response = await fetch('https://api.example.com/data');
        const result = await response.json();
        if (isMounted) {
          setData(result);
        }
      } catch (error) {
        if (isMounted) {
          console.error(error);
        }
      }
    };

    fetchData();

    return () => {
      isMounted = false;
    };
  }, []);

  return <div>{data ? JSON.stringify(data) : 'Loading...'}</div>;
};
```

### 闭包问题

在 `useEffect` 里面的回调函数会捕获**初次渲染时的变量状态**，即使之后组件状态更新，`useEffect` 内部的**旧闭包**仍然引用的是**旧的变量**。

````
```javascript
import { useState, useEffect, useRef } from "react";

function App() {
    const [count, setCount] = useState(0);

    useEffect(() => {
      const timer = setInterval(() => {
        console.log(count); // 这里总是打印初始值 0
      }, 1000);
      return () => clearInterval(timer);
    }, []); // 空数组意味着 effect 只会在组件挂载和卸载时运行
  
    return <button onClick={() => setCount(count + 1)}>Increment {count}</button>;
}

export default App;
```
````

1. 把 `count` 添加到 `useEffect` 的依赖项

```
useEffect(() => {
  const timer = setInterval(() => {
    console.log(count); // 现在可以正确打印出最新的 count 值
  }, 1000);
  return () => clearInterval(timer);
}, [count]); // 将 count 添加到依赖项数组中
```

2. 使用 `useRef` 持久化最新值

````
```javascript
import { useState, useEffect, useRef } from "react";

function App() {
    const [count, setCount] = useState(0);
    const countRef = useRef();
    
    useEffect(() => {
      countRef.current = count;
    }, [count]);
    
    useEffect(() => {
      const timer = setInterval(() => {
        console.log(countRef.current); // 使用 ref 获取最新值
      }, 1000);
      return () => clearInterval(timer);
    }, []);
  
    return <button onClick={() => setCount(count + 1)}>Increment {count}</button>;
}

export default App;
```
````

### useEffect 进入无限循环的原因

1. `useEffect` 内部直接修改依赖的状态，并且没有依赖数组或者依赖数组为状态值

````
```javascript
import { useEffect, useState } from "react";

function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log("🔥 useEffect 执行");
    setCount(count + 1); // 触发状态更新
  });

  return <p>Count: {count}</p>;
}

export default App;
```

解决

  useEffect(() => {
    console.log("🔥 useEffect 执行");
    setCount(count + 1); // 触发状态更新
  }, []);

````

### **总结：如何避免 useEffect 无限循环**

* 如果 `useEffect` 只应该执行一次，使用 `[]`。
* 如果 `useEffect` 需要依赖某些值，确保这些值不会在每次渲染时改变（使用 `useMemo` 和 `useCallback`）。
* 避免在 `useEffect` 内部直接修改依赖的状态，必要时使用条件判断。如useref保存值
