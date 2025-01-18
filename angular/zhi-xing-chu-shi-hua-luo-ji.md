# 执行初始化逻辑

## APP\_INITIALIZER

APP\_INITIALIZER 是一个用于在应用启动时执行初始化逻辑的注入令牌

```javascript
export class ConfigService {
  private config: any;

  constructor(private http: HttpClient) {}

  loadConfig(): Observable<any> {
    if (this.config) return of(this.config);

    return this.http.get('/assets/config.json').pipe(
      tap(data => this.config = data),
      catchError(error => {
        console.error('Error loading configuration:', error);
        throw error; // 或者返回默认配置
      })
    );
  }
}

import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { HttpClientModule } from '@angular/common/http';
import { AppComponent } from './app.component';
import { ConfigService } from './config.service';

export function initConfig(configService: ConfigService): () => Promise<any> {
  return () => firstValueFrom(configService.loadConfig());
}

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, HttpClientModule],
  providers: [
    // 如果你在 ConfigService 中使用了 providedIn: 'root'，这里可以省略
    // ConfigService,
    {
      provide: APP_INITIALIZER,
      useFactory: initConfig,
      deps: [ConfigService],
      multi: true
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

1. **`useFactory`**:
   * 它接受一个工厂函数，在注入 `APP_INITIALIZER` 时，Angular 会调用该函数来获取实际的值。
   * 工厂函数支持依赖注入（通过 `deps` 数组指定依赖项，例如 `HttpClient`）。
2. **`deps`**:
   * `deps` 定义了工厂函数需要哪些依赖，这些依赖会由 Angular 的依赖注入机制提供。
   * 在这个例子中，`initializeApp` 函数需要 `HttpClient`。
3. **返回值**:
   * 工厂函数返回一个函数（箭头函数），这个函数返回一个 `Promise`，确保 Angular 在应用初始化期间等待异步操作完成。

## provideAppInitializer

```javascript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { provideAppInitializer } from '@angular/core/rxjs-interop';
import { MyConfigService } from './my-config.service';

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule],
  providers: [
    MyConfigService,
    provideAppInitializer(() => (injector: Injector) => {
      const myConfigService = injector.get(MyConfigService);
      return myConfigService.loadConfig();
    })
  ],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

## 区别：&#x20;

1. APP\_INITIALIZER：是一个注入令牌，用于在应用启动之前执行初始化逻辑。
2. &#x20;provideAppInitializer：是一个辅助函数，提供了更简洁和类型安全的方式来配置 APP\_INITIALIZER。

## bootstrapApplication&#x20;

启动独立组件

```javascript
@Component({
  selector: 'app-root',
  standalone: true,
  imports: [provideHttpClient(withInterceptorsFromDi())],
  template: `
    <h1>Hello from {{ name }}!</h1>
    <a target="_blank" href="https://angular.dev/overview">
      Learn more about Angular
    </a>
  `,
})
export class App {
  name = 'Angular';

  constructor(private configService: MyConfigService) {}

  ngOnInit() {
    this.configService.loadConfig().then(() => {
      console.log('Configuration loaded');
    });
  }
}

// 自定义初始化逻辑
const initApp = async () => {
  const configService = inject(MyConfigService);
  await configService.loadConfig();
};

// 使用 bootstrapApplication 启动应用
bootstrapApplication(App, {
  providers: [
    MyConfigService,
    { provide: APP_INITIALIZER, useFactory: initApp, multi: true },
  ],
});
```

## platformBrowser().bootstrapModule(MyModule)

1. **platformBrowser()**：
   * 这是一个函数，返回一个代表当前平台的 `PlatformRef` 对象。在浏览器环境中，这通常是 `BrowserPlatformLocation`。
   * 它的作用是提供一个与特定平台交互的入口点。对于浏览器环境，这是必要的，因为 Angular 需要知道如何与浏览器 DOM 和其他特性进行交互。
2. **bootstrapModule(MyModule)**：
   * 这个方法接收一个 Angular 模块（这里是 `MyModule`），并使用该模块作为根模块来启动应用。
   * `MyModule` 通常会是你的应用的主模块（如 `AppModule`），其中包含了应用的引导组件和其他必要的配置。
3. **整体作用**：
   * <mark style="color:red;">当你在浏览器环境中运行 Angular 应用时，你需要告诉 Angular 从哪个模块开始启动应用。</mark>`platformBrowser().bootstrapModule(MyModule)` 就是用来完成这个任务的。
   * 这条语句通常放在主文件（例如 `main.ts` 或 `main.browser.ts`）中，作为整个应用启动过程的一部分。

```javascript
import { platformBrowser } from '@angular/platform-browser';
import { enableProdMode } from '@angular/core';
import { MyModule } from './app/my.module';

// 如果是在生产环境中，启用生产模式以提高性能
if (!environment.debug) {
  enableProdMode();
}

// 启动应用
platformBrowser().bootstrapModule(MyModule)
  .catch(err => console.error(err));
```

### Muti:true

使用 multi: true 可以确保这些提供者都被正确地添加到一个数组中，而不是被覆盖。

```javascript
providers: [
  {
    provide: APP_INITIALIZER,
    useValue: initializeApp1,
    multi: true,
  },
  {
    provide: APP_INITIALIZER,
    useValue: initializeApp2,
    multi: true,
  },
]
```

### **`useValue`**

* **用法**: `useValue` 提供一个直接的值（通常是一个常量或简单对象）作为依赖注入的提供者值。
