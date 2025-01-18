# 浏览器缓存

## 强制缓存

当浏览器接收到带有适当缓存头的响应后，会根据这些头部信息决定是否应该将资源存储到本地缓存中。对于强制缓存来说，只要资源没有过期（即当前时间仍在 max-age 或 Expires 所定义的有效期内），浏览器就会直接从内存或磁盘缓存中加载资源，而不会向服务器发送额外的请求

开发者通常会在部署新版本时更改资源文件名（例如添加版本号或哈希值），这样即使缓存策略保持不变，由于URL发生变化，浏览器也会认为这是一个全新的请求，从而绕过现有缓存去获取最新的资源

### Expires

Expires 是HTTP/1.0引入的一个头部字段，它指定了一个绝对的时间点，在这个时间之前，浏览器可以无条件地从本地缓存中读取资源而不必询问服务器

### Cache-Control

随着HTTP/1.1的发展，Cache-Control 成为了更常用且功能更强的控制手段，它可以提供更多的指令来精细调整缓存行为。对于强制缓存而言，最常用的选项之一是 max-age，它定义了一个相对的时间间隔（以秒为单位），在此期间内资源被视为新鲜并可以从缓存中直接获取

Cache-Control: max-age=600

这表示资源在接下来的600秒内都是有效的。与 Expires 不同的是，max-age 使用的是相对时间而非绝对时间，因此避免了因时钟不同步而导致的问题。

除了 max-age，Cache-Control 还支持其他一些重要的指令，如 no-cache、no-store 等。其中：

1. no-cache 表示虽然资源可以被缓存，但在每次使用前都需要与服务器进行验证，确保资源是最新的。
2. no-store 则指示浏览器完全不要存储任何关于该响应的信息，适用于敏感数据或者需要每次都获取最新版本的情况。



## &#x20;协商缓存

协商缓存：当缓存过期或未设置强缓存指令时，浏览器会向服务器发送条件请求（通常包含 If-None-Match 或 If-Modified-Since 头），以确认资源是否发生了变化。如果资源没有变化，服务器返回 304 Not Modified 响应，浏览器继续使用缓存；否则，返回新的资源。

**使用 ETag 实现协商缓存**

#### ETag

ETag 是一个唯一标识符，用于表示资源的具体版本。当资源发生变化时，其 ETag 也应随之改变。客户端可以在后续请求中通过 If-None-Match 头带上 ETag 值，服务器根据 ETag 判断资源是否有变化，若未变化则返回 304 Not Modified 状态码。

```javascript
app.get('/dynamic-resource', (req, res) => {
    const resource = generateDynamicResource(); // 获取或生成资源
    const etag = createHash('md5').update(resource).digest('hex'); // 创建 ETag

    // 检查 If-None-Match 请求头
    if (req.headers['if-none-match'] === etag) {
        res.status(304).end();
    } else {
        res.set('ETag', etag);
        res.send(resource);
    }
});

```

Last-Modified：

```javascript
app.get('/dynamic-resource', (req, res) => {
    const resource = generateDynamicResource(); // 获取或生成资源
    const lastModified = new Date().toUTCString(); // 设置为当前时间或其他逻辑确定的时间

    // 检查 If-Modified-Since 请求头
    if (req.headers['if-modified-since'] && req.headers['if-modified-since'] === lastModified) {
        res.status(304).end();
    } else {
        res.set('Last-Modified', lastModified);
        res.send(resource);
    }
});

```

假设你有一个图片资源 /images/logo.png，并且它的 ETag 是 "abc123"。第一次请求时，服务器返回 200 OK 并附带完整的图片内容和 ETag。之后，当浏览器再次请求同一资源时，它会在请求头中添加 If-None-Match: "abc123"。如果服务器发现资源未变，则返回 304 Not Modified；否则，返回 200 OK 和更新后的资源。



## HTTP 缓存：

这是基于 HTTP 协议定义的一组规则，用于决定哪些响应应该被缓存，以及它们应该如何被缓存。HTTP 缓存通过使用 Cache-Control、Expires 和 ETag 等响应头来工作。

强缓存：如果资源设置了 Cache-Control 或 Expires 头，并且当前时间还在缓存有效期内，浏览器将直接使用缓存中的资源，而不会向服务器发送请求。

**配置：**

Nginx 配置

```
location /static/ {
    expires 30d;
    add_header Cache-Control "public, no-transform";
}
```

Apache .htaccess 配置

```
<filesMatch "\\.(css|jpg|jpeg|png|gif|js|ico)$">
Header set Cache-Control "max-age=2592000, public"
</filesMatch>
```

Node.js Express 应用

```
app.use((req, res, next) => {
    res.set('Cache-Control', 'public, max-age=31536000');
    next();
});
```

## Service Workers：

这是一种更高级的缓存方式，允许开发者编写 JavaScript 文件来拦截和处理网络请求，从而实现完全自定义的缓存策略。这对于离线支持和性能优化非常有用。

应用缓存（AppCache）：这是一个较老的技术，已被 Service Workers 所取代。它允许开发者指定一组文件进行缓存，以便在用户离线时也可以访问。

## 浏览器缓存清除：

用户可以通过浏览器设置手动清除缓存，或者开发人员可以在部署新版本时采取措施确保旧缓存不会导致问题，例如通过改变资源文件名（如添加版本号或哈希值）来强制更新缓存。

Cookie 和 Local Storage：虽然不是传统意义上的缓存，但它们也用于保存数据并在客户端持久化，影响到网页的行为和表现。

&#x20;
