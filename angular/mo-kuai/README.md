---
description: Angular 应用程序由多个模块组成，每个模块负责处理特定的功能或一组相关的功能
---

# 模块

## 常见的模块结构分类包括：

1.  Ap**pModule**\
    \
    每个 Angular 应用程序至少有一个根模块，通常命名为 AppModule

    * 作用：
      * 应用程序的根模块，用于引导整个应用。
      * 包含应用启动时必须加载的全局内容。
      * 仅包含一次性的全局配置，不应该包含太多功能逻辑。
    * 主要内容：
      * 引导组件（通常是 `AppComponent`）。
      * 根级路由配置。
    * 示例：

    ```javascript
    @NgModule({
      declarations: [
        AppComponent, // 根组件
      ],
      imports: [
        BrowserModule,
        AppRoutingModule, // 路由模块
        CoreModule,       // 核心模块
        SharedModule,     // 共享模块
      ],
      bootstrap: [AppComponent], // 应用启动组件
    })
    export class AppModule {}
    ```
2.  #### **Feature modules**



    特性模块用于组织与特定功能相关的组件、服务、指令等。

    * 作&#x7528;**：**
      * 按功能划分的业务模块，管理独立的业务逻辑。
      * 便于功能模块的懒加载。
    * 设计原则：
      * 遵循模块独立性，避免依赖共享模块以外的模块。
      * 可以导入 `SharedModule` 以复用通用组件
    *   示例：

        ```javascript
        @NgModule({
          declarations: [
            UserListComponent,
            UserDetailsComponent,
          ],
          imports: [
            CommonModule,
            UserRoutingModule, // 功能模块路由
            SharedModule,      // 共享模块
          ],
        })
        export class UserModule {}
        ```
3. co**remodule**\
   通常包含核心模块和服务，如全局服务、拦截器等\

   * **作用：**
     * 提供应用的核心功能和单例服务。
     * 仅在 `AppModule` 中加载，避免被重复导入。
     * 包含应用的全局配置逻辑，例如全局服务。
   * **主要**内容：
     * 单例服务（如认证服务、API 服务）。
     * 全局组件（如Error/pagenotfount/success）。
     * 应用初始化逻辑（如拦截器、导航守卫）。
   * 设计原则：
     * **只加载一次**：通过防止重复导入（`throwIfAlreadyLoaded` 方法）
4. Share**dModule（共享模块）**\
   \
   共享模块包含可以在多个特性模块中复用的组件、指令和服务
   * 作用：
     * 提供通用功能和组件，供多个模块共享。
     * 避免重复定义组件或指令，提高代码复用性。
   * **主**要内容：
     * 通用组件（如按钮、模态框）。
     * 常用指令（如 `HighlightDirective`）。
     * 常用管道（如日期格式化）。\


## 模块的属性

* declarations: 声明属于该模块的组件、指令和管道。
* imports: 导入其他模块，以便在当前模块中使用它们的导出内容。
* providers: 注册服务提供者，使其在模块范围内可用。
* bootstrap: 指定应用程序启动时应该引导的根组件



## 框架的核心库或模块



**`@angular/core`**

* **作用**：这是 Angular 的核心模块，包含所有基本功能，用于创建组件、指令、服务和依赖注入。
* **主要内容**：
  * `Component`、`Directive`、`Pipe` 装饰器
  * 依赖注入的核心功能
  * 生命周期钩子（如 `OnInit`, `OnDestroy`）

***

#### 2. **浏览器相关模块**

**`@angular/platform-browser`**

* **作用**：提供与浏览器相关的功能。
* **主要内容**：
  * 运行 Angular 应用程序的核心功能
  * 提供 DOM 操作的工具
  * 包含 `BrowserModule`，必须导入才能启动应用

**`@angular/platform-browser-dynamic`**

* **作用**：支持在浏览器中动态编译 Angular 应用（仅开发时使用）。
* **主要内容**：
  * 包含 `platformBrowserDynamic`，用于引导（bootstrap）应用。

***

#### 3. **表单模块**

**`@angular/forms`**

* **作用**：用于处理用户输入和表单。
* **主要内容**：
  * **模板驱动表单**：通过模板（HTML）管理表单逻辑。
  * **响应式表单**：通过 TypeScript 管理表单逻辑，适合复杂表单。
  * 表单验证器（如必填、模式匹配等）。

***

#### 4. **HTTP 模块**

**`@angular/common/http`**

* **作用**：提供与后端通信的 HTTP 客户端。
* **主要内容**：
  * `HttpClient`：用于发起 GET、POST 等请求。
  * 拦截器（Interceptors）：用于修改请求或响应。
  * 响应流处理（如 RxJS Observables）。

***

#### 5. **路由模块**

**`@angular/router`**

* **作用**：用于设置单页面应用（SPA）的路由功能。
* **主要内容**：
  * 路由定义和导航（如 `RouterModule.forRoot`）。
  * 路由守卫（Guards）：控制路由访问（如 `CanActivate`, `CanDeactivate`）。
  * 路由参数和懒加载支持。
  *   <mark style="color:red;">**路由懒加载模块**</mark>

      ```javascript
       loadChildren: () => import('./product/product.module').then(m => m.ProductModule)
      ```

      <mark style="color:red;">如果你的功能模块是通过路由懒加载的方式引入的，那么你不需要直接在 AppModule 中导入这些模块。相反，你应该在路由配置中定义它们</mark>

***

#### 6. **动画模块**

**`@angular/animations`**

* **作用**：为应用添加动画效果。
* **主要内容**：
  * 动画 API（如 `trigger`, `state`, `transition`）。
  * 支持复杂的动画流程和时间控制。

***

#### 7. **通用工具模块**

**`@angular/common`**

* **作用**：提供常用的工具和指令。
* **主要内容**：
  * 常用管道（如 `DatePipe`, `UpperCasePipe`）。
  * 内置指令（如 `NgIf`, `NgFor`）。

***

#### 8. **服务端渲染模块**

**`@angular/platform-server`**

* **作用**：支持服务器端渲染（SSR）。
* **主要内容**：
  * 用于生成 Angular Universal 应用。
  * 提供 SEO 优化和更快的初次加载。

***

#### 9. **测试模块**

**`@angular/core/testing`**

* **作用**：用于单元测试和集成测试。
* **主要内容**：
  * 测试工具，如 `TestBed`, `async`.
  * 模拟服务和依赖的工具。

**`@angular/platform-browser/testing`**

* **作用**：用于测试与 DOM 相关的功能。
* **主要内容**：
  * 提供测试浏览器相关 API 的工具。

***

#### 10. **国际化模块**

**`@angular/localize`**

* **作用**：支持多语言和国际化。
* **主要内容**：
  * 提供多语言翻译工具。
  * 配合 CLI 实现动态内容翻译。

