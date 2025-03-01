# Eventloop

单线程：Node.js 运行在一个单线程上，这意味着所有 JavaScript 代码都在同一个线程中执行。

异步 I/O：Node.js 使用异步 I/O 操作来处理耗时任务，如文件系统操作、网络请求等，这些操作不会阻塞主线程。

事件驱动：Node.js 使用事件驱动模型，当某个事件发生时，会触发相应的回调函数。

事件循环的详细流程

1 初始化：

Node.js 启动时，首先执行主模块中的同步代码。

### 事件循环。

1. Timers：执行 setTimeout 和 setInterval 设置的回调函数。
2. Pending Callbacks：执行一些系统操作的回调函数，如 TCP 错误。
3. Idle, Prepare：内部使用。这个阶段通常用于内部统计和性能分析。
4. Poll：检索新的 I/O 事件；执行与 I/O 相关的回调。
5. Check：执行 setImmediate 设置的回调函数。
6. Close Callbacks：执行一些关闭回调，例如 socket.on('close', ...)。

### `setImmediate` 和 `setTimeout`

* 在 **主线程** 中，`setImmediate` 和 `setTimeout(..., 0)` 的执行顺序不确定，取决于事件循环的调度。
* 在 **I/O 回调** 中，`setImmediate` **总是先执行**，因为检查阶段（Check phase）紧跟在 I/O 之后，而定时器阶段（Timers phase）在后面。

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
