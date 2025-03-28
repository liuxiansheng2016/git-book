# Http

### Http

[参考链接](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CORS)\
[参考链接](https://www.bbsmax.com/A/LPdooLRwd3/)

超文本传输协议，是一种基于请求与响应的无状态应用层协议，基于 TCP/IP 传输数据。

HTTP 是无状态，有会话的。HTTP 是无状态的：在同一个连接中，两个执行成功的请求之间是没有关系的。这就带来了一个问题，用户没有办法在同一个网站中进行连续的交互，比如在一个电商网站里，用户把某个商品加入到购物车，切换一个页面后再次添加了商品，这两次添加商品的请求之间没有关联，浏览器无法知道用户最终选择了哪些商品。而使用 HTTP 的头部扩展，HTTP Cookies 就可以解决这个问题。把 Cookies 添加到头部中，创建一个会话让每次请求都能共享相同的上下文信息，达成相同的状态。

注意，HTTP 本质是无状态的，使用 Cookies 可以创建有状态的会话。

### Https

是以安全为目标的 HTTP 通道，是 HTTP 的安全版。Https 的安全基础是 SSL。SSL 协议位于 TCP/IP 协议与各种应用层协议之间，为数据通讯提供安全支持。

SSL（Secure Sockets Layer）是最早的安全协议之一，由 Netscape 在 1994 年推出。TLS 是 SSL 的后续版本，提供了更强大的安全性和更好的性能。SSL/TLS 的主要作用是在客户端和服务器之间建立加密通道，保护数据的安全性。

[参考链接](https://http2.github.io/faq/)\
[参考链接](https://mp.weixin.qq.com/s/i8qVR-b1MLQ_UI3NG7aPXw)

1. 认证用户和服务器，确保数据发送到正确的客户机和服务器
2. 加密数据以防止数据中途被窃取
3. 维护数据的完整性，确保数据在传输过程中不被改变

### 通信过程

1. 客户使用 https 的 URL 访问 Web 服务器，要求与 Web 服务器建立 SSL 连接。
2. Web 服务器收到客户端请求后，会将网站的证书信息（证书中包含公钥）传送一份给客户端。
3. 客户端的浏览器与 Web 服务器开始协商 SSL/TLS 连接的安全等级，也就是信息加密的等级。
4. 客户端的浏览器根据双方同意的安全等级，建立会话密钥，然后利用网站的公钥将会话密钥加密，并传送给网站。
5. Web 服务器利用自己的私钥解密出会话密钥。
6. Web 服务器利用会话密钥加密与客户端之间的通信。

### HTTPS 传输过程的加密

在 HTTPS 传输过程中，主要使用了以下加密方法：

* 非对称加密：用于密钥交换和身份验证
* 对称加密：用于高效地加密和解密实际传输的数据
* 消息认证码（MAC）：用于数据完整性验证
* 数字签名：用于服务器身份验证

对称加密是一种加密技术，其中使用同一个密钥进行数据的加密和解密。

1. 速度快：对称加密算法（如 AES）的加解密速度非常快，适合大量数据的加密和解密
2. 资源消耗低：对称加密算法的计算复杂度较低，对计算资源的消耗较小

非对称加密的缺点：

1. 效率慢
2. 服务器端只能用私钥加密，但黑客可能获取到公钥，不能保证服务器端的数据安全

### 8种方法

* GET：请求指定的信息
* POST：提交数据进行数据处理。数据包含在请求体
* PUT：从客户端向服务器传送的数据取代指定的文档内容
* DELETE：请求服务器删除指定的页面
* HEAD：类似 GET，但响应中没有具体的内容
* CONNECT
* TRACE
* OPTIONS：检查服务器那边是否允许自己发请求

### POST 和 PUT 的区别

PUT 具有幂等性，多次提交相同请求资源状态不会改变，一般用来更新资源。POST 一般用来创建数据或者触发服务器上的某些操作。

### 简单请求（Simple Request）

使用 GET、POST、HEAD 方法之一。POST 请求的 Content-Type 限于三个值之一：application/x-www-form-urlencoded、multipart/form-data、text/plain。

### 预检请求（Preflight Request）

在跨域资源共享（CORS）中，\*\*预检请求（Preflight Request）\*\*是由浏览器自动发起的一种 HTTP 请求。当客户端尝试对跨域资源执行可能对服务器数据产生副作用的操作（如使用 `PUT`、`DELETE` 等方法，或发送自定义头部）时，浏览器会在实际请求之前，先使用 `OPTIONS` 方法发起预检请求，以确认服务器是否允许该实际请求。只包含头部信息，不包含请求体。

**预检请求的触发条件：** 使用除了 GET、POST、HEAD 之外的非简单方法。POST 请求的 Content-Type 不是上述三个值之一。包含自定义请求头（非简单请求头）。

**预检请求的作用：**

* **验证权限：** 确保服务器允许特定的跨域请求，防止未经授权的操作。
* **保护数据：** 避免跨域请求对服务器的数据产生未预期的影响。

**预检请求的流程：**

1. **浏览器发送 `OPTIONS` 请求：** 包含以下头部：
   * `Access-Control-Request-Method`：表示实际请求将使用的方法。
   * `Access-Control-Request-Headers`：表示实际请求将包含的自定义头部。
   * `Origin`：表示请求的源。
2. **服务器响应：** 如果允许该跨域请求，服务器会返回状态码 `200 OK`，并在响应头中包含：
   * `Access-Control-Allow-Origin`：允许的源。
   * `Access-Control-Allow-Methods`：允许的方法。
   * `Access-Control-Allow-Headers`：允许的头部。
   * `Access-Control-Max-Age`：预检请求的结果可缓存的时间。
3. **浏览器根据响应决定是否发送实际请求：** 如果服务器允许，浏览器会继续发送实际请求；否则，阻止请求并抛出错误。

**示例：** 假设客户端需要对 `https://api.example.com/data` 资源发送一个包含自定义头部的 `DELETE` 请求。浏览器会先发起如下的预检请求：

```http
OPTIONS /data HTTP/1.1
Host: api.example.com
Origin: https://client.example.com
Access-Control-Request-Method: DELETE
Access-Control-Request-Headers: X-Custom-Header
```

如果服务器允许该操作，会返回：

```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://client.example.com
Access-Control-Allow-Methods: DELETE
Access-Control-Allow-Headers: X-Custom-Header
Access-Control-Max-Age: 86400
```

收到上述响应后，浏览器会继续发送实际的 `DELETE` 请求。

通过预检请求机制，CORS 能有效地保护服务器资源，确保跨域请求的安全性。



### 响应状态码：

1开头：请求被接受，需要继续处理

200 请求响应

301 永久重定向

302 暂时重定向

304 从浏览器本地缓存读取数据 (Last Modified)

404 资源找不到

405 无法请求对应的get和post处理程序

429 limit

401 unthrization

403 forbidden

500 服务器程序运行错误

#### 增删改查的四大操作（CRUD）：restful风格

GET 查询

POST 增加

PUT 修改

DELETE 删除

### Restful

是一种 API 设计风格：

特点：

1. 根据 URL 知道需要什么资源，每个 API 端点都应该代表一个具体的资源集合（如 /users 或 /orders）
2. 根据 HTTP method 就知道什么做什么动作
3. 根据状态码知道动作的结果
4. 使用 swagger 等提供详细的 API 文档

### HTTP/3

HTTP/3 是 HTTP/2 的继承者。截至 2020 年 9 月，所有主要浏览器都对 HTTP/3 提供了实验性支持，一些 CDN 也支持它。性能是 HTTP/3 相对于 HTTP/2 的主要优势。具体来说，HTTP/3 消除了连接级别的队头阻塞并减少了连接建立时间。

1. 连接迁移：QUIC 允许在网络切换或 IP 变更时迁移连接，而不需要重新建立新的连接，从而避免了连接中断和数据丢失的问题
2. 可靠性流控制：QUIC 在每个流上都实现了可靠的流控制机制，可以根据发送方和接收方的负载情况动态调整数据发送速率，从而优化传输效率和可靠性
3. 数据重传：QUIC 中每个数据包都带有唯一标识符（Packet Number），接收方可以根据这个标识符进行数据包的确认和重传，以保证数据传输的可靠性
4. 拥塞控制：QUIC 采用了基于 TCP 的拥塞控制机制，可以根据网络拥塞程度自适应调整发送速率，以避免网络拥塞和丢包等问题

HTTP/2 是一个二进制协议而不是文本。HTTP/2 引入了多路复用，该功能允许使用单个连接同时传输多个数据流。但是，对于 HTTP/2，单个丢弃的数据包会阻塞连接上的所有流（这种现象称为队头阻塞）。使用 HTTP/3，丢弃的数据包只会阻塞单个流。这种改进主要是 HTTP/3 使用 UDP（HTTP/3 通过 QUIC 使用 UDP）而不是 TCP 的结果。这使得 HTTP/3 对于通过拥塞或有损网络传输数据特别有用。

减少连接建立时间。HTTP/3 使用 TLS 1.3，因此共享其性能优势：建立新连接只需要一次往返，恢复现有连接不需要任何往返。

### 多路复用

为了避免每次请求就建立一次连接。采用了 keep-alive。一定时间内让建立一次连接可以多次请求数据。在 HTTP/1 传输基于文本，所以所有的数据必须按顺序传输。HTTP/2 采用二进制数据帧和流的概念。帧对数据进行顺序标记。浏览器接收到数据后按照序列对数据进行合并，不会出现合并数据错乱。同样是因为有了序列，服务器就可以并行的传输数据，这就是流所做的事情。

### HTTP 连接建立流程

[参考链接](https://www.jianshu.com/p/25313dbd2e46)

在浏览器中输入网址并按下回车后，HTTP连接的建立过程大致包括以下步骤：

1. **DNS解析**：浏览器首先需要将域名转换为服务器的IP地址。它会先检查本地缓存，如果未找到，则向DNS服务器查询，最终获取目标IP地址。
2. **建立TCP连接（三次握手）**：获取IP地址后，浏览器与服务器通过TCP协议建立连接，具体过程如下：
   * **第一次握手**：客户端发送一个带有SYN标志的数据包给服务器，表示请求建立连接。
   * **第二次握手**：服务器收到后，回复一个同时带有SYN和ACK标志的数据包，表示同意连接。
   * **第三次握手**：客户端再发送一个带有ACK标志的数据包，确认连接建立。
3. **发送HTTP请求**：TCP连接建立后，浏览器按照HTTP协议格式，向服务器发送请求报文，包含请求的方法、路径、头部信息等。
4. **服务器处理请求并响应**：服务器接收到请求后，处理相应的资源或数据，并将结果封装在HTTP响应报文中返回给浏览器。
5. **浏览器解析响应内容**：浏览器接收并解析服务器返回的内容（如HTML、CSS、JavaScript等），根据需要可能会发起更多的资源请求，直至页面完全加载。
6. **关闭连接（四次挥手）**：在HTTP/1.0中，每次请求响应后即关闭TCP连接；在HTTP/1.1中，默认启用持久连接（Keep-Alive），可以复用连接以减少开销。当连接不再需要时，双方通过四次挥手关闭连接：
   * **第一次挥手**：主动方发送FIN标志的数据包，表示终止发送数据。
   * **第二次挥手**：被动方收到后，回复ACK标志的数据包，确认收到。
   * **第三次挥手**：被动方发送FIN标志的数据包，表示也终止发送数据。
   * **第四次挥手**：主动方收到后，回复ACK标志的数据包，确认连接关闭。

需要注意的是，HTTPS在此基础上增加了SSL/TLS握手过程，以确保数据传输的安全性。

以上流程概述了从输入网址到页面呈现的主要步骤，涉及DNS解析、TCP连接、HTTP请求与响应以及连接的关闭等。

### HTTP 报文及其结构

* 请求行
* 请求头
* 空行
* 请求主体

请求行：由请求方法字段、URL 字段和 HTTP 协议版本字段组成，例如：GET /data/info.html HTTP/1.1

请求头：Accept, Cookie, Authorization, User-Agent, Host

[参考链接](https://segmentfault.com/a/1190000019788537)

Cookie 的 secure 属性为 true 时，表示创建的 cookie 只能在 HTTPS 连接中被浏览器传递到服务器端。

### HTTP 和 HTTPS 的区别

1. HTTPS 是加密传输协议，HTTP 是明文传输协议
2. HTTPS 需要用到 SSL 证书，而 HTTP 不用
3. HTTPS 比 HTTP 更加安全，对搜索引擎更友好
4. HTTPS 标准端口 443，HTTP 标准端口 80
5. HTTPS 基于传输层，HTTP 基于应用层
6. HTTPS 在浏览器显示绿色安全锁，HTTP 没有显示

#### SSL 握手过程

1. 协商加密套件：选择合适的加密算法和协议版本
2. 身份验证：验证服务器的身份
3. 密钥交换：安全地协商和交换会话密钥
4. 完整性验证：通过 Finished 消息确保握手过程的完整性

### 网络优化

* 优化 HTTP 请求
* 压缩资源
* 优化缓存策略
  * 浏览器缓存：设置合理的 HTTP 缓存头（如 Cache-Control、Expires），让浏览器缓存静态资源
  * CDN（内容分发网络）：使用 CDN 加速静态资源的加载，减少用户的访问延迟
  * Service Worker：使用 Service Worker 在客户端缓存资源，实现离线访问和快速加载
* 优化资源加载
  * 懒加载：使用懒加载技术，延迟加载非关键资源（如图片、视频），提高初始加载速度
  * 优先加载关键资源：使用 preload 和 prefetch 指令，提前加载关键资源
  * 异步加载 JavaScript：使用 async 和 defer 属性，确保 JavaScript 文件不会阻塞页面的渲染
* 优化服务器响应
  * 使用 HTTP/2：HTTP/2 支持多路复用，可以同时处理多个请求，减少延迟
  * 优化数据库查询：优化数据库索引和查询语句，减少数据库响应时间
  * 使用缓存层：使用 Redis、Memcached 等缓存层，减少对数据库的直接访问

### CDN 缓存

1. 选择合适的 CDN 提供商
2. 注册并设置 CDN 账户
3. 配置 DNS 设置：为了使 CDN 生效，你可能需要更新你的域名系统（DNS）记录。这通常涉及添加一个 CNAME 记录指向 CDN 提供商提供的子域名。具体的配置方法取决于你的域名托管服务提供商和所使用的 CDN 服务。例如，如果你使用的是 Cloudflare，那么只需要在 Cloudflare 控制面板中将你的域名加入，并开启其代理服务即可。对于其他 CDN，你可能需要手动配置 CNAME 或 A 记录。
4. 将静态资源上传到 CDN：有些 CDN 提供商会允许你直接上传文件到它们的存储空间（如 Amazon S3）。对于这些情况，你可以通过 FTP、SFTP 或者 API 接口来上传文件。而像 Cloudflare 这样的 CDN 则会自动从源服务器抓取资源，无需单独上传。
5. 修改应用程序引用静态资源的方式

### WebSocket

WebSocket是一种在单个TCP连接上进行全双工通信的协议。它被设计用于替代传统的HTTP请求/响应模型，以实现更高效、实时的通信。WebSocket使得服务器和客户端可以在建立连接后，双向发送数据，而无需每次发送数据前进行握手，这大大提高了数据传输的效率和速度。

#### WebSocket与HTTP的区别

* **连接方式**：HTTP是基于请求/响应的模型，每次通信都需要建立新的连接（无状态的交互），而WebSocket在连接建立后，可以持续进行双向通信。
* **数据传输**：HTTP传输数据时，需要封装成HTTP请求或响应，包含额外的头部信息，而WebSocket传输的数据更轻量(WebSocket 消息头较小)，没有HTTP头部的开销。
* **实时性**：由于WebSocket的持续连接特性，它能够实现真正的实时通信，而HTTP则需要轮询或长轮询来模拟实时性，效率较低。
* **安全性**：HTTP 可以通过 HTTPS 提供加密传输，确保数据的安全性。WebSocket 可以通过 WSS（WebSocket Secure）提供加密传输，确保数据的安全性。

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

**1. 客户端发送升级请求**

客户端通过发送一个 HTTP 请求来请求将连接升级为 WebSocket 协议。这个请求包含了一些特定的头部信息，用于标识这是一个 WebSocket 升级请求。

**请求头部**

* `Upgrade: websocket` — 表示请求将连接升级为 WebSocket 协议。
* `Connection: Upgrade` — 表示这是一个升级请求。
* `Sec-WebSocket-Key` — 一个由客户端生成的 Base64 编码的随机字符串，用于验证握手过程。
* `Sec-WebSocket-Version` — 客户端支持的 WebSocket 协议版本，通常是 13。
* `Sec-WebSocket-Protocol`（可选）— 客户端支持的子协议列表。

**2. 服务器响应升级请求**

服务器收到客户端的升级请求后，会验证请求的合法性，并生成一个响应。如果验证成功，服务器会发送一个 `101 Switching Protocols` 状态码的响应，表示连接已经成功升级为 WebSocket 协议。

**响应头部**

* `Upgrade: websocket` — 确认连接已升级为 WebSocket 协议。
* `Connection: Upgrade` — 确认这是一个升级请求。
* `Sec-WebSocket-Accept` — 服务器根据客户端提供的 `Sec-WebSocket-Key` 计算出的一个 Base64 编码的字符串，用于验证握手过程。
* `Sec-WebSocket-Protocol`（可选）— 服务器选择的子协议。

#### 加密

使用 WSS (WebSocket Secure)：WSS 是基于 TLS/SSL 的 WebSocket 协议，确保数据在传输过程中加密。客户端和服务器之间的所有通信都经过加密，防止数据被窃听或篡改。

### 协议

#### UDP

UDP 是 User Datagram Protocol 的简称，中文名是用户数据报协议，是 OSI（Open System Interconnection，开放式系统互联）参考模型中一种无连接的传输层协议，提供面向事务的简单不可靠信息传送服务。

在网络中它与 TCP 协议一样用于处理数据包，是一种无连接的协议。在 OSI 模型中，在第四层——传输层，处于 IP 协议的上一层。UDP 有不提供数据包分组、组装和不能对数据包进行排序的缺点，也就是说，当报文发送之后，是无法得知其是否安全完整到达的。UDP 用来支持那些需要在计算机之间传输数据的网络应用。包括网络视频会议系统在内的众多的客户/服务器模式的网络应用都需要使用 UDP 协议。

#### TCP（传输控制协议）和 UDP 的区别

* **连接方式**：TCP 是面向连接的协议，需要在通信前建立连接，而 UDP 是无连接的协议，可以直接发送数据包。
* **可靠性**：TCP 保证传输数据的可靠性，能够保证所有数据到达目的地且顺序正确；UDP 不保证传输数据的可靠性，可能会出现数据丢失或乱序等问题。
* **开销**：TCP 在传输过程中要维护连接状态、进行流量控制、拥塞控制等操作，因此开销较大；UDP 没有这些机制，传输开销较小。
* **速度**：由于 TCP 需要保证数据的可靠性，因此传输速度可能会受到一定的影响；UDP 没有这个限制，传输速度快。
* **适用场景**：TCP 适用于对可靠性要求较高的应用场景，如文件传输、邮件传输等；而 UDP 适用于实时性要求较高的应用场景，如语音、视频、游戏等。

#### TCP

（Transmission Control Protocol 传输控制协议）是一种面向连接的、可靠的、基于字节流的传输层通信协议，由 IETF 的 RFC 793 定义。在简化的计算机网络 OSI 模型中，它完成第四层传输层所指定的功能，用户数据报协议（UDP）是同一层内另一个重要的传输协议。在因特网协议族（Internet protocol suite）中，TCP 层是位于 IP 层之上，应用层之下的中间层。不同主机的应用层之间经常需要可靠的、像管道一样的连接，但是 IP 层不提供这样的流机制，而是提供不可靠的包交换。

应用层向 TCP 层发送用于网间传输的、用 8 位字节表示的数据流，然后 TCP 把数据流分区成适当长度的报文段（通常受该计算机连接的网络的数据链路层的最大传输单元（MTU）的限制）。之后 TCP 把结果包传给 IP 层，由它来通过网络将包传送给接收端实体的 TCP 层。TCP 为了保证不发生丢包，就给每个包一个序号，同时序号也保证了传送到接收端实体的包的按序接收。然后接收端实体对已成功收到的包发回一个相应的确认（ACK）；如果发送端实体在合理的往返时延（RTT）内未收到确认，那么对应的数据包就被假设为已丢失将会被进行重传。TCP 用一个校验和函数来检验数据是否有错误；在发送和接收时都要计算校验。

#### TCP 相比 UDP 为什么是可靠的

1. **确认和重传机制**
   * 建立连接时三次握手同步双方的“序列号 + 确认号 + 窗口大小信息”，是确认重传、流控的基础。
   * 传输过程中，如果 Checksum 校验失败、丢包或延时，发送端重传。
2. **数据排序**
   * TCP 有专门的序列号 SN 字段，可提供数据 re-order。
3. **流量控制**
   * 窗口和计时器的使用。TCP 窗口中会指明双方能够发送接收的最大数据量。
4. **拥塞控制**
   * TCP 的拥塞控制由 4 个核心算法组成：
     * “慢启动”（Slow Start）
     * “拥塞避免”（Congestion Avoidance）
     * “快速重传”（Fast Retransmit）
     * “快速恢复”（Fast Recovery）

#### TCP 拥塞

主要原因是网络带宽限制、缓冲区溢出、慢启动、拥塞避免、快重传和快恢复机制，以及拥塞控制算法的选择。

#### IP

[参考链接](https://draveness.me/whys-the-design-ipv6-replacing-ipv4/)

#### TCP/IP 协议

TCP/IP 协议（Transmission Control Protocol/Internet Protocol）是互联网的基本协议，也是国际互联网络的基础。它不仅仅指的是 TCP 和 IP 这两个协议，而是一个由多个网络协议组成的协议族，包括 FTP、SMTP、UDP、ICMP、ARP 等协议。TCP/IP 协议定义了计算机操作系统如何连入互联网，以及数据传输的标准。

#### TCP/IP 协议的层级结构

TCP/IP 协议采用四层的层级结构，每一层都呼叫它的下一层所提供的服务来完成自己的需求：

1. **链路层**：处理与电缆或其他传输媒介的物理接口细节。
2. **网络层**：处理分组的选路和转发，主要包括 IP 协议。
3. **传输层**：提供端到端的通信控制，主要包括 TCP 协议和 UDP 协议。
4. **应用层**：提供各种应用服务，如 HTTP 协议、FTP 协议、SMTP 协议等。

### OSI 七层模型

OSI（Open Systems Interconnection）模型是由国际标准化组织（ISO）提出的理论框架，旨在为网络通信提供一个标准化的参考模型。OSI 模型将网络通信功能划分为七个层次，每一层都有明确的功能和职责：

1. 物理层（Physical Layer）
   * 功能：定义了网络传输媒介的技术规范，包括电压、频率、信号强度、电缆接口、传输速率等。
   * 示例：同轴电缆、光纤、双绞线。
2. 数据链路层（Data Link Layer）
   * 功能：负责在物理层提供的服务之上建立逻辑链路，提供可靠的数据传输，包括帧同步、错误检测和纠正等。
   * 示例：以太网、PPP（点对点协议）。
3. 网络层（Network Layer）
   * 功能：负责路由选择和寻址，确保数据包从源地址正确地传输到目的地址。
   * 示例：IP 协议、ICMP 协议、ARP 协议。
4. 传输层（Transport Layer）
   * 功能：负责端到端的数据传输，提供可靠的数据传输服务。
   * 示例：TCP（传输控制协议）、UDP（用户数据报协议）。
5. 会话层（Session Layer）
   * 功能：负责建立、管理和终止会话，提供会话管理和同步功能。
   * 示例：RPC（远程过程调用）、NFS（网络文件系统）。
6. 表示层（Presentation Layer）
   * 功能：负责数据的表示形式和加密解密，处理数据编码、压缩、加密等。
   * 示例：JPEG、MPEG、ASCII 等。
7. 应用层（Application Layer）
   * 功能：提供应用程序之间的通信，为用户提供网络服务。
   * 示例：HTTP、FTP、SMTP、DNS 等。

### TCP/IP 四层模型

TCP/IP（Transmission Control Protocol/Internet Protocol）模型是实际互联网使用的模型，它将网络通信功能划分为四个层次：

1. **应用层（Application Layer）**
   * 功能：与 OSI 模型的应用层和表示层对应，提供应用程序之间的通信。
   * 示例：HTTP、FTP、SMTP、DNS 等。
2. **传输层（Transport Layer）**
   * 功能：与 OSI 模型的传输层对应，负责端到端的数据传输。
   * 示例：TCP、UDP。
3. **网络层（Internet Layer）**
   * 功能：与 OSI 模型的网络层对应，负责路由选择和寻址。
   * 示例：IP 协议、ICMP 协议、ARP 协议。
4. **网络接口层（Network Interface Layer）**
   * 功能：与 OSI 模型的数据链路层和物理层对应，负责物理介质上的数据传输。
   * 示例：以太网、PPP、Wi-Fi。

#### 对比

1. **层数不同**
   * OSI 七层模型：分为七层，每一层都有明确的功能和职责。
   * TCP/IP 四层模型：分为四层，简化了 OSI 模型中的某些层次。
2. **层次划分**
   * OSI 模型：更加细致地划分了网络通信的功能，例如会话层和表示层。
   * TCP/IP 模型：将 OSI 模型中的会话层和表示层合并到应用层中。
3. **实际应用**
   * OSI 模型：主要用于理论研究和教学，提供了详细的网络通信层次结构。
   * TCP/IP 模型：实际互联网的标准模型，更接近实际应用中的层次划分。

#### VPN

Virtual Private Network 虚拟专用网络的功能是：在公用网络上建立专用网络，进行加密通讯。在企业网络中有广泛应用。VPN 网关通过对数据包的加密和数据包目标地址的转换实现远程访问。VPN 有多种分类方式，主要是按协议进行分类。VPN 可通过服务器、硬件、软件等多种方式实现。

#### SOAP

Simple Object Access Protocol 简单对象访问协议是交换数据的一种协议规范。基于 XML 的协议。

#### WebService 三要素

* **SOAP**：用来描述传递信息的格式。
* **WSDL（Web Services Description Language）**：用来描述如何访问具体的接口。
* **UDDI（Universal Description Discovery and Integration）**：用来管理、分发、查询 WebService。

SOAP 可以和现存的许多因特网协议和格式结合使用，包括 HTTP、SMTP、MIME。它还支持从消息系统到远程过程调用（RPC）等大量的应用程序。SOAP 使用基于 XML 的数据结构和 HTTP 的组合定义了一个标准的方法来使用 Internet 上各种不同操作环境中的分布式对象。

### DNS

Domain Name System ![image](https://github.com/user-attachments/assets/21d0c63d-9f3d-442a-bb07-6437739fbeeb)

1. 查找本地 DNS 解析器缓存，是否有这个网址映射关系，如果有，直接返回，完成域名解析。
2. 在浏览器中输入 www.qq.com 域名，操作系统会先检查自己本地的 hosts 文件是否有这个网址映射关系，如果有，就先调用这个 IP 地址映射，完成域名解析。
3. 如果 hosts 里没有这个域名的映射，且本地 DNS 解析器缓存也没有相应的网址映射关系，首先会找 TCP/IP 参数中设置的首选 DNS 服务器，在此我们叫它本地 DNS 服务器，此服务器收到查询时，如果要查询的域名，包含在本地配置区域资源中，则返回解析结果给客户机，完成域名解析，此解析具有权威性。
4. 如果要查询的域名不由本地 DNS 服务器区域解析，但该服务器已缓存了此网址映射关系，则调用这个 IP 地址映射，完成域名解析，此解析不具有权威性。
5. 如果本地 DNS 服务器本地区域文件与缓存解析都失效，则根据本地 DNS 服务器的设置（是否设置转发器）进行查询。如果未用转发模式，本地 DNS 就把请求发至 13 台根 DNS，根 DNS 服务器收到请求后会判断这个域名（.com）是谁来授权管理，并会返回一个负责该顶级域名服务器的一个 IP。本地 DNS 服务器收到 IP 信息后，将会联系负责 .com 域的这台服务器。这台负责 .com 域的服务器收到请求后，如果自己无法解析，它就会找一个管理 .com 域的下一级 DNS 服务器地址（如 qq.com）给本地 DNS 服务器。当本地 DNS 服务器收到这个地址后，就会找 qq.com 域服务器，重复上面的动作，进行查询，直至找到 www.qq.com 主机。
6. 如果用的是转发模式，此 DNS 服务器就会把请求转发至上一级 DNS 服务器，由上一级服务器进行解析，上一级服务器如果不能解析，或找根 DNS 或把转请求转至上上级，以此循环。不管是本地 DNS 服务器用转发，还是根提示，最后都是把结果返回给本地 DNS 服务器，由此 DNS 服务器再返回给客户机。

从客户端到本地 DNS 服务器是属于递归查询，而 DNS 服务器之间就是的交互查询就是迭代查询。

#### DNS 只能拿到 IP，是如何将 IP 转为 MAC 地址

将 IP 地址解析为 MAC（媒体访问控制地址）地址的过程是由数据链路层的 ARP（Address Resolution Protocol）完成。

通过 ARP 协议，ARP 维护一个本地的高速缓存表，里面有 IP 到 MAC 的映射，若没有则广播消息查找。

* **发送 ARP 请求**：
  * 客户端需要将数据包发送到目标 IP 地址，但不知道该 IP 地址对应的 MAC 地址。
  * 客户端在本地网络中广播一个 ARP 请求，询问谁拥有目标 IP 地址。
  * ARP 请求包含客户端的 MAC 地址和 IP 地址，以及目标 IP 地址。
* **接收 ARP 请求**：
  * 所有在同一局域网内的设备都会接收到这个广播请求。
  * 如果某个设备的 IP 地址是目标 IP，它会回复一个 ARP 应答。
* **发送 ARP 应答**：
  * 拥有目标 IP 地址的设备向客户端发送一个 ARP 应答。
  * ARP 应答包含目标设备的 MAC 地址。
* **更新 ARP 缓存**：
  * 客户端收到 ARP 应答后，将 IP 地址和 MAC 地址的对应关系缓存起来，以便将来使用。

### 三次握手

三次握手是指建立 TCP 连接时，需要客户端和服务器总共需要发送三个包。 ![image](https://github.com/user-attachments/assets/b43532b5-749e-4cd0-9447-55a353d7b2ab)

![image](https://github.com/user-attachments/assets/df3d1738-e379-4d27-9b61-776e9b8b58c3)

* **第一次握手（SYN）**：
  * 客户端向服务器发送一个带有 SYN（Synchronize）标志的数据包，并选择一个初始序列号（Sequence Number，简称 Seq），表示客户端希望开始建立连接。
  * 例如，客户端发送一个 SYN 数据包，其中包含序列号 X。
* **第二次握手（SYN+ACK）**：
  * 服务器收到客户端的 SYN 数据包后，确认收到，并向客户端发送一个带有 SYN 和 ACK（Acknowledgment）标志的数据包。
  * 服务器选择自己的初始序列号 Y，并确认客户端的序列号 X+1（表明收到了客户端的序列号 X）。
  * 例如，服务器发送一个 SYN+ACK 数据包，其中包含序列号 Y 和确认号 X+1。
* **第三次握手（ACK）**：
  * 客户端收到服务器的 SYN+ACK 数据包后，确认收到，并向服务器发送一个带有 ACK 标志的数据包。
  * 客户端确认服务器的序列号 Y+1（表明收到了服务器的序列号 Y）。
  * 例如，客户端发送一个 ACK 数据包，其中包含确认号 Y+1。

### 四次挥手

| ① | 客户端 | `FIN` | 客户端请求断开连接 |
| - | --- | ----- | --------- |

| ② | 服务器 | `ACK` | 服务器确认客户端的断开请求 |
| - | --- | ----- | ------------- |

| ③ | 服务器 | `FIN` | 服务器通知客户端它也要断开 |
| - | --- | ----- | ------------- |

| ④ | 客户端 | `ACK` | 客户端确认，连接正式关闭 |
| - | --- | ----- | ------------ |

#### **第一步：客户端发送 `FIN`**

客户端主动发起断开连接请求，发送 `FIN`（Finish）报文。

> **作用**：告诉服务器 **"**<mark style="color:red;">**我不再发送数据了**</mark>**"**，但仍可以接收服务器数据。

📍 此时，客户端进入 **`FIN_WAIT_1`** 状态。

***

#### **🔹 第二步：服务器返回 `ACK`**

服务器收到 `FIN` 后，确认已收到客户端的关闭请求，并回复 `ACK`（Acknowledgment）。

📍 服务器进入 **`CLOSE_WAIT`** 状态，客户端进入 **`FIN_WAIT_2`** 状态。

> <mark style="color:red;">**此时，客户端已经停止发送数据，但服务器可能还有数据要发送**</mark>，因此服务器不会立刻关闭连接。

***

#### **🔹 第三步：服务器发送 `FIN`**

服务器确认数据发送完毕，主动发起关闭请求，发送 `FIN` 报文。

📍 服务器进入 **`LAST_ACK`** 状态。

> <mark style="color:red;">**服务器发送**</mark><mark style="color:red;">**&#x20;**</mark><mark style="color:red;">**`FIN`**</mark><mark style="color:red;">**&#x20;**</mark><mark style="color:red;">**表示：**</mark> <mark style="color:red;"></mark><mark style="color:red;">"我也不再发送数据了</mark>，可以断开连接了。"

***

#### **🔹 第四步：客户端返回 `ACK`**

客户端收到服务器的 `FIN` 后，<mark style="color:red;">发送</mark> <mark style="color:red;"></mark><mark style="color:red;">`ACK`</mark> <mark style="color:red;"></mark><mark style="color:red;">进行确认</mark>。

📍 客户端进入 **`TIME_WAIT`** 状态，**等待 2MSL** 后才真正关闭连接。

> **为什么要等待 `2MSL`（最大报文生存时间）？**
>
> * 确保服务器正确接收到 `ACK`。
> * 避免服务器 **未收到 `ACK`，重发 `FIN`**，导致连接异常。

📍 **服务器收到 `ACK` 后，立即进入 `CLOSED` 状态，连接正式关闭。**

### 为什么需要三次握手

如果使用 **两次握手**（客户端 `SYN` → 服务器 `SYN+ACK`），可能会出现 **服务器认为连接已经建立，但客户端并未确认** 的情况，导致服务器资源浪费。

**假设只进行两次握手，可能发生的问题：**

1. **客户端发送 SYN 并断网**
   * 客户端发送 `SYN` 请求，但由于断网或其他原因，没有收到服务器的 `SYN+ACK`。
2. **服务器收到 SYN，回复 SYN+ACK**
   * 服务器认为连接已经建立，等待客户端发送数据，但客户端并不知道这个连接。
3. **服务器一直等待，浪费资源**
   * 服务器开了一个连接，但客户端并没有真正使用，服务器可能会占用资源 **直到超时**。

### 挥手为什么需要四次

1. **双方向确认**：
   * 四次挥手过程中，连接的双方都需要确认对方的数据传输已经结束。主动关闭方发送 FIN 表示不再发送数据，而被动关闭方也需要发送 FIN 表示自己的数据传输也已完成。
2. **防止丢失确认**：
   * 如果只有三次挥手，那么在被动关闭方发送 FIN 后，如果 ACK 数据包丢失，主动关闭方无法知道被动关闭方是否收到了 FIN。因此，需要第四次挥手来确认被动关闭方的 FIN 已经被主动关闭方收到。
