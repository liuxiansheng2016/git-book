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
