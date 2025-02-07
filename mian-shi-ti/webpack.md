# Webpack

## webpack treeShaking 失效
可能原因包括：未使用 ES6 模块、代码中有副作用、配置错误、babel 配置导致 tree shaking 失效等。

## webpack 异步加载
可以使用 `import()` 语法进行代码拆分，实现按需加载，减少初始加载时间。

## webpack 中的 externals 作用是什么？
`externals` 用于防止将某个依赖打包到 bundle 中，而是在运行时从外部获取这些依赖。

## sideEffects
`sideEffects` 字段用于标记哪些文件有副作用，帮助 Webpack 更好地进行 tree shaking。

## JS文件中的 css 提取到单独的样式文件
可以使用 `mini-css-extract-plugin` 插件将 CSS 提取到单独的文件中，减少 JS 文件体积。

## Webpack hash
Webpack hash 用于文件名中，帮助实现文件缓存和版本控制。包括 `[hash]`, `[chunkhash]`, `[contenthash]` 三种。

## webpack-dev-server
`webpack-dev-server` 提供一个简单的 Web 服务器，并且具有实时重载功能，方便开发调试。

## Webpack热更新
热更新（HMR）允许在运行时替换模块，而无需刷新整个页面，提高开发效率。

## webpack 异步加载解析
Webpack 通过动态 `import()` 语法实现异步加载，解析时会生成独立的 chunk 文件。

## Webpack之公共资源提取
使用 `SplitChunksPlugin` 插件可以将公共模块提取到单独的文件中，减少重复代码。

## Webpack loader
Loader 用于对模块的源代码进行转换，如处理 CSS、图片、ES6 转 ES5 等。

## Webpack chunk/module/bundle
Chunk 是 Webpack 打包输出的代码块，Module 是代码中的一个个模块，Bundle 是最终生成的文件。

## webpack配置项，什么情况需要多个入口
如果项目中有多个独立的页面或应用模块，需要分别打包时，可以使用多个入口配置。


Tree Shaking 主要用于移除未使用的代码。
terser 主要用于压缩和混淆代码。

## Webpack 中 [hash]、[chunkhash] 和 [contenthash] 的区别

在 Webpack 的配置中，`[hash]`、`[chunkhash]` 和 `[contenthash]` 是用于生成输出文件名的占位符，它们各自有不同的用途和含义。正确使用这些占位符可以帮助优化缓存策略，提高应用的加载性能。

### 区别

#### [hash]

- **描述**：表示的是整个构建过程的哈希值。也就是说，只要项目中的任何一个文件发生变化，所有的输出文件名中的 `[hash]` 都会更新。
- **适用场景**：适用于希望当有任何更改时强制客户端重新下载所有资源的情况。
- **示例**：
  ```javascript
  output: {
    filename: '[name].[hash].js',
    chunkFilename: '[id].[hash].js',
  }
- **缺点**：即使是非相关文件的变更也会导致所有文件的哈希值变化，可能导致不必要的缓存失效

#### [chunkhash]
- **描述**：基于每个入口点(chunk)的内容生成哈希值。这意味着只有特定入口点或其依赖发生改变时，相应的哈希值才会变化。
- **适用场景**：更加细粒度地控制缓存，因为只有实际修改的部分需要被重新下载。
- **示例**：，
  ```javascript
output: {
  filename: '[name].[chunkhash].js',
  chunkFilename: '[id].[chunkhash].js',
}
```
注意：如果多个入口共享模块，则这些模块的变更会导致依赖它的所有入口点的 [chunkhash] 发生变化。
  
