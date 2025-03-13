# 升级

在 Angular 版本升级过程中，尤其是从 Angular 14 开始引入了新的独立组件特性后，一些传统的模块配置方式可以使用新的函数式 API 来替代，下面为你总结一些常见的替代情况：

### 1. HTTP 相关

**传统方式**

在旧版本中，若要使用 HTTP 客户端功能，需要在模块中导入 `HttpClientModule`。

```typescript
import { NgModule } from '@angular/core';
import { HttpClientModule } from '@angular/common/http';

@NgModule({
  imports: [
    HttpClientModule
  ]
})
export class AppModule {}
```

**新方式**

从 Angular 15 开始，可以使用 `provideHttpClient` 函数，这种方式更适合独立组件或 Standalone API 的使用场景。

```typescript
import { bootstrapApplication } from '@angular/platform-browser';
import { AppComponent } from './app/app.component';
import { provideHttpClient } from '@angular/common/http';

bootstrapApplication(AppComponent, {
  providers: [
    provideHttpClient()
  ]
});
```

### 2. 表单相关

**传统方式**

使用响应式表单时，在模块中导入 `ReactiveFormsModule`；使用模板驱动表单时，导入 `FormsModule`。

```typescript
import { NgModule } from '@angular/core';
import { ReactiveFormsModule, FormsModule } from '@angular/forms';

@NgModule({
  imports: [
    ReactiveFormsModule,
    FormsModule
  ]
})
export class AppModule {}
```

**新方式**

使用 `provideForms` 和 `provideReactiveForms` 函数。

```typescript
import { bootstrapApplication } from '@angular/platform-browser';
import { AppComponent } from './app/app.component';
import { provideForms, provideReactiveForms } from '@angular/forms';

bootstrapApplication(AppComponent, {
  providers: [
    provideForms(), // 用于模板驱动表单
    provideReactiveForms() // 用于响应式表单
  ]
});
```

### 3. 路由相关

**传统方式**

在旧版本中，通过 `RouterModule.forRoot` 和 `RouterModule.forChild` 来配置路由。

```typescript
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

const routes: Routes = [
  // 路由配置
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule {}
```

**新方式**

使用 `provideRouter` 函数。

```typescript
import { bootstrapApplication } from '@angular/platform-browser';
import { AppComponent } from './app/app.component';
import { provideRouter } from '@angular/router';

const routes = [
  // 路由配置
];

bootstrapApplication(AppComponent, {
  providers: [
    provideRouter(routes)
  ]
});
```

### 4. 动画相关

**传统方式**

在模块中导入 `BrowserAnimationsModule` 以启用动画支持。

```typescript
import { NgModule } from '@angular/core';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';

@NgModule({
  imports: [
    BrowserAnimationsModule
  ]
})
export class AppModule {}
```

**新方式**

使用 `provideAnimations` 函数。若要禁用动画，可使用 `provideNoopAnimations` 函数。

```typescript
import { bootstrapApplication } from '@angular/platform-browser';
import { AppComponent } from './app/app.component';
import { provideAnimations } from '@angular/platform-browser/animations';

bootstrapApplication(AppComponent, {
  providers: [
    provideAnimations()
  ]
});
```

这些新的函数式 API 使得 Angular 的配置更加灵活，尤其在使用独立组件构建应用时，能更方便地按需引入所需功能。



### `DestroyRef`&#x20;

是 Angular 16 引入的一个新特性，用于在**组件、指令或服务销毁时自动执行清理操作**，它提供了一种更现代化的方式来管理 `ngOnDestroy` 生命周期钩子



1. **替代 `ngOnDestroy`**：不再需要手动实现 `OnDestroy` 接口，代码更简洁。
2. **自动清理**：可用于取消订阅 `RxJS` 订阅，避免内存泄漏。
3. **适用于 `服务` 和 `组件`**：在 `服务` 中也能检测到销毁，适用于 `providedIn: 'root'` 的服务。
4. **与 `takeUntilDestroyed()` 结合使用**：在 `RxJS` 订阅中自动完成销毁。

***

#### **基本用法**

#### **✅ 方式 1：使用 `inject(DestroyRef)`**

```typescript
typescript复制编辑import { Component, inject, DestroyRef } from '@angular/core';

@Component({
  selector: 'app-example',
  template: `<p>DestroyRef 示例</p>`,
})
export class ExampleComponent {
  private destroyRef = inject(DestroyRef);

  constructor() {
    this.destroyRef.onDestroy(() => {
      console.log('组件已销毁，执行清理操作');
    });
  }
}
```

📌 **效果：** 当 `ExampleComponent` 被销毁时，`onDestroy()` 里的回调函数会自动执行。

***

#### **✅ 方式 2：结合 `RxJS` 自动取消订阅**

```typescript
typescript复制编辑import { Component, inject, DestroyRef } from '@angular/core';
import { interval } from 'rxjs';
import { takeUntilDestroyed } from '@angular/core/rxjs-interop';

@Component({
  selector: 'app-example',
  template: `<p>观察者模式示例</p>`,
})
export class ExampleComponent {
  private destroyRef = inject(DestroyRef);

  constructor() {
    interval(1000)
      .pipe(takeUntilDestroyed(this.destroyRef))
      .subscribe((count) => console.log(`计数: ${count}`));
  }
}
```

📌 **效果：** 组件销毁后，`interval` 订阅会**自动取消**，不会导致内存泄漏。

***

#### **✅ 方式 3：在 `Service` 里监听销毁**

```typescript
typescript复制编辑import { Injectable, DestroyRef, inject } from '@angular/core';

@Injectable({ providedIn: 'root' })
export class ExampleService {
  private destroyRef = inject(DestroyRef);

  constructor() {
    this.destroyRef.onDestroy(() => {
      console.log('Service 已销毁，执行清理');
    });
  }
}
```

📌 **效果：** **当 `Service` 被销毁时**，自动执行清理操作（适用于懒加载服务）。





### **`*defer`**&#x20;

| 特性       | 传统方式                          | `*defer` 方式              |
| -------- | ----------------------------- | ------------------------ |
| **懒加载**  | `ngIf + IntersectionObserver` | ✅ `*defer (on viewport)` |
| **事件触发** | `ngIf + 事件绑定`                 | ✅ `*defer (on event)`    |
| **定时加载** | `setTimeout + ngIf`           | ✅ `*defer (on timer(x))` |
| **占位符**  | `ngIf + loading`              | ✅ `defer:placeholder`    |
| **错误处理** | 额外的 `ngIf` 逻辑                 | ✅ `defer:error`          |

#### **1. 基于事件触发**

```html
html复制编辑<button (click)="loadContent = true">加载内容</button>

<ng-container *defer (on viewport) when="loadContent">
  <p>✨ 这段内容只有在点击按钮后才会加载！</p>
</ng-container>
```

📌 **效果**：

* `when="loadContent"` 让 `*defer` 在 `loadContent` 变为 `true` 时才渲染。
* **初始状态不会渲染内容**，只有用户点击按钮后才会加载。

***

#### **✅ 2. `viewport` 触发（懒加载）**

```html
html复制编辑<ng-container *defer (on viewport)">
  <p>📌 这段内容只有当进入视口（用户滚动到这里）时才会加载！</p>
</ng-container>
```

📌 **效果**：

* **当用户滚动到该元素时才渲染**，实现懒加载（类似 `IntersectionObserver`）。

***

#### **✅ 3. 设置 `timeout`（定时加载）**

```html
html复制编辑<ng-container *defer (on timer(3000))">
  <p>⏳ 3 秒后，这段内容才会显示！</p>
</ng-container>
```

📌 **效果**：

* 页面加载 **3 秒后** 才渲染内容。

***

#### **✅ 4. `placeholder`（占位内容）**

```html
html复制编辑<ng-container *defer (on viewport)>
  <p>📌 当内容加载完成后，这里会显示实际内容！</p>
  
  <template defer:placeholder>
    <p>⏳ 正在加载，请稍候...</p>
  </template>
</ng-container>
```

📌 **效果**：

* 在内容渲染前，会先显示 **"正在加载..."**，加载完成后才替换成最终内容。

***

#### **✅ 5. `error`（错误处理）**

```html
html复制编辑<ng-container *defer (on timer(2000)) when="data">
  <p>✅ 数据加载成功：{{ data }}</p>

  <template defer:error>
    <p>❌ 数据加载失败，请重试！</p>
  </template>
</ng-container>
```

📌 **效果**：

* 如果 `data` 变量没有正确加载，会显示 `"数据加载失败"`。

### `*defer` 和 `@defer`&#x20;

在 Angular 中都用于**延迟渲染内容**，但它们的应用场景和使用方式有所不同。



| 特性        | `*defer`（Angular 17+）          | `@defer`（Angular 18+，实验性）                    |
| --------- | ------------------------------ | -------------------------------------------- |
| **用法**    | 结构性指令 (`*`)                    | Angular 模板块 (`@`)                            |
| **支持触发器** | ✅ `viewport`、`timer`、`click` 等 | ✅ `on idle`、`on viewport`、`on interaction` 等 |
| **适用于**   | HTML 模板                        | 组件模板（`@defer` 块）                             |
| **占位符**   | ✅ `defer:placeholder`          | ✅ `placeholder {}`                           |
| **错误处理**  | ✅ `defer:error`                | ✅ `error {}`                                 |
| **异步数据**  | 需要手动管理                         | ✅ `@defer (prefetch: true)` 可提前预取数据          |
| **优化方式**  | 主要用于 UI 渲染优化                   | 结合 `@block` 和 `@loading`，支持更强的优化             |

***

#### **📌 `*defer` 示例**

适用于 **模板结构控制**，比如在满足特定条件时才渲染内容：

```html
<button (click)="loadContent = true">加载内容</button>

<ng-container *defer (on timer(3000)) when="loadContent">
  <p>✨ 这段内容将在 3 秒后渲染！</p>
  
  <template defer:placeholder>
    <p>⏳ 加载中...</p>
  </template>

  <template defer:error>
    <p>❌ 加载失败！</p>
  </template>
</ng-container>
```

📌 **特点：**

* `when="loadContent"` 控制渲染时机。
* `defer:placeholder` 定义占位符内容。

***

#### **📌 `@defer` 示例**

适用于 **组件模板优化**，在合适的时机加载组件：

```html
@defer (on idle) {
  <my-heavy-component></my-heavy-component>
} placeholder {
  <p>⏳ 组件加载中...</p>
} loading {
  <p>🚀 组件即将加载！</p>
} error {
  <p>❌ 组件加载失败！</p>
}
```

📌 **特点：**

* `on idle` 只有在 **浏览器空闲** 时才加载，提高性能。
* **占位符**、**加载状态**、**错误处理** 用更直观的 `@` 语法。

#### **🎯 何时使用**

* **`*defer` 适用于 UI 片段的懒加载**，比如部分内容、简单逻辑控制。
* **`@defer` 适用于完整组件的懒加载**，可以和 `@loading`、`@error` 结合，适合更复杂的优化。

&#x20;**Angular 18+ 推荐使用 `@defer` 来优化组件渲染，而 `*defer` 更适用于局部 UI 内容的控制！**&#x20;



在 **Angular 17+** 引入的新模板控制流语法中，

### `*if` 取代了 `*ngIf`，

并且支持更清晰的 **`then...else`** 语法。

`*if="isLoggedIn; then loggedIn else loggedOut"` 的作用是：

* **如果 `isLoggedIn` 为 `true`，则渲染 `loggedIn` 模板**。
* **如果 `isLoggedIn` 为 `false`，则渲染 `loggedOut` 模板**。

***

#### **✅ 1. 基本用法**

```html
<ng-template #loggedIn>
  <p>✅ 欢迎回来，用户已登录！</p>
</ng-template>

<ng-template #loggedOut>
  <p>🔒 请先登录！</p>
</ng-template>

<div *if="isLoggedIn; then loggedIn else loggedOut"></div>
```

📌 **解析**：

* **如果 `isLoggedIn === true`**，`<ng-template #loggedIn>` 的内容会被渲染。
* **如果 `isLoggedIn === false`**，`<ng-template #loggedOut>` 的内容会被渲染。

***

#### **✅ 2. 结合 `loading` 状态**

可以扩展 `loading` 逻辑，**在加载过程中先显示 "加载中..."**：

```html
<ng-template #loggedIn>
  <p>✅ 欢迎回来，用户已登录！</p>
</ng-template>

<ng-template #loggedOut>
  <p>🔒 请先登录！</p>
</ng-template>

<ng-template #loading>
  <p>⏳ 正在检查登录状态...</p>
</ng-template>

<div *if="isLoading; then loading else (isLoggedIn ? loggedIn : loggedOut)"></div>
```

📌 **逻辑**：

* `isLoading === true` 时，显示 `"⏳ 正在检查登录状态..."`。
* 加载完成后，`isLoggedIn === true` 显示 `"✅ 欢迎回来"`，否则显示 `"🔒 请先登录"`。

***

#### **`*if` vs `*ngIf`**

| 语法       | `*if`（Angular 17+）   | `*ngIf`（传统写法）           |
| -------- | -------------------- | ----------------------- |
| **可读性**  | ✅ 更清晰直观              | ❌ 需要多个 `*ngIf` 嵌套       |
| **模板结构** | ✅ `then` & `else` 语法 | ❌ `<ng-template>` 可能较复杂 |
| **灵活性**  | ✅ 支持 `loading` 等状态   | ❌ 需要手动嵌套 `ngIf`         |

### Angular Signals&#x20;

是 Angular 团队在框架中引入的一种新的响应式编程概念

#### 创建和使用 Signals

要创建一个 Signal，你可以使用 `signal` 函数，并为其提供一个初始值：

```
count = signal(0);
```

为了响应 Signal 的变化，你可以使用 `effect` 函数注册一个副作用函数，每当 Signal 的值发生变化时，这个副作用函数就会被重新执行

#### 修改 Signals

Signals 提供了多种方法来修改其值，包括直接设置新值的 `set()` 方法、基于当前值计算新值的 `update()` 方法以及用于修改复杂数据结构的 `mutate()` 方法

#### **主要 API**

| API        | 作用                        | 示例                                           |
| ---------- | ------------------------- | -------------------------------------------- |
| `signal`   | 创建一个可响应的信号（类似 `useState`） | `count = signal(0);`                         |
| `computed` | 创建一个计算信号，依赖其他信号           | `doubleCount = computed(() => count() * 2);` |
| `effect`   | 监听信号变化并执行副作用              | `effect(() => console.log(count()));`        |

#### **✅ 1. 创建 `signal`**

`signal` 是最基础的 API，用于创建一个可响应的状态变量。

```typescript
typescript复制编辑import { signal } from '@angular/core';

export class CounterComponent {
  count = signal(0); // 创建信号

  increment() {
    this.count.update(value => value + 1); // 更新信号
  }
}
```

📌 **特点**

* `signal(0)` 创建一个信号，初始值为 `0`。
* 访问值时，使用 `count()` **（调用函数方式）**。
* `this.count.update(value => value + 1)` 更新信号值。

***

#### **✅ 2. `computed` 计算派生值**

`computed` 用于创建**派生信号**，会自动跟踪依赖的信号值。

```typescript
typescript复制编辑import { computed, signal } from '@angular/core';

export class CounterComponent {
  count = signal(1);
  doubleCount = computed(() => this.count() * 2); // 计算信号

  increment() {
    this.count.set(this.count() + 1);
  }
}
```

📌 **特点**

* `computed` 会**自动更新**，无需手动订阅。
* 当 `count()` 变化时，`doubleCount()` **自动更新**。

***

#### **✅ 3. `effect` 监听信号变化**

`effect` 用于执行**副作用**（例如 API 调用、日志记录）。

```typescript
typescript复制编辑import { effect, signal } from '@angular/core';

export class CounterComponent {
  count = signal(0);

  constructor() {
    effect(() => {
      console.log('Count changed:', this.count());
    });
  }

  increment() {
    this.count.update(c => c + 1);
  }
}
```

📌 **特点**

* `effect` **自动监听** `count` 变化，无需手动订阅。
* 当 `count()` 变化时，会触发 `console.log`。

***

#### **📌 在组件中使用 Signals**

```typescript
typescript复制编辑import { Component, signal } from '@angular/core';

@Component({
  selector: 'app-counter',
  template: `
    <p>计数：{{ count() }}</p>
    <button (click)="increment()">增加</button>
  `
})
export class CounterComponent {
  count = signal(0);

  increment() {
    this.count.set(this.count() + 1);
  }
}
```

📌 **模板中直接使用 `count()`**，不需要 `async pipe`。

#### **`Signals` vs `RxJS`**

| 特性       | Signals API  | RxJS                               |
| -------- | ------------ | ---------------------------------- |
| **状态管理** | ✅ 内置状态管理     | ❌ 需要 `BehaviorSubject`             |
| **变更检测** | ✅ 仅影响相关组件    | ❌ 可能触发整个组件树                        |
| **订阅管理** | ✅ **无需手动订阅** | ❌ 需要 `subscribe` 并手动 `unsubscribe` |
| **复杂度**  | ✅ **简单**     | ❌ 复杂，适用于大规模数据流                     |
