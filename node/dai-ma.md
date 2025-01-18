# 代码

## **socket**

socket 是应用层与 TCP/IP 协议通信的中间软件抽象层，它是一组接口。在设计模式中，Socket 其实就是一个门面模式，它把复杂的 TCP/IP 协议族隐藏在 Socket 接口后面，对用户来说，一组简单的接口就是全部，让 Socket 去组织数据，以符合指定的协议。

```

const net = require('net')
// 创建TCP服务器
const server = net.createServer((socket) => {
    console.log('客户端连接')
    // 监听客户端的数据
    socket.on('data', (data) => {
        console.log('监听客户端的数据: ', data.toString())
    });
    // 监听客户端断开连接事件
    socket.on('end', (data) => {
        console.log('客户端断开连接')
    });
    // 发送数据给客户端
    socket.write('哈哈哈，我是一个测试 \r\n')
})
// 启动服务
server.listen(8080, () => {
    console.log('服务创建')
})


// client.js
const net = require('net');
// 连接服务器
const client = net.connect({port: 8080}, () => {
    console.log('连接服务器');
    client.write('http://xingxin.me \r\n')
})
// 接收服务端的数据
client.on('data', (data) => {
    console.log('接收服务端的数据: ', data.toString())
    // 断开连接
    client.end()
})
// 断开连接
client.on('end', () => {
    console.log('断开连接')
})

聊天室
//Server.js

const net = require('net')
// 创建TCP服务器
const server = net.createServer()
// 存储所有客户端socket
let sockets = []
server.on('connection', function(socket) {
    console.log('Got a new connection')
    sockets.push(socket)
    socket.on('data', function(data) {
        console.log('Got data: ',  data.toString())
        sockets.forEach(function(otherSocket) {
            if (otherSocket !== socket) {
                console.log(1111);
                otherSocket.write(data)
            }
        })
    })
    socket.on('close', function() {
        console.log('A client connection closed')
        let index = sockets.indexOf(socket);
        sockets.splice(index, 1);
    })
})
server.on('error', function(err) {
    console.log('Server error: ', err.message)
})
server.on('close', function() {
    console.log('Server closed')
})
server.listen(8080)

//Client.js
const net = require("net");
process.stdin.resume();
process.stdin.setEncoding("utf8");
const client = net.connect({ port: 8080 }, () => {
  console.log("Connected to server");
  // 获取输入的字符串
  console.log("input: ");
  process.stdin.on("data", (data) => {
    console.log("input:");
    client.write(data);
    // 输入 'close' 字符串时关闭连接
    if (data === "close\n") {
      client.end();
    }
  });
});
// 获取服务端发送过来的数据
client.on("data", (data) => {
  console.log("Other user's input", data.toString());
});
client.on("end", () => {
  console.log("Disconnected from server");
  // 退出客户端程序
  process.exit();
});

```

## **文件 copy**

```
const fs = require('fs');
const readline = require('readline');

function updateProgress(copiedSize, totalSize, startTime) {
    const percent = (copiedSize / totalSize * 100).toFixed(2);
    const size = (copiedSize / (1024 * 1024)).toFixed(6); // Convert to MB
    const elapsedTime = (Date.now() - startTime) / 1000; // Convert to seconds
    const speed = (copiedSize / (1024 * 1024) / elapsedTime).toFixed(2); // MB/s
    readline.cursorTo(process.stdout, 0);
    process.stdout.write(`已完成 ${size}MB, ${percent}% 速度：${speed}MB/s`);
}

function copyFileWithProgress(src, dest) {
    const totalSize = fs.statSync(src).size;
    let copiedSize = 0;
    const startTime = Date.now();
    const readStream = fs.createReadStream(src);
    const writeStream = fs.createWriteStream(dest);

    readStream.on('data', (chunk) => {
        copiedSize += chunk.length;
        updateProgress(copiedSize, totalSize, startTime);
    });

    readStream.pipe(writeStream);

    writeStream.on('finish', () => {
        console.log('\nCopy completed.');
    });

    writeStream.on('error', (err) => {
        console.error('Error during copy:', err);
    });
}

// Example usage
const srcFile = './README.md';
const destFile = './Test.md';
copyFileWithProgress(srcFile, destFile);
```

## **文件夹 copy**

```
onst fs = require('fs');
const path = require('path');
const readline = require('readline');

function updateProgress(copiedSize, totalSize, startTime) {
    const percent = (copiedSize / totalSize * 100).toFixed(2);
    const size = (copiedSize / (1024 * 1024)).toFixed(6); // Convert to MB
    const elapsedTime = (Date.now() - startTime) / 1000; // Convert to seconds
    const speed = (copiedSize / (1024 * 1024) / elapsedTime).toFixed(2); // MB/s
    readline.cursorTo(process.stdout, 0);
    process.stdout.write(`已完成 ${size}MB, ${percent}%, 速度：${speed}MB/s`);
}
function copyFile(src, dest, copiedSize, totalSize, startTime, callback) {
    const readStream = fs.createReadStream(src);
    const writeStream = fs.createWriteStream(dest);
    readStream.on('data', (chunk) => {
        copiedSize.size += chunk.length;
        updateProgress(copiedSize.size, totalSize.size, startTime);
    });
    readStream.pipe(writeStream);
    writeStream.on('finish', callback);
    writeStream.on('error', (err) => {
        console.error('Error during copy:', err);
    });
}
function copyDirectory(src, dest, copiedSize, totalSize, startTime, callback) {
    fs.mkdirSync(dest, { recursive: true });
    fs.readdir(src, (err, files) => {
        if (err) {
            console.error('Error reading directory:', err);
            return;
        }
        let pending = files.length;
        if (!pending) return callback();
        files.forEach((file) => {
            const srcPath = path.join(src, file);
            const destPath = path.join(dest, file);
            console.log(destPath);
            fs.stat(srcPath, (err, stats) => {
                if (err) {
                    console.error('Error stating file:', err);
                    return;
                }
                if (stats.isDirectory()) {
                    copyDirectory(srcPath, destPath, copiedSize, totalSize, startTime, () => {
                        if (!--pending) callback();
                    });
                } else {
                    totalSize.size += stats.size;
                    copyFile(srcPath, destPath, copiedSize, totalSize, startTime, () => {
                        if (!--pending) callback();
                    });
                }
            });
        });
    });
}
function copyFolderWithProgress(src, dest) {
    const copiedSize = { size: 0 };
    const totalSize = { size: 0 };
    const startTime = Date.now();
    copyDirectory(src, dest, copiedSize, totalSize, startTime, () => {
        updateProgress(copiedSize.size, totalSize.size, startTime); // Final update
        console.log('\nCopy completed.');
    });
}
// Example usage
const srcFolder = './node_modules';
const destFolder = './test';
copyFolderWithProgress(srcFolder, destFolder);
```

## **搜索**

```
const https = require('https');
const readline = require('readline');
const cheerio = require('cheerio');
const zlib = require('zlib');

// 创建可交互命令行
const rl = readline.createInterface({
    input: process.stdin,
    output: process.stdout,
    prompt: 'search>>> '
});
rl.prompt(); // 提示用户输入
rl.on('line', async (line) => {
    console.log(`正在搜索 "${line.trim()}"`);
    try {
        await search(line.trim());
    } catch (error) {
        console.error('搜索过程中出错:', error.message);
    }
    rl.prompt();
}).on('close', () => {
    console.log('再见!');
    process.exit(0);
});
async function search(words) {
    return new Promise((resolve, reject) => {
        let options = {
            hostname: 'www.baidu.com',
            port: 443,
            path: `/s?wd=${encodeURIComponent(words)}`,
            method: 'GET',
            headers: {
                'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3',
                'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8',
                'Accept-Language': 'en-US,en;q=0.5',
                'Accept-Encoding': 'gzip, deflate, br'
            }
        };
        const req = https.request(options, (res) => {
            let body = [];
            const encoding = res.headers['content-encoding'];
            console.log(encoding);
            res.on('data', (chunk) => {
                body.push(chunk);
            });
            res.on('end', () => {
                body = Buffer.concat(body);
                if (encoding === 'gzip') {
                    zlib.gunzip(body, (err, decoded) => {
                        if (err) return reject(err);
                        processBody(decoded.toString());
                        resolve();
                    });
                } else if (encoding === 'br') {
                    zlib.brotliDecompress(body, (err, decoded) => {
                        if (err) return reject(err);
                        processBody(decoded.toString());
                        resolve();
                    });
                } else {
                    processBody(body.toString());
                    resolve();
                }
            });
        });
        req.on('error', (e) => {
            reject(e);
        });
        req.end();
    });
}
function processBody(body) {
    let $ = cheerio.load(body);
    $('.t a').each(function(i, el) {
        const title = $(this).text().trim();
        const href = $(this).attr('href');
        if (title && href) {
            console.log(`${title}\n${href}\n`);
        }
    });
}
```
