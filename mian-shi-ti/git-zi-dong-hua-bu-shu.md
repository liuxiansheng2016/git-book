# Git 自动化部署

## deploy.yml&#x20;

deploy.yml 文件是 GitHub Actions 工作流配置文件的一种，通常用于定义自动化部署过程。它使用 YAML 格式来描述一系列任务和条件，这些任务将在特定事件发生时自动执行

## npm run deploy 和 GitHub Actions 的部署是两种不同的部署方式：

### &#x20;npm run deploy：

·  本地手动部署方式

·  直接从你的电脑构建并部署

·  使用 gh-pages 包将构建文件推送到 gh-pages 分支

·  适合在没有配置 GitHub Actions 时使用

·  或者需要快速测试部署时使用

### GitHub Actions (deploy.yml)：

·  自动化部署方式

·  当你推送代码到 main 分支时自动触发

·  在 GitHub 的服务器上构建和部署

·  更适合团队协作和持续集成/部署(CI/CD)

·  不需要在本地运行部署命令

"deploy": "next build && type nul > out/.nojekyll && gh-pages -d out"

#### next build

· 作用: 这个命令是 Next.js 框架提供的一个构建命令，它会编译应用程序并优化页面，将它们转换成静态文件或服务端渲染的应用程序。最终生成的文件一般位于 .next 目录中。

#### && type nul > out/.nojekyll

· 作用: 这一部分创建了一个名为 .nojekyll 的空文件，并将其放置在 out/ 目录下。

· type nul: 是 Windows 命令行用来创建空文件的方法。nul 表示空设备，在这里它用作输入源，意味着没有实际内容写入文件。

· >: 输出重定向操作符，它会将左边命令的结果输出到右边指定的文件。如果文件不存在，则会创建该文件；如果文件存在，则会覆盖其内容。

· .nojekyll 文件的作用: 它告诉 GitHub Pages 不要使用 Jekyll 来处理站点内容。Jekyll 是 GitHub Pages 默认使用的静态网站生成器，但有时你可能不希望它处理你的 HTML、CSS 和 JavaScript 文件，而是直接作为静态文件提供服务。这时就需要添加 .nojekyll 文件来阻止 Jekyll 的处理。

#### && gh-pages -d out

· 作用: 使用 gh-pages 包将构建好的静态文件部署到 GitHub Pages。

· gh-pages: 是一个 npm 包，它可以方便地将本地目录的内容推送到 GitHub 仓库的特定分支（通常是 gh-pages 分支）。

· -d out: -d 参数指定了要部署的本地目录，这里是 out/。构建过程产生的静态文件应该已经复制到了这个目录中。
