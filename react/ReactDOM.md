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
