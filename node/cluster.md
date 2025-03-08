# cluster

### **1. 什么是 `cluster` 模块？**

`cluster` 模块是 Node.js 内置模块，用于**创建多个进程**（worker 进程）来执行任务，从而充分利用多核 CPU，提高应用的并发能力和性能。

#### **为什么需要 `cluster`？**

* **Node.js 是单线程的**，默认只能使用一个 CPU 核心，容易出现性能瓶颈。
* **I/O 密集型应用（如 Web 服务器）可以受益于多进程处理**，使多个请求同时被多个进程处理，提高吞吐量。
* **避免单进程崩溃影响整个应用**，如果某个 worker 进程崩溃，`cluster` 可以自动创建新的进程。

***

### **2. `cluster` 模块的基本使用**

#### **示例：创建一个多进程 HTTP 服务器**

```javascript
const cluster = require('cluster');
const http = require('http');
const os = require('os');

if (cluster.isMaster) {
  // 获取 CPU 核心数
  const numCPUs = os.cpus().length;
  console.log(`主进程 ${process.pid} 正在运行`);

  // 创建多个 worker 进程
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }

  // 监听子进程退出，自动重启
  cluster.on('exit', (worker, code, signal) => {
    console.log(`子进程 ${worker.process.pid} 退出`);
    cluster.fork(); // 创建新进程
  });
} else {
  // 工作进程创建 HTTP 服务器
  http.createServer((req, res) => {
    res.writeHead(200);
    res.end(`Worker ${process.pid} 处理请求`);
  }).listen(8000);

  console.log(`工作进程 ${process.pid} 已启动`);
}
```

***

### **3. 代码解析**

#### **`cluster.isMaster`**

* 这是**主进程**的逻辑，负责创建多个**子进程（worker）**。

#### **`cluster.fork()`**

* 用于**创建新进程**，这里根据 `os.cpus().length`（CPU 核心数）创建多个进程。

#### **`cluster.on('exit', callback)`**

* 当 worker 进程退出时，主进程会监听到，并重新启动新的 worker 进程，保证服务不中断。

#### **子进程（worker 进程）**

* **worker 进程创建 HTTP 服务器**，每个进程监听相同的端口 `8000`，但操作系统会**自动负载均衡**，将请求分配给不同的 worker。

***

### **4. 适用于哪些场景？**

#### **✅ 适用场景**

1. **CPU 密集型任务**
   * 计算哈希值、大量数据处理、压缩等计算任务，可使用 `cluster` 分配多个进程并行计算。
2. **I/O 密集型应用**
   * 处理高并发的 Web 服务器，如 Express/Koa，可以让多个 worker 进程处理 HTTP 请求，提高吞吐量。
3. **多用户 Web 服务**
   * 当有大量用户请求时，`cluster` 允许多个 worker 进程处理不同的请求，提高服务可用性。

#### **❌ 不适用场景**

1. **共享内存场景**
   * `cluster` 进程是**独立的**，无法共享内存变量，若需要共享数据（如缓存、状态信息），需要额外的进程间通信（如 Redis）。
2. **短生命周期的任务**
   * 如果任务执行时间很短，创建和管理 worker 进程的开销可能超过性能收益。

***

### **5. `cluster` 进程间通信**

在 `cluster` 模块中，**主进程和子进程可以相互通信**，通过 `process.send()` 发送消息。

#### **示例：主进程与子进程通信**

```javascript
const cluster = require('cluster');
const http = require('http');
const os = require('os');

if (cluster.isMaster) {
  console.log(`主进程 ${process.pid} 正在运行`);

  const worker = cluster.fork();

  // 监听来自 worker 进程的消息
  worker.on('message', (msg) => {
    console.log(`主进程收到消息: ${msg}`);
  });

  // 发送消息到 worker 进程
  worker.send("Hello Worker");
} else {
  process.on('message', (msg) => {
    console.log(`子进程 ${process.pid} 收到消息: ${msg}`);
    process.send("Hello Master");
  });

  http.createServer((req, res) => {
    res.writeHead(200);
    res.end(`Worker ${process.pid} 处理请求`);
  }).listen(8000);

  console.log(`工作进程 ${process.pid} 已启动`);
}
```

#### **运行结果**

```sh
主进程 1234 正在运行
工作进程 5678 已启动
子进程 5678 收到消息: Hello Worker
主进程收到消息: Hello Master
```

***

### **6. 负载均衡**

在 `cluster` 模块中，操作系统会**自动负载均衡**，将不同请求分配给多个 worker 进程。

#### **两种负载均衡方式**

1. **Round-robin 轮询（默认方式）**
   * Node.js 14+ 版本，`cluster` 默认使用 `round-robin`，将请求**轮流**分配给不同的 worker。
   * 适用于**高并发 HTTP 服务器**。
2. **操作系统内核调度**
   * 在 `Windows` 或者 `Linux` 旧版本中，`cluster` 依赖 OS 负载均衡策略，可能导致部分 worker 处理较多请求。

***

### **7. `cluster` vs `worker_threads`**

Node.js 还有一个 `worker_threads` 模块，也可用于多线程处理。

| **对比项**    | **cluster（多进程）** | **worker\_threads（多线程）** |
| ---------- | ---------------- | ------------------------ |
| **进程类型**   | 多个独立进程           | 共享内存的线程                  |
| **是否共享内存** | ❌ 不共享            | ✅ 共享内存                   |
| **适用场景**   | 高并发 HTTP 服务器     | 计算密集型任务                  |
| **性能开销**   | 高（进程创建开销大）       | 低（线程轻量）                  |

#### **什么时候用 `worker_threads`？**

如果任务需要**共享数据**，或是**高 CPU 计算任务**（如加密、压缩等），使用 `worker_threads` 更高效。

***

### **8. 总结**

✅ `cluster` 适用于：

* **高并发 HTTP 服务器**（多 worker 处理请求）
* **CPU 密集型任务**（并行计算）
* **进程隔离**（避免单进程崩溃）

⚠ **注意事项**：

* `cluster` 不能共享内存，数据共享需要使用 **Redis、IPC** 等方式。
* `cluster` 适用于 **Web 服务器**，但计算密集型任务建议用 **worker\_threads**。

**推荐：** 如果你需要提高 Web 服务器的吞吐量，并充分利用多核 CPU，`cluster` 是非常好的选择！ 🚀
