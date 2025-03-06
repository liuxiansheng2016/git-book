---
description: "Angular 的依赖注入系统是其核心特性之一。通过依赖注入，组件和服务可以轻松地获\t取所需的依赖项，而不需要手动实例化对象"
---

# 依赖注入（Dependency Injection, DI）

#### 依赖注入的作用

1. **解耦组件**：通过依赖注入，组件不需要知道它们所依赖的服务是如何被创建或配置的。这意味着你可以更轻松地替换依赖项，而无需修改依赖于该服务的组件代码。
2. **促进单元测试**：由于依赖关系是在运行时注入的，因此可以在测试环境中提供模拟或假的依赖项来替代真实的依赖项，从而更容易进行单元测试。
3. **提高模块化和重用性**：依赖注入有助于将应用程序分解成独立的模块，每个模块都有明确的责任范围。这样做的好处是提高了代码的复用性，因为一个模块可以在不同的上下文中重复使用。
4. **简化配置管理**：通过外部配置文件或编程方式来管理依赖关系，可以使配置更加集中和易于管理。
5. **支持延迟加载**：某些情况下，你可能不希望在应用启动时就加载所有的依赖项，而是根据需要动态加载它们。依赖注入可以帮助实现这一点。

### **手动提供依赖项（`providers`）**

除了在 `@Injectable({ providedIn: 'root' })` 中全局注册服务，我们也可以 **手动提供依赖项**。

&#x20;**在 `AppModule` 或 `FeatureModule` 中手动提供**

```typescript
typescript复制编辑import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AppComponent } from './app.component';
import { LoggerService } from './logger.service'; // 导入服务

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule],
  providers: [LoggerService], // 在模块中提供服务
  bootstrap: [AppComponent]
})
export class AppModule { }
```

* `providers: [LoggerService]` 让 `LoggerService` 只在 `AppModule` 作用域内可用，而不是全局。

&#x20;**在 `Component` 级别提供**

如果你想让某个组件拥有独立的 `LoggerService` 实例（而不是共享全局的），可以 **在组件的 `providers` 里定义**。

```typescript
typescript复制编辑import { Component } from '@angular/core';
import { LoggerService } from './logger.service';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
  providers: [LoggerService] // 组件级别提供
})
export class HomeComponent {
  constructor(private logger: LoggerService) {
    this.logger.log('HomeComponent 的 logger 实例');
  }
}
```

* `providers: [LoggerService]` **让每个 `HomeComponent` 都拥有自己的 `LoggerService` 实例**，

### providedIn: 'root'

* **说明**：这个选项告诉 Angular，在根注入器中提供这个服务。这意味着服务在整个应用程序中都是单例的，可以在任何组件中注入和使用。

### @Injectable()

<mark style="color:red;">`@Injectable()`</mark> <mark style="color:red;"></mark><mark style="color:red;">是 Angular 中的一个装饰器，用于标记一个类为可注入的服务</mark>。

```javascript
@Injectable({
  providedIn: 'root'
})
export class MyService {
  constructor(private anotherService: AnotherService) {}
}
```

#### `@Injectable()` 和 `@Inject`

1. `@Injectable()`
   1. **启用依赖注入**：
      * `HeroArena` 类的构造函数中需要依赖 `HeroService` 和 `HttpClient`。
      * `@Injectable()` 告诉 Angular 这个类可以通过 DI 系统注入，并且 Angular 应该解析其构造函数中的依赖项（即 `HeroService` 和 `HttpClient`）。
   2. **配置元数据**：
      *   如果需要配置服务的提供范围（如 `providedIn`），`@Injectable()` 是必要的。例如：

          ```typescript
          @Injectable({
            providedIn: 'root'
          })
          ```

          这会让 `HeroArena` 服务在整个应用的根注入器中提供。

<pre class="language-javascript"><code class="lang-javascript">import { Injectable } from '@angular/core';
<strong>import { HttpClient } from '@angular/common/http';
</strong>import { HeroService } from './hero.service';

@Injectable()
export class HeroArena {
  constructor(
    private heroService: HeroService,
    private http: HttpClient,
  ) {}

  // test harness
  getParticipants() {
    return this.heroService.getHeroes();
  }
}
</code></pre>

2. `@Inject` 装饰器

* 非类令牌：特别适用于注入非类令牌，如 `InjectionToken` 或原始类型（例如字符串、数字）。
* 显式注入：使依赖注入更加明确和清晰。
  * 示例场景：假设你有两个不同版本的 `LoggerService`，但它们都实现了同一个接口 `ILoggerService`。在这种情况下，你可以使用 `@Inject` 来明确指定要注入哪一个具体的实现。

```javascript
import { Inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';

import { HeroService } from './hero.service';

/* avoid */

export class HeroArena {
  constructor(
    @Inject(HeroService) private heroService: HeroService,
    @Inject(HttpClient) private http: HttpClient,
  ) {}
}
```

### 怎么注入

在 Angular 中，除了 **构造函数注入（Constructor Injection）**，还有其他方式可以将依赖项提供给组件或服务。以下是几种常见的依赖注入（DI）方式：

***

#### <mark style="color:red;">**1. 构造函数注入（Constructor Injection）**</mark>

🔹 **最常见、最推荐的方式**，Angular 会自动解析并注入依赖项。

```typescript
export class HeroComponent {
  constructor(private heroService: HeroService) {}
}
```

✅ **适用于**：

* **大多数情况**，特别是**服务（Service）和组件（Component）**。
* **类型可推断**的依赖（例如 `HeroService`）。

❌ **局限性**：

* **不能动态更改依赖**（只能在组件创建时注入）。
* **如果依赖是可选的或动态的，构造函数可能不是最佳选择**。

***

#### <mark style="color:red;">**2.**</mark><mark style="color:red;">**&#x20;**</mark><mark style="color:red;">**`@Inject`**</mark><mark style="color:red;">**&#x20;**</mark><mark style="color:red;">**显式注入**</mark>

🔹 **用于无法自动推断类型的情况，例如 `InjectionToken` 或字符串、数字、对象等**。

```typescript
constructor(@Inject('API_URL') private apiUrl: string) {
  console.log(this.apiUrl);
}
```

✅ **适用于**：

* **非类依赖项（string、number、object）**。
* **多个实现（useClass/useExisting）**。

***

#### **3. `@Optional()` 可选依赖注入**

🔹 **有时候，依赖项可能不存在，例如：某些服务是可选的，或者仅在某些情况下提供**。

```typescript
constructor(@Optional() private loggerService?: LoggerService) {}
```

✅ **适用于**：

* **某些服务不是必须的，组件应该在服务不存在时仍能正常工作**。
* **避免未提供依赖项时报错**。

***

#### **4. `@Self()` 仅从当前组件或指令的 `providers` 获取依赖**

🔹 **防止 Angular 解析父级 `Injector`，强制只从当前 `Injector` 获取依赖**。

```typescript
constructor(@Self() private service: MyService) {}
```

✅ **适用于**：

* **确保某个依赖项仅在当前组件或指令中注入**，而不会从父级模块继承。

❌ **局限性**：

* **如果当前 `providers` 未提供该依赖项，则会抛出错误**。

***

#### **5. `@SkipSelf()` 跳过当前级别，从父级 `Injector` 获取依赖**

🔹 **用于指令或服务时，跳过当前 `Injector`，从父级 `Injector` 获取依赖**。

```typescript
constructor(@SkipSelf() private parentService: ParentService) {}
```

✅ **适用于**：

* **子组件或指令不应该创建自己的实例，而应使用父级提供的实例**。

❌ **局限性**：

* **如果父级 `Injector` 也没有提供依赖项，则会抛出错误**。

***

#### **6. `@Host()` 限制依赖项查找范围到宿主组件**

🔹 **用于指令或子组件时，只允许依赖项来自宿主组件，而不是更高层级的 `Injector`**。

```typescript
constructor(@Host() private parentService: ParentService) {}
```

✅ **适用于**：

* **指令依赖于宿主组件的某个服务**。
* **防止意外使用了更高层级 `Injector` 提供的实例**。

***

#### <mark style="color:red;">**7.**</mark><mark style="color:red;">**&#x20;**</mark><mark style="color:red;">**`inject()`**</mark><mark style="color:red;">**&#x20;**</mark><mark style="color:red;">**在函数或生命周期钩子中手动注入**</mark>

🔹 **适用于非构造函数上下文，比如工厂函数、`useFactory` 提供者，或类的静态方法**。

```typescript
import { inject } from '@angular/core';

export class HeroComponent {
  private heroService = inject(HeroService);

  getHero() {
    return this.heroService.getHeroes();
  }
}
```

✅ **适用于**：

* **函数式注入，例如在独立函数或静态方法中获取依赖**。
* **不依赖构造函数，可以在类的其他部分动态注入**。

***

#### **8. `ViewChild` / `ContentChild` 组件内获取子组件或指令**

🔹 **可以获取组件模板中的子组件或指令，而不是通过 `providers` 注入**。

```typescript
@ViewChild(ChildComponent) childComponent!: ChildComponent;
```

✅ **适用于**：

* **组件内部需要访问子组件的方法或属性**。

***

#### **总结**

| **方式**                           | **使用场景**      | **适用情况**                    |
| -------------------------------- | ------------- | --------------------------- |
| **构造函数注入**                       | 最常见、推荐        | 适用于所有 `Service`、`Component` |
| **`@Inject()`**                  | 需要显式指定 Token  | `InjectionToken`、字符串、对象等    |
| **`@Optional()`**                | 依赖项可能不存在      | 依赖项是可选的                     |
| **`@Self()`**                    | 限制从当前组件获取     | 确保当前 `Injector` 提供依赖        |
| **`@SkipSelf()`**                | 仅从父级获取依赖      | 避免子组件创建自己的实例                |
| **`@Host()`**                    | 依赖项仅来自宿主组件    | 限制 `Injector` 查找范围          |
| **`inject()`**                   | 需要在非构造函数中获取依赖 | `useFactory` 或非类方法          |
| **`ViewChild` / `ContentChild`** | 获取模板子组件       | 组件模板中的组件或指令                 |

***

💡 **最佳实践**

1. **首选构造函数注入**，它是最简单、最推荐的方式。
2. **当依赖项是可选的时，使用 `@Optional()`**，避免应用崩溃。
3. **如果依赖项不能自动推断，使用 `@Inject()`**。
4. **如果不希望从父级继承依赖项，使用 `@Self()`**。
5. **如果希望依赖项只来自父级，而非当前组件，使用 `@SkipSelf()`**。
6. **如果在组件内部需要获取子组件，使用 `ViewChild` 或 `ContentChild`**。

这样可以 **合理利用 Angular 依赖注入的各种方式，提高代码的灵活性和可维护性** 🚀

### **InjectionToken**

* 用途：用于创建标识符，以便在依赖注入系统中注入非类类型的值。
* 注入非类类型的值：通过 `@Inject` 装饰器和 `InjectionToken` 来实现。



### `@Inject` 装饰器 使用场景

#### **注入令牌**

* 定义：注入令牌是 Angular 的依赖注入系统用来识别和提供特定依赖项的唯一标识符。
* 使用场景：
  * 当你需要注入非类类型的值时，如字符串、数字、布尔值等。
  * 当你有多个实现同一个接口的服务，并且需要明确指定哪个实现应该被注入时。



#### 1. 注入非类类型的依赖

Angular 的依赖注入系统默认使用类作为令牌来解析依赖。但有时候，你可能需要注入一些非类类型的值，比如字符串、数字、对象等。这时就可以使用 `@Inject` 装饰器结合自定义令牌来实现。

**示例代码**

```typescript
import { Component, Inject } from '@angular/core';
import { InjectionToken } from '@angular/core';

// 定义一个 InjectionToken 作为自定义令牌
export const API_URL = new InjectionToken<string>('API_URL');

// 组件
@Component({
  selector: 'app-my-component',
  template: `
    <p>API URL: {{ apiUrl }}</p>
  `
})
export class MyComponent {
  constructor(@Inject(API_URL) public apiUrl: string) {}
}

// 在模块中提供依赖
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

@NgModule({
  declarations: [MyComponent],
  imports: [BrowserModule],
  providers: [
    { provide: API_URL, useValue: 'https://example.com/api' }
  ],
  bootstrap: [MyComponent]
})
export class AppModule {}
```

#### 2. 解决依赖注入的歧义

当存在多个相同类型的依赖时，使用 `@Inject` 可以明确指定要注入的具体依赖。（<mark style="color:red;">多个实现</mark>）

**示例代码**

```typescript
import { Component, Inject } from '@angular/core';

// 定义服务接口
interface Logger {
  log(message: string): void;
}

// 定义两个不同的日志服务实现
class ConsoleLogger implements Logger {
  log(message: string) {
    console.log('Console Logger: ', message);
  }
}

class FileLogger implements Logger {
  log(message: string) {
    console.log('File Logger: ', message);
  }
}

// 定义 InjectionToken 作为自定义令牌
const CONSOLE_LOGGER = new InjectionToken<Logger>('ConsoleLogger');
const FILE_LOGGER = new InjectionToken<Logger>('FileLogger');

// 组件
@Component({
  selector: 'app-my-component',
  template: `
    <button (click)="logMessage()">Log Message</button>
  `
})
export class MyComponent {
  constructor(
    @Inject(CONSOLE_LOGGER) private consoleLogger: Logger,
    @Inject(FILE_LOGGER) private fileLogger: Logger
  ) {}

  logMessage() {
    this.consoleLogger.log('This is a console log message');
    this.fileLogger.log('This is a file log message');
  }
}

// 在模块中提供依赖
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

@NgModule({
  declarations: [MyComponent],
  imports: [BrowserModule],
  providers: [
    { provide: CONSOLE_LOGGER, useClass: ConsoleLogger },
    { provide: FILE_LOGGER, useClass: FileLogger }
  ],
  bootstrap: [MyComponent]
})
export class AppModule {}
```

在这个例子中，我们定义了两个不同的日志服务 `ConsoleLogger` 和 `FileLogger`，它们都实现了 `Logger` 接口。为了区分这两个服务，我们创建了两个 `InjectionToken` 作为自定义令牌 `CONSOLE_LOGGER` 和 `FILE_LOGGER`。在 `MyComponent` 的构造函数中，使用 `@Inject` 装饰器分别注入这两个服务。在 `AppModule` 的 `providers` 数组中，为每个令牌提供对应的服务类。

#### 3. 注入外部库或第三方服务

当需要注入外部库或第三方服务时，由于这些库或服务可能没有遵循 Angular 的依赖注入规则，使用 `@Inject` 可以方便地将它们集成到 Angular 应用中。

**示例代码**

```typescript
import { Component, Inject } from '@angular/core';
import * as moment from 'moment';
import { InjectionToken } from '@angular/core';

// 定义 InjectionToken 作为自定义令牌
export const MOMENT = new InjectionToken<typeof moment>('Moment');

// 组件
@Component({
  selector: 'app-my-component',
  template: `
    <p>Current date: {{ currentDate }}</p>
  `
})
export class MyComponent {
  currentDate: string;

  constructor(@Inject(MOMENT) private momentService: typeof moment) {
    this.currentDate = this.momentService().format('YYYY-MM-DD');
  }
}

// 在模块中提供依赖
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

@NgModule({
  declarations: [MyComponent],
  imports: [BrowserModule],
  providers: [
    { provide: MOMENT, useValue: moment }
  ],
  bootstrap: [MyComponent]
})
export class AppModule {}
```

在这个示例中，我们使用 `@Inject` 装饰器将第三方库 `moment` 注入到 `MyComponent` 中。通过定义 `InjectionToken` 类型的 `MOMENT` 作为自定义令牌，并在 `AppModule` 的 `providers` 数组中使用 `useValue` 提供 `moment` 库的引用。在 `MyComponent` 的构造函数中，使用 `@Inject(MOMENT)` 来注入这个第三方库。
