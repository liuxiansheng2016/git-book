---
description: 它允许开发者创建不依赖于 NgModule 的组件、指令和管道。这意味着组件可以直接在其他组件中使用，而不需要通过 NgModule 进行声明和导出
---

# Standalone Components

#### 1. **无需模块声明**

* 不需要在 `NgModule` 的 `declarations` 中声明。
* 组件可以直接被引导或使用。

#### 2. **更灵活的依赖导入**

* 使用 `imports` 属性直接导入其他组件、指令、管道和模块。

#### 3. **支持路由**

* Standalone Component 可以直接配置为路由目标，无需模块的支持

#### 如何创建独立组件

```javascript
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterModule } from '@angular/router'; // 如果需要路由功能
import { MyService } from './my.service';

@Component({
  selector: 'app-standalone',
  templateUrl: './standalone.component.html',
  styleUrls: ['./standalone.component.css'],
  standalone: true, // 设置为独立组件
  imports: [CommonModule, RouterModule], // 导入必要的模块
  providers: [MyService] // 提供服务
})
export class StandaloneComponent {
  constructor(private myService: MyService) {}
}
```

设置路由配置

```javascript
import { Routes } from '@angular/router';
import { HomeComponent } from './home.component';
import { AboutComponent } from './about.component';
import { NotFoundComponent } from './not-found.component';

export const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'about', component: AboutComponent },
  { path: '**', component: NotFoundComponent }, // 404 页面
];
```

引导应用并加载路由

```javascript
import { bootstrapApplication } from '@angular/platform-browser';
import { provideRouter } from '@angular/router';
import { routes } from './app.routes';

bootstrapApplication(null, {
  providers: [provideRouter(routes)],
}).catch(err => console.error(err));
```
