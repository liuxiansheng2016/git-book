## React.createRef 
React 提供的一个 API，用于创建 refs，以便你可以直接访问 DOM 元素或者 React 组件实例。通常，你可以在组件的构造函数中使用 React.createRef() 来创建一个 ref，并通过 ref 属性将其附加到对应的元素或组件上。

示例代码如下：
```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }

  render() {
    return <div ref={this.myRef} />;
  }
}
```
使用回调

这种方式允许更精确地控制何时设置和移除 ref
```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    // 初始化一个实例属性来存储 ref
    this.inputElement = null;
  }

  handleFocus = () => {
    if (this.inputElement) {
      this.inputElement.focus();
    }
  }

  render() {
    return (
      <div>
        {/* 使用回调函数作为 ref */}
        <input 
          type="text" 
          ref={(input) => { this.inputElement = input; }} 
        />
        <button onClick={this.handleFocus}>聚焦输入框</button>
      </div>
    );
  }
}
```
### React.forwardRef
React.forwardRef 的 API 中 ref 必须指向 dom 元素而不是 React 组件

React.forwardRef 会创建一个 React 组件，这个组件能够将其接受的 ref 属性转发到其组件树下的另一个组件中。

将 DOM 节点暴露给父组件

在多个组件中转发 ref

接收渲染函数作为参数。React 将使用 props 和 ref 作为参数来调用此函数。此函数应返回 React 节点

为什么需要 React.forwardRef

在 React 中，类组件可以通过 ref 属性访问其实例。然而，函数组件没有实例，因此不能直接接收 ref。React.forwardRef 允许我们创建一个函数组件，该组件可以接收一个 ref 并将其转发给子组件或者其他需要的地方。

使用 React.forwardRef 的例子

假设我们有一个函数组件 MyInput，我们希望能够在父组件中通过 ref 获取到这个输入框的引用：
```
import React, { forwardRef } from 'react';

// 使用 forwardRef 包装组件，使得可以接收 ref 并将其转发给 input 元素
const MyInput = forwardRef((props, ref) => {
  return (
    <input {...props} ref={ref} />
  );
});

export default MyInput;
```
```
import React, { useRef } from 'react';
import MyInput from './MyInput'; // 假设 MyInput 和 ParentComponent 在同一目录下

const ParentComponent = () => {

  // 创建一个 ref，初始值为 null
  const inputRef = useRef(null);

  const handleClick = () => {
    if (inputRef.current) {
      // 调用 focus 方法让输入框获得焦点
      inputRef.current.focus();
      // 打印输入框当前的值
      console.log(inputRef.current.value);
    }
  };

  return (
    <div>
      {/* 将 ref 传递给 MyInput 组件 */}
      <MyInput ref={inputRef} type="text" placeholder="Enter something" />
      {/* 点击按钮时执行 handleClick 函数 */}
      <button onClick={handleClick}>Focus Input</button>
    </div>
  );
};

export default ParentComponent;
```
在这个例子中，我们通过 useRef 创建了一个 ref，并将其传递给了 MyInput 组件。这样，我们就可以在父组件中通过 inputRef 访问到 MyInput 组件的 DOM 节点，并对其进行操作。
