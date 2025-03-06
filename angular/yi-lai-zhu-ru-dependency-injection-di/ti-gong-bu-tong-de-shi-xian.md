# 提供不同的实现

### **. 使用 `useClass`、`useValue`、`useExisting`、`useFactory`**

#### **（1）`useClass`（提供不同的实现）**

✅ **适用于**：需要 **不同的服务实现** 时，可以动态切换实现类。

```typescript
@Injectable()
export class AdvancedLoggerService {
  log(message: string) {
    console.log(`高级日志服务: ${message}`);
  }
}

@NgModule({
  providers: [
    { provide: LoggerService, useClass: AdvancedLoggerService } // 使用 AdvancedLoggerService 代替 LoggerService
  ]
})
export class AppModule { }
```

✅ **特点**：

* 通过 **`useClass`** 指定 **不同的实现**，可以在不修改组件代码的情况下 **切换服务实现**。

***

#### **（2）`useValue`（提供静态值）**

✅ **适用于**：提供 **静态配置对象**。

```typescript
const APP_CONFIG = { apiUrl: 'https://api.example.com' };

@NgModule({
  providers: [
    { provide: 'APP_CONFIG', useValue: APP_CONFIG }
  ]
})
export class AppModule { }
```

**📌 组件中使用**

```typescript
import { Component, Inject } from '@angular/core';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
})
export class HomeComponent {
  constructor(@Inject('APP_CONFIG') private config: any) { 
    console.log(`API 地址: ${this.config.apiUrl}`);
  }
}
```

✅ **特点**：

* 适用于 **全局静态配置**，比如 `API URL`、`环境变量` 等。

***

#### **（3）`useExisting`（使用已存在的服务实例）**

✅ **适用于**：**让两个 Token 共享同一个实例**。

```typescript
@NgModule({
  providers: [
    LoggerService,
    { provide: 'CustomLogger', useExisting: LoggerService } // 共享同一个 LoggerService 实例
  ]
})
export class AppModule { }
```

**📌 组件中使用**

```typescript
typescript复制编辑import { Component, Inject } from '@angular/core';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
})
export class HomeComponent {
  constructor(@Inject('CustomLogger') private logger: LoggerService) { 
    this.logger.log('共享 LoggerService 实例');
  }
}
```

✅ **特点**：

* 让 `CustomLogger` 和 `LoggerService` 共享 **同一个实例**。

***

#### **（4）`useFactory`（动态生成实例）**

✅ **适用于**：**依赖其他服务** 或 **需要动态创建实例**。

```typescript
export function loggerFactory() {
  return new LoggerService();
}

@NgModule({
  providers: [
    { provide: LoggerService, useFactory: loggerFactory }
  ]
})
export class AppModule { }
```

✅ **特点**：

* 适用于 **复杂的服务实例创建逻辑**，可以动态返回实例。
