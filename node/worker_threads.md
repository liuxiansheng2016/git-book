# worker\_threads

### **1. 什么是 `worker_threads`？**

`worker_threads` 是 Node.js 提供的**多线程**模块，它允许我们在 Node.js 中创建**多个线程**，用于执行并行任务，从而提高性能。

#### **为什么需要 `worker_threads`？**

* **Node.js 是单线程的**，默认情况下无法有效利用多核 CPU 处理计算密集型任务。
* **与 `cluster` 不同，`worker_threads` 线程间可以共享内存**，适用于**高计算**、**数据共享**的场景，如 AI 计算、大数据处理等。

***

### **2. `worker_threads` 的基本使用**

#### **示例：创建一个多线程计算任务**

```javascript
const { Worker, isMainThread, parentPort } = require('worker_threads');

if (isMainThread) {
  console.log(`主线程 ${process.pid} 正在运行`);

  // 创建 worker 线程
  const worker = new Worker(__filename);

  // 监听 worker 线程的消息
  worker.on('message', (msg) => {
    console.log(`主线程收到消息: ${msg}`);
  });

  // 监听 worker 线程的错误
  worker.on('error', (err) => {
    console.error('Worker 线程发生错误:', err);
  });

  // 监听 worker 线程退出
  worker.on('exit', (code) => {
    console.log(`Worker 线程退出，退出码: ${code}`);
  });

  // 向 worker 线程发送消息
  worker.postMessage("Hello Worker!");
} else {
  console.log(`Worker 线程 ${process.pid} 已启动`);

  // 监听主线程的消息
  parentPort.on('message', (msg) => {
    console.log(`Worker 线程收到消息: ${msg}`);

    // 发送消息回主线程
    parentPort.postMessage("Hello Main Thread!");
  });
}
```

#### **运行结果**

```sh
主线程 1234 正在运行
Worker 线程 5678 已启动
Worker 线程收到消息: Hello Worker!
主线程收到消息: Hello Main Thread!
```

***

### **3. 代码解析**

#### **主线程**

* `isMainThread`：判断当前代码是否运行在主线程中。
* `new Worker(__filename)`：创建一个新的 worker 线程，执行当前文件（`__filename`）。
* `worker.on('message', callback)`：监听 worker 线程的消息。
* `worker.postMessage(data)`：向 worker 线程发送消息。

#### **Worker 线程**

* `parentPort.on('message', callback)`：监听主线程发送的消息。
* `parentPort.postMessage(data)`：向主线程发送消息。

***

### **4. `worker_threads` 适用于哪些场景？**

#### ✅ **适用场景**

1. **CPU 密集型任务**
   * 如加密、图像处理、AI 计算、大量数据计算等。
2. **数据共享场景**
   * 多线程可以**共享内存**（使用 `SharedArrayBuffer`）。
3. **需要低开销的多线程处理**
   * `worker_threads` 线程间通信比 `cluster` 进程间通信开销更小。

#### ❌ **不适用场景**

1. **I/O 密集型任务**
   * 处理 Web 服务器请求时，`cluster` 模块更合适（因为 I/O 操作本身是异步的，不需要多线程）。

***

### **5. `worker_threads` vs `cluster`**

| **对比项**    | **worker\_threads（多线程）**   | **cluster（多进程）** |
| ---------- | -------------------------- | ---------------- |
| **进程类型**   | 线程                         | 独立进程             |
| **是否共享内存** | ✅ 共享 (`SharedArrayBuffer`) | ❌ 不共享            |
| **适用场景**   | 计算密集型任务                    | 高并发 Web 服务器      |
| **通信方式**   | 低开销 (`postMessage`)        | 需要 IPC 或 Redis   |
| **性能开销**   | 低                          | 高（进程创建和管理开销大）    |

#### **什么时候用 `worker_threads`？**

* **计算密集型任务**（如 AI、密码哈希、视频处理）
* **需要多个线程共享数据**
* **希望降低进程管理开销**

#### **什么时候用 `cluster`？**

* **Web 服务器并发处理**（如 Express/Koa）
* **I/O 密集型任务**
* **需要进程隔离（防止单个线程崩溃影响其他线程）**

***

### **6. 线程间共享内存**

`worker_threads` 允许线程间共享数据，使用 `SharedArrayBuffer`：

```javascript
const { Worker, isMainThread, workerData } = require('worker_threads');

if (isMainThread) {
  const sharedBuffer = new SharedArrayBuffer(4); // 共享 4 字节
  const sharedArray = new Int32Array(sharedBuffer);
  sharedArray[0] = 42; // 主线程写入数据

  const worker = new Worker(__filename, { workerData: sharedArray });

  worker.on('exit', () => {
    console.log(`子线程读取到的值: ${sharedArray[0]}`); // 99
  });
} else {
  const sharedArray = workerData;
  sharedArray[0] = 99; // 子线程修改数据
  process.exit();
}
```

#### **`SharedArrayBuffer` 作用**

* **多个线程共享同一块内存**
* **避免 JSON 序列化，提高数据传输效率**

***

### **7. 线程池优化**

如果需要管理多个 worker 线程，可以使用 [`workerpool`](https://github.com/josdejong/workerpool) 这样的库。

#### **使用 `workerpool` 创建线程池**

```javascript
const workerpool = require('workerpool');

function heavyComputation(n) {
  let sum = 0;
  for (let i = 0; i < n; i++) {
    sum += i;
  }
  return sum;
}

// 创建线程池
workerpool.worker({
  heavyComputation: heavyComputation
});
```

#### **在主线程调用**

```javascript
const workerpool = require('workerpool');

// 连接 worker 线程池
const pool = workerpool.pool(__dirname + '/worker.js');

pool.exec('heavyComputation', [1000000])
  .then((result) => {
    console.log('计算结果:', result);
  })
  .catch((err) => {
    console.error(err);
  })
  .then(() => pool.terminate()); // 关闭线程池
```

***

### **8. 总结**

✅ **`worker_threads` 适用于：**

* CPU 密集型任务（如 AI、数据分析、哈希计算）
* 共享数据场景（使用 `SharedArrayBuffer`）
* 低开销多线程（比 `cluster` 进程间通信更快）

⚠ **注意事项：**

* **与主线程通信仍有一定开销**
* **Web 服务器推荐使用 `cluster`**
* **线程管理可以用 `workerpool` 提高效率**

如果你的任务需要**并行计算**，但不适合 `cluster` 进程通信，**`worker_threads` 是一个更高效的选择！** 🚀
