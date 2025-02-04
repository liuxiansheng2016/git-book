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

### React 事件机制
React 并不是将 click 事件绑定到了 div 的真实 DOM 上，而是在 document 处监听了所有的事件，当事件发生并且冒泡到 document 处的时候，React 将事件内容封装并交由真正的处理函数运行。这样的方式不仅仅减少了内存的消耗，还能在组件挂在销毁时统一订阅和移除事件。

除此之外，冒泡到 document 上的事件也不是原生的浏览器事件，而是由 react 自己实现的合成事件（SyntheticEvent）。因此如果不想要是事件冒泡的话

应该调用 event.preventDefault()方法，而不是调用 event.stopProppagation()方法。

**目的：**

兼容所有浏览器，更好的跨平台；

将事件统一存放在一个数组（事件池），避免频繁的新增与删除（垃圾回收）。

方便 react 统一管理和事务机制。

### React 的事件和普通的 HTML 事件有什么不同？
对于事件名称命名方式，原生事件为全小写，react 事件采用小驼峰；

对于事件函数处理语法，原生事件为字符串，react 事件为函数；

react 事件不能采用 return false 的方式来阻止浏览器的默认行为，而必须要地明确地调用 preventDefault()来阻止默认行为。

事件的执行顺序为原生事件先执行，合成事件后执行，合成事件会冒泡绑定到 document 上，所以尽量避免原生事件与合成事件混用，如果原生事件阻止冒泡，可能会导致合成事件不执行，因为需要冒泡到 document 上合成事件才会执行

### React 组件中怎么做事件代理？它的原理是什么？
React 基于 Virtual DOM 实现了一个 SyntheticEvent 层（合成事件层），定义的事件处理器会接收到一个合成事件对象的实例，它符合 W3C 标准，且与原生的浏览器事件拥有同样的接口，支持冒泡机制，所有的事件都自动绑定在最外层上。

在 React 底层，主要对合成事件做了两件事：

事件委派： React 会把所有的事件绑定到结构的最外层，使用统一的事件监听器，这个事件监听器上维持了一个映射来保存所有组件内部事件监听和处理函数。

自动绑定： React 组件中，每个方法的上下文都会指向该组件的实例，即自动绑定 this 为当前组件

### React-为什么不要直接改 state
直接修改 react react 不会重新渲染，setstate 是异步的

我们在改变 state 的时候，需要重新生成一个对象去代替原来的 state，而不是直接改原来的

如果连续写多次 setState，会将多次 setState 的状态修改合并成一次状态修改。

### 函数式组件没有生命周期
没有继承 React.Component,由于生命周期函数是 React.Component 类的方法实现的,所以没继承这个类,自然就没法使用生命周期函数

### 如何提高组件的渲染效率
1.子组件执行 shouldcomponentUpdate 接收两个参数控制是否渲染

使用 React.PureComponent
3. React 提供了一个辅助对象来实现浅比较(shallowCompare)这种模式 - 继承自 React.PureComponent。当组件更新时，如果组件的 props 和 state 都没发生改变，render 方法就不会触发，省去 Virtual DOM 的生成和比对过程，达到提升性能的目的。

4. immutable.js 不可突变的数据结构

Immutable Data 就是一旦创建，就不能再被更改的数据。对 Immutable 对象的任何修改或添加删除操作都会返回一个新的 Immutable 对象。Immutable 实现的原理是 Persistent Data Structure（持久化数据结构），也就是使用旧数据创建新数据时，要保证旧数据同时可用且不变

#### Immutable 的好处
**可预测性**：由于不可变对象不会改变，因此它们的行为更加可预测。
**易于调试**：状态不变意味着更容易追踪 bug，因为你可以确定某个状态在某个时间点的值。
**性能优化**：React 利用浅比较（shallow comparison）来决定是否重新渲染组件。不可变数据结构可以提高这种优化的有效性。
**并发安全**：不可变数据结构在多线程环境下更容易处理，因为不存在数据竞争（race conditions）的问题。
**易于理解**：不可变数据使得代码更容易理解和维护
最常用的库之一是 Immutable.js，它提供了一系列不可变的数据结构，如 Map, List, Set 等。

##### 使用 Redux 与 Immutable 数据

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

当使用 this.setState()
当你的组件收到新的 props
当 this.forceUpdate()被叫时。

初始化：当组件被创建并被插入到 DOM 中时，会触发初始渲染，render 方法会被调用。

更新：当组件的 props 或 state 发生改变时，会触发组件的更新。在更新过程中，React 会检查组件之前的虚拟 DOM 数和当前的虚拟 DOM 树的差异，然后只更新发生变化的部分。在更新过程中，render 方法会再次被调用。

父组件更新：如果组件的父组件发生更新，就会导致组件本身的更新，render 方法会被调用。

强制更新：通过调用组件实例的 forceUpdate 方法可以强制组件进行更新，即使组件的 props 和 state 没有发生变化，render 方法也会执行。


### React.createClass 和 extends Component 的区别
React.createClass 和 extends Component 的 bai 区别主要在于：

（1）语法区别

createClass 本质上是一个工厂函数，extends 的方式更加接近最新的 ES6 规范的 class 写法。两种方式在语法上的差别主要体现在方法的定义和静态属性的声明上。

createClass 方式的方法定义使用逗号，隔开，因为 creatClass 本质上是一个函数，传递给它的是一个 Object；而 class 的方式定义方法时务必谨记不要使用逗号隔开，这是 ES6 class 的语法规范。

（2）propType 和 getDefaultProps

React.createClass：通过 proTypes 对象和 getDefaultProps()方法来设置和获取 props.

React.Component：通过设置两个属性 propTypes 和 defaultProps

（3）状态的区别

React.createClass：通过 getInitialState()方法返回一个包含初始值的对象

React.Component：通过 constructor 设置初始状态

（4）this 区别

React.createClass：会正确绑定 this

React.Component：由于使用了 ES6，这里会有些微不同，属性并不会自动绑定到 React 类的实例上。

（5）Mixins

React.createClass：使用 React.createClass 的话，可以在创建组件时添加一个叫做 mixins 的属性，并将可供混合的类的集合以数组的形式赋给 mixins。

如果使用 ES6 的方式来创建组件，那么 React mixins 的特性将不能被使用了。

```

import React from 'react';

let MyMixin = {
   doSomething(){}
}

let TodoItem = React.createClass({
   mixins: [MyMixin], // add mixin
   render(){
      return <div></div>
  }
})
```

### 为什么 reducer 是一个纯函数
redux 源代码中将 oldState 和 newState（reducer 返回的结果）做比较，如果 reducer 为非纯函数，两者指向同一个地址，导致 react 认为 state 无变化，从而不更新页面。

### 高阶组件
一个高阶组件就是一个函数，接受一个组件作为输入，然后返回一个新的组件作为结果。

1 重用代码

2.修改现有组件的行为，， 比如使用第三方组件，独立于原有的组件，可以产生新组件，对原组件没有侵害。

**约定**

props 保持一致
你不能在函数式（无状态）组件上使用 ref 属性，因为它没有实例
不要以任何方式改变原始组件 WrappedComponent
透传不相关 props 属性给被包裹的组件 WrappedComponent
不要再 render() 方法中使用高阶组件
使用 compose 组合高阶组件
包装显示名字以便于调试

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
类似于表单元素会维护自身的状态，基于用户输入更新

在使用表单来收集用户输入时，例如<input><select><textearea>等元素都要绑定一个 change 事件，当表单的状态发生变化，就会触发 onChange 事件，更新组件的 state。这种组件在 React 中被称为受控组件

### Purecoponnet
React 创建了 PureComponent 组件创建了默认的 shouldComponentUpdate 行为。这个默认的 shouldComponentUpdate 行为会一一比较 props 和 state 中所有的属性，只有当其中任意一项发生改变是，才会进行重绘。

需要注意的是，PureComponent 使用浅比较判断组件是否需要重绘

因此，下面对数据的修改并不会导致重绘（假设 Table 也是 PureComponent)

options.push(new Option())

options.splice(0, 1)

options[i].name = "Hello"

这些例子都是在原对象上进行修改，由于浅比较是比较指针的异同，所以会认为不需要进行重绘。

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
在上面的例子中，最终打印的结果为 Hello World，而不是 "你好啊"，因为在执行完 setState 之后并不能立刻拿到最新的 state 结果。
回调函数：为了在状态更新后立即获取最新的状态值，可以传递一个回调函数作为 setState 的第二个参数。这个回调函数会在状态更新完成后执行 10。
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
因为后面的数据会覆盖前面的更改，所以最终只增加了一次。
批处理更新：对于依赖前一个状态的情况，推荐给 setState 传入一个函数，这样每次更新都会基于最新的状态进行 7。
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

