# 问题

## **读取标准输入**

```javascript
读取一行输入
const readline = require('readline');

const rl = readline.createInterface({
    input: process.stdin,
    output: process.stdout
});

rl.question('What is your name? ', (answer) => {
    console.log(`Hello, ${answer}!`);
    rl.close();
});

持续读取多行输入
process.stdin.setEncoding('utf8');

process.stdin.on('readable', () => {
  const chunk = process.stdin.read();
  if (chunk !== null) {
    process.stdout.write(`Data: ${chunk}`);
  }
});

process.stdin.on('end', () => {
  console.log('No more data.');
});

```

## **模块**

模块（Module）是封装代码的基本单元，每个文件都可以被视为一个独立的模块。

模块之间通过 require 和 module.exports 进行导入和导出。

* 核心模块：Node.js 自带了一些核心模块，例如 fs、http、path 等。这些模块可以直接使用，无需安装。
* 用户模块： 用户模块是你自己编写的模块或从 npm 安装的第三方模块。

Node.js 会按照一定的顺序查找模块路径：

核心模块：先查找核心模块。

文件模块：查找当前目录下的文件模块。

节点模块：查找 node\_modules 目录下的模块。

Node.js 默认使用 CommonJS

### 使用 ES 模块

1 在 package.json 文件中添加 "type": "module" 字段，这会告诉 Node.js 项目中的所有文件默认使用 ES 模块语法。

2 可以将文件扩展名改为 .mjs，这样 Node.js 会将这些文件视为 ES 模块。

在 ES 模块中，可以使用动态 import() 函数来导入 CommonJS 模块

从 Node.js 内置的 module 模块中导入 createRequire 函数。createRequire 是一个工厂函数，用于创建一个 require 函数

```javascript
// 引入 createRequire 方法
import { createRequire } from 'module';

// 使用 createRequire 创建 require 函数，适配 CommonJS 加载
const require = createRequire(import.meta.url);

// 使用 require 导入 CommonJS 模块
const commonjsModule = require('./commonjs-module');

// 使用 CommonJS 模块
console.log(commonjsModule.message); // 输出: This is a CommonJS module

```

在 CommonJS 模块中，可以使用 import() 函数来动态导入 ES 模块

```javascript
async function loadESModule() {
  const esModule = await import('./es-module.js');
  console.log(esModule.greeting); // 输出: Hello, World!
}

loadESModule();

```

如果你有一个现有的 CommonJS 项目，想要转换为 ES 模块，可以使用一些工具来帮助你：

* esm: 一个允许你在 Node.js 中使用 ES 模块的库。
* @babel/node: 使用 Babel 转换 ES 模块，使其在 Node.js 中运行。

## **如何检查是否有需要处理的事件**

一般过程：

检查各个阶段的队列：在每个 tick 开始时，事件循环会遍历各个阶段的队列，包括但不限于 timers, poll, check 等阶段。每个阶段都可能有一个或者多个队列，用来存放对应的事件。

执行就绪的事件：一旦发现某个阶段有可处理的事件，事件循环就会开始执行这些事件。例如，在 timers 阶段，如果有任何定时器到期，则会执行相关的回调函数。

空闲检测：在某些阶段，比如 poll 阶段，如果没有事件需要处理，事件循环可能会进入休眠状态，直到有新事件到来为止。这种机制允许 Node.js 在没有活动的情况下节省 CPU 资源。

继续下一轮循环：一旦所有的事件都被处理完毕，并且没有更多的事件需要处理，事件循环将结束这一轮的迭代，并开始新的一轮。

具体来说，Node.js 内部使用 libuv 库来实现事件循环。libuv 是一个多平台的库，负责处理底层的 I/O 操作和事件调度。libuv 维护了一个事件循环，它能够监听各种类型的事件，如文件描述符的变化、定时器到期等，并在适当的时机调用注册好的回调函数

## 洋葱模型

Koa 框架采用了洋葱模型（也称为中间件模型）来处理请求和响应。洋葱模型通过一系列中间件来处理请求，每个中间件都可以选择性地调用 await next() 来传递控制权给下一个中间件

如果没有调用 await next()，则后续的中间件将不会被执行

在 Koa 中，错误处理中间件通常放在最前面，以便捕获所有中间件抛出的错误。

```
// 错误处理中间件
app.use(async (ctx, next) => {
  try {
    await next();
  } catch (err) {
    ctx.status = err.status || 500;
    ctx.body = { error: err.message };
    ctx.app.emit('error', err, ctx);
  }
});

```

中间件的执行顺序

```javascript
const Koa = require('koa');
const app = new Koa();

app.use(async (ctx, next) => {
    console.log('Middleware 1 - before');
    await next();
    console.log('Middleware 1 - after');
});

app.use(async (ctx, next) => {
    console.log('Middleware 2 - before');
    await next();
    console.log('Middleware 2 - after');
});

app.use(async (ctx, next) => {
    console.log('Middleware 3 - before');
    await next();
    console.log('Middleware 3 - after');
});

app.use(async (ctx) => {
    ctx.body = 'Hello, World!';
});

app.listen(3000, () => {
    console.log('Server is running on port 3000');
});

//result
Middleware 1 - before
Middleware 2 - before
Middleware 3 - before
Middleware 3 - after
Middleware 2 - after
Middleware 1 - after
```

Koa 的中间件采用洋葱模型，每个中间件可以决定是否调用 await next() 来传递控制权给下一个中间件。当所有中间件都执行完毕后，控制权会按相反的顺序返回到之前的中间件。



## **Bodyparse**

body-parser 是一个非常流行的中间件，用于解析 HTTP 请求体。 qs 是一个强大的库，用于解析和字符串化 URL 查询字符串

body-parser 支持多种请求体格式：

* JSON：bodyParser.json() 用于解析 JSON 格式的请求体。
* Form Data：bodyParser.urlencoded({ extended: true }) 用于解析 application/x-www-form-urlencoded 格式的请求体。
* Raw Text：bodyParser.text() 用于解析纯文本格式的请求体。

配置

```
app.use(bodyParser.json({
    limit: '1mb', // 设置 JSON 请求体的最大大小
    strict: true // 严格模式，只接受 application/json 类型的请求
}));

app.use(bodyParser.urlencoded({
    extended: true, // 使用 qs 库进行解析
    limit: '1mb' // 设置 URL 编码请求体的最大大小
}));

```

解析请求体时可能出现的错误

```javascript
// 错误处理程序
app.use((err, req, res, next) => {
    if (err instanceof SyntaxError && err.status === 400 && 'bodyParser' in err) {
        // JSON 解析错误
        return res.status(400).json({ error: 'Bad Request', message: 'Malformed JSON' });
    } else if (err.type === 'entity.too.large') {
        // 请求体太大
        return res.status(413).json({ error: 'Request Entity Too Large', message: 'Request body exceeds the size limit' });
    } else {
        // 其他错误
        console.error(err.stack);
        return res.status(500).json({ error: 'Internal Server Error', message: err.message });
    }
});

```

## &#x20;**Node 调试**

1\. 使用内置的调试器

Node.js 内置了一个基于 V8 引擎的调试器，可以通过命令行启动。

```
node --inspect your-script.js
```

这会启动一个调试服务器，通常监听在 9229 端口。你可以使用 Chrome 浏览器或其他支持 V8 调试协议的工具连接到这个调试服务器。

使用 Chrome 浏览器调试

1. 打开 Chrome 浏览器。
2. 输入 chrome://inspect。
3. 点击 "Open dedicated DevTools for Node"。
4. 在 DevTools 中，选择你的脚本文件，设置断点，然后启动调试。

2 使用 Visual Studio Code

配置调试环境

1. 打开你的项目文件夹。
2. 在左侧活动栏中点击调试图标（或按 Ctrl+Shift+D）。
3. 点击 "创建 launch.json 文件"。
4. 选择 "Node.js" 环境。
5. VS Code 会自动生成一个 launch.json 文件，通常位于 .vscode 文件夹中。

启动调试

1. 在 VS Code 中打开你要调试的文件。
2. 在代码中设置断点。
3. 点击调试面板中的绿色播放按钮，或按 F5 启动调试。

## **pm2**

PM2 是一个非常强大的工具，可以帮助你在生产环境中管理和保持 Node.js 应用的持续运行。通过上述步骤，你可以轻松地使用 PM2 来启动、停止、重启、查看日志、配置多个应用，并实现自动重启和负载均衡。

启动应用

使用 PM2 启动你的 Node.js 应用非常简单。假设你的应用入口文件是 app.js，你可以使用以下命令启动应用

pm2 start app.js

日志管理

PM2 可以管理和查看应用的日志。你可以使用以下命令查看实时日志：

pm2 logs

监控和管理

PM2 提供了一个 Web 界面来监控和管理应用。你可以使用以下命令启动 PM2 的 Web 界面：

pm2 web

配置文件

PM2 支持使用配置文件来管理多个应用。你可以创建一个 ecosystem.config.js 文件来配置多个应用。

```javascript
module.exports = {
  apps : [{
    name: 'app1',
    script: './app1.js',
    instances: 1,
    autorestart: true,
    watch: false,
    max_memory_restart: '1G',
    env: {
      NODE_ENV: 'development'
    },
    env_production: {
      NODE_ENV: 'production'
    }
  },
  {
    name: 'app2',
    script: './app2.js',
    instances: 1,
    autorestart: true,
    watch: false,
    max_memory_restart: '1G',
    env: {
      NODE_ENV: 'development'
    },
    env_production: {
      NODE_ENV: 'production'
    }
  }]
};

```

使用配置文件启动应用

pm2 start ecosystem.config.js

负载均衡

PM2 支持多实例运行，可以用于负载均衡。你可以在启动命令中指定实例数：

pm2 start app.js -i 4

这将启动 4 个实例，并使用负载均衡来分配请求。



## **node 性能和监控**

1. 内置的性能监控工具

使用 process 对象\
Node.js 提供了 process 对象，可以用来获取当前进程的各种信息，如内存使用、CPU 使用率等。

<pre class="language-javascript"><code class="lang-javascript"><strong>const process = require('process');
</strong>console.log(process.memoryUsage());
setInterval(() => {
    const cpus = require('os').cpus();
    const load = cpus.reduce((total, cpu, index) => {
        return total + (cpu.load / 100);
    }, 0);
    console.log(`CPU Load: ${(load.toFixed(2))}%`);
}, 1000);

</code></pre>

使用 perf\_hooks 模块

Node.js 10.0.0 及以上版本提供了 perf\_hooks 模块，可以用来测量性能指标。

```javascript
const { performance } = require('perf_hooks');

const start = performance.now();

// 你的代码
function doSomething() {
    // 模拟耗时操作
    let sum = 0;
    for (let i = 0; i < 100000000; i++) {
        sum += i;
    }
}

doSomething();

const end = performance.now();
console.log(`Execution time: ${end - start}ms`);
```

2.  **第三方性能监控工具**

    Datadog 是另一个强大的性能监控工具，支持 Node.js 应用。它可以监控应用的性能、日志、指标和事件。
3. PM2 不仅是一个进程管理工具，还提供了性能监控功能。你可以使用 PM2 的 pm2 monit 命令来监控应用的资源使用情况
4. Node.js 内置了 V8 引擎的性能分析工具，可以通过 Chrome DevTools 进行性能分析。

```
NODE_ENV=production node --prof app.js
```

* **--prof**：这是一个 Node.js 的命令行选项，用于启用 V8 引擎的 CPU 性能分析。它会在应用程序运行结束后生成一个 .cpuprofile 文件，该文件可以用于分析应用程序的性能。

使用 node --prof-process isolate-0xnnnnnnnnnnnn-v8.log > processed.txt



## **中间件**

在 Node.js 中，中间件（Middleware）是处理请求和响应周期的一部分函数。中间件函数可以执行各种任务，如解析请求体、设置响应头、处理错误等。中间件函数按顺序执行，可以决定是否将请求传递给下一个中间件或路由处理函数。

中间件的类型

1. 应用级中间件：由 app.use() 或 app.METHOD() 定义，应用于整个应用。
2. 路由级中间件：由 router.use() 或 router.METHOD() 定义，应用于特定的路由。
3. 错误处理中间件：专门用于处理错误的中间件，通常有四个参数（err, req, res, next）。
4. 内置中间件：Express 自带的一些中间件，如 express.static。
5. 第三方中间件：通过 npm 安装的中间件，如 body-parser、cors 等。



## **流（Streams）**

流（Streams）是一种处理大量数据的有效方式。流可以用来读取和写入文件、处理网络请求和响应等。流的主要优点是它们可以处理超出内存限制的数据，而不需要一次性将所有数据加载到内存中。

Node.js 中主要有四种类型的流：

1. 可读流（Readable Streams）：用于读取数据。
2. 可写流（Writable Streams）：用于写入数据。
3. 双工流（Duplex Streams）：既可以读取也可以写入数据。
4. 转换流（Transform Streams）：一种特殊的双工流，可以在数据传输过程中对数据进行转换。

**基本概念**

* 事件：流使用事件来通知数据的可用性和完成情况。
* 缓冲区：流在内部使用缓冲区来暂存数据，以确保数据的平滑流动。
* 高水位线（High Water Mark）：用于控制缓冲区的最大大小，防止内存溢出。

**常见的流操作**

1. 创建流
2. 监听事件
3. 读取和写入数据
4. 管道（Piping）

### &#x20;可读可写流

```javascript
const fs = require('fs');

// 创建可读流
const readableStream = fs.createReadStream('input.txt', { encoding: 'utf8' });

// 创建可写流
const writableStream = fs.createWriteStream('output.txt');

// 监听数据事件
readableStream.on('data', (chunk) => {
    console.log(`Received chunk: ${chunk}`);
    writableStream.write(chunk);
});

// 监听结束事件
readableStream.on('end', () => {
    console.log('No more data to read.');
    writableStream.end();
});

// 监听错误事件
readableStream.on('error', (err) => {
    console.error(`Error reading file: ${err.message}`);
});

writableStream.on('error', (err) => {
    console.error(`Error writing file: ${err.message}`);
});

```

**管道（Piping）**

管道是一种将可读流的数据直接传递到可写流的方法，非常方便。

```
const fs = require('fs');

// 创建可读流
const readableStream = fs.createReadStream('input.txt', { encoding: 'utf8' });

// 创建可写流
const writableStream = fs.createWriteStream('output.txt');

// 使用管道将可读流的数据传输到可写流
readableStream.pipe(writableStream);

// 监听结束事件
writableStream.on('finish', () => {
    console.log('Data has been written to output.txt');
});

```

### &#x20;**转换流**

转换流可以在数据传输过程中对数据进行转换。

```javascript
const fs = require('fs');
const { Transform } = require('stream');

// 创建一个转换流
class UpperCaseTransform extends Transform {
  _transform(chunk, encoding, callback) {
    const upperCaseChunk = chunk.toString().toUpperCase();
    callback(null, upperCaseChunk);
  }
}

const transformStream = new UpperCaseTransform();

// 创建可读流
const readableStream = fs.createReadStream('input.txt', { encoding: 'utf8' });

// 创建可写流
const writableStream = fs.createWriteStream('output.txt');

// 使用管道将可读流的数据传输到转换流，再传输到可写流
readableStream.pipe(transformStream).pipe(writableStream);

```

## **事件**

流使用以下事件来通知数据的可用性和完成情况：

* **data**：当有新的数据可用时触发。
* **end**：当没有更多数据可读时触发。
* **error**：当发生错误时触发。
* **close**：当流关闭时触发。
* **finish**：当所有数据已写入底层系统时触发。

**方法**

流提供了一些常用的方法来控制数据的流动：

* **read(\[size])**：从可读流中读取数据。
* **write(chunk\[, encoding]\[, callback])**：向可写流中写入数据。
* **pipe(destination\[, options])**：将可读流的数据传递到可写流。
* **unpipe(\[destination])**：取消将可读流的数据传递到可写流。
* **pause()**：暂停从可读流中读取数据。
* **resume()**：恢复从可读流中读取数据。
* **destroy()**：销毁流并释放资源。

## **反压是什么？**

定义：反压描述了一种现象，即当接收端处理数据的速度跟不上发送端产生数据的速度时，在两者之间形成了数据积压。这种积压就像管道中的堵塞一样，如果不加以管理，会导致内存占用增加、性能下降甚至应用程序崩溃。

**1. 如何实现反压？**

Node.js 的流模块内置了反压机制，主要依赖于 .write() 方法的返回值来决定是否继续读取更多数据。具体来说：

* 当调用 Writable 流的 .write(chunk) 方法时，如果内部缓冲区未满，则该方法返回 true，表示可以继续发送数据；否则返回 false，指示上游暂停发送直到缓冲区有足够的空间为止。
* 如果 .write() 返回了 false，那么接下来应当监听 drain 事件，该事件会在缓冲区腾出足够空间后触发，告知上游恢复数据传输。

使用 .pipe() 方法

.pipe() 是一种简化版的流连接方式，它自动处理了反压问题。当你将一个可读流与一个可写流相连时，.pipe() 会设置必要的事件监听器以确保正确的反压行为。

对于更复杂的流链，推荐使用 pipeline API（自 Node.js 10.x 起可用），它可以更好地管理和清理错误，并提供完成回调功能。

```javascript
const { pipeline } = require('node:stream');
const fs = require('node:fs');
const zlib = require('node:zlib');

pipeline(
  fs.createReadStream('input.txt'),
  zlib.createGzip(),
  fs.createWriteStream('input.txt.gz'),
  (err) => {
    if (err) {
      console.error('Pipeline failed', err);
    } else {
      console.log('Pipeline succeeded');
    }
  }
);

```

## &#x20;**process**

process 是一个全局对象，提供了与当前 Node.js 进程相关的信息和控制方法。process 对象是一个 EventEmitter，可以监听和触发各种事件

```javascript
const process = require('process');

// 获取命令行参数
console.log('Command line arguments:', process.argv);

// 获取环境变量
console.log('Environment variable NODE_ENV:', process.env.NODE_ENV);

// 获取当前工作目录
console.log('Current working directory:', process.cwd());

// 获取操作系统平台
console.log('Operating system platform:', process.platform);

// 获取 Node.js 版本
console.log('Node.js version:', process.version);

// 获取当前进程 PID
console.log('Process PID:', process.pid);

// 处理退出信号
process.on('SIGINT', () => {
    console.log('Received SIGINT. Shutting down gracefully...');
    process.exit(0);
});

// 处理未捕获的异常
process.on('uncaughtException', (err) => {
    console.error('Caught exception:', err.stack);
    process.exit(1);
});

// 读取输入输出
process.stdin.on('data', (data) => {
    console.log('Received:', data.toString().trim());
});

process.stdout.write('Enter something: ');

// 模拟一个错误
setTimeout(() => {
    throw new Error('Simulated error');
}, 1000);

```

### **process.nextTick()**

定义：process.nextTick() 允许你在当前操作完成后立即执行一个回调函数。它将回调函数放入一个特殊的队列中，这个队列会在当前操作完成后、事件循环进入下一个阶段之前被处理。

用途：主要用于在当前操作完成后立即执行一些任务，而不需要等待事件循环的下一个周期。

使用场景：

清理资源：在某个操作完成后立即清理资源

在某个异步操作完成后立即处理其结果



## **常见的执行模式**



## **setImmediate**

* 定义：setImmediate() 允许你在当前事件循环迭代的末尾执行一个回调函数。这意味着回调函数会在所有其他 I/O 操作、定时器和 process.nextTick() 回调完成后执行。
* 用途：主要用于将任务推迟到当前事件循环迭代的末尾，以确保这些任务不会阻塞其他高优先级的任务。

process.nextTick：将回调函数放入一个特殊的队列中，在当前操作完成后立即执行，优先级高于 setTimeout 和 setImmediate。

setImmediate：将回调函数放入待处理回调队列中，在当前事件循环迭代的末尾执行，优先级低于 process.nextTick 但高于 setTimeout。

## &#x20;**Nodemon**

是一种工具，可在检测到目录中的文件更改时通过自动重新启动节点应用程序来帮助开发基于 node.js 的应用程序。

**18 典型的 npm 包结构**

* `bin/` - 可执行文件（如果有的话）
* `lib/` - 编译后的代码和库文件
* `src/` - 源代码
* `test/` - 测试文件
* `.editorconfig` - 编辑器配置文件
* `.eslintrc.js` - ESLint 配置文件（可选）
* `.gitignore` - Git 忽略文件
* `.npmignore` - npm 忽略文件（可选）
* `LICENSE` - 许可证文件
* `README.md` - 项目说明文档
* `package.json` - 包元数据和依赖关系
* `index.js` - 主入口文件（模块暴露什么东西）

**package.json**

* _name：包名，需要在 NPM 上是唯一的，小写字母和数字组成可包含\_ - .但不能有空格_
* description：包简介。通常会显示在一些列表中
* version：版本号。一个语义化的版本号（[http://semver.org/](http://semver.org/) ），通常为 x.y.z。该版本号十分重要，常常用于一些版本控制的场合
* keywords：关键字数组。用于 NPM 中的分类搜索
* maintainers：包维护者的数组。数组元素是一个包含 name、email、web 三个属性的 JSON 对象
* contributors：包贡献者的数组。第一个就是包的作者本人。在开源社区，如果提交的 patch 被 merge 进 master 分支的话，就应当加上这个贡献 patch 的人。格式包含 name 和 email
* bugs：一个可以提交 bug 的 URL 地址。可以是邮件地址（mailto:mailxx@domain），也可以是网页地址
* licenses：包所使用的许可证
* repositories：托管源代码的地址数组
* dependencies：当前包需要的依赖。这个属性十分重要，NPM 会通过这个属性，帮你自动加载依赖的包

除了前面提到的几个必选字段外，还有一些额外的字段，如 bin、scripts、engines、devDependencies、author\


## 错误处理

| **错误类型** | **处理方式** |
| -------- | -------- |

| **同步错误** | 使用 `try...catch` 捕获 |
| -------- | ------------------- |

| **回调模式** | 通过 `if (err) {...}` 处理回调中的错误 |
| -------- | ---------------------------- |

| **Promise** | 使用 `.catch()` 处理错误 |
| ----------- | ------------------ |

| **async/await** | 使用 `try...catch` 捕获异步错误 |
| --------------- | ----------------------- |

| **事件错误** | 监听 `'error'` 事件，如 `eventEmitter.on('error', handler)` |
| -------- | ----------------------------------------------------- |

| **全局错误** | `process.on('uncaughtException', handler)` 和 `process.on('unhandledRejection', handler)` |
| -------- | ---------------------------------------------------------------------------------------- |

## Node.js 的异步编程有哪些方式

| **回调函数（Callback）** | 简单异步操作 | 直接调用，性能高 | 回调地狱，错误处理复杂 |
| ------------------ | ------ | -------- | ----------- |

| **Promise** | 复杂异步流程 | 链式调用，错误处理统一 | `then()` 嵌套仍可能较复杂 |
| ----------- | ------ | ----------- | ----------------- |

| **async/await** | 复杂异步逻辑 | 代码简洁，可用 `try/catch` 处理错误 | 默认是串行执行，需 `Promise.all()` 并行 |
| --------------- | ------ | ------------------------ | ---------------------------- |

| **EventEmitter** | 长时间运行任务 | 适用于 WebSockets、事件驱动 | 事件管理可能导致内存泄漏 |
| ---------------- | ------- | ------------------- | ------------ |

| **Stream** | 大文件处理 | 高效内存使用 | 需要监听多个事件，API 较复杂 |
| ---------- | ----- | ------ | ---------------- |

这些优化涉及多个层面，包括**启动时间、内存管理、进程管理、缓存、并发、API 性能、日志、监控**等方面。我会逐一解析，并给出实际应用中的优化方法和代码示例。

***

## &#x20;**如何优化 Node.js 应用的启动速度？**

**影响启动速度的因素**：

* 依赖加载过多或缓慢
* 数据库连接、缓存加载等初始化任务过慢
* 代码体积过大

#### **优化方案**

✅ **减少不必要的 `require/import`**\
**问题**：`require()` 是同步操作，加载模块时会阻塞执行。\
**优化**：

```javascript
const fs = require("fs").promises; // 只加载 promises 版本
```

或**按需加载**：

```javascript
app.get("/route", async (req, res) => {
  const someModule = await import("./someModule.js");
  res.send(someModule.default());
});
```

✅ **并行化启动任务**

```javascript
Promise.all([initDatabase(), loadCache(), startServer()]);
```

✅ **使用 `pkg` 或 `esbuild` 进行代码打包**

```bash
npx esbuild index.js --bundle --minify --platform=node --outfile=dist.js
```

减少 Node.js 解析时间，提高启动速度。

***

## **如何优化 Node.js 的内存使用？如何避免内存泄漏？**

#### **常见内存泄漏原因**

1. **全局变量**未释放：

```javascript
global.leakData = new Array(1000000);
```

✅ **解决**：使用 `WeakMap` 代替

```javascript
const cache = new WeakMap();
```

2. **事件监听未移除**

```javascript
const emitter = new EventEmitter();
emitter.on("event", () => { ... }); // 监听器未清理
```

✅ **解决**

```javascript
emitter.removeAllListeners("event");
```

3. **未清理的 `setInterval`**

```javascript
setInterval(() => console.log("Running"), 1000); // 永不停止
```

✅ **解决**

```javascript
const interval = setInterval(() => console.log("Running"), 1000);
clearInterval(interval);
```

***

## **如何使用 pm2 进行进程管理和负载均衡？**

PM2 是 Node.js 进程管理工具，支持负载均衡、自动重启。

#### **安装**

```bash
npm install -g pm2
```

#### **启动 Node.js 应用**

```bash
pm2 start app.js -i max  # 根据 CPU 核心数开启多个进程
```

#### **常用命令**

```bash
pm2 list        # 查看所有进程
pm2 logs        # 查看日志
pm2 restart 0   # 重启进程
pm2 delete 0    # 删除进程
pm2 save        # 保存状态
pm2 startup     # 开机自启
```

***

## **如何使用 Redis 进行缓存优化？**

#### **安装 Redis**

```bash
npm install redis
```

#### **使用示例**

```javascript
const redis = require("redis");
const client = redis.createClient();

async function getUser(id) {
  const cacheKey = `user:${id}`;

  // 先查询 Redis 缓存
  const cachedUser = await client.get(cacheKey);
  if (cachedUser) return JSON.parse(cachedUser);

  // 缓存未命中，查询数据库
  const user = await db.findUserById(id);
  if (user) {
    client.set(cacheKey, JSON.stringify(user), "EX", 3600); // 设置缓存过期时间
  }

  return user;
}
```

Redis 在 **高并发读场景** 非常有用，如 API 访问量较大时可减轻数据库压力。

## **如何使用 cluster 或 worker\_threads 提高 Node.js 的并发能力？**

Node.js 默认是**单线程**的，但可以通过 **Cluster** 或 **Worker Threads** 来创建多个进程/线程，提高并发能力。

#### **Cluster 适用于 CPU 密集型任务**

```javascript
const cluster = require("cluster");
const os = require("os");

if (cluster.isMaster) {
  os.cpus().forEach(() => cluster.fork()); // 根据 CPU 核心数创建 worker 进程
} else {
  require("./server.js");
}
```

#### **Worker Threads 适用于计算密集型任务**

```javascript
const { Worker, isMainThread, parentPort } = require("worker_threads");

if (isMainThread) {
  const worker = new Worker(__filename);
  worker.on("message", (msg) => console.log("Received:", msg));
} else {
  parentPort.postMessage("Hello from worker!");
}
```

***

## **如何优化 API 响应速度？**

✅ **使用 Gzip 压缩**

```javascript
const compression = require("compression");
app.use(compression());
```

✅ **使用 HTTP/2**

```javascript
const http2 = require("http2");
```

✅ **减少数据库查询**

* **索引优化**（参考上文）
* **Redis 缓存**
* **分页查询**

```javascript
db.users.find().skip(10).limit(10);
```

✅ **异步请求并行化**

```javascript
Promise.all([fetchUsers(), fetchOrders()]);
```

***

## **如何进行日志管理？常见的日志库有哪些？**

**常见库**：

* **Winston**
* **Pino**
* **Bunyan**

#### **使用 Winston**

```javascript
const winston = require("winston");
const logger = winston.createLogger({
  transports: [new winston.transports.File({ filename: "logfile.log" })],
});
logger.info("This is an info log");
```

***

## **如何监控 Node.js 应用的性能？有哪些工具？**

✅ **使用 `clinic` 监控性能**

```bash
npm install -g clinic
clinic doctor -- node app.js
```

✅ **使用 `pm2 monit` 监控** ✅&#x20;

**使用 APM（如 New Relic、Datadog）**

***

## **Node.js 的流（Stream）如何提高性能？**

**流（Stream）可用于**：

* **大文件处理**（避免一次性加载到内存）
* **网络请求**
* **数据库操作**

#### **示例：流式读取文件**

```javascript
const fs = require("fs");
fs.createReadStream("large-file.txt")
  .pipe(fs.createWriteStream("output.txt"));
```

***

## **如何使用 GraphQL 代替 RESTful API 提高查询性能？**

GraphQL 允许前端**按需查询**，避免 REST API 过度请求。

#### **示例**

```javascript
const { ApolloServer, gql } = require("apollo-server");

const typeDefs = gql`
  type User {
    id: ID!
    name: String!
  }
  type Query {
    users: [User]
  }
`;

const resolvers = {
  Query: {
    users: () => [{ id: 1, name: "Alice" }, { id: 2, name: "Bob" }]
  }
};

const server = new ApolloServer({ typeDefs, resolvers });
server.listen().then(({ url }) => console.log(`Server ready at ${url}`));
```

***

### **总结**

| **优化方向**    | **方法**                           |
| ----------- | -------------------------------- |
| **启动优化**    | 减少 `require`，并行初始化，打包优化          |
| **内存优化**    | 避免全局变量，及时清理事件监听                  |
| **进程管理**    | `pm2`，`cluster`，`worker_threads` |
| **缓存优化**    | Redis                            |
| **API 性能**  | Gzip，HTTP/2，索引优化                 |
| **日志管理**    | Winston，Pino                     |
| **性能监控**    | Clinic，PM2                       |
| **GraphQL** | 代替 REST API                      |

## 安全

| **安全风险** | **解决方案** |
| -------- | -------- |

| **SQL 注入** | 使用参数化查询或 ORM |
| ---------- | ------------ |

| **XSS** | `helmet.js`，用户输入过滤 |
| ------- | ------------------ |

| **CSRF** | `csurf`，`SameSite` Cookie |
| -------- | ------------------------- |

| **DDoS 攻击** | `express-rate-limit`，Redis，WAF |
| ----------- | ------------------------------ |

| **密码存储** | `bcrypt.js` |
| -------- | ----------- |

| **API 密钥管理** | `.env` 文件，环境变量 |
| ------------ | -------------- |

| **依赖漏洞** | `npm audit`，`snyk` |
| -------- | ------------------ |

| **JWT 认证** | `jsonwebtoken` |
| ---------- | -------------- |

你的问题涉及到 **Node.js 在高并发、分布式、微服务、性能优化等多个方面的应用**，我会逐一解答这些关键问题，并提供示例代码。🚀

***

### **如何在 Node.js 中处理大文件上传？**

#### **问题**

直接读取大文件到内存可能导致内存溢出，因此要 **使用流（Stream）** 处理大文件。

#### **解决方案**

✅ **使用 `multer` 处理文件上传**

```bash
npm install multer
```

```javascript
const express = require("express");
const multer = require("multer");

const upload = multer({ dest: "uploads/" });
const app = express();

app.post("/upload", upload.single("file"), (req, res) => {
  res.send("File uploaded successfully");
});

app.listen(3000, () => console.log("Server started"));
```

✅ **使用 `stream` 处理大文件**

```javascript
const fs = require("fs");

fs.createReadStream("largefile.txt")
  .pipe(fs.createWriteStream("copy.txt"))
  .on("finish", () => console.log("File copied"));
```

✅ **支持大文件分片上传（前端 + 后端）**

1. **前端** 将文件分片上传
2. **后端** 逐步接收合并文件片段

***

### **如何实现 API 限流（Rate Limiting）？**

#### **问题**

如果 API **没有速率限制**，可能导致 **DDoS 攻击** 或 **滥用 API 资源**。

#### **解决方案**

✅ **使用 `express-rate-limit`**

```bash
npm install express-rate-limit
```

```javascript
const rateLimit = require("express-rate-limit");

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 分钟
  max: 100, // 每个 IP 100 次请求
});

app.use(limiter);
```

✅ **使用 Redis 实现分布式限流**

```javascript
const Redis = require("ioredis");
const redis = new Redis();

async function rateLimiter(req, res, next) {
  const key = `rate-limit:${req.ip}`;
  const count = await redis.incr(key);
  if (count === 1) await redis.expire(key, 60); // 60 秒窗口期
  if (count > 10) return res.status(429).send("Too Many Requests");
  next();
}

app.use(rateLimiter);
```

***

### &#x20;**如何实现多线程任务调度？**

#### **问题**

Node.js **单线程**，但可以使用 **Worker Threads** 进行计算密集型任务。

#### **解决方案**

✅ **使用 `worker_threads`**

```javascript
const { Worker } = require("worker_threads");

const worker = new Worker("./worker.js");
worker.on("message", (msg) => console.log("Result:", msg));
worker.postMessage({ task: "compute" });
```

✅ **Worker 线程计算任务**

```javascript
const { parentPort } = require("worker_threads");

parentPort.on("message", (msg) => {
  // 计算任务
  parentPort.postMessage({ result: "done" });
});
```

***

### **如何在 Node.js 中实现 WebSocket 连接？**

✅ **使用 `ws`**

```bash
npm install ws
```

```javascript
const WebSocket = require("ws");
const server = new WebSocket.Server({ port: 8080 });

server.on("connection", (ws) => {
  ws.on("message", (msg) => console.log(`Received: ${msg}`));
  ws.send("Hello WebSocket!");
});
```

✅ **使用 `socket.io`**

```javascript
const io = require("socket.io")(server);

io.on("connection", (socket) => {
  socket.on("message", (msg) => console.log(`Received: ${msg}`));
  socket.emit("response", "Hello Client!");
});
});
```

***

### **如何在 Node.js 中处理消息队列（RabbitMQ、Kafka）？**

✅ **使用 `amqplib` 连接 RabbitMQ**

```javascript
const amqp = require("amqplib");

async function sendMsg() {
  const conn = await amqp.connect("amqp://localhost");
  const channel = await conn.createChannel();
  const queue = "tasks";
  await channel.assertQueue(queue);
  channel.sendToQueue(queue, Buffer.from("Hello, RabbitMQ!"));
}
sendMsg();
```

✅ **使用 `kafkajs` 连接 Kafka**

```javascript
const { Kafka } = require("kafkajs");

const kafka = new Kafka({ clientId: "app", brokers: ["localhost:9092"] });
const producer = kafka.producer();

async function sendMsg() {
  await producer.connect();
  await producer.send({ topic: "topic1", messages: [{ value: "Hello Kafka" }] });
}
sendMsg();
```

***

### **如何使用 Redis 进行分布式锁？**

✅ **实现分布式锁**

```javascript
const Redis = require("ioredis");
const redis = new Redis();

async function acquireLock(key, ttl) {
  const lock = await redis.set(key, "locked", "NX", "PX", ttl);
  return lock === "OK";
}
```

***

### **如何在 Node.js 中进行单元测试和集成测试？**

✅ **使用 Jest 进行单元测试**

```bash
npm install --save-dev jest
```

```javascript
test("adds 1 + 2", () => {
  expect(1 + 2).toBe(3);
});
```

✅ **使用 Supertest 进行 API 测试**

```javascript
const request = require("supertest");

test("GET /api", async () => {
  const res = await request(app).get("/api");
  expect(res.statusCode).toBe(200);
});
```

***

### **如何实现 OAuth2.0 登录？**

✅ **使用 `passport.js`**

```bash
npm install passport passport-google-oauth20
```

```javascript
const passport = require("passport");
const GoogleStrategy = require("passport-google-oauth20").Strategy;

passport.use(
  new GoogleStrategy(
    {
      clientID: "GOOGLE_CLIENT_ID",
      clientSecret: "GOOGLE_CLIENT_SECRET",
      callbackURL: "/auth/google/callback",
    },
    (accessToken, refreshToken, profile, done) => {
      done(null, profile);
    }
  )
);
```

***

### **如何在 Node.js 中构建微服务架构？**

✅ **使用 NATS 消息总线**

```javascript
const nats = require("nats").connect("nats://localhost:4222");

nats.subscribe("order.created", (msg) => console.log(`Received: ${msg}`));
```

✅ **使用 API Gateway（如 Express）**

```javascript
app.use("/orders", proxy({ target: "http://order-service" }));
```

***

### **如何实现 SSR（服务端渲染）？**

✅ **使用 Next.js**

```javascript
export async function getServerSideProps() {
  return { props: { data: "SSR Example" } };
}
```

✅ **使用 `express-handlebars`**

```javascript
const exphbs = require("express-handlebars");
app.engine("handlebars", exphbs());
app.set("view engine", "handlebars");
```

***



| **问题**          | **解决方案**                     |
| --------------- | ---------------------------- |
| **大文件上传**       | `multer` + 流                 |
| **API 限流**      | `express-rate-limit` + Redis |
| **多线程**         | `worker_threads`             |
| **WebSocket**   | `ws` + `socket.io`           |
| **消息队列**        | RabbitMQ、Kafka               |
| **Redis 分布式锁**  | `ioredis`                    |
| **单元测试**        | Jest + Supertest             |
| **OAuth2.0 登录** | `passport.js`                |
| **微服务**         | NATS、API Gateway             |
| **SSR**         | Next.js、Express Handlebars   |

***

### &#x20;**`ws` 和 `socket.io`**&#x20;

| **特性**        | **ws**                | **socket.io**                           |
| ------------- | --------------------- | --------------------------------------- |
| **传输协议**      | 仅支持 WebSocket         | WebSocket + HTTP 长轮询                    |
| **自动重连**      | ❌ 需要手动实现              | ✅ 内置支持                                  |
| **广播消息**      | ❌ 需要手动实现              | ✅ 内置支持（`socket.broadcast`）              |
| **房间（Rooms）** | ❌ 不支持                 | ✅ 内置支持（`socket.join(room)`）             |
| **支持事件**      | ❌ 仅 `message` 事件      | ✅ 自定义事件（`socket.on("event", callback)`） |
| **兼容性**       | 仅支持现代浏览器              | 兼容旧浏览器（支持 WebSocket 不可用时的降级）            |
| **适用场景**      | **轻量级、高性能** WebSocket | **功能丰富、适用于多人实时通信**                      |

***

#### **2. 使用 `ws` 实现 WebSocket**

**安装 `ws`**

```bash
npm install ws
```

**创建 WebSocket 服务器**

```javascript
const WebSocket = require("ws");

const server = new WebSocket.Server({ port: 8080 });

server.on("connection", (ws) => {
  console.log("Client connected");

  ws.on("message", (message) => {
    console.log(`Received: ${message}`);
    ws.send(`Echo: ${message}`);
  });

  ws.on("close", () => console.log("Client disconnected"));
});
```

**客户端连接 WebSocket**

```javascript
const ws = new WebSocket("ws://localhost:8080");

ws.onopen = () => ws.send("Hello Server!");
ws.onmessage = (event) => console.log("Message from server:", event.data);
```

✅ **`ws` 适用于**：

* 轻量级应用
* 需要高性能、低延迟的 WebSocket 连接
* 不需要额外功能（如广播、房间、自动重连）

***

#### **3. 使用 `socket.io` 实现 WebSocket**

**安装 `socket.io`**

```bash
npm install socket.io
```

**创建 `socket.io` 服务器**

```javascript
const io = require("socket.io")(3000);

io.on("connection", (socket) => {
  console.log("Client connected");

  socket.on("message", (msg) => {
    console.log("Received:", msg);
    socket.emit("response", `Echo: ${msg}`);
  });

  socket.on("disconnect", () => console.log("Client disconnected"));
});
```

**客户端连接 `socket.io`**

```javascript
const socket = io("http://localhost:3000");

socket.on("connect", () => {
  console.log("Connected to server");
  socket.emit("message", "Hello Server!");
});

socket.on("response", (data) => console.log("Message from server:", data));
```

✅ **`socket.io` 适用于**：

* 需要 **自动重连**，防止连接丢失
* **多人聊天室**，需要 **广播和房间**
* 需要 **HTTP 轮询** 作为 WebSocket 兼容方案
* 适用于 **游戏、聊天、协作应用**

***

#### **4. `ws` vs `socket.io` 代码对比**

#### **纯 WebSocket（ws）**

```javascript
ws.on("message", (msg) => {
  ws.send(`Echo: ${msg}`);
});
```

#### **socket.io 事件**

```javascript
socket.on("chat", (msg) => {
  io.emit("chat", msg); // 广播消息
});
```

***

#### **5. 选择哪一个？**

✅ **用 `ws`**

* 只需要 **WebSocket**，不需要其他功能
* 需要 **高性能**（`ws` 速度更快）
* 服务器与客户端 **都支持 WebSocket**

✅ **用 `socket.io`**

* 需要 **自动重连**
* 需要 **事件机制**（`on("chat", handler)`）
* 需要 **房间（Rooms）** 和 **广播（Broadcast）**
* 需要 **兼容 HTTP 轮询**

***

#### **6. 总结**

| **对比项**           | **ws** | **socket.io** |
| ----------------- | ------ | ------------- |
| **轻量级**           | ✅      | ❌             |
| **仅支持 WebSocket** | ✅      | ❌（可回退到 HTTP）  |
| **自动重连**          | ❌      | ✅             |
| **房间（Rooms）**     | ❌      | ✅             |
| **适用于游戏、多人协作**    | ❌      | ✅             |

