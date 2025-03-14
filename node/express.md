---
description: 是一个 nodejs web 应用框架，用于构建 Web 应用和 API。
---

# Express

## **Express 和 koa**

### **选择 Express**

* 成熟稳定：如果你需要一个成熟稳定的框架，Express 是一个不错的选择。
* 丰富的生态系统：如果你需要大量的第三方中间件和工具，Express 的生态系统非常丰富。
* 易于上手：如果你是初学者或者项目时间紧张，Express 的学习曲线平缓，可以快速上手。

### **选择 Koa**

* 现代化：如果你希望使用更现代化的语法和特性，Koa 是一个更好的选择。
* 基于生成器/异步函数：Koa 最初使用生成器（generators），后来转向全面支持 async/await，这使得编写非阻塞 I/O 操作的代码更加直观。
* 中间件系统：采用了洋葱模型的中间件架构，每个中间件可以访问上下文对象（context），并且可以在请求和响应之间插入逻辑。
* HTTP2 支持：更好地支持 HTTP2 和其他现代网络协议。
* 错误处理：通过 try-catch 结构，可以更容易地捕获和处理异步操作中的错误。

Koa 的最大特色，，就是中间件（middleware）Koa 应用程序是一个包含一组中间件函数的对象，它是按照类似堆栈的方式组织和执行的。Koa 中使用 app.use()用来加载中间件，基本上 Koa 所有的功能都是通过中间件实现的。每个中间件默认接受两个参数，第一个参数是 Context 对象，第二个参数是 next 函数。只要调用 next 函数，就可以把执行权转交给下一个中间件。

```javascript
const Koa = require('koa')
const app = new Koa()

app.use(async (ctx, next) => {
  console.log('Middleware 1 - before')
  await next()
  console.log('Middleware 1 - after')
})

app.use(async (ctx, next) => {
  console.log('Middleware 2 - before')
  await next()
  console.log('Middleware 2 - after')
})

app.use(async (ctx, next) => {
  console.log('Middleware 3 - before')
  await next()
  console.log('Middleware 3 - after')
})

app.use(async (ctx) => {
  ctx.body = 'Hello, World!'
})

app.listen(3000, () => {
  console.log('server is running on port 3000')
})
```

上面的执行顺序就是：

```
Middleware 1 - before
Middleware 2 - before
Middleware 3 - before
Middleware 3 - after
Middleware 2 - after
Middleware 1 - after
```

通过这个顺序我们可以发现这是个栈结构以"先进后出"（first-in-last-out）的顺序执行

Koa 框架采用了洋葱模型（也称为中间件模型）来处理请求和响应。洋葱模型通过一系列中间件来处理请求，每个中间件都可以选择性地调用 await next() 来传递控制权给下一个中间件

如果没有调用 await next()，则后续的中间件将不会被执行

在 Koa 中，错误处理中间件通常放在最前面，以便捕获所有中间件抛出的错误。

### express-session

是一个用于 Express.js 应用程序的中间件，它允许你轻松地管理用户会话（session）。通过 express-session，你可以存储和访问用户的会话数据，这对于保持用户登录状态、购物车内容等非常有用

**1. express-session 的主要作用**

**保持用户状态**

* 登录状态：允许用户登录后，在多个请求之间保持认证状态，而无需每次请求都重新登录。
* 个性化体验：保存用户偏好设置或其他个性化信息，以便在不同页面或请求间提供一致的用户体验。

**管理会话数据**

* 临时数据存储：可以将一些需要跨多个请求使用的临时数据（如购物车内容）存储在会话中。
* 安全性增强：通过签名和加密机制保护会话数据的安全性，防止篡改。

**2. 工作原理**

express-session 的工作流程大致如下：

1. 生成会话 ID：当用户首次访问时，express-session 会为该用户生成一个唯一的会话 ID，并将其存储在客户端的 cookie 中。
2. 存储会话数据：服务器端会创建一个与该会话 ID 关联的数据对象，用来存储会话期间所需的数据。默认情况下，这些数据被保存在内存中，但在生产环境中通常会使用持久化存储（如 Redis 或 MongoDB）。
3. 后续请求验证：每当用户发出新的请求时，服务器会检查请求中的 cookie，从中提取出会话 ID 并查找对应的会话数据。如果找到匹配项，则恢复用户的会话状态；否则，创建新的会话。
4. 更新会话数据：根据应用逻辑，可以在会话数据中添加、修改或删除信息，并确保这些更改被正确保存。

```javascript
app.use(
  session({
    secret: 'your_very_secure_and_random_secret_key', // 用于签名 session ID 的密钥
    resave: false, // 不要重新保存未修改的会话
    saveUninitialized: false, // 不要保存未初始化的会话
    cookie: {
      secure: process.env.NODE_ENV === 'production', // 生产环境中启用 HTTPS
      httpOnly: true, // 防止 JavaScript 访问 cookie
      maxAge: 24 * 60 * 60 * 1000, // 设置 cookie 的最大存活时间为 24 小时
      sameSite: 'lax', // 增加对 CSRF 攻击的防护
    },
  }),
)
app.get('/', (req, res) => {
  if (req.session.views) {
    req.session.views++
    res.send(`You've visited this page ${req.session.views} times.`)
  } else {
    req.session.views = 1
    res.send('Welcome to the site!')
  }
})
```

但在生产环境中，你应该考虑使用更持久化的存储方案，例如 Redis、MongoDB 或其他数据库。这可以通过第三方库（如 connect-redis 或 connect-mongo）来实现。

```javascript
app.use(
  session({
    secret: 'your_secret_key',
    resave: false,
    saveUninitialized: false,
    store: MongoStore.create({
      mongoUrl: 'mongodb://localhost:27017/sessiondb',
      ttl: 14 * 24 * 60 * 60, // 会话数据的有效期为 14 天，以秒为单位
    }),
    cookie: { secure: true },
  }),
)
```

### express.static

是 Express.js 提供的一个内置中间件函数，用于提供静态文件（如 HTML 文件、图像、CSS 文件和 JavaScript 文件）。它使得你可以轻松地将某个目录下的文件作为静态资源提供给客户端。

在单页应用（SPA）中，前端路由由客户端 JavaScript 代码（例如 React Router）处理。服务器只需要提供一个入口点，即 index.html 文件。所有的前端路由都由客户端处理，而不是服务器。

```javascript
app.use(
  express.static(path.join(__dirname, '../build'), {
    maxAge: '1d', // 设置缓存时间为1天
    setHeaders: (res, path) => {
      if (path.endsWith('.html')) {
        res.setHeader('Cache-Control', 'no-cache')
      } else {
        res.setHeader('Cache-Control', 'public, max-age=86400') // 1天
      }
    },
  }),
)

app.get('*', (req, res) => {
  res.sendFile(path.join(__dirname, '../build/index.html'))
})
```

### **Express、Koa、NestJS 的主要区别及适用场景**

| 特性        | Express                    | Koa                        | NestJS                         |
| --------- | -------------------------- | -------------------------- | ------------------------------ |
| **架构风格**  | 轻量级，基于回调和中间件               | 轻量级，基于 `async/await` 和洋葱模型 | OOP、DI、模块化架构                   |
| **核心功能**  | 需要手动配置中间件（如 `body-parser`） | 内置 `koa-bodyparser`，更现代化   | 基于 `Express`/`Fastify`，支持模块化开发 |
| **中间件处理** | 基于回调                       | 洋葱模型                       | 依赖管道 (Pipes) 和 Guards          |
| **适用场景**  | 轻量级应用，灵活可扩展                | 适用于现代异步 Web 服务             | 适用于大型、可扩展的企业级应用                |

***

### &#x20;**使用 Express 创建 RESTful API**

安装 Express：

```sh
npm init -y
npm install express
```

创建 `server.js`：

```javascript
const express = require('express');
const app = express();
const PORT = 3000;

app.use(express.json()); // 解析 JSON 请求体

app.get('/api/hello', (req, res) => {
  res.json({ message: 'Hello, Express!' });
});

app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
```

***

### **在 Express 中实现中间件（Middleware）**

Express 的中间件主要分为以下几类：

1. **应用级中间件**（`app.use()`）
2. **路由级中间件**（`router.use()`）
3. **错误处理中间件**（`app.use(err, req, res, next)`）
4. **内置中间件**（如 `express.json()`、`express.static()`）
5. **第三方中间件**（如 `cors`、`morgan`）

Express 中间件是一个函数，它可以访问请求对象（`req`）、响应对象（`res`）以及应用程序请求 - 响应循环中的下一个中间件函数（通常用 `next` 表示）。中间件函数可以执行各种任务，如记录日志、解析请求体、进行身份验证、修改响应头等。

```javascript
function logger(req, res, next) {
  console.log(`${req.method} ${req.url}`);
  next(); // 继续执行下一个中间件
}

app.use(logger);
```

| **应用级中间件** | `app.use()` | 作用于所有请求 |
| ---------- | ----------- | ------- |

| **路由级中间件** | `router.use()` | 作用于特定路由 |
| ---------- | -------------- | ------- |

| **错误处理中间件** | `app.use(err, req, res, next)` | 处理错误 |
| ----------- | ------------------------------ | ---- |

| **内置中间件** | `express.json()`、`express.static()` | 解析请求、提供静态资源 |
| --------- | ----------------------------------- | ----------- |

| **第三方中间件** | `cors`、`morgan` | 处理跨域、日志 |
| ---------- | --------------- | ------- |

***

### **在 Express 中处理错误**

Express 提供了**错误处理中间件**：

```javascript
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({ message: '服务器内部错误' });
});
```

示例：

```javascript
app.get('/error', (req, res, next) => {
  next(new Error('这是一个错误'));
});
```

***

### **在 Express 中进行路由管理**

使用 `express.Router()` 进行模块化管理：

```javascript
const express = require('express');
const router = express.Router();

router.get('/users', (req, res) => {
  res.json([{ id: 1, name: 'Alice' }]);
});

router.post('/users', (req, res) => {
  res.status(201).json({ message: '用户已创建' });
});

module.exports = router;
```

然后在 `server.js` 中引入：

```javascript
const userRoutes = require('./routes/users');
app.use('/api', userRoutes);
```

**内置中间件**

**`express.static`**：用于提供静态文件服务，比如 HTML、CSS、JavaScript、图片等。可以将指定目录下的文件直接暴露给客户端访问。

```javascript
const express = require('express');
const app = express();
// 将 'public' 目录作为静态文件目录
app.use(express.static('public'));

```

**`express.json`**：用于解析 JSON 格式的请求体，将请求体中的 JSON 数据转换为 JavaScript 对象，并挂载到 `req.body` 上。

```javascript
app.use(express.json());
```

**`express.urlencoded`**：用于解析表单数据，将表单提交的数据转换为 JavaScript 对象，并挂载到 `req.body` 上。



```javascript
app.use(express.urlencoded({ extended: true }));
```

***

### **使用 JWT 在 Express 中进行身份验证**

安装 `jsonwebtoken`：

```sh
npm install jsonwebtoken
```

示例代码：

```javascript
const jwt = require('jsonwebtoken');
const SECRET_KEY = 'your_secret_key';

// 生成 Token
app.post('/login', (req, res) => {
  const user = { id: 1, username: 'admin' };
  const token = jwt.sign(user, SECRET_KEY, { expiresIn: '1h' });
  res.json({ token });
});

// 认证中间件
function authenticateToken(req, res, next) {
  const token = req.headers['authorization'];
  if (!token) return res.sendStatus(401);

  jwt.verify(token, SECRET_KEY, (err, user) => {
    if (err) return res.sendStatus(403);
    req.user = user;
    next();
  });
}

app.get('/protected', authenticateToken, (req, res) => {
  res.json({ message: '这是受保护的资源' });
});
```

***

### **在 Express 中进行参数校验（常用库）**

常见的参数校验库：

* `express-validator`
* `joi`

**使用 `express-validator`**

安装：

```sh
npm install express-validator
```

示例：

```javascript
const { body, validationResult } = require('express-validator');

app.post('/register',
  body('email').isEmail(),
  body('password').isLength({ min: 6 }),
  (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }
    res.json({ message: '注册成功' });
  }
);
```

***

### **在 Express 中进行 CORS 处理**

安装 `cors`：

```sh
npm install cors
```

启用：

```javascript
const cors = require('cors');
app.use(cors()); // 允许所有跨域请求

// 限制特定域名
app.use(cors({
  origin: 'https://example.com'
}));
```

***

#### **总结**

Express 适合**小型应用**，可灵活扩展。它的中间件机制、路由管理、错误处理、JWT 认证、参数校验和 CORS 处理让开发更高效。对于更复杂的项目，NestJS 提供了更完善的架构。



### **如何使用 Helmet.js 保护 Express/Koa 应用？**

`helmet.js` 可提供一系列安全增强，如 XSS 保护、隐藏服务器信息等。

#### **安装**

```bash
bash复制编辑npm install helmet
```

#### **在 Express 中使用**

```javascript
javascript复制编辑const helmet = require("helmet");
app.use(helmet());
```
