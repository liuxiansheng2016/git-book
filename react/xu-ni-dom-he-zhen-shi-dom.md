# 虚拟dom和真实dom

## Dom diff

\
计算出Virtual DOM中真正变化的部分，并只针对该部分进行原生DOM操作，而非重新渲染整个页面。

### React 渲染过程

#### 1. Render (渲染) 阶段

这个阶段主要是生成新的虚拟DOM树，并与旧的虚拟DOM树进行比较（diff）。它包括以下子阶段：

* **Tree 构建**：从根组件开始递归地构建出整个应用的虚拟DOM树。
* **Diffing**：React 使用 Fiber 架构来遍历新旧两棵树，通过比较节点来确定哪些部分需要更新。这一过程可以被打断，以便给其他更高优先级的任务让路。
  * **Tree 层级对比**：仅在同一层级上进行比较，不会跨层级比较。
  * **Component 层级对比**：如果组件类型相同，则继续向下深入比较；如果不同，则直接替换整个组件。
  * **Element 层级对比**：对于同一层级的元素，React 会检查它们的 key 和 type 来决定是否可以复用现有的 DOM 节点。

#### 2. Pre-commit (预提交) 阶段

在这个阶段，React 已经决定了哪些更新需要应用到实际的DOM上。但是，在真正执行这些更新之前，React 可能会做一些准备工作，比如计算布局测量(layout measurements)，这在某些情况下是必要的，例如当组件依赖于其尺寸或位置时。

#### 3. Commit (提交) 阶段

一旦所有的准备工作完成，React 就会进入commit阶段，此时真正的DOM更新会被应用。这个阶段涉及以下几个方面：

* **DOM 更新**：根据前面阶段所做的决定，React 会添加、删除或修改DOM节点。
* **生命周期方法调用**：在 commit 阶段，React 还会触发相应的生命周期方法，如 componentDidMount, componentDidUpdate, 或者 componentWillUnmount（对于即将被移除的组件）。
* **Ref 处理**：如果任何组件使用了 refs，那么这些引用也会在这个阶段被更新。

### React Diff 的主要步骤

1. **生成虚拟 DOM 树**：当组件重新渲染时，React 会生成新的虚拟 DOM 树。
2. **比较新旧虚拟 DOM 树**：React 将新的虚拟 DOM 树与旧的虚拟 DOM 树进行比较，找出差异。
3. **应用差异**：React 将找出的差异应用到实际的 DOM 上，只更新需要更改的部分。

### React Diff 的比较规则主要包括以下几点：

* **相同类型的节点**：如果新旧树中的节点类型相同（例如都是 `<div>` 或 `<span>`），那么只会比较它们的属性和子节点。
* **不同类型的节点**：如果新旧树中的节点类型不同，则旧节点将被完全替换。
* **键（key）**：如果节点带有 `key` 属性，React 会使用 `key` 来唯一标识节点。这有助于 React 更精确地识别节点，从而减少不必要的重新渲染。
* **属性和状态**：比较节点的属性（如 `className`、`style` 等）和状态（如 `state`），如果发现不同，则更新相应的属性或状态。
* **子节点**：递归比较子节点，找出子节点之间的差异。

### React 将 diff 分为三个阶段：tree diff、component diff 和 element diff。

#### Tree Diff

Tree diff 涉及的是对整个组件树的不同层级之间的比较。由于 Web UI 中 DOM 节点跨层级的移动操作较少，React 选择只在同一层次内的节点间进行比较。

#### Component Diff

Component diff 主要是针对同类型的组件实例之间的比较。如果两个组件属于同一类别，那么 React 会继续深入比较它们各自的子树；但如果组件类型不同，则直接替换整个组件。

#### Element Diff

Element diff 是指在同一层级的所有节点之间进行比较。当节点处于同一层级时，diff 提供了三种操作：INSERT\_MARKUP（插入）、MOVE\_EXISTING（移动）和 REMOVE\_NODE（删除）。\
虚拟 DOM 不会进行排版与重绘操作，而真实 DOM 会频繁重排与重绘

虚拟 DOM 的总损耗是“虚拟 DOM 增删改+真实 DOM 差异增删改+排版与重绘”，真实 DOM 的总损耗是“真实 DOM 完全增删改+排版与重绘”

## Real DOM vs Virtual DOM

```
|           Real DOM           |       Virtual DOM        |
| :--------------------------: | :----------------------: |
|           更新较慢           |         更新较快         |
|      可以直接更新 HTML       |    无法直接更新 HTML     |
| 如果元素更新，则创建新的 DOM | 如果元素更新，则更新 JSX |
|       DOM 操作非常昂贵       |     DOM 操作非常简单     |
|        较多的内存浪费        |       没有内存浪费       |
```

### 使用虚拟 DOM 的优势如下：

1\. 减少 DOM 操作

直接操作真实的 DOM 是昂贵的操作，因为每次操作都会引起重排（reflow）和重绘（repaint），导致性能下降。虚拟 DOM 可以在内存中进行操作，然后一次性同步到真实 DOM，减少了不必要的重排和重绘。

2\. 提高性能

虚拟 DOM 可以通过高效的 Diff 算法（如 React 的 Diff 算法）找出最小的更新集，从而减少实际 DOM 的更新次数，提高渲染效率。

3.易于调试

虚拟 DOM 是 JavaScript 对象，可以在开发工具中更容易地查看和调试。

4.跨平台

虚拟 DOM 提供了一种统一的表示形式，使得开发者可以在不同的平台上（如 Web、Native）使用相同的 API。

5.易于单元测试

虚拟 DOM 可以在没有实际 DOM 环境的情况下进行单元测试，因为它是纯 JavaScript 对象。

6.延迟渲染

虚拟 DOM 可以延迟渲染，直到需要时才真正更新到 DOM 中，这样可以避免不必要的渲染。

### 缺点：

1.内存消耗:

维护虚拟 DOM 需要额外的内存开销，尤其是对于大型应用而言。虚拟 DOM 的大小与实际 DOM 的大小成正比，因此可能会增加内存使用量。

2.Diff 算法的复杂性

虚拟 DOM 的 Diff 算法本身也是复杂的操作，特别是在大数据集的情况下，Diff 算法可能会变得非常耗时。

3.初始化成本

创建虚拟 DOM 和执行 Diff 算法都有一定的初始化成本，这在初次渲染时可能会导致性能下降。

4.过度优化

虚拟 DOM 试图优化每一次的 DOM 更新，但在某些情况下，直接操作 DOM 可能更加简单和高效。过度依赖虚拟 DOM 可能会导致不必要的复杂性。

### React JSX转换成真实DOM过程？

#### JSX 编译

```
const element = <h1>Hello, world!</h1>;
```

将被编译为：

```
const element = React.createElement('h1', null, 'Hello, world!')
```

* 使用编译工具将 JSX 转换为 `React.createElement` 调用。
* `React.createElement` 创建 React 元素（普通 JavaScript 对象）。
* React 元素构建虚拟 DOM 树。
* 状态或属性变化时，React 进行调和过程（Diff 算法），计算虚拟 DOM 之间的差异。
* 根据差异生成最小化的更新操作，应用到真实 DOM 上。
* 浏览器渲染更新后的真实 DOM。\


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
