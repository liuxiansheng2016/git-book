---
description: "Angular 的依赖注入系统是其核心特性之一。通过依赖注入，组件和服务可以轻松地获\t取所需的依赖项，而不需要手动实例化对象"
---

# 依赖注入（Dependency Injection, DI）

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
