## HTTP网络通信协议

TIP/IP：传输控制协议/网际协议 有很多子协议 http ftp https

HTTP：超文本传输协议 简单说就是传输html数据的，HTTP协议是无连接，无状态的，客户端发出请求，连接服务器，得到响应，立即断开。

请求request-响应response：必须由客户端主动发出请求，服务器才能产生响应，一般来说，请求是客户端向服务器端发送数据，而响应是服务器端向客户端发送数据

如果请求的是一个静态资源，服务端会直接响应数据到客户端，如果请求的是一个动态资源，比如java,php,asp，服务器端会先在服务器端执行完毕这些动态内容，然后把执行的结果响应到客户端。

### HTTP请求的三部分组成：

请求行：请求方法（GET POST PUT DELETE）请求的URI 协议和版本 例如http1.1

消息报头：客户端的一些相关信息，例如语言，浏览器，操作系统，压缩方式......

请求正文：GET请求没有请求正文，POST的请求正文存储了发送的数据

### 响应状态码：

200 请求响应

304 从浏览器本地缓存读取数据 (Last Modified)

404 资源找不到

405 无法请求对应的get和post处理程序

429 limit

401 unthrization

403 forbidden

500 服务器程序运行错误

301 永久重定向

302 暂时重定向

### 增删改查的四大操作（CRUD）：restful风格

GET 查询

POST 增加

PUT 修改

DELETE 删除

### WebSocket

WebSocket是一种在单个TCP连接上进行全双工通信的协议。它被设计用于替代传统的HTTP请求/响应模型，以实现更高效、实时的通信。WebSocket使得服务器和客户端可以在建立连接后，双向发送数据，而无需每次发送数据前进行握手，这大大提高了数据传输的效率和速度。

#### WebSocket与HTTP的区别

- **连接方式**：HTTP是基于请求/响应的模型，每次通信都需要建立新的连接（无状态的交互），而WebSocket在连接建立后，可以持续进行双向通信。
- **数据传输**：HTTP传输数据时，需要封装成HTTP请求或响应，包含额外的头部信息，而WebSocket传输的数据更轻量(WebSocket 消息头较小)，没有HTTP头部的开销。
- **实时性**：由于WebSocket的持续连接特性，它能够实现真正的实时通信，而HTTP则需要轮询或长轮询来模拟实时性，效率较低。
- **安全性**：HTTP 可以通过 HTTPS 提供加密传输，确保数据的安全性。WebSocket 可以通过 WSS（WebSocket Secure）提供加密传输，确保数据的安全性。

#### WebSocket的工作原理

WebSocket的工作流程如下：

1. **握手**：客户端通过HTTP发起一个WebSocket连接请求，服务器响应并完成握手过程。
2. **连接建立**：握手成功后，客户端和服务器之间的连接建立，此时可以开始双向通信。
3. **数据传输**：客户端和服务器可以发送任意类型的数据，包括文本和二进制数据。
4. **关闭连接**：任何一方都可以发送关闭帧来关闭连接，另一方接收到关闭帧后，会进行相应的处理并关闭连接。

#### WebSocket 升级握手过程

1. **客户端发送升级请求**
2. **服务器响应升级请求**
3. **连接升级为 WebSocket**

##### 1. 客户端发送升级请求

客户端通过发送一个 HTTP 请求来请求将连接升级为 WebSocket 协议。这个请求包含了一些特定的头部信息，用于标识这是一个 WebSocket 升级请求。

**请求头部**

- `Upgrade: websocket` — 表示请求将连接升级为 WebSocket 协议。
- `Connection: Upgrade` — 表示这是一个升级请求。
- `Sec-WebSocket-Key` — 一个由客户端生成的 Base64 编码的随机字符串，用于验证握手过程。
- `Sec-WebSocket-Version` — 客户端支持的 WebSocket 协议版本，通常是 13。
- `Sec-WebSocket-Protocol`（可选）— 客户端支持的子协议列表。

##### 2. 服务器响应升级请求

服务器收到客户端的升级请求后，会验证请求的合法性，并生成一个响应。如果验证成功，服务器会发送一个 `101 Switching Protocols` 状态码的响应，表示连接已经成功升级为 WebSocket 协议。

**响应头部**

- `Upgrade: websocket` — 确认连接已升级为 WebSocket 协议。
- `Connection: Upgrade` — 确认这是一个升级请求。
- `Sec-WebSocket-Accept` — 服务器根据客户端提供的 `Sec-WebSocket-Key` 计算出的一个 Base64 编码的字符串，用于验证握手过程。
- `Sec-WebSocket-Protocol`（可选）— 服务器选择的子协议。

#### 加密

使用 WSS (WebSocket Secure)：WSS 是基于 TLS/SSL 的 WebSocket 协议，确保数据在传输过程中加密。客户端和服务器之间的所有通信都经过加密，防止数据被窃听或篡改。

