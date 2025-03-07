# 设计理念

#### **依赖注入（Dependency Injection, DI）的设计理念**

依赖注入（DI）是一种**设计模式**，其核心理念是**将对象的依赖项交由外部管理，而不是在对象内部创建依赖**。DI 主要用于**降低耦合性**、**提高可测试性**和**增强可维护性**。

***

### **1. 依赖注入的核心原则**

#### **（1）控制反转（IoC, Inversion of Control）**

传统开发中，类会自行创建或管理依赖，例如：

```typescript
class UserService {
  private http = new HttpClient(); // 直接在类中创建依赖
}
```

这种方式**导致强耦合**，如果 `HttpClient` 需要替换，就需要修改 `UserService` 的代码。

DI 采用**控制反转**（IoC），将依赖的创建交由**外部**管理：

```typescript
class UserService {
  constructor(private http: HttpClient) {} // 依赖由外部注入
}
```

**优势：**

* **解耦合**：`UserService` 不依赖具体实现，而依赖外部提供的实例。
* **易替换**：可以替换 `HttpClient` 例如换成 `MockHttpClient` 进行单元测试。
* **易扩展**：不同环境可以使用不同的依赖实现，如测试环境、生产环境等。

***

#### **（2）依赖倒置原则（DIP, Dependency Inversion Principle）**

依赖倒置原则是 SOLID 原则中的 "D"，其核心思想：

* **高层模块（业务逻辑）不应该依赖低层模块（具体实现），二者应该依赖于抽象（接口/抽象类）**
* **抽象（接口）不应该依赖于具体实现，而具体实现应该依赖于抽象**

在 Angular 中，通常使用 **`InjectionToken`** 或 **抽象类** 来实现依赖倒置。例如：

```typescript
import { InjectionToken } from '@angular/core';

export const API_URL = new InjectionToken<string>('apiUrl'); // 定义一个抽象的 Token
```

然后在 `providers` 里提供具体的实现：

```typescript
providers: [
  { provide: API_URL, useValue: 'https://api.example.com' }
]
```

这样，任何需要 `API_URL` 的地方都可以通过 DI 获取，而不必直接依赖具体的字符串。

***

#### **（3）单一职责原则（SRP, Single Responsibility Principle）**

依赖注入有助于将不同的职责分离，使每个类专注于自己的任务。例如：

```typescript
class UserService {
  constructor(private http: HttpClient) {}

  getUserData() {
    return this.http.get('/user');
  }
}
```

* `UserService` 只负责业务逻辑，不负责 `HttpClient` 的创建和管理。
* `HttpClient` 由 Angular 的 DI 机制管理，并提供给 `UserService` 使用。

这种方式提高了代码的可维护性，使各个模块更加**专注**且**易于替换**。

***

### **2. Angular 的 DI 设计**

Angular 的 DI 机制基于：

* **`Provider`（提供者）**：定义如何提供依赖项，例如 `useClass`、`useValue`、`useFactory`。
* **`Injector`（注入器）**：管理依赖项的实例化和生命周期。
* **`@Injectable()`（可注入类）**：用于标记可以被注入的类。
* **`@Inject()` 或 `inject()`**：用于手动注入依赖。

**示例：**

```typescript
@Injectable({
  providedIn: 'root' // 让 Angular 在根级别提供此服务
})
class AuthService {
  login() {
    console.log('User logged in');
  }
}

class AppComponent {
  constructor(private auth: AuthService) {} // 依赖注入
}
```

**优势：**

* **解耦合**：`AppComponent` 不直接创建 `AuthService`，而是由 DI 提供实例。
* **可测试性**：可以在测试时替换 `AuthService` 为 `MockAuthService`。

***

### **3. 依赖注入的优点**

| **优点**      | **解释**                                      |
| ----------- | ------------------------------------------- |
| **降低耦合**    | 组件和服务之间通过 DI 交互，不直接依赖具体实现。                  |
| **提高可测试性**  | 允许使用 Mock 依赖进行单元测试，而不影响真实依赖。                |
| **增强可维护性**  | 依赖项的修改不会影响使用它们的类，修改更安全。                     |
| **更好的扩展性**  | 通过 `InjectionToken` 或 `useFactory` 轻松替换依赖项。 |
| **提高模块化能力** | 让服务、组件可以更独立地组合和重用。                          |

***

### **4. 什么时候使用 DI**

#### ✅ **适合使用 DI 的场景**

* **服务（Service）**
  * 例如：`AuthService`、`UserService`、`LoggerService`
* **HTTP 请求**
  * 例如：`HttpClient`
* **配置参数**
  * 例如：API 端点 `API_URL`
* **不同环境的实现**
  * 例如：开发环境和生产环境的不同数据服务



***

### **5. 总结**

依赖注入（DI）是一种设计模式，旨在**降低耦合、提高可测试性和可维护性**。在 Angular 中，DI 通过 `Provider`、`Injector` 和 `@Injectable()` 进行管理，使服务、组件可以独立开发并灵活组合。

* **控制反转（IoC）**：对象不创建依赖，而是由外部注入。
* **依赖倒置原则（DIP）**：依赖于抽象，而不是具体实现。
* **单一职责原则（SRP）**：每个类只负责一个任务，避免职责混乱。

DI 是 Angular 框架的核心设计之一，使其更加**模块化、易测试和可扩展**。
