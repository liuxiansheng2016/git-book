# 缓存

### 浏览器什么情况下会使用本地缓存

**.首次访问后的重复请求**

当用户首次访问一个网页时，浏览器会下载所有需要的资源（如HTML、CSS、JavaScript文件、图片等），并将它们存储到本地缓存中。如果这些资源被标记为可缓存，并且在后续页面加载或同一网站的不同页面中再次引用相同资源，浏览器将尝试从本地缓存中读取这些资源，而不是重新从服务器请求

**遵循HTTP响应头部中的缓存指令**

服务器可以通过HTTP响应头部提供具体的缓存指示，指导浏览器如何处理和存储资源。常见的缓存控制头部包括：

* `Cache-Control`：定义了资源的缓存策略，例如是否允许缓存、缓存的最大年龄（`max-age`）、是否必须验证缓存等。
* `Expires`：指定了资源过期的具体日期和时间。
* `ETag` 和 `Last-Modified`：用于条件请求，帮助浏览器判断缓存内容是否仍然有效。当浏览器发送带有 `If-None-Match` 或 `If-Modified-Since` 头部的请求时，服务器可以根据这些头部信息决定是返回304 Not Modified状态码还是完整的资源。

## 强制缓存

当浏览器接收到带有适当缓存头的响应后，会根据这些头部信息决定是否应该将资源存储到本地缓存中。对于强制缓存来说，只要资源没有过期（即当前时间仍在 max-age 或 Expires 所定义的有效期内），浏览器就会直接从内存或磁盘缓存中加载资源，而不会向服务器发送额外的请求

开发者通常会在部署新版本时更改资源文件名（例如添加版本号或哈希值），这样即使缓存策略保持不变，由于URL发生变化，浏览器也会认为这是一个全新的请求，从而绕过现有缓存去获取最新的资源

### Expires

Expires 是HTTP/1.0引入的一个头部字段，它指定了一个绝对的时间点，在这个时间之前，浏览器可以无条件地从本地缓存中读取资源而不必询问服务器

尽管 Expires 提供了一种简单的方式来设置缓存过期时间，但它存在一些局限性：

时钟同步问题：由于 Expires 是基于服务器时间来设定的，如果客户端的时间与服务器不同步，可能会导致缓存的行为不符合预期。 不够灵活：对于动态内容，很难准确预测何时该资源会过期。因此，通常建议为静态资源设置较长的过期时间，而对于动态资源，则考虑使用其他缓存机制，如 Cache-Control。

### Cache-Control

随着HTTP/1.1的发展，Cache-Control 成为了更常用且功能更强的控制手段，它可以提供更多的指令来精细调整缓存行为。对于强制缓存而言，最常用的选项之一是 <mark style="color:red;">max-age</mark>，它定义了一个相对的时间间隔（以秒为单位），在此期间内资源被视为新鲜并可以从缓存中直接获取

Cache-Control: max-age=600

这表示资源在接下来的600秒内都是有效的。与 Expires 不同的是，max-age 使用的是相对时间而非绝对时间，因此避免了因时钟不同步而导致的问题。

除了 max-age，Cache-Control 还支持其他一些重要的指令，如 no-cache、no-store 等。其中：

1. no-cache 表示虽然资源可以被缓存，但在每次使用前都需要与服务器进行验证，确保资源是最新的。
2. no-store 则指示浏览器完全不要存储任何关于该响应的信息，适用于敏感数据或者需要每次都获取最新版本的情况。

## 协商缓存

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

### **CDN 缓存**

1\. 选择合适的 CDN 提供商

2\. 注册并设置 CDN 账户

3\. 配置 DNS 设置

为了使 CDN 生效，你可能需要更新你的域名系统（DNS）记录。这通常涉及添加一个 CNAME 记录指向 CDN 提供商提供的子域名。具体的配置方法取决于你的域名托管服务提供商和所使用的 CDN 服务。

例如，如果你使用的是 Cloudflare，那么只需要在 Cloudflare 控制面板中将你的域名加入，并开启其代理服务即可。对于其他 CDN，你可能需要手动配置 CNAME 或 A 记录。

4\. 将静态资源上传到 CDN

有些 CDN 提供商会允许你直接上传文件到它们的存储空间（如 Amazon S3）。对于这些情况，你可以通过 FTP、SFTP 或者 API 接口来上传文件。而像 Cloudflare 这样的 CDN 则会自动从源服务器抓取资源，无需单独上传。

5\. 修改应用程序引用静态资源的方式

1.  原本的样式表链接：

    ```
    <link rel="stylesheet" href="/css/style.css">
    ```
2.  使用 CDN 的 URL：

    ```
    <link rel="stylesheet" href="https://cdn.yourdomain.com/css/style.css">
    ```
