# 更新

[https://react.dev/blog/2024/04/25/react-19-upgrade-guide](https://react.dev/blog/2024/04/25/react-19-upgrade-guide)

{% embed url="https://zh-hans.react.dev/blog/2024/12/05/react-19#ref-as-a-prop" %}

### React DOM 相关

* **ReactDOM.render 与 ReactDOM.hydrate**：
  * ReactDOM.render 被移除，取而代之的是 ReactDOM.createRoot；ReactDOM.hydrate 则需要迁移到 hydrateRoot。
* **卸载和查找 DOM 节点方法**：
  * `unmountComponentAtNode` 需要使用 `root.unmount()`；`findDOMNode` 被彻底移除，建议使用 refs 替代。

### React 组件相关

* **废弃 Function 组件的 propTypes 与 defaultProps**：
  * 移除对函数组件中 `propTypes` 的检查，且默认属性推荐使用 ES6 默认参数代替 `defaultProps`。
  * 类组件依然保留 `defaultProps`，因为没有 ES6 替代方案。
* **废弃 Legacy Context**：
  * 不再支持通过 `contextTypes` 和 `getChildContext` 实现的旧 Context。
  * 开发者需迁移到新 Context API（`React.createContext` 和 `contextType` 或 `useContext`）。
* **废弃字符串 refs**：
  * 建议全部迁移为回调 refs 或使用 `React.createRef`，以避免性能和安全性问题。
* **废弃 Module Pattern Factories 与 React.createFactory**：
  * 这些旧写法均被移除，推荐直接使用 JSX 表达组件。

### &#x20;TypeScript 更新

* **TypeScript 类型改进**：
  * 移除了已废弃 API 相关的类型，更新了 useRef（现要求必须传入初始值）、ReactElement 的默认 props 类型由 any 改为 unknown，改进了 useReducer 的类型推断，以及对 JSX 命名空间进行了作用域限制（不再污染全局）

### ref 相关变化

* **ref 作为普通 prop**
  * 从 React v19 开始，在函数组件中可以直接将 `ref` 作为 prop 使用，而不必再借助 `forwardRef`。未来版本中将弃用 `forwardRef`，推荐直接使用新的模式。
  * 在类组件中，`ref` 不作为 props 传递，因为它们引用的是组件实例。这意味着，如果你在类组件中需要访问 `ref`，你需要使用 `React.forwardRef` 或者 `React.createRef`
* **ref 清理函数支持**
  * 新的 ref 回调现在可以返回一个清理函数，这个函数将在元素卸载或 ref 更新时调用，帮助开发者在取消订阅或清理副作用时更加方便。
  * 与之前的行为不同，以前卸载时会用 `null` 调用 ref 函数，现在如果返回清理函数，则不会再调用 ref 回调传入 null。

### Context 与默认属性

* **Context 提供者的新语法**
  * React v19 允许直接将 `<Context>` 渲染为提供者，而不必使用 `<Context.Provider>`。这使得代码更简洁，并且将来会弃用 `<Context.Provider>` 的写法。
* **函数组件默认属性**
  * 移除了对函数组件中 `propTypes` 和 `defaultProps` 的支持，推荐使用 TypeScript 或 ES6 默认参数来处理组件属性类型和默认值。

**删除：字符串引用**

[类组件支持字符串引用，但由于存在多个缺点](https://github.com/facebook/react/issues/1373)而被引用回调取代。在 React 19 中，我们删除了字符串引用，以使 React 更简单、更易于理解。

```
// Before
class MyComponent extends React.Component {
  componentDidMount() {
    this.refs.input.focus();
  }

  render() {
    return <input ref='input' />;
  }
}
```

```
// After
class MyComponent extends React.Component {
  componentDidMount() {
    this.input.focus();
  }

  render() {
    return <input ref={input => this.input = input} />;
  }
}
```

**已删除：`React.createFactory`**&#x20;

`createFactory`[已于2020 年 2 月 (v16.13.0)](https://legacy.reactjs.org/blog/2020/02/26/react-v16.13.0.html#deprecating-createfactory)弃用。

在广泛支持 JSX 之前，使用`createFactory`很常见，但现在很少使用，可以用 JSX 代替。在 React 19 中，我们将删除它`createFactory`，您需要迁移到 JSX：

```
// Before
import { createFactory } from 'react';

const button = createFactory('button');
```

```
// After
const button = <button />;
```

**已删除：`ReactDOM.findDOMNode`**&#x20;

`ReactDOM.findDOMNode`[已于 2018 年 10 月 (v16.6.0)](https://legacy.reactjs.org/blog/2018/10/23/react-v-16-6.html#deprecations-in-strictmode)弃用。

我们删除`findDOMNode`它是因为它是一个遗留的逃生舱，执行速度慢，重构脆弱，只返回第一个子项，并且破坏了抽象级别（请参阅[此处](https://legacy.reactjs.org/docs/strict-mode.html#warning-about-deprecated-finddomnode-usage)了解更多信息）。您可以`ReactDOM.findDOMNode`用[DOM refs](https://react.dev/learn/manipulating-the-dom-with-refs)替换：

```
// Before
import {findDOMNode} from 'react-dom';

function AutoselectingInput() {
  useEffect(() => {
    const input = findDOMNode(this);
    input.select()
  }, []);

  return <input defaultValue="Hello" />;
}
```

```
// After
function AutoselectingInput() {
  const ref = useRef(null);
  useEffect(() => {
    ref.current.select();
  }, []);

  return <input ref={ref} defaultValue="Hello" />
}
```

#### 已弃用：`element.ref`  <a href="#deprecated-element-ref" id="deprecated-element-ref"></a>

React 19 支持[`ref`作为 prop](https://react.dev/blog/2024/04/25/react-19#ref-as-a-prop)，因此我们弃用了`element.ref`而改用`element.props.ref`。

访问`element.ref`会警告：

**删除：`propTypes`和`defaultProps`函数**

`PropTypes`[已于2017 年 4 月 (v15.5.0)](https://legacy.reactjs.org/blog/2017/04/07/react-v15.5.0.html#new-deprecation-warnings)弃用。

`propType`在 React 19 中，我们将从React 包中删除这些检查，并且使用这些检查将被默默忽略。如果您正在使用`propTypes`，我们建议迁移到 TypeScript 或其他类型检查解决方案。

我们还将从函数组件中删除ES6 默认参数。由于没有 ES6 替代方案，因此`defaultProps`将继续支持类组件。`defaultProps`

```
// Before
import PropTypes from 'prop-types';

function Heading({text}) {
  return <h1>{text}</h1>;
}
Heading.propTypes = {
  text: PropTypes.string,
};
Heading.defaultProps = {
  text: 'Hello, world!',
};
```

```
// After
interface Props {
  text?: string;
}
function Heading({text = 'Hello, world!'}: Props) {
  return <h1>{text}</h1>;
}
```
