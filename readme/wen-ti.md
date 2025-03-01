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
