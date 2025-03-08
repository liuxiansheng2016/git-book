# crypto

`crypto` 模块是 Node.js 提供的**原生加密工具**，可用于数据加密、哈希计算、HMAC 认证、对称加密、非对称加密等。

***

### **1. 使用 `crypto` 进行哈希计算**

哈希函数（Hash）用于将输入数据转换为固定长度的字符串，常用于密码存储、数据完整性校验等。

#### **示例：生成 SHA-256 哈希**

```javascript
const crypto = require('crypto');

const data = "Hello, Node.js!";
const hash = crypto.createHash('sha256').update(data).digest('hex');

console.log("SHA-256 哈希:", hash);
```

**⚡ 解释：**

* `createHash('sha256')` 创建 SHA-256 哈希算法实例。
* `update(data)` 输入需要哈希的数据。
* `digest('hex')` 以十六进制字符串输出哈希值。

**示例输出**：

```sh
SHA-256 哈希: a592b19a94e1f73f8e2f2f5084c246c569f9c6e2b3f626e258c796cc1fce47db
```

***

### **2. HMAC（哈希消息认证码）**

HMAC 使用密钥对消息进行哈希计算，常用于**API 请求签名**或**消息完整性验证**。

#### **示例：使用 HMAC 生成签名**

```javascript
const secretKey = 'my_secret_key';

const hmac = crypto.createHmac('sha256', secretKey)
                   .update("Hello, Node.js!")
                   .digest('hex');

console.log("HMAC 签名:", hmac);
```

**⚡ 解释：**

* `createHmac('sha256', secretKey)` 创建带密钥的哈希对象。
* 适用于 API 请求签名、消息完整性校验。

***

### **3. 对称加密（AES）**

AES（Advanced Encryption Standard）是一种**对称加密算法**，加密和解密使用相同的密钥。

#### **示例：AES-256 加密和解密**

```javascript
const algorithm = 'aes-256-cbc';
const key = crypto.randomBytes(32); // 生成 32 字节密钥
const iv = crypto.randomBytes(16);  // 生成 16 字节初始向量

// 加密
function encrypt(text) {
  const cipher = crypto.createCipheriv(algorithm, key, iv);
  let encrypted = cipher.update(text, 'utf8', 'hex');
  encrypted += cipher.final('hex');
  return { encryptedData: encrypted, iv: iv.toString('hex') };
}

// 解密
function decrypt(encryptedData, ivHex) {
  const decipher = crypto.createDecipheriv(algorithm, key, Buffer.from(ivHex, 'hex'));
  let decrypted = decipher.update(encryptedData, 'hex', 'utf8');
  decrypted += decipher.final('utf8');
  return decrypted;
}

// 测试加解密
const data = "Sensitive Data";
const encrypted = encrypt(data);
console.log("加密后的数据:", encrypted);

const decrypted = decrypt(encrypted.encryptedData, encrypted.iv);
console.log("解密后的数据:", decrypted);
```

**⚡ 解释：**

* `aes-256-cbc` 是一种对称加密算法，需要 `32` 字节密钥和 `16` 字节 IV（初始向量）。
* `createCipheriv()` 创建加密实例，`createDecipheriv()` 创建解密实例。
* `update()` 处理数据，`final()` 结束加密/解密。

***

### **4. 非对称加密（RSA）**

RSA 是**非对称加密算法**，适用于数据加密、签名验证等。它使用**公钥加密，私钥解密**。

#### **4.1 生成 RSA 密钥对**

```sh
openssl genpkey -algorithm RSA -out private.pem
openssl rsa -in private.pem -pubout -out public.pem
```

#### **4.2 使用 RSA 进行加密和解密**

```javascript
const fs = require('fs');

// 读取 RSA 密钥
const publicKey = fs.readFileSync('public.pem', 'utf8');
const privateKey = fs.readFileSync('private.pem', 'utf8');

// 加密
function encryptRSA(text) {
  return crypto.publicEncrypt(publicKey, Buffer.from(text)).toString('base64');
}

// 解密
function decryptRSA(encryptedText) {
  return crypto.privateDecrypt(privateKey, Buffer.from(encryptedText, 'base64')).toString('utf8');
}

// 测试加解密
const encryptedRSA = encryptRSA("Secret Message");
console.log("RSA 加密后:", encryptedRSA);

const decryptedRSA = decryptRSA(encryptedRSA);
console.log("RSA 解密后:", decryptedRSA);
```

**⚡ 解释：**

* `publicEncrypt()` 使用公钥加密数据。
* `privateDecrypt()` 使用私钥解密数据。
* 适用于**安全通信、身份验证**等场景。

***

### **5. 使用 PBKDF2 进行安全密码存储**

PBKDF2 是一种密码派生函数，可用于**安全存储用户密码**。

#### **示例：PBKDF2 生成安全密码哈希**

```javascript
const password = "my_secure_password";
const salt = crypto.randomBytes(16).toString('hex');

crypto.pbkdf2(password, salt, 100000, 64, 'sha512', (err, derivedKey) => {
  if (err) throw err;
  console.log("存储密码哈希:", derivedKey.toString('hex'));
});
```

**⚡ 解释：**

* `pbkdf2()` 通过多次哈希计算提高安全性，防止暴力破解。
* 适用于**用户密码存储**，数据库存储时需保存 `salt` 值。

***

### **6. 数据签名和验证**

数字签名用于验证数据完整性，常用于**API 认证、JWT 令牌、电子签名**。

#### **示例：RSA 进行数据签名和验证**

```javascript
const sign = crypto.createSign('SHA256');
sign.update("Important Data");
const signature = sign.sign(privateKey, 'hex');

console.log("签名:", signature);

// 验证签名
const verify = crypto.createVerify('SHA256');
verify.update("Important Data");
const isValid = verify.verify(publicKey, signature, 'hex');

console.log("签名是否有效:", isValid);
```

**⚡ 解释：**

* `createSign()` 生成数字签名，`createVerify()` 验证签名。
* 适用于**数字签名、JWT 认证、证书验证**。

***

### **7. 总结**

| 用途         | 方法                                     | 适用场景           |
| ---------- | -------------------------------------- | -------------- |
| **哈希计算**   | `createHash()`                         | 存储密码、校验文件完整性   |
| **HMAC**   | `createHmac()`                         | API 请求签名、数据完整性 |
| **对称加密**   | `createCipheriv()`                     | AES-256 保护数据   |
| **非对称加密**  | `publicEncrypt()` / `privateDecrypt()` | 安全通信、公钥加密      |
| **PBKDF2** | `pbkdf2()`                             | 用户密码存储         |
| **数字签名**   | `createSign()` / `createVerify()`      | 电子签名、JWT       |

***

🚀 **总结**

* **哈希函数** 用于不可逆加密，如 SHA-256。
* **HMAC** 适用于 API 认证，提供数据完整性校验。
* **AES**（对称加密）适合加密存储敏感数据，如数据库密码。
* **RSA**（非对称加密）适用于安全通信，如 HTTPS、数字签名。
* **PBKDF2** 适用于**安全存储密码**，结合 `salt` 可防止彩虹表攻击。

如果你的项目涉及**密码存储、API 安全、加密数据传输**，使用 `crypto` 是最佳选择！🚀
