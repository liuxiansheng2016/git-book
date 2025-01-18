# HttpClient

## 配置HttpClient

HttpClientModule 在 Angular 16 及更高版本中已被标记为不推荐使用，不需要再导入httpclientModule

```javascript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { HttpClientModule } from '@angular/common/http'; // 引入HttpClientModule
import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    HttpClientModule // 注册HttpClientModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

## **provideHttpClient 函数**

这是一个函数，用于配置 HttpClient

独立组件（standalone component）的应用

```javascript
import { Component, inject } from '@angular/core';
import { bootstrapApplication } from '@angular/platform-browser';
import { provideHttpClient, withInterceptors } from '@angular/common/http';
import { MyInterceptor } from './my-interceptor';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [],
  template: `
    <h1>Hello from {{ name }}!</h1>
    <a target="_blank" href="https://angular.dev/overview">
      Learn more about Angular
    </a>
  `,
})
export class App {
  name = 'Angular';
}

// 启动应用并配置 HttpClient
bootstrapApplication(App, {
  providers: [
    // 直接提供拦截器实例
    provideHttpClient(withInterceptors([new MyInterceptor()]))
  ]
})();
```

**使用 NgModule**

```javascript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { provideHttpClient, withInterceptors } from '@angular/common/http';
import { MyInterceptor } from './my-interceptor';

@NgModule({
  declarations: [AppComponent],
  imports: [
    BrowserModule,
  ],
  providers: [
    // 直接提供拦截器实例
    provideHttpClient(withInterceptors([new MyInterceptor()])),
    // 其他提供者
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

## 怎么使用HttpClient



1. 创建service

```javascript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class MyService {
  private apiUrl = 'https://api.example.com/data';

  constructor(private http: HttpClient) {}

  getData(): Observable<any> {
    return this.http.get(this.apiUrl);
  }
}
```

2. 使用service

```javascript
import { Component, OnInit } from '@angular/core';
import { MyService } from './my.service';

@Component({
  selector: 'app-my-component',
  templateUrl: './my-component.component.html',
  styleUrls: ['./my-component.component.css']
})
export class MyComponent implements OnInit {
  data: any;

  constructor(private myService: MyService) {}

  ngOnInit(): void {
    this.getData();
  }

  getData(): void {
    this.myService.getData().subscribe(
      (response) => {
        this.data = response;
        console.log('Data:', this.data);
      },
      (error) => {
        console.error('Error:', error);
      }
    );
  }
}
```

## 将 Observable 转换为 Promise

1. 使用 toPromise（RxJS 6.x 及之前版本）
2. 使用firstValueFrom or lastValueFrom

```javascript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { firstValueFrom } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class MyService {
  private apiUrl = 'https://api.example.com/data';

  constructor(private http: HttpClient) {}

  async getDataAsPromise(): Promise<any> {
    try {
      const response = await firstValueFrom(this.http.get(this.apiUrl));
      return response;
    } catch (error) {
      console.error('Error:', error);
      throw error;
    }
  }
}
```
