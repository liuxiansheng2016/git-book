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

**Angular Signals** 是 Angular v16 引入的新特性，用于管理 **状态和数据流**，旨在提供更高效的变更检测机制，与 **Zone.js** 相比，`Signals` 的数据变更机制更加 **精准**，避免了不必要的组件重渲染

* **高效变更检测**：跳过不必要的检测，只更新真正变化的部分。
* **易于追踪依赖**：明确声明组件依赖的信号，自动追踪变化。
* **提高性能**：精确触发组件更新，减少不必要的 DOM 变更。

***

#### **Signal（信号）**

* **基本的数据容器**，用于存储和管理状态变化。
* 通过 `set()` 和 `update()` 更新状态。
* 访问状态时，通过调用 `signal()` 直接读取数据。

```typescript
import { signal } from '@angular/core';

const count = signal(0);
console.log(count()); // 0

// 更新 signal
count.set(10);
console.log(count()); // 10

// 使用 update 更新当前状态
count.update(value => value + 1);
console.log(count()); // 11
```

***

#### &#x20;**Computed（计算属性）**

* **基于其他 Signal 的派生状态**，会自动追踪依赖变化。
* 只有在依赖的 Signal 发生变化时，Computed 才会重新计算。

```typescript
import { signal, computed } from '@angular/core';

const length = signal(5);
const width = signal(10);

// 计算面积
const area = computed(() => length() * width());

console.log(area()); // 50

// 更新 length
length.set(7);
console.log(area()); // 70
```

***

#### **Effect（副作用）**

* **监听信号的变化，并执行副作用操作**。
* 当依赖的 Signal 发生变化时，`Effect` 会自动触发回调。

```typescript
import { signal, effect } from '@angular/core';

const message = signal('Hello');

// 监听变化
effect(() => {
  console.log(`Message changed: ${message()}`);
});

message.set('Hi there!'); // Console: Message changed: Hi there!
```

#### &#x20;**使用 Signal 绑定到 Angular 组件**

**在组件中创建 Signal**

```typescript
import { Component, signal } from '@angular/core';

@Component({
  selector: 'app-counter',
  template: `
    <div>
      <p>Counter: {{ count() }}</p>
      <button (click)="increment()">Increment</button>
      <button (click)="reset()">Reset</button>
    </div>
  `,
})
export class CounterComponent {
  count = signal(0);

  increment() {
    this.count.update(value => value + 1);
  }

  reset() {
    this.count.set(0);
  }
}
```

***

**✅ (2) 使用 Computed 和 Effect**

```typescript
import { Component, signal, computed, effect } from '@angular/core';

@Component({
  selector: 'app-cart',
  template: `
    <div>
      <p>Price: {{ price() }}</p>
      <p>Quantity: {{ quantity() }}</p>
      <p>Total: {{ total() }}</p>
      <button (click)="increaseQuantity()">Add One</button>
    </div>
  `,
})
export class CartComponent {
  price = signal(50);
  quantity = signal(1);

  // 计算总价
  total = computed(() => this.price() * this.quantity());

  constructor() {
    // 监听 total 的变化
    effect(() => {
      console.log(`Total updated: ${this.total()}`);
    });
  }

  increaseQuantity() {
    this.quantity.update(q => q + 1);
  }
}
```

***

#### 📌 **1. 在 `@Input()` 中使用 Signal**

* 可以将 `@Input` 与 `Signal` 结合使用。

```typescript
import { Component, Input, signal } from '@angular/core';

@Component({
  selector: 'app-product',
  template: `
    <p>{{ productName() }}</p>
  `,
})
export class ProductComponent {
  @Input() productName = signal('Default Product');
}
```

***

#### 📌 **2. 使用 `@Output()` 和 Signal**

* `@Output()` 可以结合 Signal 使用 `emit()` 来触发事件。

```typescript
import { Component, EventEmitter, Output, signal } from '@angular/core';

@Component({
  selector: 'app-like',
  template: `
    <button (click)="like()">👍 Like ({{ likes() }})</button>
  `,
})
export class LikeComponent {
  likes = signal(0);
  @Output() liked = new EventEmitter<number>();

  like() {
    this.likes.update(l => l + 1);
    this.liked.emit(this.likes());
  }
}
```

***

***

#### 🎯 **1. 精准变更检测**

* Signals 通过追踪依赖来优化变更检测，只触发相关部分的更新，避免整个视图重新渲染。

***

#### 🎯 **2. 免去 Zone.js**

* 使用 Signals 时可以跳过 Zone.js 的运行，大幅提升性能。

***

#### 🎯 **3. 直观的数据流**

* 数据流更易于管理，组件可以仅更新需要更新的部分，不再依赖 `@Input` 和 `@Output`。

***

#### 🎯 **4. 简化状态管理**

* Signals 作为轻量级的状态管理工具，可在组件之间共享状态。

***

#### 📌 **1. Signal 结合服务实现全局状态管理**

```typescript
import { Injectable, signal, computed } from '@angular/core';

@Injectable({ providedIn: 'root' })
export class CounterService {
  private count = signal(0);

  // 公开只读计算属性
  currentCount = computed(() => this.count());

  increment() {
    this.count.update(value => value + 1);
  }

  reset() {
    this.count.set(0);
  }
}
```

***

#### 📌 **2. 在组件中使用服务 Signal**

```typescript
import { Component } from '@angular/core';
import { CounterService } from './counter.service';

@Component({
  selector: 'app-counter',
  template: `
    <p>Counter: {{ counterService.currentCount() }}</p>
    <button (click)="counterService.increment()">Increment</button>
    <button (click)="counterService.reset()">Reset</button>
  `,
})
export class CounterComponent {
  constructor(public counterService: CounterService) {}
}
```

***

#### 📌 **3. Signal 结合 Async Pipe 使用**

```html
<p>{{ count() | async }}</p>
```

#### ⚡️ **1. 避免滥用 Effect**

* `Effect` 会在每次依赖变化时执行，避免复杂逻辑直接放入 `Effect`。

#### ⚡️ **2. 小心循环依赖**

* `Computed` 和 `Effect` 可能引发循环依赖，应避免嵌套信号过深。

#### ⚡️ **3. Signal 不支持深层嵌套变更**

* 深层对象需要手动使用 `update()` 进行更改，避免信号无法检测深层对象变更。

***

### 🎉 **总结**

✅ **Angular Signals** 提供了更优的变更检测机制，降低了性能开销。\
✅ 通过 `signal()`、`computed()` 和 `effect()` 可以轻松实现响应式数据流。\
✅ Signals 适用于管理状态、优化性能和实现精确 DOM 变更检测。

使用 `Signals` 可以大大提升 Angular 应用的性能，尤其适合需要精确控制数据更新的场景！🚀
