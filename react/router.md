# Router

## 1. BrowserRouter

工作原理

**HTML5 History API**：BrowserRouter 使用 HTML5 的**History**  API (pushState, replaceState, popstate) 来管理路由。

**优点**

**更好的用户体验**：它利用浏览器的地址栏和历史记录来实现平滑的页面切换，不需要重新加载整个页面。

**更干净的 URL**：URL 不包含 # 符号，看起来更专业和整洁。

**搜索引擎优化 (SEO)**：由于 URL 更干净，搜索引擎更容易抓取和索引页面。

**缺点**

**服务器配置**：需要服务器配置来处理所有路由请求，将它们重定向到主入口文件（通常是 index.html）。否则，直接访问非根路径的 URL 会导致 404 错误。

**不支持旧浏览器**：HTML5 History API 在一些旧浏览器中可能不可用。

：包裹应用以启用路由。

：包含多个 ，匹配 URL 并渲染组件。

：定义路径到组件的映射。

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



## **2. HashRouter工作原理URL 哈希 (#)**：

HashRouter 使用 URL 的哈希部分（即 # 后面的部分）来管理路由。浏览器的地址栏会显示类似于 http://example.com/#/about 的 URL。HashRouter 依赖于浏览器的 hashchange 事件来检测路由的变化

**优点无需服务器配置**：由于路由信息包含在 URL 的哈希部分，服务器只需要处理根路径的请求，不需要特殊配置。

**兼容性好**：支持所有现代浏览器和大部分旧浏览器。

**缺点URL 不够美观**：URL 中包含 # 符号，看起来不够专业。

**搜索引擎优化 (SEO)**：搜索引擎可能不会很好地抓取和索引带有 # 的 URL，影响 SEO。

**书签和分享**：用户在书签或分享 URL 时，可能会遇到问题，因为哈希部分不会发送到服务器。

### &#x20;区别

`BrowserRouter` 和 `HashRouter` 是 `react - router - dom` 库中用于实现路由功能的两个组件，它们有以下区别：

* **底层原理**：
  * `BrowserRouter` 使用的是 H5 的 `history API`，比如 `pushState`、`replaceState` 和 `popState` 等方法，通过操作浏览器的历史记录来实现路由跳转和状态管理。但它不兼容 IE9 及以下版本。
  * `HashRouter` 使用的是 URL 的哈希值（即 `window.location.hash`），URL 中 `#` 后面的内容变化时，并不会向服务器发起新的请求，而是通过监听 `hashchange` 事件来实现页面的局部更新。
* **path 表现形式**：
  * `BrowserRouter` 的路径中没有 `#`，例如 `https://example.com/demo/test`，这种形式更加简洁直观，看起来更像是正常的 URL。
  * `HashRouter` 的路径包含 `#`，例如 `https://example.com/#/demo/test` ，`#` 及其后面的内容就是哈希值。
* **刷新后对路由 state 参数的影响**：
  * `BrowserRouter` 没有任何影响，因为 `state` 保存在 `history` 对象中，即使刷新页面，`history` 对象中的数据依然存在，能保证路由状态的完整性。
  * `HashRouter` 刷新后会导致路由 `state` 参数的丢失，因为其 `state` 并没有被持久化存储在服务器或其他可靠的地方，刷新时页面重新加载，之前通过路由传递的 `state` 信息就会丢失 。
* **应用场景与部署**：
  * `BrowserRouter` 适用于部署在 Web 服务器上，支持动态路由和服务端渲染（SSR）的项目，它可以让 URL 更美观和符合常规习惯。不过，使用时需要正确配置 Web 服务器，以确保在刷新页面或直接访问深层路由时，服务器能够正确处理并返回对应的页面。
  * `HashRouter` <mark style="color:red;">适用于静态站点、无服务器环境（如本地文件系统直接打开 HTML 文件）以及一些对兼容性要求较高的场景，因为它不依赖于服务器端的配置</mark>。但 URL 中包含哈希部分，可能会被认为是旧式的 URL 表示方式。
* **解决路径错误问题**：`HashRouter` 可以用于解决一些路径错误的问题。因为其 URL 中的哈希部分不会被发送到服务器，所以即使哈希部分的路径写错，也不会导致服务器返回 404 错误。而 `BrowserRouter` 中如果路径错误，服务器可能会返回 404 错误，需要在服务器端进行相应的配置来处理这种情况。d

## 路由跳转

useNavigate

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

#### redirect

```
import { redirect } from "react-router";

export async function action({ request }) {
  const formData = await request.formData();
  const submission = Object.fromEntries(formData.entries());

  // 假设这里有一些异步操作来处理表单数据
  await submitForm(submission);

  // 成功后重定向
  return redirect("/success");
}
```

* **`useNavigate` vs. `redirect`**：
  * `useNavigate` 更适合于在React组件中直接调用，尤其是在用户交互触发导航的情况下。
  * `redirect` 主要应用于数据加载器或动作处理器中，当你需要根据数据加载或提交的结果来决定是否进行重定向时。
* **何时使用哪个**：
  * 如果你需要在组件内通过事件处理程序（如按钮点击）进行导航，请使用`useNavigate`。
  * 如果你在编写数据加载器或动作处理器，并希望在这些函数中根据某些条件执行重定向，则应使用`redirect`

## Link 和 NavLink

Link 还是 NavLink，它们的工作原理都是拦截浏览器默认的行为（即页面刷新），并通过调用 history.pushState() 方法来改变 URL，而不触发完整的页面加载。

Link 组件的主要功能是生成一个 HTML 标签

NavLink 是基于 Link 构建的一个更高级别的组件，它不仅提供了与 Link 相同的功能，而且还增加了自动检测是否处于活动状态的能力这意味着你可以轻松地为当前选中的链接应用特定的样式或类名声明式导

**替换历史条目**：replace 属性设置为 true 可以确保这次导航不会添加新的历史记录条目，而是替换掉当前的历史条目。这对于避免用户点击浏览器的“后退”按钮返回到受保护页面是有用的。
