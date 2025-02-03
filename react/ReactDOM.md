**1. ReactDOM.render()**
用途：这是最常用的 API 之一，用于将 React 元素渲染到提供的容器中并返回对 mounted 实例的引用。
用法示例：
```
const element = <h1>Hello, world</h1>;
ReactDOM.render(element, document.getElementById('root'));
```
**2. ReactDOM.createPortal()**
用途：提供了一种方式，可以将子节点渲染到一个位于 DOM 层次结构之外的新位置（即 DOM 树中的任何位置）。
用法示例：
```
const modalRoot = document.getElementById('modal-root');
class Modal extends React.Component {
  render() {
    return ReactDOM.createPortal(
      this.props.children,
      modalRoot
    );
  }
}
```
**3. ReactDOM.unmountComponentAtNode()**
用途：从 DOM 中卸载 React 组件，并清理其事件处理器和 state。
用法示例：
```
ReactDOM.unmountComponentAtNode(document.getElementById('root'));
```
如果成功卸载了组件，则返回 true；如果没有组件被卸载，则返回 false。

### Fragments
React 还提供了用于减少不必要嵌套的组件。

React.Fragment

使用场景：
1. 当你需要返回多个元素而不希望为这些元素增加额外的包裹标签（如 <div>）时。
2. 优化渲染性能，避免不必要的 DOM 结构层级。

```
render() {
  return (
    <React.Fragment>
      <ChildA />
      <ChildB />
      <ChildC />
    </React.Fragment>
  );
}
```

<p  style="color: red;">也可以使用简写语法<></> </p>

key 是唯一可以传递给 Fragment 的属性

### portal
ReactDOM.createPortal(child, container)

一个 portal 的典型用例是当父组件有 overflow: hidden 或 z-index 样式时，但你需要子组件能够在视觉上“跳出”其容器。例如，对话框、悬浮卡以及提示框：

### SusPense
Suspense 使得组件可以等待某些操作结束后再渲染支持的场景是 React.lazy 动态加载组件,以防止某些子组件尚未具备渲染条件。

```
// 使用 React.lazy 动态加载 OtherComponent 组件
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    // 使用 React.Suspense 包裹动态加载的组件
    <React.Suspense fallback={<Spinner />}>
      <div>
        {/* 动态加载的 OtherComponent 组件 */}
        <OtherComponent />
      </div>
    </React.Suspense>
  );
}
```

### React.lazy

允许定义一个动态加载的组件，这有助于缩减 bundle 的体积，并延迟加载初次渲染未使用道德组件

Const SomeComponnet = React.lazy(() => import(‘./SomeCoponment’))
