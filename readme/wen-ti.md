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

### **🔹 1. 启用 AOT（Ahead-of-Time）编译**

**AOT（预编译）** 通过在 **构建时** 进行编译，减少运行时的编译工作，加快应用启动速度。\
✅ **在 `angular.json` 中启用 AOT**：

```json
"angularCompilerOptions": {
  "enableIvy": true,
  "strictInjectionParameters": true
}
```

✅ **生产环境默认使用 AOT**：

```sh
ng build --prod
```

### **🔹 2. 使用 Ivy 以减少构建时间**

**Ivy** 是 Angular 的新编译器，提供了更快的构建时间和更小的 Bundle 体积。\
✅ **确保在 `angular.json` 中启用了 Ivy**：

```json
"angularCompilerOptions": {
  "enableIvy": true
}
```

### **🔹 3. 启用增量构建（Build Cache）**

**Angular 13+** 版本支持 **增量编译**，避免重复编译未更改的代码。\
✅ **在 `angular.json` 中启用**：

```json
"cli": {
  "cache": {
    "enabled": true,
    "path": ".angular/cache",
    "environment": "all"
  }
}
```

✅ **手动清除缓存**（避免缓存问题时）：

```sh
ng cache clean
```

### **🔹 4. 使用 ESBuild**

从 **Angular 14+** 开始，**ESBuild** 提供了更快的构建速度。\
✅ **确保项目已升级到 Angular 14+**，它会自动使用 ESBuild。

### **🔹 5. 代码分割（Lazy Loading & Differential Loading）**

✅ **模块级 Lazy Loading**：

```typescript
const routes: Routes = [
  { path: 'dashboard', loadChildren: () => import('./dashboard/dashboard.module').then(m => m.DashboardModule) }
];
```

✅ **启用 Differential Loading**（只生成浏览器支持的代码）：

```sh
ng build --configuration production
```

✅ **使用 `esbuild` 来替代 Terser 进行 JS 优化**：

```sh
NG_BUILD_MANGLE=true ng build --prod
```

### **🔹 6. 优化依赖项**

✅ **移除不必要的依赖**：

```sh
npm prune
```

✅ **使用 `webpack-bundle-analyzer` 检查依赖体积**：

```sh
npm install --save-dev webpack-bundle-analyzer
ng build --prod --stats-json
npx webpack-bundle-analyzer dist/stats.json
```

### **🔹 7. 生产环境构建优化**

✅ **在生产环境中使用 `ng build --configuration=production`** ✅ **开启 `sourceMap=false` 以减少构建时间**

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

### **🔹 8. 使用 `ng serve --hmr` 进行热更新**

✅ **启用 HMR（Hot Module Replacement）** 以减少开发时的刷新时间：

```sh
ng serve --hmr
```

✅ **在 `angular.json` 中配置 `hmr`**

```json
"configurations": {
  "hmr": {
    "sourceMap": false,
    "optimization": false
  }
}
```

### **🔹 9. 代码优化**

✅ **减少不必要的 `Change Detection`**，使用 `ChangeDetectionStrategy.OnPush` ✅ **避免过多 `ngIf`，使用 `hidden` 或 `ng-container`** ✅ **避免 `console.log` 影响编译** ✅ **合理使用 `trackBy` 优化 `*ngFor`**

### **🔹 10. 使用 `Nx` 进行更快的增量编译**

**Nx** 是一个用于大规模 Angular 项目的构建优化工具。\
✅ **安装 Nx 并使用**

```sh
npx create-nx-workspace myworkspace
```

✅ **启用 `Nx` 增量编译**

```sh
nx build my-app --parallel
```

***

#### **🔹 总结**

| **优化方式**         | **作用**                   |
| ---------------- | ------------------------ |
| **AOT 编译**       | 提前编译减少运行时开销              |
| **Ivy 编译器**      | 提高构建速度和体积优化              |
| **增量构建**         | 避免重复编译未更改代码              |
| **Lazy Loading** | 减少初始加载时间                 |
| **ESBuild**      | 替代 Terser 提高构建速度         |
| **依赖优化**         | 减少包大小和编译时间               |
| **生产环境优化**       | 使用 `sourceMap=false` 等优化 |
| **HMR 热更新**      | 提高开发时的刷新速度               |
| **Nx 工具**        | 提供更快的增量构建                |
| **Webpack 分析**   | 发现并优化依赖                  |

✅ **最佳实践：**

1. **开启 Ivy + AOT**
2. **启用增量构建（Build Cache）**
3. **使用 Lazy Loading 进行代码分割**
4. **优化依赖项，移除无用代码**
5. **启用 ESBuild 提升构建速度**

如果你的 Angular 项目构建速度较慢，结合上述优化策略可以 **大幅提升构建速度** 🚀
