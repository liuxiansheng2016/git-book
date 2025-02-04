# 问题

### ReactDOM
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
1. 当你需要返回多个元素而不希望为这些元素增加额外的包裹标签时。
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

### React.createRef 
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

### Element 和 componnet
#### Element
element 其实就基本就可以解释为 Virtual DOM

#### Component
ReactComponent 则是可以接收参数输入并且返回某个 ReactElement 的函数或者类.

### React 严格模式
React.StrictMode

严格模式检查仅仅在开发模式运行。
```
import React from 'react';

function ExampleApplication() {
  return (
    <div>
      <Header />
      {/* 使用 StrictMode 包裹部分子组件 */}
      <React.StrictMode>
        <div>
          <ComponentOne />
          <ComponentTwo />
        </div>
      </React.StrictMode>
      <Footer />
    </div>
  );
}
```
在上述的示例中，不会对 Header 和 Footer 组件运行严格模式检查。但是，ComponentOne 和 ComponentTwo 以及它们的所有后代元素都将进行检查。

不安全的生命周期

过时 API

### UseEffect 和 useLayoutEffect

**执行时机**

useEffect：在浏览器绘制之后执行。
useLayoutEffect：在浏览器绘制之前同步执行。

**阻塞性**

useEffect：不会阻塞浏览器更新屏幕，因此更适合用于不需要立即更新 DOM 的副作用操作。
useLayoutEffect：会阻塞浏览器更新屏幕，因此更适合用于需要在浏览器绘制之前读取或修改 DOM 的副作用操作。

**性能影响**
useEffect：由于不会阻塞浏览器更新屏幕，对性能的影响较小。

useLayoutEffect：由于会阻塞浏览器更新屏幕，可能会导致性能下降，特别是当副作用操作复杂或耗时较长时。

useEffect 导致屏幕闪烁或布局抖动的主要原因是它在浏览器绘制之后执行，这意味着在 useEffect 中进行的 DOM 操作或状态更新可能会在用户已经看到初始渲染结果之后发生，从而导致视觉上的不连贯或闪烁。

**优化 useEffect 的使用**

如果必须使用 useEffect，可以通过以下方法来减少闪烁或抖动的影响：

延迟更新：
```
import React, { useEffect, useState } from 'react';

function MyComponent() {
  const [value, setValue] = useState(null);

  useEffect(() => {
    requestAnimationFrame(() => {
      // 这里可以放置你需要执行的操作，比如从服务器获取数据
      // 模拟异步操作
      setTimeout(() => {
        setValue('New Value');
      }, 1000);
    });
  }, []); // 空数组意味着这个 effect 只会在组件挂载和卸载时运行

  return (
    <div>
      {value === null ? 'Loading...' : value}
    </div>
  );
}

export default MyComponent;
```

使用 requestAnimationFrame 来延迟 DOM 更新，确保在下一个浏览器绘制周期中进行更新。

条件渲染：
```
import React, { useEffect, useState } from 'react';

function MyComponent() {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    // 模拟数据获取
    setTimeout(() => {
      setData({ title: 'Hello World' });
      setLoading(false);
    }, 2000);
  }, []);

  if (loading) {
    return <div>Loading...</div>;
  }

  return (
    <div>
      <h1>{data.title}</h1>
    </div>
  );
}

export default MyComponent;
```

在初始渲染时显示一个占位符或加载状态，确保在 useEffect 中完成所有必要的操作后再显示最终内容。

为什么 useLayoutEffect 在 SSR 中不可用？

**缺少浏览器环境**：服务器端没有浏览器环境，因此无法访问 DOM，也无法执行与 DOM 相关的操作。

**同步执行**：useLayoutEffect 需要在浏览器绘制之前同步执行，而服务器端渲染是在服务器上完成的，没有浏览器绘制的概念

### React 事件传参数

**使用箭头函数**

箭头函数可以方便地将额外的参数传递给事件处理函数。下面是一个例子：
```
<button onClick={(event) => this.handleClick(id, event)}>点击我</button>
```
在这个例子中，handleClick 函数接收 id 和 event 作为参数。

**使用 bind 方法**

另一种方法是使用 Function.prototype.bind() 来预设部分参数：
```
<button onClick={this.handleClick.bind(this, id)}>点击我</button>
```

### React 事件绑定

**1. 在构造函数中使用 bind 方法**

这是 React 官方推荐的方法之一，它是在组件的构造函数中对需要使用的类方法进行 this 绑定。这样做的好处是性能较好，因为绑定只会在组件实例化时发生一次。

```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    console.log('Clicked!');
  }

  render() {
    return <button onClick={this.handleClick}>Click Me</button>;
  }
}
```
**3. 定义阶段使用箭头函数绑定**

而当你使用属性初始化器来定义类方法时，比如：
```
class MyComponent extends React.Component {
  handleClick = () => {
    console.log('Clicked!');
  };

  render() {
    return <button onClick={this.handleClick}>Click Me</button>;
  }
}
```
在这种情况下，handleClick 是作为一个类的字段（Field）来定义的，它在组件实例化时就被创建了一次，并且不会在每次渲染时重新创建。这使得它比直接在 JSX 中定义匿名箭头函数更高效。

**3. 在 JSX 中使用箭头函数**

当你直接在 JSX 的事件处理属性中定义一个匿名的箭头函数时，例如：
```
class MyComponent extends React.Component {
  render() {
    return <button onClick={() => this.handleClick()}>Click Me</button>;
  }

  handleClick() {
    console.log('Clicked!');
  }
}
```
这种做法每次组件渲染时都会创建一个新的函数实例，因为匿名箭头函数是在 render 方法内定义的。这样做可能导致性能问题，尤其是在该函数作为 props 传递给子组件的情况下，可能会导致不必要的重新渲染。

**4. 在 JSX 中直接使用 bind**

你可以在 JSX 的事件属性中直接使用 bind 方法来绑定事件处理函数。例如：

```
class MyComponent extends React.Component {
  handleClick() {
    console.log('Clicked!');
  }

  render() {
    return <button onClick={this.handleClick.bind(this)}>Click Me</button>;
  }
}
```
这种方法的问题在于每次组件重新渲染时都会创建一个新的函数实例，这可能会导致性能问题，特别是在该函数作为 props 传递给子组件的情况下，可能会导致不必要的重新渲染15。


