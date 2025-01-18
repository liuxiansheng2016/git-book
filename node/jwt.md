# JWT

## **JWT 的结构**

一个 JWT 通常由三部分组成

* Header（头部）
* Payload（载荷）
* Signature（签名）

**Header**

头部通常包含两部分：令牌的类型（即 JWT）和所使用的签名算法（如 HMAC SHA256 或 RSA）。例如：

{ "alg": "HS256", "typ": "JWT"}

**Payload**

载荷就是存放实际想要传递的数据的地方。JWT 规范中定义了一些标准字段（称为声明），但你也可以添加自定义字段。例如：

{ "sub": "1234567890", "name": "John Doe", "iat": 1516239022}

**Signature**

签名是用于验证消息在传输过程中没有被篡改，并且，对于使用私钥签名的令牌来说，还可以验证发送者的身份。签名是由编码后的头部、编码后的载荷、一个秘钥以及头部中指定的算法生成的。例如，如果使用的是 HMAC SHA256 算法，则签名如下计算：

HMACSHA256( base64UrlEncode(header) + "." + base64UrlEncode(payload),secret)

**最终的 JWT 就看起来像这样**

\<base64url-encoded-header>.\<base64url-encoded-payload>.\<signature>

## **使用场景**

身份验证：这是 JWT 最常见的用途。一旦用户登录成功，每个后续请求都会包含 JWT，允许用户访问该令牌授权的路由、服务和资源。由于跨域资源共享（CORS）不是问题，因此可以很容易地进行跨域认证。

信息交换：JWT 可以用于安全地在各方之间传输信息。因为 JWT 可以被签名（例如使用公钥/私钥对），所以你可以确定发送者是谁。此外，由于签名是基于内容的，你可以确保内容未被篡改。

## **JWT 的优点**

* 无状态：服务器不需要存储会话信息，因为所有必要的信息都在令牌中。
* 易于传递：可以通过 URL、POST 参数或 HTTP 头部传递。
* 跨域支持：不像 Cookies，JWT 不受同源策略限制。
* 安全性：通过数字签名确保数据完整性。

## **JWT 的缺点**

* 大小问题：JWT 相对于 Session ID 更大，尤其是在载荷较大的时候。
* 无法撤销：由于 JWT 是无状态的，一旦发出就很难失效。除非实现黑名单机制或设定较短的有效期。
* 敏感信息暴露风险：虽然 JWT 支持加密，但是默认情况下只提供签名而不加密载荷。如果载荷中含有敏感信息，应该考虑加密。

## **创建 JWT**

```javascript
const jwt = require('jsonwebtoken');

// 秘钥
const SECRET_KEY = 'your_secret_key';

// 创建 token
function createToken(user) {
    const payload = {
        sub: user.id,
        name: user.name
    };
    return jwt.sign(payload, SECRET_KEY, { expiresIn: '1h' });
}

// 使用示例
const user = { id: 1, name: 'John Doe' };
const token = createToken(user);
console.log(token);

```

**验证 JWT**

```javascript
function verifyToken(token) {
    try {
        const decoded = jwt.verify(token, SECRET_KEY);
        console.log('Decoded:', decoded);
        return true;
    } catch (err) {
        console.error('Invalid token');
        return false;
    }
}

// 使用示例
const isValid = verifyToken(token);
console.log('Is valid:', isValid);

```
