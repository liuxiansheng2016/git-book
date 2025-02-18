# 问题

https://github.com/semlinker/reactjs-interview-questions/blob/master/README.md#%E7%9B%AE%E5%BD%95

### ReactDOM

**1.** ~~<mark style="color:red;">**ReactDOM.render()**</mark>~~ 用途：这是最常用的 API 之一，用于将 React 元素渲染到提供的容器中并返回对 mounted 实例的引用。 用法示例：

````
const element = <h1>Hello, world</h1>;
ReactDOM.render(element, document.getElementById('root'));


从React 18开始，推荐使用`createRoot`来替换上述方法：

```javascript
import React from 'react';
import { createRoot } from 'react-dom/client'; // 注意这里的导入路径
import App from './App';

// 获取DOM容器
const container = document.getElementById('root');
// 创建一个新的根目录
const root = createRoot(container);
// 渲染你的应用
root.render(<App />);

#### 关键点

- **`createRoot`**：这是新的API，用来创建一个React根实例。它允许你利用React 18的新特性，如并发模式等。
  
- **移除第二个参数**：不同于之前的`ReactDOM.render`方法需要两个参数（要渲染的元素和目标DOM节点），`createRoot`只需要一个目标DOM节点，并通过调用`.render()`方法指定要渲染的内容。
````

**2. ReactDOM.createPortal()** 用途：提供了一种方式，可以将子节点渲染到一个位于 DOM 层次结构之外的新位置（即 DOM 树中的任何位置）。 用法示例：

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

**3.&#x20;**<mark style="color:red;">**`ReactDOM.unmountComponentAtNode()`**</mark> 用途：从 DOM 中卸载 React 组件，并清理其事件处理器和 state。 用法示例：

<mark style="color:red;">`ReactDOM.unmountComponentAtNode`</mark><mark style="color:red;">已于</mark>[<mark style="color:red;">2022 年 3 月 (v18.0.0)</mark>](https://react.dev/blog/2022/03/08/react-18-upgrade-guide)<mark style="color:red;">弃用。在 React 19 中，你需要使用 迁移到</mark><mark style="color:red;">`root.unmount()`</mark><mark style="color:red;">。</mark>

```
ReactDOM.unmountComponentAtNode(document.getElementById('root'));
// Before
unmountComponentAtNode(document.getElementById('root'));

// After
root.unmount();
```

如果成功卸载了组件，则返回 true；如果没有组件被卸载，则返回 false。

**4.&#x20;**~~<mark style="color:red;">**`ReactDOM.hydrate`**</mark>~~ 专门设计用来配合 SSR 使用。它不会重新创建已经存在于页面上的 DOM 节点，而是尝试复用它们，并在此基础上添加事件监听器等交互逻辑，使静态页面变得动态和交互式。

<mark style="color:red;">在 React 19 中，我们将删除</mark><mark style="color:red;">`ReactDOM.hydrate`</mark><mark style="color:red;">您需要迁移到的</mark>[<mark style="color:red;">`ReactDOM.hydrateRoot`</mark>](https://react.dev/reference/react-dom/client/hydrateRoot)<mark style="color:red;">，</mark>

```
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App'; // 假设这是你的根组件

// 客户端入口文件
if (typeof document !== 'undefined') {
ReactDOM.hydrate(<App />, document.getElementById('root'));
}


// Before
import {hydrate} from 'react-dom';
hydrate(<App />, document.getElementById('root'));

// After
import {hydrateRoot} from 'react-dom/client';
hydrateRoot(document.getElementById('root'), <App />);
```

### ReactDOMServer 是什么?

`ReactDOMServer` 对象使你能够将组件渲染为静态标记（通常用于 Node 服务器中），此对象主要用于服务端渲染（SSR）。以下方法可用于服务器和浏览器环境：

1. `renderToString()`
2. `renderToStaticMarkup()`

例如，你通常运行基于 Node 的 Web 服务器，如 Express，Hapi 或 Koa，然后你调用 `renderToString` 将根组件渲染为字符串，然后作为响应进行发送。

```javascript
// using Express
import { renderToString } from 'react-dom/server'
import MyPage from './MyPage'

app.get('/', (req, res) => {
  res.write('<!DOCTYPE html><html><head><title>My Page</title></head><body>')
  res.write('<div id="content">')
  res.write(renderToString(<MyPage/>))
  res.write('</div></body></html>')
  res.end()
})
```

### 在 React 中如何使用 innerHTML?

`dangerouslySetInnerHTML` 属性是 React 用来替代在浏览器 DOM 中使用 `innerHTML`。与 `innerHTML` 一样，考虑到跨站脚本攻击（XSS），使用此属性也是有风险的。使用时，你只需传递以 `__html` 作为键，而 HTML 文本作为对应值的对象。

在本示例中 MyComponent 组件使用 `dangerouslySetInnerHTML` 属性来设置 HTML 标记：

```jsx
function createMarkup() {
  return { __html: 'First &middot; Second' }
}

function MyComponent() {
  return <div dangerouslySetInnerHTML={createMarkup()} />
}
```

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

也可以使用简写语法<>

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

### ~~<mark style="color:red;">React.forwardRef</mark>~~

React.forwardRef 的 API 中 ref 必须指向 dom 元素而不是 React 组件

React.forwardRef 会创建一个 React 组件，这个组件能够将其接受的 ref 属性转发到其组件树下的另一个组件中。

将 DOM 节点暴露给父组件

在多个组件中转发 ref

接收渲染函数作为参数。React 将使用 props 和 ref 作为参数来调用此函数。此函数应返回 React 节点

为什么需要 React.forwardRef

在 React 中，类组件可以通过 ref 属性访问其实例。然而，函数组件没有实例，因此不能直接接收 ref。React.forwardRef 允许我们创建一个函数组件，该组件可以接收一个 ref 并将其转发给子组件或者其他需要的地方。

使用 React.forwardRef 的例子

假设我们有一个函数组件 MyInput，我们希望能够在父组件中通过 ref 获取到这个输入框的引用：

<pre><code>import React, { forwardRef } from 'react';

// 使用 forwardRef 包装组件，使得可以接收 ref 并将其转发给 input 元素
const MyInput = forwardRef((props, ref) => {
    return (
<strong>        &#x3C;input {...props} ref={ref} />
</strong>    );
});

export default MyInput;

</code></pre>

<mark style="color:red;">从 React 19 开始，你现在可以在函数组件中将 ref 作为 prop 进行访问：</mark>

~~<mark style="color:red;">新的函数组件将不再需要</mark> <mark style="color:red;"></mark><mark style="color:red;">`forwardRef`</mark>~~，我们将发布一个 codemod 来自动更新你的组件以使用新的 `ref` prop。在未来的版本中，我们将弃用并移除 `forwardRef`。

#### 注意

在类组件中，`ref` 不作为 props 传递，因为它们引用的是组件实例。这意味着，如果你在类组件中需要访问 `ref`，你需要使用 `React.forwardRef` 或者 `React.createRef`。

```

function MyInput({placeholder, ref}) {
  return <input placeholder={placeholder} ref={ref} />
}

//...
<MyInput ref={ref} />
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

* 一个 React Element 是一个描述 UI 外观的简单对象。它通常包含组件类型、属性（props）以及子元素。可以把它看作是 UI 的“蓝图”。
* **创建方式**：
  * 使用 JSX（例如 `<div>Hello</div>`）
  * 或使用 `React.createElement`

#### Component

ReactComponent 则是可以接收参数输入并且返回某个 ReactElement 的函数或者类.

* **React Element** 是描述 UI 的不可变对象，是 UI 的最小构建块。
* **React Component** 是一个逻辑单元，可以接受输入、管理状态，并返回一个或多个 React Elements 来构成完整的 UI。

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

useEffect：在浏览器绘制之后执行。 useLayoutEffect：在浏览器绘制之前同步执行。

**阻塞性**

useEffect：不会阻塞浏览器更新屏幕，因此更适合用于不需要立即更新 DOM 的副作用操作。 useLayoutEffect：会阻塞浏览器更新屏幕，因此更适合用于需要在浏览器绘制之前读取或修改 DOM 的副作用操作。

**性能影响** useEffect：由于不会阻塞浏览器更新屏幕，对性能的影响较小。

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

<mark style="color:red;">使用 requestAnimationFrame 来延迟 DOM 更新，确保在下一个浏览器绘制周期中进行更新。</mark>

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

### React 事件机制

React 的事件机制是一套由 React 自行实现的、基于原生浏览器事件系统构建的事件处理方案，其核心在于“合成事件（SyntheticEvent）”和“事件委托机制”。下面详细介绍这一概念：

***

#### 1. 合成事件（SyntheticEvent）

* **封装原生事件**\
  React 并不直接使用浏览器原生的事件对象，而是将其封装成合成事件对象。这个对象遵循 W3C 标准，提供统一的接口（例如：`preventDefault()`、`stopPropagation()` 等），从而屏蔽了不同浏览器之间的差异。\
  citeturn0search9
* **事件池机制的演变**\
  在 React 16 及之前版本中，合成事件采用了事件池（event pooling）来复用事件对象，减少内存分配。但从 React 17 开始，事件池被移除，每次事件触发时都会创建新的事件对象，这使得异步事件处理更加直观，开发者也无需调用 `e.persist()` 来保留事件对象。\
  citeturn0search8

***

#### 2. 事件委托机制

* **统一绑定**\
  React 并不是在每个组件的真实 DOM 节点上直接绑定事件处理函数，而是在整个应用的根容器上统一注册事件监听器。在 React 16 之前，这个绑定点通常是在全局的 `document` 上；而从 React 17 开始，事件监听器绑定在通过 `ReactDOM.createRoot` 创建的容器上。这种设计不仅降低了内存消耗，还减少了全局事件冲突的可能性。\
  citeturn0search5
* **事件分发**\
  当一个原生事件触发后，它会沿着 DOM 冒泡到根容器。React 捕获到这个事件后，会根据事件的目标节点及组件树的结构，查找并依次调用注册在各组件上的事件处理函数，从而模拟了事件的捕获和冒泡过程。

***

#### 3. 设计目的与优点

* **跨浏览器兼容性**\
  合成事件封装了各浏览器之间的差异，使得开发者可以编写一致的事件处理代码。
* **性能优化**\
  通过事件委托，只需在根容器上绑定一次事件监听器，大幅减少了每个 DOM 节点单独绑定事件带来的内存和性能开销。
* **统一管理**\
  统一的事件注册和分发机制使得组件挂载与卸载时，事件的添加和移除更为高效，降低了开发维护成本。

***

#### 4. 阻止默认行为与冒泡

* **阻止默认行为**\
  调用 `e.preventDefault()` 用于阻止浏览器的默认操作（如表单提交或链接跳转）。
* **阻止事件传播**\
  调用 `e.stopPropagation()` 才能防止事件继续冒泡到父元素。这两者各有用途，不能混用。

***

#### 总结

React 的事件机制通过合成事件和事件委托，实现了跨浏览器统一接口、性能优化以及便捷的事件管理。特别是在 React 17 中，绑定位置从全局 `document` 转移到了根容器上，同时取消了事件池机制，这些更新使得事件处理更加清晰、直观和高效，为开发者提供了更好的用户交互体验。

### React 的事件和普通的 HTML 事件有什么不同？

对于事件名称命名方式，原生事件为全小写，react 事件采用小驼峰；

对于事件函数处理语法，原生事件为字符串，react 事件为函数；

react 事件不能采用 return false 的方式来阻止浏览器的默认行为，而必须要地明确地调用 preventDefault()来阻止默认行为。

事件的执行顺序为原生事件先执行，合成事件后执行，合成事件会冒泡绑定到 document 上，所以尽量避免原生事件与合成事件混用，如果原生事件阻止冒泡，可能会导致合成事件不执行，因为需要冒泡到 document 上合成事件才会执行



### React-为什么不要直接改 state

直接修改 react react 不会重新渲染，setstate 是异步的

我们在改变 state 的时候，需要重新生成一个对象去代替原来的 state，而不是直接改原来的

如果连续写多次 setState，会将多次 setState 的状态修改合并成一次状态修改。

### 函数式组件没有生命周期

没有继承 React.Component,由于生命周期函数是 React.Component 类的方法实现的,所以没继承这个类,自然就没法使用生命周期函数

### 如何提高组件的渲染效率

* 子组件执行 shouldcomponentUpdate 接收两个参数控制是否渲染
* 使用 React.PureComponent React 提供了一个辅助对象来实现浅比较(shallowCompare)这种模式 - 继承自 React.PureComponent。当组件更新时，如果组件的 props 和 state 都没发生改变，render 方法就不会触发，省去 Virtual DOM 的生成和比对过程，达到提升性能的目的。
* immutable.js 不可突变的数据结构

Immutable Data 就是一旦创建，就不能再被更改的数据。对 Immutable 对象的任何修改或添加删除操作都会返回一个新的 Immutable 对象。Immutable 实现的原理是 Persistent Data Structure（持久化数据结构），也就是使用旧数据创建新数据时，要保证旧数据同时可用且不变

#### Immutable 的好处

**可预测性**：由于不可变对象不会改变，因此它们的行为更加可预测。 **易于调试**：状态不变意味着更容易追踪 bug，因为你可以确定某个状态在某个时间点的值。 **性能优化**：React 利用浅比较（shallow comparison）来决定是否重新渲染组件。不可变数据结构可以提高这种优化的有效性。 **并发安全**：不可变数据结构在多线程环境下更容易处理，因为不存在数据竞争（race conditions）的问题。 **易于理解**：不可变数据使得代码更容易理解和维护 最常用的库之一是 Immutable.js，它提供了一系列不可变的数据结构，如 Map, List, Set 等。

**使用 Redux 与 Immutable 数据**

Redux 是一个流行的用于状态管理的库，可以结合 Immutable 数据结构来提高性能。

在使用 redux 过程中也可以结合 Immutable，不使用 Immutable 前修改一个数据需要做一个深拷贝

**初始化 Store：**

当你创建 Redux store 时，需要使用 redux-immutable 提供的 combineReducers 函数代替原生的 Redux 版本。这是因为你需要确保整个 state tree 都是由 Immutable.js 对象组成的。

```
import { combineReducers } from 'redux-immutable';
import { createStore } from 'redux';
import { Map } from 'immutable';

const rootReducer = combineReducers({
// your reducers here
});

const initialState = Map();
const store = createStore(rootReducer, initialState);
```

**编写 Reducers：**

你的 reducers 需要返回 Immutable.js 的数据结构。例如，你可以使用 Map 和 List 来代替普通的 JavaScript 对象和数组。

```
function counter(state = Map({ count: 0 }), action) {
switch (action.type) {
case 'INCREMENT':
  return state.set('count', state.get('count') + 1);
default:
  return state;
}
}
```

**连接 React 组件：**

当你使用 react-redux 的 connect 函数将 React 组件与 Redux store 连接起来时，记得从 Immutable.js 对象中提取数据。例如，如果你的状态是一个 Map，你应该使用 .get() 方法而不是点操作符来访问属性。

```
const mapStateToProps = (state) => ({
count: state.get('count')
});
```

在做 react 性能优化的时候，为了避免重复渲染，我们会在 shouldComponentUpdate()中做对比，当返回 true 执行 render 方法

Immutable 通过 is 方法则可以完成对比，而无需像一样通过深度比较的方式比较

### Render 方法调用

* 当使用 this.setState()
* 当你的组件收到新的 props -当 this.forceUpdate()被调用时。

初始化：当组件被创建并被插入到 DOM 中时，会触发初始渲染，render 方法会被调用。

更新：当组件的 props 或 state 发生改变时，会触发组件的更新。在更新过程中，React 会检查组件之前的虚拟 DOM 数和当前的虚拟 DOM 树的差异，然后只更新发生变化的部分。在更新过程中，render 方法会再次被调用。

父组件更新：如果组件的父组件发生更新，就会导致组件本身的更新，render 方法会被调用。

强制更新：通过调用组件实例的 forceUpdate 方法可以强制组件进行更新，即使组件的 props 和 state 没有发生变化，render 方法也会执行。

### 高阶组件

一个高阶组件就是一个函数，接受一个组件作为输入，然后返回一个新的组件作为结果。

1 重用代码

2.修改现有组件的行为，， 比如使用第三方组件，独立于原有的组件，可以产生新组件，对原组件没有侵害。

**约定**

props 保持一致 你不能在函数式（无状态）组件上使用 ref 属性，因为它没有实例 不要以任何方式改变原始组件 WrappedComponent 透传不相关 props 属性给被包裹的组件 WrappedComponent 不要再 render() 方法中使用高阶组件 使用 compose 组合高阶组件 包装显示名字以便于调试

```
import React, { Component } from 'react';

// 定义高阶组件 withPersistentData
function withPersistentData(WrappedComponent, storageKey) {
return class extends Component {
constructor(props) {
  super(props);
  this.state = {
    data: null,
  };
}

componentDidMount() {
  // 在组件挂载后尝试从 localStorage 获取数据
  const data = localStorage.getItem(storageKey);
  if (data !== null) {
    this.setState({ data: JSON.parse(data) });
  }
}

render() {
  // 将数据通过 props 传递给被包裹的组件
  return <WrappedComponent {...this.props} data={this.state.data} />;
}
};
}

// 定义一个简单的展示组件 MyComponent2
class MyComponent2 extends React.Component {
render() {
const { data } = this.props;
return (
  <div>
    {data ? `Cached Data: ${data}` : 'No cached data found.'}
  </div>
);
}
}

// 使用高阶组件包装 MyComponent2，指定存储键为 'myData'
const MyComponentWithPersistentData = withPersistentData(MyComponent2, 'myData');

// 渲染带有缓存功能的组件
ReactDOM.render(<MyComponentWithPersistentData />, document.getElementById('root'));
```

### 受控组件

在React中，处理表单数据主要有两种方式：使用受控组件和非受控组件。

#### 受控组件

* **工作原理**：受控组件的表单元素值由React组件的状态管理。每当用户输入时，组件的`state`会更新，从而控制表单元素的值。
*   **示例**：

    ```jsx
    function ControlledInput() {
      const [value, setValue] = useState('');

      function handleChange(e) {
        setValue(e.target.value);
      }

      return <input type="text" value={value} onChange={handleChange} />;
    }
    ```
* **优点**：数据流清晰，表单状态完全可控，易于实现复杂的表单逻辑和验证。
* **缺点**：需要编写更多的代码来管理表单状态。

#### 非受控组件

* **工作原理**：非受控组件的表单元素值由DOM节点本身管理。React通过`ref`来访问和操作DOM节点的值。
*   **示例**：

    ```jsx
    function UncontrolledInput() {
      const inputRef = useRef(null);

      function handleSubmit() {
        console.log(inputRef.current.value);
      }

      return (
        <form onSubmit={handleSubmit}>
          <input type="text" ref={inputRef} />
          <button type="submit">提交</button>
        </form>
      );
    }
    ```
* **优点**：代码简洁，适用于简单的表单场景。
* **缺点**：数据流不够清晰，难以实现复杂的表单逻辑和验证。

#### 使用场景

* **受控组件**：适用于需要实时验证、格式化输入或复杂表单交互的场景。
* **非受控组件**：适用于简单的表单输入，如搜索框等，不需要复杂逻辑的场景。

### Purecomponent

React 创建了 PureComponent 组件创建了默认的 shouldComponentUpdate 行为。 这个默认的 shouldComponentUpdate 行为会一一比较 props 和 state 中所有的属性，只有当其中任意一项发生改变是，才会进行重绘。

需要注意的是，PureComponent 使用浅比较判断组件是否需要重绘

因此，下面对数据的修改并不会导致重绘（假设 Table 也是 PureComponent)

```
options.push(new Option())

options.splice(0, 1)

options[i].name = "Hello"
```

<mark style="color:red;">这些例子都是在原对象上进行修改，由于浅比较是比较指针的异同，所以会认为不需要进行重绘</mark>。

### 传递数据

Props 传递参数

Props 传递方法

Ref 获取组件的实例

Redux

React context

发布订阅

### 为什么 useState 要使用数组而不是对象 ？

useState 返回一个数组而不是对象的主要原因是，数组的解构赋值更加灵活。这样，你可以自由地命名你的状态变量和更新函数，而不是被迫使用像 this.state 和 this.setState 这样的命名。

### setState

异步更新：setState 是异步的，这意味着它不会立即更新状态。如果你试图在调用 setState 后立即访问最新的状态值，你可能会得到旧的状态值 25。

```
changeText() {
this.setState({
message: "你好啊"
})
console.log(this.state.message); // Hello World
}
```

在上面的例子中，最终打印的结果为 Hello World，而不是 "你好啊"，因为在执行完 setState 之后并不能立刻拿到最新的 state 结果。 回调函数：为了在状态更新后立即获取最新的状态值，可以传递一个回调函数作为 setState 的第二个参数。这个回调函数会在状态更新完成后执行 10。

```
changeText() {
this.setState({
message: "你好啊"
}, () => {
console.log(this.state.message); // 你好啊
});
}
```

合并状态更新：如果在同一个事件循环中多次调用 setState 来更新相同的状态属性，React 会将这些更新合并，并只应用最后一次更新 1。

```
handleClick = () => {
this.setState({
count: this.state.count + 1,
})
console.log(this.state.count) // 1

this.setState({
count: this.state.count + 1,
})
console.log(this.state.count) // 1

this.setState({
count: this.state.count + 1,
})
console.log(this.state.count) // 1
}
```

实际上等价于如下操作：

```
Object.assign(
previousState,
{count: state.count + 1},
{count: state.count + 1},
...
)
```

因为后面的数据会覆盖前面的更改，所以最终只增加了一次。 批处理更新：对于依赖前一个状态的情况，推荐给 setState 传入一个函数，这样每次更新都会基于最新的状态进行 7。

```
onClick = () => {
this.setState((prevState, props) => {
return {count: prevState.count + 1};
});

this.setState((prevState, props) => {
return {count: prevState.count + 1};
});
}
```

通过这种方式，你可以确保每次更新都是基于最新的状态进行的，从而避免了由于状态合并导致的问题。此外，这种做法也支持 React 的批处理机制，有助于提高性能并减少不必要的渲染次数。

### 组件间通信

1. 父组件向子组件传递 props
2. 子组件向父组件传递 父组件向子组件传一个函数，然后通过这个函数的回调 3.兄弟组件之间的通信 则父组件作为中间层来实现数据的互通，通过使用父组件传递 4.父组件向后代组件传递 使用 context 提供了组件之间通讯的一种方式 5.非关系组件传递 建议将数据进行一个全局资源管理 redux

### ErrorBoundary

组件内异常，也就是异常边界组件能够捕获的异常，主要包括：

1. 渲染过程中异常；
2. 生命周期方法中的异常；
3. 子组件树中各组件的 constructor 构造函数中异常。

不能捕获的异常，主要是异步及服务端触发异常： 1.事件处理器中的异常； 处理方法： 使用 try/catch 代码进行捕获 2. 异步任务异常，如 setTiemout，ajax 请求异常等； 处理方法：使用全局事件 window.addEventListener 捕获 3. 服务端渲染异常； 4. 异常边界组件自身内的异常； 处理方法：将边界组件和业务组件分离，各司其职，不能在边界组件中处理逻辑代码，也不能在业务组件中使用 didcatch

**错误边界（Error Boundaries）:**

错误边界是在 React 16 中引入的一个特性，它允许你捕捉发生在其子组件树任何位置的 JavaScript 错误，并且不会让整个组件树崩溃。错误边界可以是任何类组件或函数组件，只要它们定义了特定的生命周期方法或 hook。

形成错误边界组件的两个条件：

使用了 static getDerivedStateFromError()

使用了 componentDidCatch()

抛出错误后，请使用 static getDerivedStateFromError() 渲染备用 UI ，使用 componentDidCatch() 处理错误

```
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props)
    this.state = { hasError: false }
  }
  
  componentDidCatch(error, info) {
    // You can also log the error to an error reporting service
    logErrorToMyService(error, info)
  }
  
  static getDerivedStateFromError(error) {
     // Update state so the next render will show the fallback UI.
     return { hasError: true };
  }
  
  render() {
    if (this.state.hasError) {
      // You can render any custom fallback UI
      return <h1>{'Something went wrong.'}</h1>
    }
    return this.props.children
  }
}
```

之后，将其作为常规组件使用：

<pre><code>&#x3C;ErrorBoundary>
<strong>    &#x3C;MyWidget />
</strong>&#x3C;/ErrorBoundary>
</code></pre>

### Key

<mark style="color:red;">提供唯一的 key 可以帮助 React 更快地识别和更新元素</mark>，从而提高性能。当列表中有大量元素时，这一点尤为重要。

1.使用唯一标识符：确保提供的 key 是唯一的，并且在每次渲染时都保持一致。

2.避免使用索引作为 key：如果列表项的位置可能会改变，避免使用索引作为 key，因为这会导致不必要的重新渲染。

3.使用稳定的标识符：如果可能，使用来自数据源的稳定标识符（如数据库 ID）作为 key。

数组中使用的键在其同级中应该是唯一的，但它们不需要是全局唯一的。也就是说，你可以在两个不同的数组中使用相同的键

![image](https://github.com/user-attachments/assets/10503c61-a656-40c6-bd30-a2805cb23913)

### 展示组件和 容器组件

展示组件更关心 UI

容器组件 更关心组件如何运作的，通常是有状态的

### Fiber

React中的Fiber架构是React核心算法的一次重大重构，旨在提高React在处理大型复杂应用时的性能表现，特别是解决长时间运行任务导致的页面卡顿问题。以下是关于Fiber架构的理解及其解决的问题：

#### 理解Fiber架构

* **Fiber是什么**：Fiber是React内部用于表示组件树的一种数据结构。每个Fiber节点代表一个React元素，包含组件的类型、状态、属性等信息，以及指向父节点、子节点和兄弟节点的指针，形成了一个链表结构，取代了之前的递归遍历方式。
* **工作原理**：Fiber架构将渲染任务拆分成多个小任务，每个任务可以在浏览器空闲时执行。React通过维护一个Fiber树，在每次渲染时遍历这棵树，更新节点的状态。在遍历过程中，React可以根据当前帧的剩余时间决定是否继续执行下一个任务，或者暂停当前任务，将控制权交还给浏览器，确保页面的响应性。

#### 解决的问题

* **避免阻塞主线程**：传统的React使用递归方式遍历组件树，一旦开始渲染，必须等到整个树遍历完成才能释放主线程，可能导致页面卡顿。Fiber架构通过将渲染任务切片，可以中断和恢复，确保主线程有足够的时间响应用户输入。
* **支持异步渲染**：Fiber架构引入了异步渲染机制，如`React.lazy()`和`Suspense`，允许组件在需要时动态加载，提高了应用的初始加载性能和用户体验。
* **优先级调度**：Fiber架构可以根据任务的优先级动态调整执行顺序，高优先级的任务（如用户输入响应）会被优先处理，低优先级的任务（如数据加载）则可以在空闲时执行，提高了应用的响应速度和流畅度。
* **更好的性能监控和调试**：Fiber架构提供了更详细的性能分析工具，如React Profiler，可以更精确地分析每个组件的渲染性能，帮助开发者找出性能瓶颈并进行优化。

总之，Fiber架构通过引入新的数据结构和调度机制，显著提升了React在处理大型复杂应用时的性能表现，为开发者提供了更好的工具和手段来优化应用的性能和用户体验。

#### 为什么采用 Fiber 架构？

1. 并发渲染（Concurrency） React 之前的实现是基于 Stack（栈）驱动的，这意味着在渲染期间，如果有一个长任务正在进行，那么整个 UI 都会被阻塞，直到这个任务完成。这对于用户体验来说是非常不利的，特别是在大型应用中，长任务可能会导致应用变得无响应。Fiber 架构的设计是为了支持并发渲染，即在渲染过程中能够中断并恢复，从而允许 React 在渲染过程中插入其他工作，如处理用户输入，使应用更加流畅。
2. 更好的错误处理 Fiber 结构提供了更好的错误处理机制。每个 Fiber 节点都有一个指向父节点的指针，这使得错误处理更加容易，因为可以在树的任意位置中断渲染，捕获错误，并且可以选择性地重试。
3. 细粒度的更新 Fiber 架构使得 React 可以更好地追踪每个组件的状态，因为每个 Fiber 节点都可以保存其自己的工作进度。这意味着 React 可以在需要的时候回溯到之前的某个状态，或者在某个点暂停渲染，稍后再继续。
4. 改进的性能 Fiber 结构通过将工作分解成更小的任务来改进性能。每个 Fiber 节点代表一个单独的工作单元，React 可以根据优先级调度这些单元，这样就可以优先处理重要的更新，而将次要的更新推迟。
5. 更灵活的更新逻辑 由于 Fiber 结构的引入，React 可以更灵活地管理更新逻辑。例如，通过 getDerivedStateFromProps 和 getSnapshotBeforeUpdate 生命周期方法，React 可以在渲染的不同阶段收集信息并做出决策。
6. 可中断性和优先级 Fiber 结构允许 React 在渲染过程中中断，这意味着 React 可以根据优先级来安排渲染任务，比如先渲染高优先级的任务，然后再处理低优先级的任务。

#### fiber 架构的工作原理

Fiber 架构的核心思想是将工作分割成更小的单位，并且可以中断这些单位的工作，以便在需要时恢复。

**下面是一个简化的 Fiber 架构的工作原理概述：**

Fiber 节点结构 每个 Fiber 节点都是一个对象，包含以下属性：

**Type**: 表示节点的类型，如类组件、函数组件、DOM 元素等。

**Key**: 用于唯一标识兄弟节点。

**State Node**: 实际的 DOM 节点或组件实例。

**Child/Sibling/Return**: 指向子节点、同级节点以及父节点的指针。

**Work In Progress**: 当前正在执行的工作副本。

**Flags/Effects**: 记录当前节点的副作用，如需要添加、删除或更新 DOM。

**Priority**: 工作的优先级。

**Tag**: 标记节点的类型，如同步、异步等。

#### 工作循环（Work Loop）

React 使用一个工作循环来处理渲染任务。这个循环是可中断的，意味着它可以被中断并在后续的时间片中恢复。工作循环的基本步骤如下：

**初始化**：当需要更新组件时，React 创建一个根 Fiber 节点，并启动工作循环。

**调度**：React 使用调度器（Scheduler）来确定何时执行渲染工作。调度器考虑优先级和其他系统负载来决定何时执行工作。

**执行**：在每个时间片内，React 处理一小部分工作，例如，遍历虚拟 DOM 树的一部分，计算差异，等等。

**中断**：如果时间片结束或有更高优先级的任务到来，React 会中断当前的工作，并保存所有的工作进度。

**恢复**：当再次获得执行时间时，React 会从中断的地方恢复工作，直到所有的工作都完成。

**提交**：当所有的工作都完成时，React 会执行提交阶段，将实际的 DOM 更新操作应用到界面上。

#### 主要阶段

Fiber 架构的工作流程主要包括以下两个主要阶段：

**1. Render Phase（渲染阶段）** 在渲染阶段，React 会根据最新的状态和属性构建新的虚拟 DOM 树。这个阶段涉及到：

**递归遍历**：遍历整个虚拟 DOM 树，为每个节点创建或更新对应的 Fiber 节点。

**计算差异**：比较新的虚拟 DOM 树与旧的虚拟 DOM 树之间的差异。

**中断与恢复**：如果需要，中断当前的工作并在下一个时间片恢复。

**2. Commit Phase（提交阶段）** 提交阶段是在新的虚拟 DOM 树构建完成后发生的，它涉及到：

**更新 DOM**：将计算出的差异应用到真实的 DOM 上。

**执行副作用**：如调用生命周期方法、执行回调等。

**清理工作**：清理不再需要的 Fiber 节点。

### useImperativeHandle

useImperativeHandle 是 React Hooks 中的一个 Hook，它用于自定义父组件通过 ref 访问子组件实例时的行为。这个 Hook 可以让你指定当一个父组件获取一个子组件的 ref 时，希望暴露哪些方法或值给父组件。这样可以让你控制父组件如何与子组件交互。

useImperativeHandle 的用途

**暴露方法**：允许子组件向父组件暴露方法，以便父组件可以调用这些方法。

**定制 ref 行为**：让你能够自定义暴露给父组件的 ref 对象的值或方法，使得父组件能够按照预期的方式与子组件交互。

useImperativeHandle 的基本用法 useImperativeHandle(ref, createHandle, \[input]) \*\* ref\*\*：由父组件传递给子组件的 ref 对象。 createHandle：一个函数，返回一个对象，该对象包含了希望暴露给父组件的方法或值。 input：可选的依赖数组，用于确定何时重新创建 createHandle 函数的结果。 使用示例

```
import React, { forwardRef, useImperativeHandle, useState } from 'react';

const ChildComponent = forwardRef((props, ref) => {
const [count, setCount] = useState(0);

useImperativeHandle(ref, () => ({
increment: () => setCount(prevCount => prevCount + 1),
getCount: () => count,
}));

return <div>Count: {count}</div>;
});

export default ChildComponent;

import React, { useRef } from 'react';
import ChildComponent from './ChildComponent';

const ParentComponent = () => {
const childRef = useRef();

return (
<div>
  <ChildComponent ref={childRef} />
  <button onClick={() => childRef.current.increment()}>Increment</button>
  <button onClick={() => alert(childRef.current.getCount())}>Show Count</button>
</div>
);
};

export default ParentComponent;
```

### 在 React 中如何校验 props 属性?

当应用程序以开发模式运行的时，React 将会自动检查我们在组件上设置的所有属性，以确保它们具有正确的类型。如果类型不正确，React 将在控制台中生成警告信息。由于性能影响，它在生产模式下被禁用。使用 `isRequired` 定义必填属性。

预定义的 prop 类型：

1. `PropTypes.number`
2. `PropTypes.string`
3. `PropTypes.array`
4. `PropTypes.object`
5. `PropTypes.func`
6. `PropTypes.node`
7. `PropTypes.element`
8. `PropTypes.bool`
9. `PropTypes.symbol`
10. `PropTypes.any`

我们可以为 `User` 组件定义 `propTypes`，如下所示：

```jsx
import React from 'react'
import PropTypes from 'prop-types'

class User extends React.Component {
  static propTypes = {
    name: PropTypes.string.isRequired,
    age: PropTypes.number.isRequired
  }

  render() {
    return (
      <>
        <h1>{`Welcome, ${this.props.name}`}</h1>
        <h2>{`Age, ${this.props.age}`}</h2>
      </>
    )
  }
}
```

**注意:** 在 React v15.5 中，_PropTypes_ 从 `React.PropTypes` 被移动到 `prop-types` 库中。

### 在使用 ES6 类的 React 中 `super()` 和 `super(props)` 有什么区别?

当你想要在 `constructor()` 函数中访问 `this.props`，你需要将 props 传递给 `super()` 方法。

使用 `super(props)`:

```javascript
class MyComponent extends React.Component {
  constructor(props) {
    super(props)
    console.log(this.props) // { name: 'John', ... }
  }
}
```

使用 `super()`:

```javascript
class MyComponent extends React.Component {
  constructor(props) {
    super()
    console.log(this.props) // undefined
  }
}
```

在 `constructor()` 函数之外，访问 `this.props` 属性会显示相同的值。

### 在 JSX 中如何进行循环?

你只需使用带有 ES6 箭头函数语法的 `Array.prototype.map` 即可。例如，`items` 对象数组将会被映射成一个组件数组：

```jsx
<tbody>
  {items.map(item => <SomeComponent key={item.id} name={item.name} />)}
</tbody>
```

你不能使用 `for` 循环进行迭代：

```jsx
<tbody>
  for (let i = 0; i < items.length; i++) {
    <SomeComponent key={items[i].id} name={items[i].name} />
  }
</tbody>
```

### 在 React 状态中删除数组元素的推荐方法是什么?

更好的方法是使用 `Array.prototype.filter()` 方法。

例如，让我们创建用于更新状态的 `removeItem()` 方法。

```javascript
removeItem(index) {
  this.setState({
    data: this.state.data.filter((item, i) => i !== index)
  })
}
```

### React memo 函数是什么?

当类组件的输入属性相同时，可以使用 **pureComponent** 或 **shouldComponentUpdate** 来避免组件的渲染。现在，你可以通过把函数组件包装在 **React.memo** 中来实现相同的功能。

```jsx
const MyComponent = React.memo(function MyComponent(props) {
 /* only rerenders if props change */
});
```

### 严格模式有什么好处?

```
 在下面的情况下，<StrictMode> 将有所帮助：

 1. 使用 **unsafe lifecycle methods** 标识组件。
 2. 有关 **legacy string ref** API 用法发出警告。
 3. 检测无法预测的 **side effects**。
 4. 检测 **legacy context** API。
 5. 有关已弃用的 findDOMNode 用法的警告。
 
```

### reducer 为什么必须是纯函数？

#### 可预测性

* **纯函数**：给定相同的输入，总是返回相同的输出，且不产生副作用。
* **非纯函数**：可能因外部状态或随机性导致结果不确定，难以预测应用状态的变化。

#### 易于调试和测试

* **纯函数**：行为简单明确，方便编写单元测试，隔离验证其正确性。
* **非纯函数**：依赖外部因素，测试复杂且不稳定，难以定位问题。

#### 支持时间旅行调试

* Redux DevTools通过记录每次action和state，实现状态回溯和重现。
* **纯函数**：确保每次执行结果一致，保证时间旅行调试的准确性。
* **非纯函数**：可能导致状态回溯时结果不一致，影响调试功能。

#### 性能优化

* Redux通过浅比较新旧state来判断是否更新组件。
* **纯函数**：总是返回新的state对象，方便Redux进行比对，优化性能。
* **非纯函数**：可能修改原state，导致浅比较失效，强制组件重新渲染，降低性能。

如果reducer不是纯函数，可能会出现以下问题：

* **状态管理不可靠**：结果不确定，导致应用状态混乱，难以维护。
* **调试困难**：难以追踪和重现问题，增加调试难度。
* **性能下降**：不必要的组件重新渲染，降低应用性能。

### 在React中，如何进行表单验证

在React中，表单验证可以通过多种方法实现，包括使用原生JavaScript、React内置的特性，以及第三方库。以下是几种常用的表单验证方法：

#### 使用原生JavaScript

你可以直接在React组件中使用JavaScript逻辑进行表单验证。通常，这涉及到使用React的状态（`useState`）来存储表单字段的值，并在提交表单或字段失去焦点时进行验证。

示例代码：

```jsx
import React, { useState } from 'eact';

function LoginForm() {
  const [username, setUsername] = useState('');
  const [password, setPassword] = useState('');
  const [errors, setErrors] = useState({});

  const validateForm = () => {
    let newErrors = {};
    if (!username) {
      newErrors.username = 'Username is required.';
    }
    if (!password) {
      newErrors.password = 'Password is required.';
    } else if (password.length < 6) {
      newErrors.password = 'Password must be at least 6 characters long.';
    }
    return Object.keys(newErrors).length === 0? null : newErrors;
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    const formErrors = validateForm();
    if (formErrors) {
      setErrors(formErrors);
    } else {
      // 表单验证通过，可以执行登录操作
      console.log('Form is valid, submitting...');
      // 清除错误信息
      setErrors({});
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="username">Username:</label>
        <input
          id="username"
          type="text"
          value={username}
          onChange={(e) => setUsername(e.target.value)}
        />
        {errors.username && <p>{errors.username}</p>}
      </div>
      <div>
        <label htmlFor="password">Password:</label>
        <input
          id="password"
          type="password"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
        />
        {errors.password && <p>{errors.password}</p>}
      </div>
      <button type="submit">Login</button>
    </form>
  );
}

export default LoginForm;
```

#### 使用HTML5内置验证

HTML5提供了一些内置的表单验证属性，如`required`、`pattern`、`minlength`等，可以直接在表单元素上使用。

#### 使用第三方库

### 路由守卫

在React中，路由守卫（Route Guards）是一种机制，用于在用户访问特定路由之前进行权限检查或其他逻辑验证。它类似于现实世界中的门禁系统，确保只有具备特定条件的用户才能进入某些区域。路由守卫在React路由管理中发挥着重要作用，主要体现在以下几个方面：

#### 权限验证

路由守卫可以检查用户是否登录，以及是否具有访问特定页面的权限。例如，确保只有管理员才能访问管理页面。

#### 数据预加载

在进入需要大量数据的页面之前，路由守卫可以预先获取所需数据，提高页面加载速度和用户体验。

#### 页面访问控制

根据特定条件限制对某些页面的访问。例如，某些页面可能只允许在特定时间段内访问，路由守卫可以检查当前时间是否符合要求。

#### 实现方式

React中通常通过自定义高阶组件（如`PrivateRoute`）或使用`Route`组件的`render`属性来实现路由守卫。这些方法允许在渲染组件前执行逻辑，根据条件决定是否渲染目标组件或进行重定向。

示例代码（使用高阶组件实现路由守卫）：

```jsx
import React, { useState, useEffect } from 'react';
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';
import Home from './Home';
import Login from './Login';
import Dashboard from './Dashboard';
import PrivateRoute from './PrivateRoute';

function App() {
  const [isAuthenticated, setIsAuthenticated] = useState(false);

  // 模拟从服务器获取认证状态
  useEffect(() => {
    // 假设这里是从服务器获取认证状态
    const checkAuth = async () => {
      try {
        // 这里应该有实际的认证检查逻辑
        const response = await fetch('/api/check-auth');
        if (response.ok) {
          setIsAuthenticated(true);
        }
      } catch (error) {
        console.error("Authentication check failed", error);
      }
    };

    checkAuth();
  }, []);

  return (
    <Router>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/login" element={<Login />} />
        {/* 使用 element 属性来指定组件，在需要保护的路由上使用 PrivateRoute */}
        <Route path="/dashboard" element={<PrivateRoute isAuthenticated={isAuthenticated} component={Dashboard} />} />
      </Routes>
    </Router>
  );
}

export default App;

import React from 'react';
import { Navigate, Outlet } from 'react-router-dom';

const PrivateRoute = ({ isAuthenticated, component: Component }) => {
  return isAuthenticated
    ? <Outlet /> // 如果用户已认证，则渲染子组件或元素
    : <Navigate to="/login" />; // 否则重定向到登录页
};

// 在父组件中使用时，需将要保护的组件作为子元素传递给 PrivateRoute
// 如：<PrivateRoute isAuthenticated={isAuthenticated}><Dashboard /></PrivateRoute>

export default PrivateRoute;
```

#### 总结

路由守卫是React路由管理中的重要概念，用于控制对特定路由的访问。通过实现路由守卫，可以增强应用的安全性和用户体验，确保用户只能访问他们有权访问的资源，并在需要时预先加载数据，提升应用的性能。

### 在React中，如何处理复杂的业务逻辑

在React中处理复杂的业务逻辑时，可以采用以下设计模式和方法：

#### 设计模式

1. **容器组件与展示组件模式**：
   * **容器组件**负责处理数据和业务逻辑，通常包含状态管理和数据获取。
   * **展示组件**负责UI渲染，接收来自容器组件的props进行展示，无状态、易于复用。
2. **高阶组件（HOC）模式**：
   * HOC是一个函数，接收一个组件并返回一个新的增强功能的组件。
   * 用于代码复用，如添加日志、权限控制、数据获取等功能。
3. **Render Props模式**：
   * 组件通过一个名为`render`的prop接收一个函数，该函数返回需要渲染的React元素。
   * 允许组件之间共享逻辑，提高复用性。
4. **Hook模式**：
   * 使用React提供的`useState`、`useEffect`等Hook，在函数组件中管理状态和副作用。
   * 自定义Hook可封装可复用的状态逻辑，提升代码复用性和可维护性。

#### 方法

1. **组件拆分**：
   * 将复杂的组件拆分为更小、职责单一的组件，提高代码可读性和复用性。
2. **状态管理**：
   * 使用`useState`、`useReducer`管理组件内部状态。
   * 对于全局状态，可使用Redux、MobX等状态管理库。
3. **逻辑复用**：
   * 利用HOC、Render Props、自定义Hook等方式复用组件逻辑。
4. **函数式编程**：
   * 采用不可变数据，使用纯函数处理数据，避免副作用，提高代码可预测性和可测试性。
5. **类型检查**：
   * 使用TypeScript或Flow进行类型检查，减少类型错误，提高代码健壮性。
6. **单元测试**：
   * 编写单元测试，覆盖关键逻辑，确保代码质量和稳定性。

通过合理运用这些设计模式和方法，可以有效处理React中的复杂业务逻辑，提高代码的可维护性和可扩展性。

### Render Props

Render Props模式是一种在React中复用组件逻辑的强大方式。通过将渲染逻辑作为prop传递给组件，可以灵活地根据不同的场景渲染不同的UI，同时共享相同的业务逻辑

Render Props 是一种在 React 中共享代码的模式，它通过使用一个函数类型的 prop 来实现。这个函数通常接收一些数据并返回 React 元素。以下是一个具体的 Render Props 示例，该示例展示了如何使用 Render Props 模式来追踪鼠标的位置，并将这些信息传递给渲染逻辑。

### 示例：MouseTracker 组件

首先，我们创建一个 `Mouse` 组件，它负责监听鼠标移动事件，并将其位置作为参数传递给 render 函数：

```javascript
import React from 'react';

class Mouse extends React.Component {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = { x: 0, y: 0 };
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div style={{ height: '100vh' }} onMouseMove={this.handleMouseMove}>
        {/* 这里调用传入的 render 函数，将当前鼠标位置传递给它 */}
        {this.props.render(this.state)}
      </div>
    );
  }
}

export default Mouse;
```

接下来，我们将创建一个父组件 `MouseTracker`，它使用 `Mouse` 组件并通过 render prop 决定如何渲染鼠标的位置：

```javascript
import React from 'react';
import Mouse from './Mouse'; // 假设 Mouse 组件在同一目录下

function MouseTracker() {
  return (
    <div>
      <h1>Move the mouse around!</h1>
      <Mouse render={mouse => (
        <p>The current mouse position is ({mouse.x}, {mouse.y})</p>
      )}/>
    </div>
  );
}

export default MouseTracker;
```

以下是更多关于Render Props模式的示例，展示了它在不同场景下的应用：

### 示例 5: 权限控制

在需要基于用户权限控制UI展示的应用中，可以使用Render Props来封装权限检查逻辑。

```javascript
const PermissionChecker = ({ permission, children }) => {
  const hasPermission = checkPermission(permission); // 假设有一个函数用于检查权限

  return children({ hasPermission });
};

function App() {
  return (
    <PermissionChecker permission="admin">
      {({ hasPermission }) => (
        hasPermission? (
          <button>管理设置</button>
        ) : (
          <p>您没有权限访问此内容。</p>
        )
      )}
    </PermissionChecker>
  );
}
```

### 示例 6: 无限滚动加载

创建一个可复用的无限滚动组件，当用户滚动到列表底部时加载更多数据。

```javascript
class InfiniteScroll extends React.Component {
  state = {
    hasMore: true,
    data: [],
  };

  componentDidMount() {
    this.fetchData();
  }

  fetchData = () => {
    // 模拟异步数据加载
    setTimeout(() => {
      const newData = [...this.state.data,...generateData()];
      this.setState({
        data: newData,
        hasMore: newData.length < 100, // 假设总共有100条数据
      });
    }, 1000);
  };

  handleScroll = () => {
    if (this.state.hasMore && window.innerHeight + window.scrollY >= document.body.offsetHeight) {
      this.fetchData();
    }
  };

  render() {
    return (
      <div style={{ overflowY: 'croll', height: '300px' }} onScroll={this.handleScroll}>
        {this.state.data.map((item, index) => (
          <div key={index}>{item}</div>
        ))}
        {this.props.render(this.state)}
      </div>
    );
  }
}

function App() {
  return (
    <InfiniteScroll render={({ hasMore }) => (hasMore? <p>加载中...</p> : <p>没有更多数据了</p>)} />
  );
}
```

### 示例 7: 模态框管理

创建一个通用的模态框组件，通过Render Props控制模态框的显示和隐藏。

```javascript
class ModalManager extends React.Component {
  state = {
    isOpen: false,
  };

  toggleModal = () => {
    this.setState(prevState => ({
      isOpen:!prevState.isOpen,
    }));
  };

  render() {
    return this.props.render({
      isOpen: this.state.isOpen,
      toggleModal: this.toggleModal,
    });
  }
}

function App() {
  return (
    <ModalManager render={({ isOpen, toggleModal }) => (
      <>
        <button onClick={toggleModal}>打开模态框</button>
        {isOpen && (
          <div style={{ position: 'fixed', top: '50%', left: '50%', transform: 'translate(-50%, -50%)', background: '#fff', padding: '20px' }}>
            <p>这是一个模态框</p>
            <button onClick={toggleModal}>关闭</button>
          </div>
        )}
      </>
    )} />
  );
}
```

### 示例 8: 拖放功能

创建一个可复用的拖放组件，允许用户拖放元素。

```javascript
class Draggable extends React.Component {
  state = {
    isDragging: false,
    position: { x: 0, y: 0 },
  };

  handleMouseDown = (e) => {
    this.setState({
      isDragging: true,
      position: { x: e.clientX, y: e.clientY },
    });
  };

  handleMouseMove = (e) => {
    if (this.state.isDragging) {
      this.setState({
        position: { x: e.clientX, y: e.clientY },
      });
    }
  };

  handleMouseUp = () => {
    this.setState({
      isDragging: false,
    });
  };

  componentDidMount() {
    window.addEventListener('mousemove', this.handleMouseMove);
    window.addEventListener('mouseup', this.handleMouseUp);
  }

  componentWillUnmount() {
    window.removeEventListener('mousemove', this.handleMouseMove);
    window.removeEventListener('mouseup', this.handleMouseUp);
  }

  render() {
    return this.props.render({
      isDragging: this.state.isDragging,
      position: this.state.position,
    });
  }
}

function App() {
  return (
    <Draggable render={({ isDragging, position }) => (
      <div style={{ position: 'absolute', top: position.y, left: position.x }}>
        {isDragging? '正在拖动' : '拖动我'}
      </div>
    )} />
  );
}
```

在这个例子中，`MouseTracker` 组件并不直接关心鼠标的坐标是如何获取的，而是通过传递一个函数给 `Mouse` 组件的 `render` prop 来决定当鼠标移动时应如何渲染内容 。

Render Props 是一种强大的模式，它允许你将跨多个组件的逻辑抽象出来，并通过简单的函数传递来复用这些逻辑。然而，在现代 React 应用中，Hooks（如 `useEffect`, `useState` 等）也提供了类似的功能，并且有时会更加直观和易于理解 。因此，在选择使用 Render Props 还是 Hooks 时，可以根据具体的应用需求和个人偏好来决定。

### CSS动画

在React中实现动画可以通过多种方式，每种方式都有其优缺点和适用场景。以下是几种常用的动画实现方式：

#### 使用CSS动画

* **优点**：实现简单，性能较好，浏览器能够优化CSS动画。
* **缺点**：可操控性较差，难以精确控制动画细节，对于复杂动画实现较为困难。
* **示例**：通过React的内联样式或CSS类名，结合CSS的`transition`、`animation`属性实现动画。

#### 使用React Transition Group

* **优点**：提供简单的组件用于处理进入、离开和移动动画，易于使用。
* **缺点**：功能相对有限，对于复杂动画可能需要额外的库支持。
* **示例**：使用`<CSSTransition>`、`<TransitionGroup>`等组件实现列表项的添加、删除动画。

#### 使用React Spring

* **优点**：提供强大的动画功能，支持物理动画和复杂的动画效果，API简单直观。
* **缺点**：学习曲线较陡峭，对于简单动画可能过于复杂。
* **示例**：使用`useSpring`、`useTransition`等钩子函数实现各种动画效果。

#### 使用Styled Components

* \*\*优点

### React中组件拆分的理解

在React中，组件拆分是将一个复杂的UI界面分解为多个独立、可复用的组件的过程。合理拆分组件有助于提高代码的可维护性、可读性和可重用性。

#### 组件拆分的原则

1. **单一职责原则**：每个组件应负责单一的功能或展示特定的UI部分。避免在一个组件中包含过多的逻辑和样式。
2. **高内聚、低耦合**：组件内部应高度内聚，即相关的功能和状态应集中在一个组件中；组件之间应低耦合，减少不必要的依赖。
3. **可复用性**：设计组件时应考虑其复用性，尽量使组件在不同的场景下都能使用。
4. **层级清晰**：组件的层级结构应清晰，父组件负责整体布局和状态管理，子组件负责具体的展示和交互。

#### 拆分方法

1. **按功能拆分**：将具有独立功能的部分拆分为组件，如搜索框、分页器、表单等。
2. **按业务逻辑拆分**：根据业务逻辑的复杂程度，将不同的业务逻辑拆分为独立的组件。
3. **按UI元素拆分**：将页面中的UI元素，如按钮、图标、卡片等，拆分为基础组件。
4. **按数据流拆分**：根据数据的流向和处理方式，将数据处理和展示拆分为不同的组件。

### React中处理国际化

在React中处理国际化和本地化问题，可以使用以下库和实现思路：

#### 相关库

1. **`react-intl`**：一个广泛使用的React国际化库，提供组件和API来处理日期、数字、消息的本地化，支持多种语言环境。
2. **`i18next`** 和 **`react-i18next`**：功能强大的国际化库，支持嵌套翻译、复数形式、上下文等高级特性，易于扩展和维护。

#### 实现思路

1. **定义语言环境**：确定应用支持的语言列表，如`en`、`zh-CN`等。
2. **创建翻译文件**：为每种语言创建翻译文件，通常使用JSON或YAML格式，包含所有需要翻译的文本及其对应的翻译。
3. **配置国际化库**：初始化国际化库，加载翻译文件，设置默认语言环境。
4. **使用国际化组件**：在React组件中使用国际化库提供的组件或API，如`<FormattedMessage>`、`useTranslation`等，来显示本地化的文本。
5. **切换语言环境**：提供用户界面，允许用户切换语言环境，并更新应用的国际化状态。

#### 示例代码

1.  **安装依赖**

    ```bash
    npm install react-intl
    ```
2. **创建翻译文件**
   *   **`en.json`**

       ```json
       {
         "hello": "Hello",
         "welcome": "Welcome to my app"
       }
       ```
   *   **`zh-CN.json`**

       ```json
       {
         "hello": "你好",
         "welcome": "欢迎来到我的应用"
       }
       ```
3.  **配置国际化**

    ```javascript
    import { IntlProvider } from 'eact-intl';

    const App = () => {
      return (
        <IntlProvider locale="en" messages={en}>
          {/* 应用组件 */}
        </IntlProvider>
      );
    };
    ```
4.  **使用国际化组件**

    ```javascript
    import { FormattedMessage } from 'eact-intl';

    const Greeting = () => {
      return <FormattedMessage id="hello" defaultMessage="Hello" />;
    };
    ```

通过以上步骤，可以实现React应用的国际化和本地化，支持多语言显示，提升用户体验。

### 路由参数

在React中，路由参数是一种在URL中传递数据的方式，常用于构建动态页面。路由参数允许根据URL的不同部分加载不同的内容或执行不同的操作。

#### 路由参数的类型

1. **动态路由参数**：在路由路径中使用冒号 `:` 定义参数，例如 `/users/:id`。当URL匹配该路径时，参数值会被提取并传递给对应的组件。
2. **查询参数**：在URL中使用问号 `?` 后面跟着键值对的形式传递参数，例如 `/search?q=react`。查询参数不会改变路由的路径，但可以提供额外的信息。
3. **状态参数**：通过 `history` 对象的 `push` 或 `replace` 方法传递参数，例如 `history.push('/dashboard', { userId: 123 })`。状态参数不会显示在URL中，但可以在组件中通过 `location.state` 访问。

#### 获取和使用路由参数

1. **动态路由参数**
   *   在组件中使用 `useParams` Hook 获取参数，例如：

       ```javascript
       import { useParams } from 'eact-router-dom';

       function UserDetail() {
         const { id } = useParams();
         // 使用 id 进行后续操作，如数据请求
         return <div>User {id}</div>;
       }
       ```
2. **查询参数**
   *   使用 `useLocation` Hook 获取查询参数，然后使用 `URLSearchParams` 或第三方库（如 `qs`）进行解析，例如：

       ```javascript
       import { useLocation } from 'eact-router-dom';
       import qs from 'qs';

       function SearchResults() {
         const location = useLocation();
         const query = qs.parse(location.search, { ignoreQueryPrefix: true });
         const searchTerm = query.q;
         // 使用 searchTerm 进行后续操作，如显示搜索结果
         return <div>Search results for "{searchTerm}"</div>;
       }
       ```
3. **状态参数**
   *   通过 `useLocation` Hook 获取 `location.state`，例如：

       ```javascript
       import { useLocation } from 'eact-router-dom';

       function Dashboard() {
         const location = useLocation();
         const userId = location.state?.userId;
         // 使用 userId 进行后续操作
         return <div>Welcome to the dashboard, User {userId}</div>;
       }
       ```

#### 示例路由配置

```javascript
import { BrowserRouter as Router, Route, Switch } from 'eact-router-dom';

function App() {
  return (
    <Router>
      <Switch>
        <Route path="/users/:id" component={UserDetail} />
        <Route path="/search" component={SearchResults} />
        <Route path="/dashboard" component={Dashboard} />
      </Switch>
    </Router>
  );
}
```

通过合理使用路由参数，可以实现动态内容的加载和页面之间的数据传递，提升应用的灵活性和用户体验。

