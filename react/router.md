# Router

## 1. BrowserRouter

工作原理

**HTML5 History API**：BrowserRouter 使用 HTML5 的**History**  API (pushState, replaceState, popstate) 来管理路由。

**优点**

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
* **解决路径错误问题**：`HashRouter` 可以用于解决一些路径错误的问题。因为其 URL 中的哈希部分不会被发送到服务器，所以即使哈希部分的路径写错，也不会导致服务器返回 404 错误。而 `BrowserRouter` 中如果路径错误，服务器可能会返回 404 错误，需要在服务器端进行相应的配置来处理这种情况。

## 路由跳转

#### useNavigate

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

### 动态路由

#### useParams

* **动态路由定义**：在 `<Route>` 组件中，同样使用 `:id` 来定义动态参数。
* **获取动态参数**：在 `User` 组件中，使用 `useParams` 钩子来获取动态路由中的 `id` 值

<mark style="color:red;">**`useParams`**</mark><mark style="color:red;">**动态路由匹配**</mark><mark style="color:red;">：在需要根据不同的参数展示不同内容的场景中非常有用，例如用户详情页、商品详情页等，通过动态参数获取特定用户或商品的信息</mark>

```
import React from 'react';
import { BrowserRouter as Router, Routes, Route, useParams } from 'react-router-dom';

// 定义一个动态路由，:id 是动态参数
const UserPage = () => {
    const { id } = useParams();
    return <div>当前用户的 ID 是: {id}</div>;
};

const App = () => {
    return (
        <Router>
            <Routes>
                <Route path="/users/:id" element={<UserPage />} />
            </Routes>
        </Router>
    );
};

export default App;
```

#### **`useLocation`**

返回一个包含当前 URL 信息的对象，其常见属性如下：

* `pathname`：当前 URL 的路径部分。
* `search`：URL 中的查询字符串，以 `?` 开头。
* `hash`：URL 中的哈希值，以 `#` 开头。
* `state`：在导航时传递的额外状态数据。

<mark style="color:red;">**根据 URL 状态渲染不同内容**</mark><mark style="color:red;">：根据查询字符串或哈希值的不同，渲染不同的组件或执行不同的逻辑。例如，在搜索结果页根据查询参数显示不同的搜索结果。</mark>

#### &#x20;使用场景

* **根据 URL 路径渲染不同内容**：根据当前 URL 的路径名来决定渲染哪些组件。

```
import { useLocation } from 'react-router-dom';

function ContentBasedOnPath() {
    const location = useLocation();
    const { pathname } = location;

    if (pathname === '/home') {
        return <h1>Home Page</h1>;
    } else if (pathname === '/about') {
        return <h1>About Page</h1>;
    }

    return <h1>Unknown Page</h1>;
}

```

* **获取查询参数**：从 URL 的查询字符串中提取所需的参数。

```
import { useLocation } from 'react-router-dom';
import { useSearchParams } from 'react-router-dom';

function GetQueryParams() {
    const location = useLocation();
    const [searchParams] = useSearchParams();

    const paramValue = searchParams.get('param');

    return (
        <p>The value of 'param' in the URL is: {paramValue}</p>
    );
}
```

* **处理导航传递的状态数据**：获取在导航过程中传递的额外状态数据。

```
import { useLocation } from 'react-router-dom';

function ReceiveDataPage() {
    const location = useLocation();
    const { state } = location;

    if (state && state.message) {
        return <p>{state.message}</p>;
    }

    return <p>No data received.</p>;
}
```

#### `useParams` 和 `useLocation`

\
当访问 `/users/123` 时，`useParams` 会返回 `{ id: '123' }`，通过解构赋值可以轻松获取 `id` 的值。

* `useParams` 主要关注动态路由参数的获取，用于处理路由路径中的可变部分。
* `useLocation` 更侧重于获取整个 URL 的详细信息，包括路径名、查询字符串、哈希值和导航状态等。

### useSearchParams

`useSearchParams` 是 React Router v6 引入的一个 Hook，用来<mark style="color:red;">读取 URL 中的查询字符串参数</mark>（query string parameters）。这个 Hook 返回一个包含两个元素的数组：当前的 `searchParams` 对象和一个用于更新这些参数的函数。

**使用示例**

如果你想从 URL 中获取查询参数，比如 `/search?q=react&sort=latest`，你可以使用 `useSearchParams` 来访问这些参数：

```
import React from'react';
import { useSearchParams } from'react-router-dom';

function SearchPage() {
    // 获取 searchParams 对象和 setSearchParams 函数
    const [searchParams, setSearchParams] = useSearchParams();

    // 获取名为 'keyword' 的查询参数的值
    const keyword = searchParams.get('keyword');

    const handleChange = (e) => {
        const newKeyword = e.target.value;
        setSearchParams({ keyword: newKeyword });
    };

    return (
        <div>
            <input
                type="text"
                placeholder="Search..."
                value={keyword || ''}
                onChange={handleChange}
            />
            <p>当前搜索关键词: {keyword || '无'}</p>
        </div>
    );
}

export default SearchPage;
```

* **`useSearchParams`**：专<mark style="color:red;">门用于处理 URL 中的查询参数（即 URL 中</mark> <mark style="color:red;"></mark><mark style="color:red;">`?`</mark> <mark style="color:red;"></mark><mark style="color:red;">后面的部分）。它允许你读取、修改和监听查询参数的变化，提供了类似于</mark> <mark style="color:red;"></mark><mark style="color:red;">`URLSearchParams`</mark> <mark style="color:red;"></mark><mark style="color:red;">的 API 来操作查询参数。</mark>
* **`useLocation`**：用于获取当前 URL 的完整信息，包括路径名（`pathname`）、查询字符串（`search`）、哈希值（`hash`）以及在导航时传递的状态数据（`state`）。它返回一个包含这些信息的对象，让你可以根据 URL 的不同部分执行相应的逻辑。

## Link 和 NavLink

Link 还是 NavLink，它们的工作原理都是拦截浏览器默认的行为（即页面刷新），并通过调用 history.pushState() 方法来改变 URL，而不触发完整的页面加载。

Link 组件的主要功能是生成一个 HTML 标签

NavLink 是基于 Link 构建的一个更高级别的组件，它不仅提供了与 Link 相同的功能，而且还增加了自动检测是否处于活动状态的能力这意味着你可以轻松地为当前选中的链接应用特定的样式或类名声明式导

**替换历史条目**：replace 属性设置为 true 可以确保这次导航不会添加新的历史记录条目，而是替换掉当前的历史条目。这对于避免用户点击浏览器的“后退”按钮返回到受保护页面是有用的。\


### `Outlet`

&#x20;是 React Router v6 中引入的一个重要组件，用于实现嵌套路由的渲染。比如一个页面有侧边栏导航，点击不同的菜单项会在主内容区域显示不同的子页面。`Outlet` 就是为了处理这种嵌套路由而设计的，它相当于一个占位符，用于渲染当前路由匹配的子路由组件。

```
import React from 'react';
import { BrowserRouter as Router, Routes, Route, Outlet } from 'react-router-dom';

// 父路由组件
const Parent = () => {
    return (
        <div>
            <h1>这是父路由页面</h1>
            {/* 使用 Outlet 渲染子路由组件 */}
            <Outlet />
        </div>
    );
};

// 子路由组件
const Child1 = () => <p>这是子路由 1</p>;
const Child2 = () => <p>这是子路由 2</p>;

const App = () => {
    return (
        <Router>
            <Routes>
                <Route path="/parent" element={<Parent />}>
                    {/* 嵌套的子路由 */}
                    <Route path="child1" element={<Child1 />} />
                    <Route path="child2" element={<Child2 />} />
                </Route>
            </Routes>
        </Router>
    );
};

export default App;
```
