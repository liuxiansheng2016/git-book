# 升级

在 Angular 版本升级过程中，尤其是从 Angular 14 开始引入了新的独立组件特性后，一些传统的模块配置方式可以使用新的函数式 API 来替代，下面为你总结一些常见的替代情况：

#### 1. HTTP 相关

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

#### 2. 表单相关

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

#### 3. 路由相关

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

#### 4. 动画相关

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
