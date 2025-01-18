# Buffer

## **buffer**

Buffer 是 Node.js 中处理二进制数据的核心模块，提供了多种方法来创建、读取、写入、比较和操作二进制数据。

```javascript
const { Buffer } = require('buffer');

// 创建 Buffer
const buffer1 = Buffer.alloc(10); // 创建一个长度为 10 的 Buffer，初始填充为 0
const buffer2 = Buffer.from('Hello, World!', 'utf8'); // 从字符串创建
const buffer3 = Buffer.from([104, 101, 108, 108, 111]); // 从数组创建
const buffer4 = Buffer.allocUnsafe(10); // 创建一个长度为 10 的未初始化的 Buffer

// 写入数据
buffer1.write('Buffer1', 0, 7, 'utf8'); // 从索引 0 开始写入 'Buffer1'

// 读取数据
const str1 = buffer2.toString('utf8', 0, 5); // 从索引 0 到 5 读取数据
console.log(str1); // 输出 'Hello'

// 转换为 JSON
const json = buffer2.toJSON();
console.log(json); // 输出 { type: 'Buffer', data: [72, 101, 108, 108, 111, 44, 32, 87, 111, 114, 108, 100, 33] }

// 比较 Buffer
const result = buffer2.compare(buffer3);
console.log(result); // 输出 1，表示 buffer2 大于 buffer3

// 拷贝 Buffer
buffer2.copy(buffer1, 0, 0, 5); // 从 buffer2 复制 5 个字节到 buffer1
console.log(buffer1.toString()); // 输出 'Hello'

// 切片 Buffer
const slice = buffer2.slice(0, 5);
console.log(slice.toString()); // 输出 'Hello'

// 拼接 Buffer
const buffer5 = Buffer.from('Hello, ');
const buffer6 = Buffer.from('World!');
const buffer7 = Buffer.concat([buffer5, buffer6]);
console.log(buffer7.toString()); // 输出 'Hello, World!'

```

### **Buffer.alloc 和 Buffer.allocUnsafe**

Buffer.alloc(size\[, fill\[, encoding]]) 方法用于创建一个新的 Buffer 对象，并且会根据指定的大小预先填充内容。这个方法有两个可选参数：

* fill：用于填充 Buffer 的值，默认为空字符串 ' '。
* encoding：用于解析 fill 参数的编码，默认为 'utf8'。

```javascript
let buffer = Buffer.allocUnsafe(10); // 创建一个长度为 10 字节的 Buffer。 不进行初始化
console.log(buffer);
// Output:
// <Buffer 7f 9d 0a 0b 0c 0d 0e 0f 10 11> (具体内容依赖于之前的内存状态)
```

Buffer.allocUnsafe(size) 方法同样用于创建一个新的 Buffer 对象，但是不会预填充内容。这意味着创建出来的 Buffer 可能包含之前内存块的内容，这是不安全的，因为它可能暴露敏感信息。

```javascript
let buffer = Buffer.allocUnsafe(10); // 创建一个长度为 10 字节的 Buffer，不进行初始化
console.log(buffer);
// Output:
// <Buffer 7f 9d 0a 0b 0c 0d 0e 0f 10 11> (具体内容依赖于之前的内存状态)
```

避免大 Buffer：

对于大 Buffer，尽量分批处理，避免一次性分配过多内存。可以使用流式处理（如 ReadableStream 和 WritableStream）来处理大数据。

* Buffer：Node.js 中用于处理二进制数据的核心模块。
* Base64：基于 64 个可打印字符表示二进制数据的编码方法，常用于网络传输。
* Blob：浏览器环境中的对象，用于表示不可变的原始数据，常用于处理文件和图像等二进制数据。

**Buffer /Base64/Blob**

* Buffer：Node.js 中用于处理二进制数据的核心模块。
* Base64：基于 64 个可打印字符表示二进制数据的编码方法，常用于网络传输。
* Blob：浏览器环境中的对象，用于表示不可变的原始数据，常用于处理文件和图像等二进制数据。
