# Eventloop

单线程：Node.js 运行在一个单线程上，这意味着所有 JavaScript 代码都在同一个线程中执行。

异步 I/O：Node.js 使用异步 I/O 操作来处理耗时任务，如文件系统操作、网络请求等，这些操作不会阻塞主线程。

事件驱动：Node.js 使用事件驱动模型，当某个事件发生时，会触发相应的回调函数。

事件循环的详细流程

1 初始化：

Node.js 启动时，首先执行主模块中的同步代码。

然后进入事件循环。

1. Timers：执行 setTimeout 和 setInterval 设置的回调函数。
2. Pending Callbacks：执行一些系统操作的回调函数，如 TCP 错误。
3. Idle, Prepare：内部使用。这个阶段通常用于内部统计和性能分析。
4. Poll：检索新的 I/O 事件；执行与 I/O 相关的回调。
5. Check：执行 setImmediate 设置的回调函数。
6. Close Callbacks：执行一些关闭回调，例如 socket.on('close', ...)。

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
