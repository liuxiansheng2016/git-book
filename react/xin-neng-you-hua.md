# 常见性能优化指南

Https://zhuanlan.zhihu.com/p/559922432

一、避免不必要的渲染

二、优化条件渲染

三、列表中正确的使用 key

四、组件销毁后清理引用数据

五、正确的处理数据

六、缓存优化

七、批量更新，减少 Render 次数

八、懒渲染/虚拟列表

九、debounce、throttle 优化频繁触发的回调

### 使用 React.memo 和 useMemo usecallback

**React.memo**：用于优化函数组件的重新渲染。当父组件重新渲染时，如果函数组件的 props 没有变化，则不会重新渲染。

**useMemo**：用于缓存计算结果，当依赖项没有变化时，不会重新计算。

### 使用 React.lazy 和 \<Suspense>
使用 shouldComponentUpdate 或 PureComponent

**shouldComponentUpdate**：在类组件中可以重写此方法来手动控制组件是否需要更新。

**PureComponent**：React 提供的基类，它会根据 props 和 state 的浅层比较来决定是否需要重新渲染。

### 优化状态管理

**useState** 和 **useReducer**：尽量减少全局状态的数量，优先考虑局部状态。

**Context API**：对于需要跨多个组件传递的状态，可以使用 Context API 来避免 prop-drilling。

### 优化虚拟 DOM

**避免不必要的重新渲染**：确保只有在状态或 props 改变时才重新渲染。

**使用 Key 属性**：确保列表中的元素都有唯一的 key 属性，以帮助 React 识别哪些元素发生了变化。
