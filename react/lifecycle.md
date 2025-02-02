挂载（Mounting）、更新（Updating）和卸载（Unmounting

### Mounting 阶段
1. **constructor(props)**：构造函数，在组件实例创建时调用。在这里可以初始化 this.state。

2. **static getDerivedStateFromProps(props, state)**：静态方法，用于在组件实例化或更新时根据 props 更新 state。此方法返回的对象将与 state 合并。

3. **render()**：渲染方法，返回一个描述 UI 的 React 元素。

4. **componentDidMount()**：在组件挂载到 DOM 后立即调用。这里可以执行数据获取、设置定时器等操作。

### Updating 阶段
1. **static getDerivedStateFromProps(props, state)**：在每次更新前调用，用于根据新的 props 更新 state。
2. **shouldComponentUpdate(nextProps, nextState)**在更新前调用，返回 true 或 false 来决定是否需要更新。建议使用 PureComponent 或 React.memo。
3. **render()**：渲染方法，返回一个描述 UI 的 React 元素。
4. **getSnapshotBeforeUpdate(prevProps, prevState)**：在 React 应用更改之前调用，用于捕获有关当前 DOM 的信息。返回的值将作为参数传递给 componentDidUpdate。
5. **componentDidUpdate(prevProps, prevState, snapshot)**：在组件更新到 DOM 后立即调用。可以在这里执行副作用操作，如网络请求等。

### Unmounting 阶段
**componentWillUnmount()**：在组件即将从 DOM 中卸载之前调用。可以在这里清理定时器、事件监听器等。
