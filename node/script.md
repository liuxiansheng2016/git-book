# Script

**#!/usr/bin/env node**

!/usr/bin/env node 告诉操作系统使用 Node.js 来解释和执行这个脚本文件。

1. **#!**：这是 Shebang 的开始标志，告诉操作系统这是一个可执行脚本文件。
2. **/usr/bin/env**：这是一个路径，指向系统的 env 命令。env 命令用于查找并执行指定的程序，通常从环境变量 PATH 中查找。
3. **node**：这是 env 命令要查找并执行的程序名称。在这个例子中，node 是 Node.js 的可执行文件

只有当脚本直接在 shell 中运行时，才有意义

**设置环境变量并运行脚本**

set NODE\_ENV=production & set PORT=3000 & node app.js

Powershell:

$env:NODE\_ENV="production"; $env:PORT="3000"; node app.js

在脚本中访问环境变量：

console.log('Environment:', process.env.NODE\_ENV);

**使用 REPL (Read-Eval-Print Loop)**

如果你想快速测试一些代码片段而不创建文件，可以使用 Node.js 的交互式解释器（REPL）。只需在命令行中输入 node 即可启动 REPL。

* 启动 REPL:
  *   Bash

      ```
      node
      ```
* 然后你可以直接在命令行中输入 JavaScript 代码进行测试:
  *   JavaScript

      ```
      > console.log("Hello, world!");
      Hello, world!
      ```

npx 是一个与 npm 一起安装的工具，它允许你执行本地或远程安装的 npm 包中的命令

#### 使用 .env 文件（推荐）

为了更方便地管理环境变量，特别是对于开发环境，可以使用 .env 文件结合像 dotenv 这样的库来加载这些变量。
