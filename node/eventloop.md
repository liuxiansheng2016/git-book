# Eventloop

单线程：Node.js 运行在一个单线程上，这意味着所有 JavaScript 代码都在同一个线程中执行。

异步 I/O：Node.js 使用异步 I/O 操作来处理耗时任务，如文件系统操作、网络请求等，这些操作不会阻塞主线程。

事件驱动：Node.js 使用事件驱动模型，当某个事件发生时，会触发相应的回调函数。

### 事件循环的详细流程

初始化：

Node.js 启动时，首先执行主模块中的同步代码。

### **Node.js 事件循环中的执行顺序**

在 Node.js 事件循环的每个迭代中，任务的执行顺序如下：

1. **执行所有的同步代码（执行栈）**
2. **执行 `process.nextTick()` 队列**
   1. `process.nextTick()` 会在**当前执行栈清空后**，立即执行，而不会等待进入事件循环的下一个阶段。
3. **执行所有的微任务队列（Promise 回调等）**
4. **事件循环进入各个阶段，按顺序执行宏任务**
   1. **Timers（定时器阶段）**：
      * 执行 `setTimeout()` 和 `setInterval()` 设定的 **回调函数**。
      * 但并不保证回调会 **准时** 执行，而是**最早**在设定的时间后执行（受 CPU 任务、I/O 操作影响）。
   2. **Pending Callbacks（待处理回调阶段）**：
      * 处理上一轮循环中延迟执行的 I/O 回调（如 TCP 错误）。
   3. **Idle, Prepare（空闲阶段）**：
      * 仅供内部使用，一般不涉及开发者的代码。
   4. **Poll（轮询阶段）**：
      * 处理 **I/O 事件**（如读取文件、网络请求等）。
      * 如果 **无 I/O 任务**，事件循环会：
        * 如果有 `setImmediate()` 任务，跳到 `check` 阶段执行。
        * 如果 **没有** `setImmediate()` 任务，则 **阻塞** 在 `poll` 阶段，直到有新的事件到达（如 I/O 事件）。
   5. **Check（setImmediate 阶段）**：
      * 执行 `setImmediate()` 设定的 **回调函数**。
      * `setImmediate()` **总是在 poll 阶段执行完后** 立即执行。
   6. **Close Callbacks（关闭回调阶段）**：
      * 执行 `close` 事件的回调函数（如 `socket.on('close', callback)`）。
      * 例如：当 `socket` 连接被关闭时触发 `close` 事件。
5. **循环进入下一个迭代，继续执行 `nextTick()` → 微任务 → 宏任务**

**事件循环的 6 个阶段**

Node.js 的事件循环由 **libuv** 负责管理，主要分为以下 6 个阶段（按照执行顺序）：

| **阶段** | **执行的任务** |
| ------ | --------- |

| **1️⃣ Timers** | 执行 `setTimeout()` 和 `setInterval()` |
| -------------- | ----------------------------------- |

| **2️⃣ Pending Callbacks** | 执行 I/O 相关的回调（如 TCP 连接错误） |
| ------------------------- | ------------------------ |

| **3️⃣ Idle, Prepare** | 内部使用，通常不影响开发 |
| --------------------- | ------------ |

| **4️⃣ Poll（轮询）** | 处理 **I/O 任务**（如文件读写、网络请求） |
| ---------------- | ------------------------- |

| **5️⃣ Check** | 执行 `setImmediate()` |
| ------------- | ------------------- |

| **6️⃣ Close Callbacks** | 处理 `close` 事件，如 `socket.on('close', ...)` |
| ----------------------- | ----------------------------------------- |

### **事件循环的执行流程**

**同步代码 →&#x20;**<mark style="color:red;">**异步回调任务**</mark>

1. **Node.js 先执行所有同步代码**
2. **遇到异步任务（如 I/O 操作、`setTimeout`），将其交给 `libuv`**
3. **`libuv` 监听 I/O 操作的完成情况**
4. **当 I/O 任务完成，回调函数被推入事件循环的队列**
5. **事件循环按顺序执行相应的回调**

<mark style="color:red;">**定时器阶段（timers phase）只在每一轮事件循环的开始时检查和执行已到期的定时器**</mark>。如果你在 I/O 回调内部调用了定时器（比如 setTimeout(callback, 0)），那么此时当前轮次的定时器阶段已经过去，<mark style="color:red;">新的定时器回调只能在下一轮事件循环的 timers 阶段中被执行</mark>。这是由事件循环的设计决定的。

具体来说：

* **事件循环阶段顺序：** Node.js 的事件循环由多个阶段构成，每个阶段都有特定的任务。定时器阶段位于整个事件循环的最前面，每一轮循环一开始就检查所有到期的定时器。
* **I/O 回调执行时机：** 当进入 I/O 操作的 poll 阶段并执行相应的 I/O 回调时，定时器阶段已经结束。如果在 I/O 回调中安排了新的定时器，这些定时器会在当前轮次中等待，但不会被执行，而是等到下一轮事件循环的 timers 阶段。
* **设计目的：** 这种设计确保了事件循环各阶段之间有明确的划分，避免在同一轮内反复切换，从而帮助维护代码执行的顺序和系统的整体响应性。

这样，**即使设置了 0 毫秒延时，定时器也要等到下一个事件循环轮次才能执行**，这与 setImmediate() 在 I/O 回调中通常先于 setTimeout(callback, 0) 执行形成了对比。

### `setImmediate` 和 `setTimeout`

* 在 **主线程** 中，`setImmediate` 和 `setTimeout(..., 0)` 的执行顺序不确定，取决于事件循环的调度。
  * 在主模块中，事件循环启动时，定时器阶段和检查阶段的执行顺序可能因 Node.js 版本、系统性能或环境因素而变化。

<pre><code>```javascript
<strong>// 示例：在主模块中使用
</strong>setImmediate(() => {
    console.log("setImmediate callback");
  });
setTimeout(() => {
    console.log("setTimeout callback");
  }, 0);
  
    console.log("Main module end");
  
```
</code></pre>

输出

```
Main module end
setTimeout callback
setImmediate callback
```

* 在 I/O 回调中，`setImmediate` 通常会比 `setTimeout`（延迟设为 0）先执行。因为在 I/O 回调完成后，事件循环会先进入检查阶段执行 `setImmediate` 队列中的回调，然后再进入下一个定时器阶段执行 `setTimeout` 回调。

### `process.nextTick()` 和 `setImmediate()`

&#x20;都用于调度异步回调，但它们的执行时机和优先级有所不同：

* **执行时机**
  * **process.nextTick()** 会在当前操作完成后立即执行回调，也就是说，在当前事件循环阶段结束之前，它会清空 nextTick 队列。这使得它的回调能够在继续处理任何 I/O 事件或进入下一个事件循环之前运行。
  * **setImmediate()** 则将回调调度到下一轮事件循环的 **check 阶段**。这意味着它会在当前 I/O 事件和 nextTick 队列执行完毕之后执行。
* **优先级与风险**
  * 由于 `process.nextTick()` 的回调在当前操作完成后立即执行，它的优先级更高。如果频繁或递归地使用 `process.nextTick()`，可能会阻塞事件循环，导致 I/O 事件无法及时处理。
  * `setImmediate()` 的回调在下一轮事件循环执行，相对来说更“温和”，不会阻塞当前的 I/O 操作。

### 宏任务（Macrotask）

宏任务主要包括以下几种：

* **I/O 操作**：如文件读写、网络请求等。
* **计时器**：`setTimeout` 和 `setInterval` 等。
* **UI 渲染**：虽然在 Node.js 环境中不适用，但在浏览器环境中是宏任务的一部分。
* **`process.nextTick()`**：尽管其行为类似于微任务，但根据 Node.js 文档，它被分类为一个特殊的宏任务队列。

### 微任务（Microtask）

微任务包括但不限于：

* **`Promise`**：当 Promise 被解析后，它的回调函数会进入微任务队列。
* **`MutationObserver`**：用于观察 DOM 变化的 API，主要在浏览器环境中使用。
* **`process.nextTick()`**：在 Node.js 中，`process.nextTick()` 实际上会在当前操作完成后立即执行，优先于所有其他微任务。

```javascript
console.log('Start');

setTimeout(() => {
  console.log('Timer 1');
}, 0);

setImmediate(() => {
  console.log('Immediate');
});

process.nextTick(() => {
  console.log('Next tick');
});

console.log('End');
```

```javascript
输出顺序
Start
End
Next tick
Timer 1
Immediate
```

在 Node.js 中，错误处理是开发中至关重要的部分。不同类型的错误（同步错误和异步错误）需要不同的处理方式。以下是详细的错误处理方法：

***

### **错误处理**

#### **1. 同步错误处理**

同步代码的错误可以直接使用 `try...catch` 捕获。例如：

```javascript
try {
  let data = JSON.parse('invalid json'); // 解析错误
  console.log(data);
} catch (error) {
  console.error("捕获到同步错误:", error.message);
}
```

* **特点**：
  * 代码在 `try` 语句块中执行，如果发生错误会立即抛出，并被 `catch` 捕获。
  * 适用于**同步代码**，不能捕获**异步操作中的错误**。

***

#### **2. 异步错误处理**

在 Node.js 中，大部分操作（如文件 I/O、网络请求等）都是异步的。异步错误不能直接用 `try...catch` 处理，而需要根据不同的编程模式来捕获。

#### **2.1 回调模式（Error-First Callback）**

传统的 Node.js 异步 API 采用**错误优先回调**模式，即回调函数的**第一个参数是错误对象**，如果没有错误，则该参数为 `null`。

```javascript
const fs = require('fs');

fs.readFile('nonexistent.txt', 'utf8', (err, data) => {
  if (err) {
    console.error("读取文件出错:", err.message);
    return;
  }
  console.log("文件内容:", data);
});
```

* **优点**：适用于**回调风格**的 API，可确保错误不会被忽略。
* **缺点**：容易出现**回调地狱**（回调嵌套过深）。

***

#### **2.2 Promise 处理异步错误**

使用 `Promise` 的 `.catch()` 方法捕获错误：

```javascript
const fs = require('fs').promises;

fs.readFile('nonexistent.txt', 'utf8')
  .then(data => console.log("文件内容:", data))
  .catch(err => console.error("读取文件出错:", err.message));
```

***

#### **2.3 async/await 结合 try...catch**

在 `async/await` 中，可以使用 `try...catch` 捕获异步错误：

```javascript
const fs = require('fs').promises;

async function readFileAsync() {
  try {
    let data = await fs.readFile('nonexistent.txt', 'utf8');
    console.log("文件内容:", data);
  } catch (err) {
    console.error("读取文件出错:", err.message);
  }
}

readFileAsync();
```

* **优点**：
  * 代码结构更加清晰，避免回调地狱问题。
  * 易于维护，类似于同步代码的错误处理方式。

***

#### **3. 事件触发错误（EventEmitter）**

Node.js 使用 `EventEmitter` 处理事件，有些错误会通过 `'error'` 事件触发。例如：

```javascript
const EventEmitter = require('events');
const emitter = new EventEmitter();

emitter.on('error', (err) => {
  console.error("捕获到事件错误:", err.message);
});

// 触发错误
emitter.emit('error', new Error("Something went wrong"));
```

* **注意**：如果 `error` 事件没有监听器，Node.js 进程会崩溃。因此，建议始终**监听 `'error'` 事件**。

***

#### **4. 全局错误处理**

#### **4.1 捕获未处理的同步错误**

```javascript
process.on('uncaughtException', (err) => {
  console.error("未捕获的异常:", err.message);
  process.exit(1); // 退出进程，避免不稳定状态
});
```

* **注意**：仅用于**日志记录**或**应急处理**，不推荐在生产环境下依赖它。

#### **4.2 捕获未处理的 Promise 错误**

```javascript
process.on('unhandledRejection', (reason, promise) => {
  console.error("未处理的 Promise 拒绝:", reason);
});
```

* `unhandledRejection` 监听的是所有**未处理的 Promise 错误**，可以用于**调试和日志记录**。

***

### **总结**

| **错误类型**        | **处理方式**                                                                                 |
| --------------- | ---------------------------------------------------------------------------------------- |
| **同步错误**        | 使用 `try...catch` 捕获                                                                      |
| **回调模式**        | 通过 `if (err) {...}` 处理回调中的错误                                                             |
| **Promise**     | 使用 `.catch()` 处理错误                                                                       |
| **async/await** | 使用 `try...catch` 捕获异步错误                                                                  |
| **事件错误**        | 监听 `'error'` 事件，如 `eventEmitter.on('error', handler)`                                    |
| **全局错误**        | `process.on('uncaughtException', handler)` 和 `process.on('unhandledRejection', handler)` |
