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

<mark style="color:red;">req.session 当前用户的 Session 对象</mark>

### session 的存储方式

#### **默认存储方式：内存（MemoryStore）**

**默认情况下**，`express-session` 使用 `MemoryStore` 存储数据：

```javascript
javascript复制编辑app.use(session({
    secret: 'your-secret-key',
    resave: false,
    saveUninitialized: true
}));
```

🔹 **数据存储在服务器的内存（RAM）中**，适用于 **开发环境**，但 **生产环境不推荐**，因为：

* **重启服务器会丢失所有 session 数据**。
* **占用内存，用户多时会影响性能**。

***

#### **生产环境推荐的 Session 存储方式**

Redis 是高效的 **内存数据库**，适合存储 Session 数据。

```javascript
javascript复制编辑const session = require('express-session');
const RedisStore = require('connect-redis').default;
const { createClient } = require('redis');

const redisClient = createClient({ legacyMode: true });
redisClient.connect().catch(console.error);

app.use(session({
    store: new RedisStore({ client: redisClient }),
    secret: 'your-secret-key',
    resave: false,
    saveUninitialized: false
}));
```

📌 **优点：**

* **高性能，持久化存储，不会因服务器重启丢失**。
* **支持分布式存储**，适合多个服务器共享 Session。

***

#### **✅使用数据库（MySQL、MongoDB）存储**

**🔹MongoDB 存储 Session**

```javascript
javascript复制编辑const session = require('express-session');
const MongoStore = require('connect-mongo');

app.use(session({
    store: MongoStore.create({ mongoUrl: 'mongodb://localhost:27017/session_db' }),
    secret: 'your-secret-key',
    resave: false,
    saveUninitialized: false
}));
```

#### **✅ 使用文件存储**

可以将 Session 存储在本地文件中：

```javascript
javascript复制编辑const session = require('express-session');
const FileStore = require('session-file-store')(session);

app.use(session({
    store: new FileStore(),
    secret: 'your-secret-key',
    resave: false,
    saveUninitialized: false
}));
```

📌 **适用于：**

* **小型应用或临时存储 Session**，但不适合分布式系统。

#### **如何查看 Session 存储数据？**

不同的存储方式，数据会存储在不同位置：

| **存储方式**             | **数据存放位置**       | **查看方式**                           |
| -------------------- | ---------------- | ---------------------------------- |
| **MemoryStore** (默认) | 服务器内存            | 不能直接查看                             |
| **Redis**            | Redis 内存数据库      | `redis-cli` -> `keys *` 查看 session |
| **MySQL**            | `session_db` 数据表 | `SELECT * FROM sessions;`          |
| **MongoDB**          | `session_db` 集合  | `db.sessions.find().pretty();`     |
| **文件存储**             | 本地 `tmp` 目录      | 打开 `.json` 文件                      |

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

* 自定义中间件需要 `next();`，否则请求不会继续执行。
* 在路由处理函数 (`app.get()`、`app.post()`等) 里，不需要 `next();`，因为 `res.send()` 会终止请求。
* 如果一个中间件已经返回了响应 (`res.send()`)，就不应该再调用 `next();`，否则会报错

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

### next () 在 Express.js 中的作用是什么？

在 Express.js 中，<mark style="color:red;">`next()`</mark> <mark style="color:red;"></mark><mark style="color:red;">是一个函数，用于将控制权传递给当前中间件之后的下一个中间件或路由处理函数。在中间件函数中，如果需要继续执行后续的中间件或路由处理逻辑</mark>，就需要调用 `next()` 函数。如果不调用 `next()`，请求 - 响应循环将被中断，后续的中间件和路由处理函数将不会被执行。

```
const express = require('express');
const app = express();

const firstMiddleware = (req, res, next) => {
    console.log('First middleware');
    next(); // 传递控制权给下一个中间件或路由处理函数
};

const secondMiddleware = (req, res, next) => {
    console.log('Second middleware');
    next();
};

app.use(firstMiddleware);
app.use(secondMiddleware);

app.get('/', (req, res) => {
    res.send('Home page');
});

const port = 3000;
app.listen(port, () => {
    console.log(`Server is running on port ${port}`);
});

输出
Server is running on port 3000
First middleware
Second middleware
First middleware
Second middleware
```

你看到的输出是因为浏览器在访问根路径 `/` 时，通常会发出两个请求：<mark style="color:red;">一个是针对页面本身的请求，另一个是针对浏览器的图标（favicon.ico）的请求。</mark>

**修改代码后，没有重新启动服务器**（没有 `Ctrl + C` 重新运行 `node app.js`），那么终端输出的可能是 **上次运行的日志**



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
npm install helmet
```

#### **在 Express 中使用**

```javascript
const helmet = require("helmet");
app.use(helmet());
```

### 如何在 Express.js 中处理错误？

#### 1. 基本错误处理

Express 的错误处理中间件和普通中间件的区别是：**它有 4 个参数 (`err, req, res, next`)**。

```
const express = require('express');
const app = express();

// 普通中间件
app.use((req, res, next) => {
    console.log('Request received');
    next(); // 继续执行后续中间件或路由
});

// 一个有意制造错误的路由
app.get('/error', (req, res, next) => {
    const error = new Error('Something went wrong!');
    error.status = 500;
    next(error); // 传递错误到错误处理中间件
});

// 错误处理中间件
app.use((err, req, res, next) => {
    console.error(err.stack); // 记录错误日志
    res.status(err.status || 500).json({ message: err.message });
});

// 启动服务器
app.listen(3000, () => {
    console.log('Server running on port 3000');
});

```

#### 2. 处理 404 Not Found

如果用户访问了一个不存在的路由，可以使用 **404 处理器**：

**Express 处理请求时是按注册顺序执行的**，如果请求 URL 没有匹配到任何 `app.get()` 等路由，就会执行 `app.use()`。

```
const express = require('express');
const app = express();

// 处理 `/` 主页
app.get('/', (req, res) => {
    res.send('Home page');
});

// 处理所有未匹配的路由（404）
app.use((req, res, next) => {
    res.status(404).json({ message: 'Not Found' });
});

app.listen(3000, () => {
    console.log('Server is running on port 3000');
});

```

### `res`（`Response` 对象）

| `res.send()` | 发送字符串或 HTML |
| ------------ | ----------- |

| `res.json()` | 发送 JSON 响应 |
| ------------ | ---------- |

| `res.status(code)` | 设置 HTTP 状态码 |
| ------------------ | ----------- |

| `res.sendFile(path)` | 发送文件 |
| -------------------- | ---- |

| `res.redirect(url)` | 进行 URL 重定向 |
| ------------------- | ---------- |

| `res.set(header, value)` | 设置响应头 |
| ------------------------ | ----- |

| `res.end()` | 结束响应 |
| ----------- | ---- |

| `res.download(path)` | 触发文件下载 |
| -------------------- | ------ |

### req对象

| `req.method` | 获取请求方法（GET、POST） |
| ------------ | ---------------- |

| `req.path` | 获取请求路径（不含查询参数） |
| ---------- | -------------- |

| `req.query` | 获取查询参数（`?name=John`） |
| ----------- | -------------------- |

| `req.params` | 获取 URL 路径参数（`/user/:id`） |
| ------------ | ------------------------ |

| `req.headers` | 获取请求头信息 |
| ------------- | ------- |

| `req.body` | 获取 `POST` 请求体（需 `express.json()`） |
| ---------- | --------------------------------- |

| `req.ip` | 获取客户端 IP 地址 |
| -------- | ----------- |

| `req.originalUrl` | 获取完整 URL |
| ----------------- | -------- |

| `req.baseUrl` | 获取路由的基础路径 |
| ------------- | --------- |

### 如何优化 Express.js 应用的性能？

| **优化策略** | **作用** |
| -------- | ------ |

| **Gzip 压缩（compression）** | 减少响应体大小，提高加载速度 |
| ------------------------ | -------------- |

| **使用 `cluster` 多进程** | 提高服务器并发能力 |
| -------------------- | --------- |

| **静态资源缓存** | 通过 `cache-control` 减少 HTTP 请求 |
| ---------- | ----------------------------- |

| **Redis 缓存 API 响应** | 降低数据库负载，加速请求 |
| ------------------- | ------------ |

| **安全性（helmet）** | 防止 XSS、CSRF 攻击 |
| --------------- | -------------- |

| **使用 `morgan` 记录日志** | 监控慢查询、异常请求 |
| -------------------- | ---------- |

| **数据库查询优化** | 使用索引、分页，减少数据查询时间 |
| ----------- | ---------------- |

| **代码优化（async/await）** | 避免同步阻塞，提高服务器响应能力 |
| --------------------- | ---------------- |



**1. 路由优化**

* **路由模块化**：将不同功能的路由拆分成独立的模块，提高代码的可维护性和可扩展性。例如使用 `express.Router()` 创建子路由模块。
* **路由顺序**：将常用的路由放在前面，减少不必要的匹配。

**2. 中间件优化**

* **减少中间件使用**：避免使用不必要的中间件，每个中间件都会增加请求处理的时间。
* **异步中间件**：使用异步中间件时，确保正确处理错误和调用 `next()`。

**3. 数据库优化**

* **连接池**：使用数据库连接池，减少数据库连接的开销，如在使用 MySQL 时使用 `mysql2` 的连接池。
* **查询优化**：优化数据库查询语句，避免全表扫描，使用索引提高查询效率。

**4. 静态文件优化**

* **使用缓存**：设置静态文件的缓存头，减少客户端对静态文件的重复请求。

```javascript
app.use(express.static('public', { maxAge: 3600000 }));
```

**5. 性能监控和分析**

* **使用工具**：如 `New Relic`、`AppDynamics` 等工具监控应用性能，找出性能瓶颈。

### 如何在 Express.js 中实现缓存？

#### **1. 内存缓存** 可以使用 `node - cache` 库实现简单的内存缓存。

```javascript
const express = require('express');
const NodeCache = require('node - cache');
const app = express();
const myCache = new NodeCache();

app.get('/data', (req, res) => {
    const cachedData = myCache.get('data');
    if (cachedData) {
        return res.json(cachedData);
    }
    // 模拟从数据库或其他数据源获取数据
    const data = { message: 'This is some data' };
    myCache.set('data', data, 60); // 缓存 60 秒
    res.json(data);
});

const port = 3000;
app.listen(port, () => {
    console.log(`Server running on port ${port}`);
});
```

**2. 分布式缓存**\
可以使用 Redis 作为分布式缓存。

```javascript
const express = require('express');
const Redis = require('ioredis');
const app = express();
const redis = new Redis();

app.get('/data', async (req, res) => {
    const cachedData = await redis.get('data');
    if (cachedData) {
        return res.json(JSON.parse(cachedData));
    }
    const data = { message: 'This is some data' };
    await redis.set('data', JSON.stringify(data), 'EX', 60); // 缓存 60 秒
    res.json(data);
});

const port = 3000;
app.listen(port, () => {
    console.log(`Server running on port ${port}`);
});
```

### **Express.js 如何与 Nginx 进行负载均衡？**

#### &#x20;**修改 Nginx 配置**

编辑 `/etc/nginx/sites-available/default`：

```nginx
upstream node_app {
    server 127.0.0.1:3001;
    server 127.0.0.1:3002;
    server 127.0.0.1:3003;
}

server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://node_app;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

#### &#x20;**启动多个 Express 实例**

```sh
pm2 start server.js --name "app1" -- --port 3001
pm2 start server.js --name "app2" -- --port 3002
pm2 start server.js --name "app3" -- --port 3003
```

重启 Nginx：

```sh
sudo systemctl restart nginx
```

✅ ：Nginx 作为 **负载均衡器**，将请求分配给多个 Express 实例，提高并发能力。
