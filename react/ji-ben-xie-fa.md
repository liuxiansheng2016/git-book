# 基本写法

## 基本写法

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
