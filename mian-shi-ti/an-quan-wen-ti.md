# 安全问题

## 点击劫持

点击劫持的工作原理

1\. 嵌入式框架：攻击者创建一个网页，其中包含一个透明的 \<iframe>，该 \<iframe> 指向目标网站的某个页面。

2\. 覆盖按钮：攻击者将目标网站的按钮或链接放置在 \<iframe> 中，并在其上覆盖一个透明的按钮或链接。

3\. 用户交互：当用户点击透明的按钮或链接时，实际上是在点击目标网站的按钮或链接，从而执行了攻击者预设的操作

&#x20;

攻击者通过在受害者点击某个按钮或链接时，将其点击操作重定向到另一个页面或执行其他恶意操作。这种攻击通常利用嵌入式框架（如 \<iframe>）来实现

解决方法：  X-Frame-Options  和 csp Content-Security-Policy: frame-ancestors 'none'

### &#x20;X-Frame-Options

X-Frame-Options 是一个 HTTP 响应头，用于指示浏览器是否允许一个页面在一个 \<frame>中显示。

· DENY：不允许页面在任何框架中显示。

· SAMEORIGIN：只允许页面在同源框架中显示。

· ALLOW-FROM uri：允许页面在指定来源的框架中显示（不被所有浏览器支持）。

&#x20;

1\. 在服务器配置文件中设置

2在 Nginx 服务器的配置文件中（通常是 nginx.conf 或站点配置文件）添加以下行：add\_header X-Frame-Options DENY;

3在 IIS 服务器的 web.config 文件中添加以下配置：

```javascript
<configuration>
  <system.webServer>
    <httpProtocol>
      <customHeaders>
        <add name="X-Frame-Options" value="DENY" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>

```

4在node Express 应用中，可以使用 helmet 中间件来设置 X-Frame-Options：

```javascript
const express = require('express');
const helmet = require('helmet');

const app = express();
app.use(helmet.frameguard(
    { action: 'deny' }
));
```

&#x20;

## &#x20;XSS（跨域脚本攻击）

1: 反射型Xss: 恶意链接，点击完以后会获取有用信息。

通过 URL 链接点击触发，是一次性行为，本质上是服务器端没有对用户恶意输入做安全处理，直接反射输入内容。如输入baidu.com/?id=\<script>alert(1)\</script>，弹出弹窗内容为 1。谷歌这类安全性高的浏览器基本可以自动处理这类攻击。

2：存储型：将恶意代码上传到服务器，下次浏览页面，恶意代码就会执行。

3：Sql注入。

措施\
1.对服务器提交的信息做检查，URL,http/post数据进行查询，对不是规定的格式，长度的内容进行过滤。

2.设置http only的cookie, Js脚本就不能获取到cookie.

3 白名单制度 Content-Security-Policy开发者明确告诉客户端哪些外部资源可以加载和执行。

4 HTML 标签转义 将标签符号"<"，">"等全局转义



## DDOS分布式拒绝服务攻击

攻击者通过控制多个计算机（通常称为“僵尸网络”）同时向目标系统发送大量请求，导致目标系统无法正常处理合法用户的请求，从而使服务不可用。DDoS 攻击可以针对多种目标，包括网站、服务器、网络设备等。

表现形式

一种为流量攻击，主要是针对网络带宽的攻击，即大量攻击包导致网络带宽被阻塞，合法网络包被虚假的攻击包淹没而无法到达主机；

另一种为资源耗尽攻击，主要是针对服务器主机的攻击，即通过大量攻击包导致主机的内存被耗尽或CPU被内核及应用程序占完而造成无法提供网络服务。

&#x20;

解决方式：

1 带宽和资源冗余：

增加带宽：确保有足够的带宽来应对突发的流量。

负载均衡：使用负载均衡器分散流量，减轻单个服务器的压力。

2 防火墙和入侵检测系统：

配置防火墙：设置防火墙规则，过滤掉明显的恶意流量。

入侵检测系统 (IDS)：部署 IDS，实时监测网络流量，识别和阻止可疑活动。

3 流量清洗：

使用 DDoS 防护服务：使用云服务商提供的 DDoS 防护服务，如 AWS Shield、Cloudflare 等，这些服务可以自动检测和清洗恶意流量。

流量清洗中心：将流量导向专门的流量清洗中心，过滤掉恶意流量后再转发给目标系统。

4 限速和流量控制：

IP 黑名单：将已知的恶意 IP 地址加入黑名单，拒绝其访问。

速率限制：对每个 IP 地址的请求频率进行限制，防止单一 IP 发送大量请求。

5内容分发网络 (CDN)：

使用 CDN：通过 CDN 分散流量，减轻单个服务器的压力。CDN 还可以缓存静态内容，减少服务器的负担。

6应用层防护：

Web 应用防火墙 (WAF)：使用 WAF 保护 Web 应用，过滤掉恶意的 HTTP 请求。

会话管理：加强会话管理，防止会话劫持和会话固定攻击。

7备份和恢复：

定期备份：定期备份重要数据，确保在攻击发生后可以快速恢复。

灾难恢复计划：制定详细的灾难恢复计划，确保在攻击发生后能够迅速恢复正常服务。

## websocket 安全

使用 WSS (WebSocket Secure)：WSS 是基于 TLS/SSL 的 WebSocket 协议，确保数据在传输过程中加密。

&#x20;

## 中间人攻击（Man-in-the-Middle, MITM）

是一种常见的网络安全攻击手段，攻击者在通信双方之间插入自己，拦截并可能篡改通信内容

**中间人攻击的工作原理**

1拦截通信：

攻击者在通信双方之间插入自己，拦截双方的通信数据。

例如，在客户端和服务器之间，攻击者可以拦截客户端发送的请求和服务器返回的响应。

2篡改数据：

攻击者可以修改拦截到的数据，插入恶意内容或篡改原有内容。

例如，攻击者可以修改登录凭证、交易信息或会话数据。

3伪装身份：

攻击者可以伪装成合法的一方，欺骗另一方继续通信。

例如，攻击者可以伪装成服务器，向客户端发送伪造的证书。

**常见的中间人攻击类型**

ARP 欺骗：

描述：在局域网中，攻击者通过发送伪造的 ARP（Address Resolution Protocol）响应，将自己的 MAC 地址与目标 IP 地址绑定，从而拦截网络流量。

arpspoof -i eth0 -t \<target\_ip> \<gateway\_ip>

DNS 欺骗：

描述：攻击者通过篡改 DNS 响应，将合法的域名解析为攻击者的 IP 地址，使用户访问到恶意网站。

dnschef --fakeip 192.168.1.100 --fakedomains example.com

SSL 剥离：

描述：攻击者将 HTTPS 连接降级为 HTTP 连接，使通信内容以明文形式传输，从而可以被拦截和篡改。

sslstrip -l 8080

Wi-Fi 钓鱼：

描述：攻击者设置一个假冒的 Wi-Fi 热点，引诱用户连接，然后拦截和篡改用户的网络通信

&#x20;

### **防范中间人攻击的措施**

1. 使用 HTTPS：

描述：HTTPS 协议使用 SSL/TLS 加密，确保数据在传输过程中不被窃听或篡改。

2. 证书验证：

描述：客户端在建立 HTTPS 连接时，验证服务器的证书是否由可信的 CA（Certificate Authority）签发。

3. &#x20;HSTS（HTTP Strict Transport Security）：

描述：HSTS 是一种安全策略，强制浏览器仅通过 HTTPS 访问网站，防止 SSL 剥离攻击。

Strict-Transport-Security: max-age=31536000; includeSubDomains; preload

4. &#x20;ARP 监控：

描述：在网络中部署 ARP 监控工具，检测和防止 ARP 欺骗攻击。

sudo apt-get install arpwatch

sudo systemctl start arpwatch

sudo systemctl enable arpwatch

5. &#x20;DNSSEC（DNS Security Extensions）：

描述：DNSSEC 通过数字签名确保 DNS 响应的完整性和真实性，防止 DNS 欺骗攻击。

```
# 在 DNS 服务器上启用 DNSSEC
named.conf:
options {
    dnssec-validation yes;
};

```

6. 子资源完整性

&#x20;

## CSRF

CSRF 攻击的核心在于利用用户已经认证的会话，通过伪造请求来执行恶意操作。

&#x20;

**CSRF 攻击的工作原理**

1用户登录：

用户在合法网站 A 上登录，获得一个会话 cookie。

2用户访问恶意网站：

用户在同一个浏览器中访问恶意网站 B。

3恶意网站发送请求：

恶意网站 B 包含一个隐藏的表单或脚本，该表单或脚本会向合法网站 A 发送请求。

由于浏览器会自动附带上一次登录时获得的会话 cookie，合法网站 A 会认为这是一个合法的请求并执行相应的操作。

4执行恶意操作：

合法网站 A 执行了恶意请求，例如更改用户的密码、转账等。

&#x20;

### **防范 CSRF 攻击的措施**

1. CSRF 令牌：

描述：在每个表单中包含一个唯一的 CSRF 令牌，并在服务器端验证该令牌

```html
<form action="/transfer-money" method="POST">
  <input type="hidden" name="csrf_token" value="{{ csrf_token }}" />
  <input type="text" name="amount" placeholder="Amount" required />
  <input type="text" name="to_account" placeholder="To Account" required />
  <button type="submit">Transfer Money</button>
</form>
```

```javascript
const express = require('express');
const csrf = require('csurf');
const app = express();
const csrfProtection = csrf({ cookie: true });

app.use(express.urlencoded({ extended: false }));
app.use(csrfProtection);

app.get('/transfer-money', (req, res) => {
  res.render('transfer-money', { csrfToken: req.csrfToken() });
});

app.post('/transfer-money', csrfProtection, (req, res) => {
  const { amount, to_account, csrf_token } = req.body;
  if (csrf_token === req.csrfToken()) {
    // 处理转账
    res.send('Money transferred successfully');
  } else {
    res.status(403).send('CSRF token mismatch');
  }
});

app.listen(3000, () => {
  console.log('Server is running on port 3000');
});
```

2. Referer 检查：

描述：检查请求的 Referer 头，确保请求来自合法的来源。

```javascript
app.post('/transfer-money', (req, res) => {
    const referer = req.headers.referer || req.headers.referrer;
    if (referer && referer.startsWith('https://legitimate-site.com')) {
        // 处理转账
        res.send('Money transferred successfully');
    } else {
        res.status(403).send('Invalid referer');
    }
});
```

3. &#x20;SameSite 属性：

描述：使用 SameSite 属性限制 cookie 的发送范围。

```
Set-Cookie: session=abc123; Path=/; Secure; HttpOnly; SameSite=Lax
```

### &#x20;SameSite

SameSite 是一个用于增强 cookie 安全性的属性，它可以防止跨站请求伪造（CSRF）攻击和某些类型的跨站脚本（XSS）攻击。属性可以设置为 Strict、Lax 或 None

**分类**

1 Strict：

行为：cookie 只会在第一方上下文中发送，即用户直接访问站点时才会发送 cookie。如果请求是从其他站点发起的（例如通过链接或表单提交），cookie 将不会被发送。

2 Lax：

行为：cookie 在大多数跨站请求中不会发送，但在导航到目标站点的顶级导航（例如用户点击链接）时会发送 cookie。

3 None：

行为：cookie 在所有请求中都会发送，包括跨站请求。为了防止 CSRF 攻击，必须同时设置 Secure 属性，确保 cookie 只通过 HTTPS 发送。

&#x20;

**设置**

1\. 在服务器端设置

Apache

Header edit Set-Cookie ^(.\*)$ $1;SameSite=Strict

2 Nginx

add\_header Set-Cookie "session=abc123; Path=/; Secure; HttpOnly; SameSite=Strict";

3 IIS

在web.config 文件中

```
<configuration>
    <system.webServer>
        <rewrite>
            <outboundRules>
                <rule name="Add SameSite">
                    <match serverVariable="RESPONSE_Set_Cookie" pattern=".*" />
                    <action type="Rewrite" value="{R:0}; SameSite=Strict" />
                </rule>
            </outboundRules>
        </rewrite>
    </system.webServer>
</configuration>
```

4 Node.js (Express)

```javascript
const express = require('express');
const app = express();

app.use((req, res, next) => {
    res.cookie('session', 'abc123', {
        path: '/',
        secure: true,
        httpOnly: true,
        sameSite: 'Strict'
    });
    next();
});

app.get('/', (req, res) => {
    res.send('Hello, World!');
});

app.listen(3000, () => {
    console.log('Server is running on port 3000');
});

```

## &#x20;SRI

SRI（Subresource Integrity，子资源完整性）是一种安全特性，旨在确保浏览器加载的外部资源（如 JavaScript 和 CSS 文件）没有被篡改。通过在 HTML 中添加一个哈希值，浏览器可以在加载资源后验证其完整性，从而防止中间人攻击（Man-in-the-Middle Attack）或其他形式的资源篡改。

```
openssl dgst -sha384 -binary somefile.min.js | openssl base64 -A
```

基本概念

1\. 哈希值：一个固定长度的字符串，用于唯一标识文件内容。常见的哈希算法有 SHA-256、SHA-384 和 SHA-512。

2\. 完整性验证：浏览器在下载资源后，会计算其哈希值并与提供的哈希值进行对比。如果两者不匹配，浏览器将拒绝加载该资源。

1\. 生成哈希值

你可以使用在线工具或命令行工具生成文件的哈希值。例如，使用 openssl 命令行工具：

2.在 \<script> 或 \<link> 标签中添加 integrity 属性，指定哈希值和算法。

![](file:///C:/Users/CeHe/AppData/Local/Temp/ksohtml15668/wps28.jpg)&#x20;

注意： 跨域请求：如果资源来自不同的域名，需要设置 crossorigin 属性。

3 计算哈希值：下载完成后，浏览器会使用指定的哈希算法（如 SHA-256、SHA-384 或 SHA-512）计算资源的哈希值。

4 对比哈希值：浏览器将计算出的哈希值与 integrity 属性中提供的哈希值进行对比。

5 验证结果：

如果匹配：浏览器会继续执行或应用该资源。

如果不匹配：浏览器会拒绝加载该资源，并可能记录错误信息。
