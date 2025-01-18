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
