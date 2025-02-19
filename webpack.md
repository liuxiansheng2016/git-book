# Webpack

下面是格式化、简化并更新后的内容：

***

### **Webpack 简介**

Webpack 是一个模块化打包工具，它会分析项目结构，将浏览器不能直接运行的扩展语言（如 SCSS、TypeScript 等）转换成浏览器可执行的格式，并将各个模块打包成一个或多个静态资源。通常，Webpack 可替代 Gulp 进行前端构建任务。

> 参考资料：
>
> * [简书文章](https://www.jianshu.com/p/42e11515c10f)
> * [petehunt 的 webpack-howto](https://github.com/petehunt/webpack-howto)
> * [吴浩林的 webpack 优化](https://webpack.wuhaolin.cn/4%E4%BC%98%E5%8C%96/4-12%E6%8C%89%E9%9C%80%E5%8A%A0%E8%BD%BD.html)

***

### **Entry**

指示 Webpack 使用哪个模块作为构建依赖图的起点。例如：

```javascript
module.exports = {
  entry: './path/to/my/entry/file.js'
};
```

***

### **Output**

指定 Webpack 打包后输出文件的位置和命名规则。示例：

```javascript
const path = require('path');

module.exports = {
  entry: './path/to/my/entry/file.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'my-first-webpack.bundle.js'
  }
};
```

***

### **Loaders**

Loaders 允许 Webpack 处理非 JavaScript 文件（例如 CSS、SCSS、图片等），将它们转换为 Webpack 能够理解的模块。配置时常用两个属性：

* **test**：匹配应由该 loader 处理的文件扩展名（正则表达式）。
* **use** 或 **loader**：指定要使用的 loader 名称及其配置。

例如，使用 raw-loader 处理 .txt 文件：

```javascript
module.exports = {
  output: {
    filename: 'my-first-webpack.bundle.js'
  },
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' }
    ]
  }
};
```

**注意：** loader 的执行顺序是从后往前（例如对于 CSS 文件，通常先使用 css-loader 解析 CSS，再使用 style-loader 将 CSS 注入到页面）。

***

### **Plugins**

Plugins 用于执行范围更广的任务，比如打包优化、代码压缩、环境变量定义、资源注入等。插件配置单独放在 webpack 配置的 plugins 数组中，每个插件通常是一个插件类的实例，其参数通过构造函数传入。

例如，使用 HtmlWebpackPlugin 将模板文件转换成最终 HTML：

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({ template: './src/index.html' })
  ]
};
```

***

### **Runtime**

Webpack 的 runtime 包含了模块加载、解析和依赖连接的逻辑。它负责：

* 加载已打包的模块
* 实现懒加载和模块热替换（HMR）
* 管理模块之间的依赖关系（通过 manifest 文件）

***

### **Tree-shaking**

Tree-shaking 是一种基于 ES Module 静态分析的技术，用于移除未被引用的“死代码”，从而减小打包体积。启用条件包括：

* 使用 ES Module（ESM）编写模块代码
* 配置 `optimization.usedExports: true`
* 在生产模式下或启用代码压缩（如 `optimization.minimize: true`）

工作流程：Webpack 在构建时收集模块导出，标记未使用的部分，然后在生成产物时剔除这些代码。

***

### **TerserPlugin**

TerserPlugin 是 Webpack 内置的压缩插件，使用 Terser 库对 JavaScript 代码进行压缩、丑化和移除未使用的代码。默认在 production 模式下启用。

```
const TerserWebpackPlugin = require('terser-webpack-plugin');

module.exports = {
  optimization: {
    minimize: true,
    minimizer: [
      new TerserWebpackPlugin({
        parallel: true,  // 并行多进程加速压缩
        terserOptions: {
          compress: {
            unused: true,    // 删除未使用的代码
            dead_code: true, // 移除无法到达的路径
          },
          mangle: true,     // 启用变量名混淆
          output: {
            comments: false // 移除所有注释
          }
        }
      })
    ]
  }
};

```

***

### **模块热替换（HMR）**

HMR 允许在不刷新整个页面的情况下替换模块。使用方法通常是检查 `module.hot` 是否存在，并调用 `module.hot.accept` 来接收更新。例如：

```javascript
if (module.hot) {
  module.hot.accept('./library.js', function() {
    // 使用更新后的 library 模块执行某些操作...
  });
}
```

***

### **多入口与代码分割**

* **多入口**\
  对于多页面应用，可以配置多个入口点，Webpack 会生成多个 bundle，每个 bundle 对应一个页面或功能模块。
* **代码分割**\
  使用动态导入（import()）或 `optimization.splitChunks` 配置，将公共代码提取成单独的 chunk，从而提高加载效率和缓存利用率。
* **外部化依赖**\
  使用 externals 配置排除那些已通过全局变量提供的库，减少打包体积。

***

### **Webpack 插件开发**

实现一个 Webpack 插件的基本步骤：

1. **创建插件文件**：编写一个新的 JavaScript 文件。
2. **定义插件类**：实现一个类，该类的构造函数接收配置选项，必须包含一个 `apply(compiler)` 方法。
3. **监听生命周期事件**：在 `apply` 方法中，通过 `compiler.hooks` 监听构建过程中的事件（如 compile、emit 等）。
4. **修改输出**：在合适的钩子中对 `compilation.assets` 进行修改，或执行其他自定义逻辑。
5. **在 webpack.config.js 中使用插件**：在配置的 plugins 数组中引入并实例化该插件。

***

### **构建速度优化**

* **持久化缓存（Persistent Caching）**
  * 利用 Webpack 5 内置的文件系统缓存（`cache.type: 'filesystem'`），将编译结果存储到磁盘，显著缩短增量构建时间。
* **使用更快的编译工具**
  * **esbuild-loader** 或 **swc-loader**：替换传统的 Babel Loader，利用 Go 或 Rust 编译器大幅提升编译速度。
* **多进程/多线程编译**
  * **thread-loader**：将较重的 Loader（如 Babel Loader）的任务分发到子进程并行处理，充分利用多核 CPU 资源。
  * 插件（如 TerserWebpackPlugin）支持 `parallel: true`，在压缩阶段进行并行处理。
* **优化 Loader 配置**
  * **减少 Loader 数量**：仅针对需要处理的文件使用 Loader，避免无关文件被处理。
  * **启用缓存**：例如在 Babel Loader 中设置 `cacheDirectory: true`，减少重复编译的开销。
* **精简插件与外部化依赖**
  * 只使用必要的插件，避免功能重叠。
  * 通过 externals 配置将已由全局变量或 CDN 提供的库排除在打包外，减小 Bundle 体积和构建时间。
* **代码分割与多入口**
  * 利用动态导入（`import()`）和 `optimization.splitChunks` 自动提取公共代码，降低初始加载体积。
  * 对多页面应用配置多个入口点，实现并行构建。
* **DllPlugin 预编译**
  * 使用 DllPlugin 和 DllReferencePlugin 对第三方依赖进行预编译，使得这些依赖在后续构建时无需重新编译。
* **合理区分开发和生产模式**
  * 开发模式（`mode: 'development'`）：关闭不必要的压缩与优化，加快构建和热更新。
  * 生产模式（`mode: 'production'`）：启用 Tree-shaking、压缩等优化，以减小最终产物体积。
* **Source Map 策略**
  * 开发环境使用较快的 Source Map 类型（如 `'eval-cheap-module-source-map'`），生产环境则选择精确度更高的 Source Map 或关闭 Source Map，以平衡调试和构建速度。
