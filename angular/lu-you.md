# 路由



### 结构

1. &#x20;路由（Routes）：定义了 URL 与组件之间的映射关系。
2. &#x20;路由模块（RouterModule）：用于配置路由的模块。
3. 路由器（Router）：负责根据当前 URL 导航到相应的组件。
4. 路由出口（RouterOutlet）：用于显示匹配到的组件
5. 路由参数（Route Parameters）：可以从 URL 中提取动态参数
6. 路由守卫（Route Guards）：用于控制路由的访问权限，如登录验证、权限检查等

### **路由懒加载模块**

```
loadChildren: () => import('./product/product.module').then(m => m.ProductModule)
```

### **RouterModule.forChild()**&#x548C; **RouterModule.forRoot()**

**RouterModule.forRoot()**：用于在根模块中配置主路由。它会创建一个路由服务的单例实例，并提供路由指令和服务。通常在根模块的 `imports` 数组中调用。

**RouterModule.forChild()**:

* 用于特性模块（Feature Modules），允许你为特定的功能区域定义路由，而不会与应用中的其他路由冲突。
* 特性模块可能有自己的路由配置，当你想把特性模块作为独立的一部分集成进主应用时非常有用。

**RouterModule.forRoot()**:

* 用于应用的主模块（通常是 `AppModule`），它初始化顶级路由配置，并且会创建一个可以监听 URL 变化并导航到不同视图的路由器服务实例。
* 通常在应用的根模块中调用一次，用来定义应用的基础路由。
* **RouterModule.forChild()**：用于在特性模块中配置子路由。它不会创建新的路由服务实例，而是将子路由添加到现有的路由配置中。通常在特性模块的 `imports` 数组中调用。

RouterModule.forRoot 是配置 Angular 应用程序路由的核心方法

### 使用

1. #### &#x20;配置路由

```javascript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { AppPreloadingStrategy } from './app-preloading-strategy';

const routes: Routes = [
  { path: 'home', component: HomeComponent },
  { path: 'about', component: AboutComponent,data: { preload: true, delay: 3000 }  }
];

@NgModule({
  imports: [
    RouterModule.forRoot(routes, {
      scrollPositionRestoration: 'enabled',
      preloadingStrategy: AppPreloadingStrategy
    })
  ],
  providers: [AppPreloadingStrategy],
  exports: [RouterModule]
})
export class AppRoutingModule {}

export class AppPreloadingStrategy implements PreloadingStrategy {
  preload(route: Route, load: Function): Observable<any> {
    const loadRoute = (delay) => delay
      ? timer(3000).pipe(flatMap(() => load()))
      : load();

    return route.data && route.data.preload
      ? loadRoute(route.data.delay)
      : of(null);
  }
}
```

2. 在模块中导入路由模块

在 `app.module.ts` 中导入 `AppRoutingModule`。

```
@NgModule({
  declarations: [
    AppComponent,
    HomeComponent,
    AboutComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

## 实际使用

在 **Angular** 中，如果项目包含多个 **Feature Module**（例如 `OrderModule`、`UserModule` 等），我们应该为每个 Feature Module 创建独立的 **Routing Module**，然后在 `AppRoutingModule` 统一管理这些路由，通常使用 **懒加载（Lazy Loading）** 来优化性能。

**1. 创建 feature`-routing.module.ts`**

```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { OrderListComponent } from './order-list/order-list.component';
import { OrderDetailComponent } from './order-detail/order-detail.component';

const routes: Routes = [
  { path: '', component: OrderListComponent }, // 订单列表
  { path: ':id', component: OrderDetailComponent } // 订单详情
];

@NgModule({
  imports: [RouterModule.forChild(routes)], // forChild() 用于 Feature Module
  exports: [RouterModule]
})
export class OrderRoutingModule { }
```

### **2. 创建 feature`.module.ts`**

```typescript
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { OrderRoutingModule } from './order-routing.module';
import { OrderListComponent } from './order-list/order-list.component';
import { OrderDetailComponent } from './order-detail/order-detail.component';

@NgModule({
  declarations: [
    OrderListComponent,
    OrderDetailComponent
  ],
  imports: [
    CommonModule,
    OrderRoutingModule
  ]
})
export class OrderModule { }
```

📌 **注意**：

* `OrderRoutingModule` 负责 `OrderModule` 的路由。
* `OrderModule` 仅导入 `CommonModule`，不需要 `BrowserModule`。

**3. 在 `app-routing.module.ts` 进行懒加载**

在 `app-routing.module.ts` 统一管理 `OrderModule` 和 `UserModule`：

```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';

const routes: Routes = [
  { path: '', redirectTo: '/order', pathMatch: 'full' }, // 默认路由
  { path: 'order', loadChildren: () => import('./order/order.module').then(m => m.OrderModule) },
  { path: 'user', loadChildren: () => import('./user/user.module').then(m => m.UserModule) },
  { path: '**', redirectTo: '/order' } // 404 重定向到订单页面
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

&#x20;**使用 `loadChildren` 进行懒加载**：

* 只有访问 `order` 或 时 其他feature，相关模块才会被加载，提升首屏性能。

### &#x20;**4. 在 `app.module.ts` 中导入 `AppRoutingModule`**

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';

@NgModule({
  declarations: [AppComponent],
  imports: [
    BrowserModule,
    AppRoutingModule // 仅需导入 AppRoutingModule
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

### **在 `app.component.html` 中添加导航**

```html
<nav>
  <a routerLink="/order">订单管理</a>
  <a routerLink="/user">用户管理</a>
</nav>

<router-outlet></router-outlet>
```

**额外优化**

#### **（1）守卫**

如果 `order` 页面需要 **身份验证**，可以使用 `AuthGuard`：

```typescript
{ path: 'order', loadChildren: () => import('./order/order.module').then(m => m.OrderModule), canActivate: [AuthGuard] }
```

#### **（2）预加载**

默认懒加载会等到访问路由才加载模块，可以使用 **预加载** 提前加载：

```typescript
import { PreloadAllModules } from '@angular/router';

const routes: Routes = [
  { path: 'order', loadChildren: () => import('./order/order.module').then(m => m.OrderModule) },
  { path: 'user', loadChildren: () => import('./user/user.module').then(m => m.UserModule) }
];

@NgModule({
  imports: [RouterModule.forRoot(routes, { preloadingStrategy: PreloadAllModules })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

### **总结**

| **步骤** | **操作**                                            |
| ------ | ------------------------------------------------- |
| **1**  | 在 `feature` 模块创建 `xxx-routing.module.ts`          |
| **2**  | 在 `xxx.module.ts` 中 `imports: [XxxRoutingModule]` |
| **3**  | 在 `app-routing.module.ts` 中 `loadChildren` 懒加载    |
| **4**  | 在 `app.module.ts` 仅导入 `AppRoutingModule`          |
| **5**  | 在 `app.component.html` 使用 `router-outlet`         |

这样，我们的 **Angular 项目路由清晰、可维护，并且支持懒加载提升性能！**

### **`routerLink`** `routerLink` 用于创建导航链接，点击链接时会触发路由导航。

```
<!-- app.component.html -->
<a routerLink="">Home</a>
<a routerLink="/about">About</a>
```

### **路由守卫**

* **CanActivate**：用于控制是否可以激活某个路由。

```
import { Injectable } from '@angular/core';
import { CanActivate, Router } from '@angular/router';

@Injectable({
  providedIn: 'root'
})
export class AuthGuard implements CanActivate {
  constructor(private router: Router) {}

  canActivate(): boolean {
    // 检查用户是否已登录
    const isLoggedIn = true;
    if (!isLoggedIn) {
      this.router.navigate(['/login']);
      return false;
    }
    return true;
  }
}
```

* **CanActivateChild**：用于控制是否可以激活某个子路由。
* **CanDeactivate**：用于控制是否可以离开某个路由。
* **Resolve**：用于在路由激活前预获取数据。
* **CanLoad**：用于控制是否可以懒加载某个模块



### 使用provideRouter

`provideRouter` 主要用于替代 `RouterModule.forRoot()` 或 `RouterModule.forChild()` 中的调用方式，特别是在使用 Angular 的依赖注入系统时提供了更大的灵活性。通过使用 `provideRouter`，你可以在任何地方提供路由配置，而不仅仅是模块级别。

```javascript
const appRoutes: Routes = [];
bootstrapApplication(AppComponent, {
  providers: [
    provideRouter(appRoutes,
      withDebugTracing(),
      withRouterConfig({paramsInheritanceStrategy: 'always'})
    )
  ]
});

withInMemoryScrolling()：启用内存滚动恢复，确保用户在导航时保持滚动位置。
withDebugTracing()：启用路由调试跟踪，有助于调试路由问题
```

### 使用 Resolve 接口

`Resolve` 是 Angular 路由提供的一个接口，允许你在导航到一个路由之前获取数据。这样可以确保当用户到达目标页面时，所有必要的数据已经准备好了。

**实现步骤：**

1.  **创建一个 Resolver 服务**: 首先，你需要创建一个实现了 `Resolve` 接口的服务。这个服务负责在路由激活之前获取数据。

    <pre data-overflow="wrap"><code>import { Injectable } from '@angular/core';
    import { Resolve, ActivatedRouteSnapshot, RouterStateSnapshot } from '@angular/router';
    import { Observable, of } from 'rxjs';
    // 假设你有一个服务叫做 DataService
    import { DataService } from './data.service';

    @Injectable({
      providedIn: 'root'
    })
    export class DataResolver implements Resolve&#x3C;any> {
      constructor(private dataService: DataService) {}

    <strong>  resolve(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): Observable&#x3C;any>|Promise&#x3C;any>|any {
    </strong>    return this.dataService.getData();
      }
    }
    </code></pre>
2.  **配置路由以使用 Resolver**: 在你的路由配置中指定使用上述创建的 Resolver。

    ```
    import { NgModule } from '@angular/core';
    import { Routes, RouterModule } from '@angular/router';
    import { YourComponent } from './your.component';
    import { DataResolver } from './data.resolver';

    const routes: Routes = [
      {
        path: 'your-path',
        component: YourComponent,
        resolve: {
          yourDataKey: DataResolver
        }
      }
    ];

    @NgModule({
      imports: [RouterModule.forRoot(routes)],
      exports: [RouterModule]
    })
    export class AppRoutingModule {}
    ```
3.  **在组件中使用预取的数据**: 在目标组件中，你可以通过 `ActivatedRoute` 来访问预取的数据。

    ```
    import { Component, OnInit } from '@angular/core';
    import { ActivatedRoute } from '@angular/router';

    @Component({
      selector: 'app-your',
      templateUrl: './your.component.html',
      styleUrls: ['./your.component.css']
    })
    export class YourComponent implements OnInit {
      constructor(private route: ActivatedRoute) {}

      ngOnInit() {
        this.route.data.subscribe((data) => {
          console.log(data.yourDataKey); // 访问预取的数据
        });
      }
    }
    ```
