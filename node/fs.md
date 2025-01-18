# FS

### **fs.Stats**

fs.Stats：提供了文件或目录的各种属性，包括文件大小、权限、修改时间等。

### **文件查找优先级**

缓存的模块优先级最高

如果是内置模块，则直接返回，优先级仅次缓存的模块

如果是绝对路径 / 开头，则从根目录找

如果是相对路径 ./开头，则从当前 require 文件相对位置找

如果文件没有携带后缀，先从 js、json、node 按顺序查找

如果是目录，则根据 package.json 的 main 属性值决定目录下入口文件，默认情况为 index.js

如果文件为第三方模块，则会引入 node\_modules 文件，如果不在当前仓库文件中，则自动从上级递归查找，直到根目录

### **path 模块常用方法**

```
const path = require('node:path');
const notes = '/users/joe/notes.txt';

path.dirname(notes); // /users/joe
path.basename(notes); // notes.txt
path.extname(notes); // .txt
```

**path.join(\[...paths])**：将多个路径片段连接成一个完整的路径。

```
const path = require('path');
const filePath = path.join('/home', 'user', 'documents', 'file.txt');
console.log(filePath); // 输出：/home/user/documents/file.txt
```

path.resolve(\[...paths])：将路径或路径片段解析为绝对路径。

```
const path = require('path');
const absolutePath = path.resolve('src', 'index.js');
console.log(absolutePath); // 输出：/current/working/directory/src/index.js
```

**path.normalize(path)**：规范化路径，消除多余的 . 和 ..。

**path.parse(path)**：将路径解析为对象。

**path.format(pathObject)**：将路径对象格式化为路径字符串。

### **读取文件**

1. **使用文件描述符读取文件**

首先需要获取文件描述符。这可以通过 fs 模块提供的方法来完成：

```
const fs = require('node:fs');
fs.open('/path/to/file', 'r', (err, fd) => {
  if (err) throw err;
  console.log(`File descriptor: ${fd}`);
});
```

如果你已经有一个文件描述符（例如通过 fs.open 获取），你可以使用 fs.read 方法来读取文件内容。

```
const fs = require('node:fs');

fs.open('/path/to/file.txt', 'r', (err, fd) => {
  if (err) throw err;

  const buffer = Buffer.alloc(1024); // 分配一个缓冲区

  fs.read(fd, buffer, 0, buffer.length, null, (err, bytesRead, buffer) => {
    if (err) throw err;

    console.log(`Read ${bytesRead} bytes: ${buffer.toString('utf8', 0, bytesRead)}`);

    fs.close(fd, (err) => {
      if (err) throw err;
      console.log('File closed.');
    });
  });
});
```

2. **fs.readFile /readFileSync**\
   第二个参数是编码（如 'utf8'），如果省略则返回的是 Buffer 对象。

```
const fs = require('node:fs');

// 异步读取文件
fs.readFile('/path/to/file.txt', 'utf8', (err, data) => {
    if (err) {
        console.error('Error reading file:', err);
        return;
    }
    console.log('File content:', data);
});

```

3. **使用 fs.promises.readFile 基于 Promise 的读取方法**

```
const fs = require('node:fs/promises');

async function readFile() {
    try {
        // 使用 Promise 读取文件
        const data = await fs.readFile('/path/to/file.txt', 'utf8');
        console.log('File content:', data);
    } catch (err) {
        console.error('Error reading file:', err);
    }
}

readFile();

```

4. **fs.createReadStream 流式读取大文件**

直接以文本形式读取 .docx 文件会导致乱码，因为这些文件不是纯文本文件。

```
const fs = require('node:fs');
const readline = require('node:readline');

// 创建可读流
const fileStream = fs.createReadStream('/path/to/largefile.txt', { encoding: 'utf8' });

// 使用 readline 模块逐行读取文件
const rl = readline.createInterface({
  input: fileStream,
  crlfDelay: Infinity
});

rl.on('line', (line) => {
  console.log('Line from file:', line);
});

rl.on('close', () => {
  console.log('Finished reading the file.');
});

```

### **写入文件**

**fs.writeFile**

是一个异步方法，它会覆盖目标文件的内容（如果文件已存在）或创建新文件（如果文件不存在），然后写入指定的数据。

```
const fs = require('node:fs')
fs.writeFile('/path/to/file.txt', 'Hello, World!', (err) => {
    if (err) {
        console.error('Error writing file:', err);
        return;
    }
    console.log('File written successfully.');
});

```

**fs.appendFile 异步追加内容到文件**

```
const fs = require('node:fs');

// 异步追加内容到文件
fs.appendFile('/path/to/file.txt', '\nThis is an appended line.', (err) => {
    if (err) {
        console.error('Error appending to file:', err);
        return;
    }
    console.log('Content appended successfully.');
});

```

**fs.createWriteStream**

流式写入适合处理大文件，因为它不会一次性占用大量内存。

```
const fs = require('node:fs');
const path = '/path/to/largefile.txt';

// 创建可写流
const writeStream = fs.createWriteStream(path);

writeStream.on('open', () => {
    console.log('File opened for writing.');
});

writeStream.write('First line.\n');
writeStream.write('Second line.\n');

// 结束写入
writeStream.end('End of file.\n');

writeStream.on('finish', () => {
    console.log('Writing completed.');
});

writeStream.on('error', (err) => {
    console.error('Error writing file:', err);
});

```

### 文件夹

Use fs.mkdir() or fs.mkdirSync() or fsPromises.mkdir() to create a new folder.

Use fs.readdir() or fs.readdirSync() or fsPromises.readdir() to read the contents of a directory.

用于检查文件或目录是否存在。

* 使用**fs.existsSync**：同步方法，简单直接，适用于不需要复杂逻辑和性能要求不高的场景。
* 异步方法：如 fs.access() 和 fs.promises.access()，更适合高性能和并发处理场景，避免阻塞事件循环。
* 内置模块：Node.js 自带的 fs 模块和 fs.promises 提供了基本的文件系统操作。
* 第三方库：如 memfs、fs-extra 等扩展了文件系统功能，或提供了替代方案。
* 云存储集成：使用特定云服务的 SDK 来与远程文件系统交互。

#### **使用 memfs**

memfs 是一个内存中的虚拟文件系统，适用于测试环境或需要临时存储而不涉及磁盘 I/O 的场景。它完全兼容 Node.js 的 fs API，因此你可以无缝切换到 memfs 而无需修改现有代码。

```
const fs = require('memfs').fs;

// 创建和写入文件
fs.writeFileSync('/example.txt', 'Hello, world!');

// 读取文件内容
console.log(fs.readFileSync('/example.txt', 'utf8'));

```
