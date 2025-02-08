# 基本写法

## 基本写法

### 何时使用类组件和函数组件?

### 使用函数组件的情况
- 简单的UI展示：函数组件更简洁，适合只负责展示数据的组件。
- 状态和副作用管理：引入Hooks后，函数组件也能管理状态和副作用。
- 性能优化：函数组件无实例化特性，有轻微性能优势。可用React.memo避免不必要的重新渲染。
- 逻辑复用：自定义Hooks使函数组件共享逻辑更容易。
- 现代项目趋势：新的React项目倾向于使用函数组件和Hooks。

### 使用类组件的情况
- 复杂的生命周期方法：需要精细控制生命周期事件或实现错误边界时使用类组件。
- 老旧项目迁移成本：维护大量类组件的项目时，继续使用类组件可减少重构工作量。
- 状态管理特别复杂时：极端复杂状态管理场景下，类组件结构可能更直观。
- 依赖特定的类组件功能：某些第三方库或工具专为类组件设计，需考虑使用类组件。。


### 函数组件

<pre class="language-javascript"><code class="lang-javascript">Import react from “react”

function MyComponent({message}) {
 return (
<strong>    &#x3C;div>
</strong>        &#x3C;p>{message}&#x3C;/p>
    &#x3C;/div>
    )
}
</code></pre>

### 类组件

```javascript
import React from "react";
// import React, { Component } from 'react';

class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      message: props.initialMessage,
    };
  }

  componentDidMount() {
    // 组件挂载后执行的逻辑
  }

  render() {
    return (
      <div>
        <p>{this.state.message}</p>
      </div>
    );
  }
}
export default MyComponent;
```

### 高阶函数

```javascript
// 基础写法
// 最简单的 HOC 只是对组件进行包装，并返回新的组件
const withClass = (WrappedComponent, className) => {
  return (props) => (
    <div className={className}>
      <WrappedComponent {...props} />
    </div>
  );
};

// 使用
const Button = (props) => <button {...props}>Click Me</button>;
const ButtonWithClass = withClass(Button, 'btn');

// 带状态的 HOC
const withCounter = (WrappedComponent) => {
  const WithCounter = (props) => {
    const [count, setCount] = useState(0);
    return <WrappedComponent count={count} increment={() => setCount(count + 1)} {...props} />;
  };
  return WithCounter;
};

// 使用
const CounterButton = ({ count, increment }) => (
  <button onClick={increment}>
    Clicked {count} times
  </button>
);
const EnhancedCounterButton = withCounter(CounterButton);

// 带认证的 HOC
function withAuth(MyComponent, checkAuth) {
  return class extends React.Component {
    state = {
      isAuthenticated: false,
    };

    async componentDidMount() {
      const isAuthenticated = await checkAuth();
      this.setState({ isAuthenticated });
    }

    render() {
      const { isAuthenticated } = this.state;
      if (!isAuthenticated) return <div>unAuthenticated</div>;
      if (isAuthenticated) return <MyComponent {...this.props} />;
    }
  };
}
```

### 条件渲染

#### **三目运算符(推荐)**

```javascript
import React from 'react';

function MyFunctionComponent({ isLoggedIn }) {
  return (
    <div>
      {isLoggedIn ? (
        <p>Welcome, user!</p>
      ) : (
        <p>Please log in.</p>
      )}
    </div>
  );
}

export default MyFunctionComponent;

```

**逻辑运算符**

```javascript
import React from 'react';

function MyFunctionComponent({ isLoggedIn }) {
  return (
    <div>
      {isLoggedIn && <p>Welcome, user!</p>}
      {!isLoggedIn && <p>Please log in.</p>}
    </div>
  );
}

export default MyFunctionComponent;

```

#### **If 语句**

```javascript
// MyFunctionComponent.jsx
import React from 'react';

function MyFunctionComponent({ isLoggedIn }) {
    if (isLoggedIn) {
        return <p>Welcome, user!</p>;
    } else {
        return <p>Please log in.</p>;
    }
}

export default MyFunctionComponent;
```
