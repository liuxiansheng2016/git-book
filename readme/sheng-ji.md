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



### &#x20;`@defer`&#x20;

在 Angular 中都用于**延迟渲染内容**，但它们的应用场景和使用方式有所不同。

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



### `@if`&#x20;

是 **Angular 18+** 引入的新语法，用于替代 `*ngIf`，提供更 **简洁**、**可读性更高** 的条件渲染方式。

***



```html
@if (condition) {
  <p>条件为 true，显示这个内容！</p>
} @else {
  <p>条件为 false，显示这个内容！</p>
}
```

```typescript
typescript复制代码import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
    <button (click)="isLoggedIn = !isLoggedIn">切换状态</button>

    @if (isLoggedIn) {
      <p>🎉 欢迎回来，用户已登录！</p>
    } @else {
      <p>🔑 请先登录！</p>
    }
  `,
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  isLoggedIn = false;
}
```

**📌 解释**

* `@if (isLoggedIn)`：如果 `isLoggedIn` 为 `true`，显示 "🎉 欢迎回来"。
* `@else`：如果 `isLoggedIn` 为 `false`，显示 "🔑 请先登录"。
* **点击按钮** 切换 `isLoggedIn` 的值。

***



```html
html复制代码@if (status === 'success') {
  <p>✅ 操作成功！</p>
} @else if (status === 'loading') {
  <p>⏳ 加载中...</p>
} @else {
  <p>❌ 发生错误！</p>
}
```

```typescript
typescript复制代码import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  status: 'success' | 'loading' | 'error' = 'loading';
}
```

Angular 18 还支持 `@for`，可以和 `@if` 结合：

```html
html复制代码<ul>
  @for (item of items; track item) {
    @if (item.visible) {
      <li>{{ item.name }}</li>
    }
  }
</ul>
```

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  items = [
    { name: '苹果', visible: true },
    { name: '香蕉', visible: false },
    { name: '橙子', visible: true }
  ];
}
```

**📌 解释**

* 只显示 `visible: true` 的项目（`苹果` 和 `橙子`）。
* `@for` 进行循环，`@if` 过滤数据。



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
