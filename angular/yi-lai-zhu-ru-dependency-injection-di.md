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
          typescript复制代码@Injectable({
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

### 构造函数注入

在 Angular 中，依赖项通常是通过构造函数注入的。Angular 的编译器会分析构造函数参数，并从 DI 系统中获取相应的依赖项。

<pre class="language-javascript"><code class="lang-javascript"><strong>@Injectable({
</strong>  providedIn: 'root'
})
export class MyService {
  constructor(private anotherService: AnotherService) {}
}
</code></pre>

### 生命周期钩子和注入

```javascript
import { Component, inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Component({
  selector: 'app-root',
  template: `<h1>Hello, {{ title }}!</h1>`
})
export class AppComponent {
  title = 'Angular App';
  private http = inject(HttpClient);

  ngOnInit() {
    this.http.get('https://api.example.com/data').subscribe(data => {
      console.log(data);
    });
  }
}
```

#### **InjectionToken**

* 用途：用于创建标识符，以便在依赖注入系统中注入非类类型的值。
* 注入非类类型的值：通过 `@Inject` 装饰器和 `InjectionToken` 来实现。

#### **注入令牌**

* 定义：注入令牌是 Angular 的依赖注入系统用来识别和提供特定依赖项的唯一标识符。
*   使用场景：

    * 当你需要注入非类类型的值时，如字符串、数字、布尔值等。
    * 当你有多个实现同一个接口的服务，并且需要明确指定哪个实现应该被注入时。



```javascript
// 定义 InjectionToken
export const API_URL = new InjectionToken<string>('apiUrl');
export const TIMEOUT = new InjectionToken<number>('timeout');

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [HttpClientModule], // 确保导入 HttpClient Module
  template: `
    <h1>Hello from {{ name }}!</h1>
    <a target="_blank" href="https://angular.dev/overview">
      Learn more about Angular
    </a>
  `,
})
export class App implements OnInit {
  name = 'Angular';

  constructor(
    private httpClient: HttpClient,
    @Inject(API_URL) private apiUrl: string,
    @Inject(TIMEOUT) private timeout: number
  ) {}

  ngOnInit() {
    console.log('API URL:', this.apiUrl);
    console.log('Timeout:', this.timeout);

    // 你可以在这里使用这些配置进行其他操作
    // 例如，发起一个 HTTP 请求
    this.httpClient.get(this.apiUrl).subscribe(response => {
      console.log('Response from API:', response);
    });
  }
}

// 使用 bootstrapApplication 启动应用
bootstrapApplication(App, {
  providers: [
    { provide: API_URL, useValue: 'https://api.example.com' },
    { provide: TIMEOUT, useValue: 5000 },
  ],
});
```
