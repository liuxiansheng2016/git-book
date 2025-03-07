# Eventloop

单线程：Node.js 运行在一个单线程上，这意味着所有 JavaScript 代码都在同一个线程中执行。

异步 I/O：Node.js 使用异步 I/O 操作来处理耗时任务，如文件系统操作、网络请求等，这些操作不会阻塞主线程。

事件驱动：Node.js 使用事件驱动模型，当某个事件发生时，会触发相应的回调函数。

### 事件循环的详细流程

初始化：

Node.js 启动时，首先执行主模块中的同步代码。

**事件循环的 6 个阶段**

Node.js 的事件循环由 **libuv** 负责管理，主要分为以下 6 个阶段（按照执行顺序）：

<table><thead><tr><th>阶段</th><th width="321">说明</th><th>主要执行任务</th></tr></thead><tbody><tr><td><strong>1. timers</strong></td><td>处理 <code>setTimeout</code> 和 <code>setInterval</code> 的回调</td><td>触发已到期的定时器</td></tr><tr><td><strong>2. I/O callbacks</strong></td><td>处理上一轮循环中延迟的 I/O 回调</td><td>读取文件、网络请求等</td></tr><tr><td><strong>3. idle, prepare</strong></td><td>内部使用，Node.js 内部调用</td><td>很少涉及</td></tr><tr><td><strong>4. poll</strong></td><td>处理新的 I/O 事件</td><td>主要的 I/O 处理阶段</td></tr><tr><td><strong>5. check</strong></td><td>处理 <code>setImmediate</code> 回调</td><td><code>setImmediate()</code> 回调会在这里执行</td></tr><tr><td><strong>6. close callbacks</strong></td><td>处理 <code>close</code> 事件</td><td>关闭文件描述符、socket 连接等</td></tr></tbody></table>

***

### **事件循环的执行流程**

**同步代码 → 异步回调任务**

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
