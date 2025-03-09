# 问题

### **捕获错误**

#### **组件内处理**：

在组件中使用 `try...catch` 块或在 `Observable` 的 `subscribe` 方法中处理错误。

#### **全局捕获错误**

1. **window.onerror**
2. 创建一个实现 `ErrorHandler` 接口的全局错误处理器类，并在根模块中提供该类。

```
import { ErrorHandler, Injectable } from '@angular/core';

@Injectable()
export class GlobalErrorHandler implements ErrorHandler {
  handleError(error: any) {
    console.error('Global error:', error);
    // 可以在这里添加更多的错误处理逻辑，如发送错误报告到服务器
  }
}
```

```
import { NgModule } from '@angular/core';
import { GlobalErrorHandler } from './global - error - handler';

@NgModule({
  providers: [
    { provide: ErrorHandler, useClass: GlobalErrorHandler }
  ]
})
export class AppModule { }
```

### 在 Angular 中优化性能

Angular 应用程序的性能优化可以从多个方面入手，下面列出了一些常见的策略：

1. **使用 OnPush 变更检测策略**：默认情况下，Angular 的变更检测机制会检查所有绑定的数据是否发生变化。采用 `OnPush` 策略可以减少不必要的变更检测，提高性能
2. **懒加载模块**：利用 Angular 的懒加载特性，可以在用户访问特定路由时才加载相应的模块，从而减少初始加载时间
3. **使用轻量级管道**：尽量使用纯管道（pure pipes），避免复杂的计算逻辑，这样可以减少不必要的渲染工作
4. **启用生产模式**：确保在生产环境中启用了 AOT 编译和生产模式，这将移除调试信息并最小化代码体积
5. **优化组件的渲染性能**：比如使用 `trackBy` 函数帮助 `ngFor` 提升列表渲染效率
6. **使用虚拟滚动技术**：当处理大量数据时，可以考虑使用虚拟滚动技术，只渲染可见的部分内容，以提升滚动性能
7. **其他优化手段**：还包括索引术、压缩术、缓存术、预取术等通用的性能优化方法，这些方法不仅适用于后端开发，也可以应用于前端场景

#### 优化 Angular 应用的 SEO

为了优化 Angular 应用的 SEO，你可以采取以下几种方法：

1. **服务器端渲染 (SSR)**：使用 Angular Universal 来实现服务端渲染。这使得搜索引擎爬虫可以直接获取到页面的内容，而不是依赖 JavaScript 渲染
2. **预渲染**：对于静态内容，可以考虑预渲染技术，如 Scully 或者 prerender.io，它们可以在构建时生成静态 HTML 文件
3. **Meta 标签管理**：动态设置 meta 标签是提高 SEO 的一个重要方面。可以通过 Angular 的 Meta 服务来动态地设置 title、description 等 meta 标签
4. **Stateful URLs** 和 **Configurable URLs**：确保 URL 是有意义且易于理解的，并且能够准确反映页面的内容。同时避免使用哈希模式（hash mode），除非必要，因为这会影响 SEO
5. **性能优化**：包括减少加载时间、懒加载模块、代码分割等。更快的加载速度有助于提升用户体验，间接影响 SEO 排名

## 优化 **Angular 项目的构建速度**&#x20;

#### **Angular 命令与编译模式**

* `ng serve` 默认使用 JIT（开发环境）。
* `ng build --prod` 默认使用 AOT（生产环境）。
* 可通过 `--aot` 标志强制启用 AOT（如 `ng serve --aot`）。

### **启用 AOT（Ahead-of-Time）编译（默认）**

### **使用 Ivy 以减少构建时间 （默认）**

**Ivy** 是 Angular 的新编译器，提供了更快的构建时间和更小的 Bundle 体积。

### 构建缓存（默认启用）

当开发者运行构建命令（如 `ng build` 或 `ng serve`）时，Angular CLI 会检查是否有可用的缓存。如果存在有效的缓存，则会使用这些缓存来加快构建过程。持久化的构建缓存是一种将构建结果存储在磁盘上的功能，具体来说是在 `.angular/cache` 文件夹下

#### 缓存的内容

缓存的内容非常广泛，包括但不限于 Babel 转换的结果、压缩后的 CSS 和 JS 的结果、构建依赖项及其解析、模块转换的结果等

### &#x20;**启用增量构建**

**Angular 13+** 版本支持 **增量编译**，避免重复编译未更改的代码。\
增量编译（**Watch Mode**）允许 Angular 在代码变更时 **仅重新编译修改的部分**，加快构建速度。\
在 Angular 中，可以使用以下方式启用 **增量编译模式**。

#### **🔹 方式 1：使用 `ng build --watch`**

```sh
ng build --watch
```

* **作用**：监听文件变化，并在修改代码时 **自动增量编译**。
* **适用场景**：如果你的应用需要 **不断进行构建**（如 SSR 或与后台系统集成）。
* **默认使用 AOT 编译**（可通过 `--aot=false` 关闭）。

#### **`ng build 和 ng build --watch`**

* **`ng build`**：适用于需要进行一次性构建的场景，例如准备将应用部署到生产环境时。在部署前，使用 `ng build --configuration=production` 进行生产环境的构建，生成优化后的静态文件，然后将这些文件部署到服务器上。
* **`ng build --watch`**：主要用于开发过程中。在开发新功能、修复 bug 或进行代码调试时，开发者可以使用该命令，实时看到代码修改后的效果。它与本地开发服务器（如 `ng serve`）配合使用效果更佳，当文件变化时，服务器会检测到构建结果的更新并自动刷新浏览器，实现实时预览。

#### **🔹 方式 2：使用 `ng serve`（推荐开发模式）**&#x20;

#### `ng serve` 不仅启动了本地开发服务器，还默认包含了文件监控和增量构建的功能

**适用于本地开发（JIT 模式 + HMR）**

```sh
ng serve
```

* **作用**：在 **开发服务器模式** 下监听文件变化，并自动 **重新编译 + 刷新页面**。
*   **默认使用 JIT（Just-In-Time）编译**，但可以启用 AOT：

    ```sh
    ng serve --aot
    ```
*   **如果启用 HMR（热模块替换），只更新变更的模块，而不刷新整个页面**：

    ```sh
    ng serve --hmr
    ```

#### **`angular.json` 自定义 Watch Mode**

可以在 `angular.json` 的 `build` 选项中启用 `watch`：

```json
"projects": {
  "my-angular-app": {
    "architect": {
      "build": {
        "options": {
          "watch": true
        }
      }
    }
  }
}
```

然后执行：

```sh
ng build
```

此时 Angular 会**默认开启 Watch Mode**，无需手动添加 `--watch` 选项。

### **代码分割（Lazy Loading & Differential Loading）**

✅ **模块级 Lazy Loading**：

```typescript
const routes: Routes = [
  { path: 'dashboard', loadChildren: () => import('./dashboard/dashboard.module').then(m => m.DashboardModule) }
];
```

### **优化依赖项**

✅ **移除不必要的依赖**：

```sh
npm prune
```

`npm prune` 主要用于 **清理 `node_modules` 目录**，删除 `package.json` 和 `package-lock.json` 中 **未列出的依赖**。

```sh
npm prune --production
```

**只保留 `dependencies`，删除 `devDependencies`**（适用于生产环境）。

| 命令           | 作用                                              | 适用场景                     |
| ------------ | ----------------------------------------------- | ------------------------ |
| `npm prune`  | 删除 `node_modules` 里不需要的依赖                       | **清理无用依赖**               |
| `npm ci`     | 彻底删除 `node_modules` 并按 `package-lock.json` 重新安装 | **CI/CD、干净安装**           |
| `npm dedupe` | 去重 `node_modules` 里的重复依赖                        | **优化 `node_modules` 体积** |

### **生产环境构建优化**

在生产环境中使用 `ng build --configuration=production`&#x20;

开启 `sourceMap=false` 以减少构建时间

```json
"configurations": {
  "production": {
    "optimization": true,
    "sourceMap": false,
    "extractCss": true,
    "namedChunks": false,
    "aot": true
  }
}
```

* 启用 **AOT（Ahead-of-Time）编译**
* **启用 `optimization`（代码压缩、混淆）**
* **启用 `buildOptimizer`（构建优化）**
* **移除 `console.log` 等调试代码**
* **Tree-shaking**（删除未使用的代码）
* **禁用 source map**（默认）

### **代码优化**

减少不必要的 `Change Detection`，使用 `ChangeDetectionStrategy.OnPush`&#x20;

避免过多 `ngIf`，使用 `hidden` 或 `ng-container`&#x20;

合理使用 `trackBy` 优化 `*ngFor`



## **Angular CLI 的 Webpack 配置**

Angular CLI 使用 **Webpack 内部配置**，通常开发者无需修改。但你可以通过以下方式 **自定义 Webpack 配置**：**拓展**原有的Webpack配置。

**使用 `angular.json` 配置：**

在 `angular.json` 文件中，你可以修改构建选项，例如：

* 设置 `optimization`、`sourceMap` 等选项
* 配置文件输出路径

**自定义 Webpack 配置：**

如果需要更细粒度的控制，可以通过自定义 Webpack 配置，配合 `@angular-builders/custom-webpack` 实现：

```sh
npm install @angular-builders/custom-webpack --save-dev
```

然后在 `angular.json` 中修改构建配置：

```json
{
  "builder": "@angular-builders/custom-webpack:browser",
  "options": {
    "webpackConfig": "./webpack.config.js"
  }
}
```

```
module.exports = {
  plugins: [
    // 自定义插件
  ],
  module: {
    rules: [
      // 自定义模块规则
    ]
  }
};
```

这个配置将会与默认的Angular CLI Webpack配置合并，从而允许你添加额外的加载器、插件等。
