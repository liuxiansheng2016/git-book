# Node

Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行时环境，它允许开发者使用 JavaScript 在服务器端编写可扩展的网络应用。Node.js 的主要特点包括：

1. 非阻塞 I/O：\
   Node.js 使用事件驱动、非阻塞 I/O 模型，这使得它非常适合处理大量并发连接。Node.js 可以高效地处理 I/O 密集型任务，如网络请求、文件读写等。
2. 单线程模型：\
   Node.js 是单线程的，这意味着它在一个进程中只运行一个主线程。这种设计简化了编程模型，并且避免了多线程带来的复杂性问题。
3. 事件循环（Event Loop）：\
   Node.js 的核心是事件循环，它负责管理和调度异步操作。事件循环确保了非阻塞 I/O 操作的高效执行。
4. 模块系统：\
   Node.js 有一个强大的模块系统，允许开发者将代码组织成模块。每个文件都是一个模块，可以通过 require 函数导入其他模块。
5. 丰富的标准库：\
   Node.js 提供了大量的内置模块，如 http, fs, path, crypto 等，这些模块使得开发网络应用变得更加容易。
6. npm（Node Package Manager）：\
   npm 是 Node.js 的包管理器，它提供了大量的第三方库和工具，极大地丰富了 Node.js 生态系统。

其他特性

Stream 是一个抽象接口，Node 中有很多对象实现了这个接口。例如，对 http 服务器发起请求的 request 对象就是一个 Stream，还有 stdout（标准输出）。

util 是一个 Node.js 核心模块，提供常用函数的集合，用于弥补核心 JavaScript 的功能 过于精简的不足。

events 模块只提供了一个对象： events.EventEmitter。EventEmitter 的核心就是事件触发与事件监听器功能的封装。

你可以通过 require("events");来访问该模块。
