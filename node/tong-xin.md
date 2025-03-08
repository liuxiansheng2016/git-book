# 通信

## **进程间通信 (IPC)**

1. 使用 child\_process 模块

child\_process 模块允许你创建子进程，并通过标准输入（stdin）、标准输出（stdout）和标准错误（stderr）与子进程通信。

父进程代码

```javascript
const { spawn } = require('child_process');

const child = spawn('node', ['child.js']);

child.stdout.on('data', (data) => {
  console.log(`Received from child: ${data.toString()}`);
});

child.stderr.on('data', (data) => {
  console.error(`Child stderr: ${data.toString()}`);
});

child.on('close', (code) => {
  console.log(`Child process exited with code ${code}`);
});

// 向子进程发送数据
child.stdin.write('Hello from parent\n');
child.stdin.end();
```

子进程代码 (child.js)

```javascript
process.stdin.setEncoding('utf8');

process.stdin.on('data', (data) => {
  console.log(`Received from parent: ${data.toString().trim()}`);
  process.stdout.write('Hello from child\n');
});

process.stdin.on('end', () => {
  process.stdout.write('Parent closed stdin\n');
  process.exit(0);
});
```



2. 使用 IPC（Inter-Process Communication）

child\_process 模块还支持通过 IPC 通道进行通信。这适用于 fork 创建的子进程。 父进程代码 (parent.js)

```
const { fork } = require('child_process');

const child = fork('child.js');

child.on('message', (msg) => {
  console.log(`Received from child: ${msg}`);
});

// 向子进程发送消息
child.send('Hello from parent');
```

子进程代码 (child.js)

```
process.on('message', (msg) => {
  console.log(`Received from parent: ${msg}`);
  process.send('Hello from child');
});
```

## **使用 SharedArrayBuffer 和 Atomics**

在 Node.js 10.5.0 及以上版本中，可以使用 SharedArrayBuffer 和 Atomics 进行共享内存通信。这种方式适用于需要高性能、低延迟的场景。

父进程 (parent.js)

```
const { Worker } = require('worker_threads');
const { SharedArrayBuffer } = require('util');

const buffer = new SharedArrayBuffer(1824);
const int32Array = new Int32Array(buffer);

int32Array[0] = 0; // 初始化

const worker = new Worker('child.js', { workerData: { buffer } });

worker.on('message', (msg) => {
  console.log(`Received from child: ${msg}`);
});

// 向子进程发送消息
worker.postMessage('Hello from parent');
```

子进程 (child.js)

```
const { workerData, parentPort } = require('worker_threads');

const int32Array = new Int32Array(workerData.buffer);

parentPort.on('message', (msg) => {
  console.log(`Received from parent: ${msg}`);
  int32Array[0] = 1; // 更新共享内存
  parentPort.postMessage('Hello from child');
});
```

## **使用 socket 通信**

可以通过 TCP 或 UDP 套接字进行进程间通信。这种方式适用于跨机器的进程通信。

```
服务器进程 (server.js)
const net = require('net');

const server = net.createServer((socket) => {
  socket.on('data', (data) => {
    console.log(`Received from client: ${data.toString()}`);
    socket.write('Hello from server\n');
  });

  socket.on('end', () => {
    console.log('Client disconnected');
  });
});

server.listen(3000, () => {
  console.log('Server listening on port 3000');
});
```

```
客户端进程 (client.js)
const net = require('net');

const client = net.connect({ port: 3000 }, () => {
  console.log('Connected to server');
  client.write('Hello from client\n');
});

client.on('data', (data) => {
  console.log(`Received from server: ${data.toString()}`);
  client.end();
});

client.on('end', () => {
  console.log('Disconnected from server');
});
```

## **使用 Redis 或 MQ（消息队列）**

可以使用 Redis 或者其他消息队列（如 RabbitMQ、Kafka 等）进行进程间通信。这种方式适用于分布式系统和微服务架构。

```
生产者进程 (producer.js)
const redis = require('redis');
const client = redis.createClient();

client.on('error', (err) => {
  console.error('Redis error:', err);
});

client.publish('channel', 'Hello from producer');
client.quit();
```

```
消费者进程 (consumer.js)
const redis = require('redis');
const subscriber = redis.createClient();

subscriber.on('error', (err) => {
  console.error('Redis error:', err);
});

subscriber.subscribe('channel');

subscriber.on('message', (channel, message) => {
  console.log(`Received from ${channel}: ${message}`);
  subscriber.unsubscribe();
  subscriber.quit();
});
```

## EventEmitter&#x20;

是 Node.js 中处理事件驱动编程的核心模块。通过注册事件监听器和发射事件，可以构建灵活和响应式的应用程序。

```javascript
const EventEmitter = require('events');

class MyEmitter extends EventEmitter {}
const myEmitter = new MyEmitter();

// 监听事件
myEmitter.on('message', (msg) => {
  console.log(`接收到消息: ${msg}`);
});

// 触发事件
myEmitter.emit('message', 'Hello, EventEmitter!');


```

默认情况下，EventEmitter 实例的最大监听器数量是 10。如果超过这个数量，Node.js 会发出警告。可以通过 setMaxListeners 方法更改最大监听器数量。

## **网络通信**

Node.js 提供了多种模块来实现网络通信，包括 HTTP、HTTPS、TCP 和 UDP 等。

网络通信：使用 http, https, net, dgram 等模块实现基于 TCP 和 UDP 的网络通信。

WebSocket 通信：使用 ws 库实现全双工的 WebSocket 通信。



## **使用 WebSocket（socket.io）实现实时通信**

WebSocket 允许**全双工通信**，适用于聊天应用、在线游戏、实时通知等场景。`socket.io` 是 WebSocket 的封装，支持**自动降级到 HTTP 长轮询**，并提供更友好的 API。

***

#### **1. 安装 socket.io**

```sh
npm install socket.io express
```

***

#### **2. 在 Node.js（Express）中创建 WebSocket 服务器**

```javascript
const express = require('express');
const http = require('http');
const { Server } = require('socket.io');

const app = express();
const server = http.createServer(app); // 创建 HTTP 服务器
const io = new Server(server, { cors: { origin: '*' } }); // 允许跨域

io.on('connection', (socket) => {
  console.log(`用户连接：${socket.id}`);

  // 监听消息
  socket.on('chat message', (msg) => {
    console.log(`消息: ${msg}`);
    io.emit('chat message', msg); // 广播消息给所有客户端
  });

  // 用户断开连接
  socket.on('disconnect', () => {
    console.log(`用户断开连接：${socket.id}`);
  });
});

server.listen(3000, () => {
  console.log('服务器运行在 http://localhost:3000');
});
```

***

#### **3. 在客户端（HTML）中连接 WebSocket**

```html
<!DOCTYPE html>
<html lang="zh">
<head>
  <meta charset="UTF-8">
  <title>WebSocket 聊天</title>
  <script src="https://cdn.socket.io/4.5.4/socket.io.min.js"></script>
</head>
<body>
  <input id="message" type="text" placeholder="输入消息">
  <button onclick="sendMessage()">发送</button>
  <ul id="messages"></ul>

  <script>
    const socket = io('http://localhost:3000'); // 连接 WebSocket 服务器

    socket.on('chat message', (msg) => {
      const li = document.createElement('li');
      li.textContent = msg;
      document.getElementById('messages').appendChild(li);
    });

    function sendMessage() {
      const message = document.getElementById('message').value;
      socket.emit('chat message', message); // 发送消息
      document.getElementById('message').value = ''; // 清空输入框
    }
  </script>
</body>
</html>
```

***

#### **4. 监听特定房间（分组）**

如果要实现私聊或特定房间通信，可以使用 `socket.join(room)`：

```javascript
io.on('connection', (socket) => {
  socket.on('join room', (room) => {
    socket.join(room);
    console.log(`${socket.id} 加入房间 ${room}`);
  });

  socket.on('message', ({ room, message }) => {
    io.to(room).emit('message', message); // 仅发送给该房间的用户
  });
});
```

***

#### **5. 服务器推送（如实时通知）**

```javascript
setInterval(() => {
  io.emit('notification', { message: '服务器时间 ' + new Date().toLocaleTimeString() });
}, 5000);
```

客户端监听：

```javascript
socket.on('notification', (data) => {
  console.log(data.message);
});
```

***

#### **总结**

* `socket.io` 封装了 WebSocket，支持自动降级（如 HTTP 轮询）。
* `io.emit` 广播消息，`socket.emit` 发送给特定用户，`io.to(room).emit` 发送给特定房间。
* 适用于**聊天、实时通知、多人协作、在线游戏**等应用。

你需要 WebSocket 的更多高级功能，比如身份验证、断线重连吗？
