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
