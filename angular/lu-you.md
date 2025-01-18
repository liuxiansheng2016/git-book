# 路由



## 结构

1. &#x20;路由（Routes）：定义了 URL 与组件之间的映射关系。
2. &#x20;路由模块（RouterModule）：用于配置路由的模块。
3. 路由器（Router）：负责根据当前 URL 导航到相应的组件。
4. 路由出口（RouterOutlet）：用于显示匹配到的组件
5. 路由参数（Route Parameters）：可以从 URL 中提取动态参数
6. 路由守卫（Route Guards）：用于控制路由的访问权限，如登录验证、权限检查等

## RouterModule.forRoot

RouterModule.forRoot 是配置 Angular 应用程序路由的核心方法

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

## 使用provideRouter

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
