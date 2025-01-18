# HTTP

### **response**

对象用于向客户端发送回复，它是一个可写流（Writable Stream）。你可以通过以下几种方式设置响应：

```
response.statusCode = 200; // 默认是 200 OK

response.setHeader('Content-Type', 'text/plain');

response.end(); // 或者 response.end('Some data');
```

response.write 方法用于向 HTTP 响应中写入一部分数据（chunk）

response.end 方法用于结束 HTTP 响应

```
response.writeHead(200, { 'Content-Type': 'application/json' });
response.end(JSON.stringify({ message: 'Hello World' }));
```

### **创建 HTTP 服务器**

```javascript
const http = require('node:http');

const server = http.createServer((request, response) => {
    if (request.method === 'POST' && request.url === '/echo') {
        request.on('error', err => {
            console.error(err);
            response.statusCode = 400;
            response.end();
        });

        response.on('error', err => {
            console.error(err);
        });

        request.pipe(response);
    } else {
        response.statusCode = 404;
        response.end();
    }
});

server.listen(8080, () => {
    console.log('Echo server is running on port 8080');
});

```

### **URL**

提供了一个 url 模块与 querystring (查询字符串)模块

```
querystring.parse('foo=bar&baz=qux&baz=quux&corge')
输出：
{
  foo: 'bar',
  baz: ['qux', 'quux'],
  corge: ''
}

querystring.stringify({name: 'whitemu', sex: [ 'man', 'women' ] });
// returns
'name=whitemu&sex=man&sex=women'
```

### **向其他网站求数据**

可以用 http.get(options\[, callback])

```javascript
http.get('http://www.google.com', (res) => {
  console.log(`状态码: ${res.statusCode}`);
  res.on('data', (chunk) => {
    console.log(`数据: ${chunk}`);
  });
});

```

request 方法的 options 参数，可以是一个对象，也可以是一个字符串。如果是字符串，就表示这是一个 URL，

```javascript
const http = require('http');

let options = {
  hostname: 'www.example.com',
  port: 80,
  path: '/',
  method: 'GET'
};

const req = http.request(options, (res) => {
  console.log(`STATUS: ${res.statusCode}`); // 返回状态码
  console.log(`HEADERS: ${JSON.stringify(res.headers, null, 4)}`); // 返回头部
  res.setEncoding('utf8'); // 设置编码
  res.on('data', (chunk) => { // 监听 'data' 事件
    console.log(`主体: ${chunk}`);
  });
});

req.end(); // end方法结束请求

```

#### 和 `http.request` 的区别

1. 简化:
   * `http.get` 是 `http.request` 的封装，专门用于 `GET` 请求。
   * 不需要调用 `req.end()`，因为它自动完成。
2. 功能限制:
   * `http.get` 仅支持 `GET` 方法。
   * 如果需要发送数据（如 `POST` 请求），必须使用 `http.request`。

**特点**

* 低级别 API：提供了更底层的 HTTP 客户端和服务器实现，适合需要高度定制化的情况。
* 事件驱动：采用事件驱动的方式处理请求和响应，对于流式数据处理非常友好。
* 无自动解析：不会自动解析响应体，需要自行处理响应流并解析内容。
* 完全控制：给予开发者对请求和响应的完全控制权，适用于构建自定义 HTTP 客户端或服务器。
* 性能优化：由于是 Node.js 的一部分，通常会有更好的性能表现，特别是在高并发场景下。

### 1. Axios

**特点**

* 基于 Promise：所有请求都是基于 Promise 的，可以轻松地与 async/await 结合使用。
* 自动转换 JSON：默认情况下会自动解析 JSON 响应。
* 请求/响应拦截器：允许你在请求发送之前或响应接收之后执行逻辑。
* 取消请求：支持通过 CancelToken 或 AbortController 取消正在进行的请求。
* 全局配置：可以通过创建 Axios 实例来设置全局默认配置。
* 浏览器和 Node.js 兼容：可以在浏览器和服务器环境中使用。

### 2. node-fetch

**特点**

* 遵循 Fetch API 标准：API 与浏览器中的 Fetch API 高度一致，使得跨环境开发更加容易。
* 轻量级：相比 Axios 更加精简，没有内置的请求/响应拦截功能。
* 不自动转换 JSON：不会自动解析 JSON 响应，需要手动调用 .json() 方法。
* 仅限于 HTTP 请求：主要用于发起 HTTP 请求，不像 Axios 提供那么多高级特性。
