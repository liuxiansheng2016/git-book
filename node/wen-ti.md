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

使用 ES 模块

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

除了前面提到的几个必选字段外，还有一些额外的字段，如 bin、scripts、engines、devDependencies、author

