### BrowserRouter 和 HashRouter

#### 1. BrowserRouter
工作原理

**HTML5 History API**：BrowserRouter 使用 HTML5 的历史记录 API (pushState, replaceState, popstate) 来管理路由。它利用浏览器的地址栏和历史记录来实现平滑的页面切换，而不需要重新加载整个页面。

**优点**

**更好的用户体验**：用户可以使用浏览器的前进和后退按钮来导航，而不会导致页面重新加载。

**更干净的 URL**：URL 不包含 # 符号，看起来更专业和整洁。

**搜索引擎优化 (SEO)**：由于 URL 更干净，搜索引擎更容易抓取和索引页面。

**缺点**

**服务器配置**：需要服务器配置来处理所有路由请求，将它们重定向到主入口文件（通常是 index.html）。否则，直接访问非根路径的 URL 会导致 404 错误。

**不支持旧浏览器**：HTML5 History API 在一些旧浏览器中可能不可用。

<BrowserRouter>：包裹应用以启用路由。

<Routes>：包含多个 <Route>，匹配 URL 并渲染组件。

<Route>：定义路径到组件的映射。
```
import React from 'react';
import { BrowserRouter as Router, Route, Routes, Link } from 'react-router-dom';

// 定义几个简单的页面组件
const Home = () => <h2>Home</h2>;
const About = () => <h2>About</h2>;
const Contact = () => <h2>Contact</h2>;

// 主应用组件
function App() {
  return (
    <Router>
      <div>
        {/* 导航栏 */}
        <nav>
          <ul>
            <li><Link to="/">Home</Link></li>
            <li><Link to="/about">About</Link></li>
            <li><Link to="/contact">Contact</Link></li>
          </ul>
        </nav>

        {/* 路由配置 */}
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/about" element={<About />} />
          <Route path="/contact" element={<Contact />} />
        </Routes>
      </div>
    </Router>
  );
}

export default App;
```

路由跳转
```
const HomeButton = () => {
  const navigate = useNavigate();

  return (
    <button onClick={() => navigate('/')}>
      Go to Home
    </button>
  );
};
```

#### Link 和 NavLink
Link 还是 NavLink，它们的工作原理都是拦截浏览器默认的行为（即页面刷新），并通过调用 history.pushState() 方法来改变 URL，而不触发完整的页面加载。

Link 组件的主要功能是生成一个 HTML <a> 标签

NavLink 是基于 Link 构建的一个更高级别的组件，它不仅提供了与 Link 相同的功能，而且还增加了自动检测是否处于活动状态的能力这意味着你可以轻松地为当前选中的链接应用特定的样式或类名

#### <Navigate>

声明式导航：这种方式是声明式的，意味着它直接作为 JSX 的一部分出现在组件返回的渲染树中。当条件满足时，React 将会渲染 <Navigate> 组件，这会导致 URL 和视图的变化。

支持状态传递：通过 state 属性，你可以传递额外的信息给目标页面，例如当前试图访问的位置（location），以便登录成功后能够返回原来的页面。
```
<Route path="/login" element={<Navigate to="/dashboard" state={{ from: '/login' }} />} />

import { useLocation } from 'react-router-dom';

function Dashboard() {
  const location = useLocation();
  const { state } = location;

  return (
    <div>
      {state?.from
        ? <p>You are coming from: {state.from}</p>
        : <p>Welcome to the dashboard!</p>}
    </div>
  );
}
```

替换历史条目：replace 属性设置为 true 可以确保这次导航不会添加新的历史记录条目，而是替换掉当前的历史条目。这对于避免用户点击浏览器的“后退”按钮返回到受保护页面是有用的。

#### 2. HashRouter

**工作原理**

**URL 哈希 (#)**：HashRouter 使用 URL 的哈希部分（即 # 后面的部分）来管理路由。浏览器的地址栏会显示类似于 http://example.com/#/about 的 URL。

HashRouter 依赖于浏览器的 hashchange 事件来检测路由的变化

**优点**

**无需服务器配置**：由于路由信息包含在 URL 的哈希部分，服务器只需要处理根路径的请求，不需要特殊配置。

**兼容性好**：支持所有现代浏览器和大部分旧浏览器。

**缺点**

**URL 不够美观**：URL 中包含 # 符号，看起来不够专业。

**搜索引擎优化 (SEO)**：搜索引擎可能不会很好地抓取和索引带有 # 的 URL，影响 SEO。

**书签和分享**：用户在书签或分享 URL 时，可能会遇到问题，因为哈希部分不会发送到服务器。
