# 项目结构

\


**项目结构示例**

```javascript
src/
├── app/
│   ├── app.module.ts           // 根模块
│   ├── core/                   // 核心模块
│   │   ├── core.module.ts
│   │   ├── services/           // 单例服务
│   │   ├── guards/             // 路由守卫
│   │   ├── components/         // 全局组件
│   ├── shared/                 // 共享模块
│   │   ├── shared.module.ts
│   │   ├── directives/         // 通用指令
│   │   ├── pipes/              // 通用管道
│   ├── features/               // 功能模块
│   │   ├── user/               // 用户模块
│   │   │   ├── user.module.ts
│   │   │   ├── components/
│   │   │   ├── services/
│   │   │   ├── user-routing.module.ts
│   │   ├── orders/             // 订单模块
│   │   │   ├── order.module.ts
│   │   │   ├── order-routing.module.ts
```

\
**核心模块 (core)**

* **全局服务**
* **组件**
  * `ErrorComponent`, `PageNotFoundComponent`, `SuccessComponent`
* **工厂**
  * `initial-load.factory.ts`,&#x20;
  * `translate-loader.factory.ts`,&#x20;
  * `app-preloading-strategy.factory.ts`
* **守卫 (Guards)**
* **拦截器 (Interceptors)**
  * `error.interceptor.ts`,&#x20;
  * `http-header.interceptor.ts`,&#x20;
  * `logging.interceptor.ts`,&#x20;
  * `retry.interceptor.ts`,&#x20;
  * `token.interceptor.ts`
* **模型 (Models)**: 接口文件（也可以放在 `shared` 目录下）
* **枚举 (Enums)**: 枚举文件（也可以放在 `shared` 目录下）
* `core.module.ts`: 定义了应用的核心模块配置。
* `core-routing.module.ts`: 核心模块的路由配置（404/error等）

**功能模块 (features)**

* 包含应用的各个功能模块，每个功能模块可能包含自己的组件、服务和路由。
  * 如果使用懒加载模块，则无需导入到 `app.module` 中。

**共享模块 (shared)**

* 包含共享的组件、服务和管道等，可以在多个功能模块中复用。
  * `shared.module.ts`: 定义了共享模块的配置。

**应用配置**

* `app-routing.module.ts`: 应用的路由模块，定义了应用的路由配置。
* `app.module.ts`: 应用的根模块，定义了应用的模块配置。

**静态资源 (assets)**

* **品牌资源 (brands)**
* **文档资源 (docs)**
* **favicon 图标 (favicon)**
* **字体文件 (fonts)**
* **国际化资源文件 (i18n)**
* **图片资源 (images)**

**样式 (css)**

* **品牌相关的 CSS 文件 (brands)**
* **组件相关的 CSS 文件 (components)**
* **字体相关的 CSS 文件 (fonts)**
* **CSS 混入文件 (mixins)**
* **页面设计相关的 CSS 文件 (page-design)**
* **页面相关的 CSS 文件 (pages)**
  * `Footer`, `Header`, `Menu`
* `reset.scss`: 重置样式文件。
*   `styles.scss`: 主要的样式文件。



**环境配置 (environments)**

* 不同环境下的配置文件。

**数据模拟 (mirage)**

* Mirage.js 相关文件，用于模拟数据。

**模块联邦配置**

* `module-federation.ts`: 模块联邦相关的配置文件。
