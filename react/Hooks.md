随着 React Hooks 的引入，函数组件可以拥有类似于类组件的生命周期方法。主要使用的 Hooks 包括：

1. **useState()**：用于添加组件的局部状态。
2. **useEffect()**：用于执行副作用操作，如数据获取、设置订阅等。可以模拟类组件中的 componentDidMount、componentDidUpdate 和 componentWillUnmount。
3. **useContext()**：用于消费上下文中的值。
4. **useReducer()**：用于处理更复杂的状态逻辑。
5. **useCallback()**：返回一个 memoized 回调函数。
6. **useMemo()**：返回一个 memoized 值。
7. **useRef()**：返回一个可变的引用对象。
