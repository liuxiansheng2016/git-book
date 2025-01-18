# 拦截器

## 使用例子

1. httpHeaderIntercepter\
   添加请求头
2. ErrorIntercepter\
   拿到错误码，转换错误对象
3. LogIntercepter\
   排除文字 图片获取时的log
4. tokenIntercepter\
   给http 请求添加token，并且如果没有token的话
5. Retry

```javascript
intercept(
  request: HttpRequest<unknown>,
  next: HttpHandler
): Observable<HttpEvent<unknown>> {
  return next.handle(request).pipe(
    retryWhen((errors) => {
      return errors.pipe(
        mergeMap((error) => {
          error.status >= 500 ? timer(0) : throwError(error);
        }),
        take(1)
      );
    })
  );
}
```

（timer(0) 表示立即重试）



<mark style="color:orange;">**可以定义一个core-interceptors.providers.ts**</mark>

将所有的拦截器放进去



## 实现拦截器

1. 创建一个实现了 HttpInterceptor 接口的类。这个接口只有一个方法 intercept

```javascript
import { Injectable } from '@angular/core';
import {
  HttpEvent,
  HttpInterceptor,
  HttpHandler,
  HttpRequest,
  HttpResponse,
  HttpErrorResponse
} from '@angular/common/http';
import { Observable, throwError } from 'rxjs';
import { catchError, tap } from 'rxjs/operators';

@Injectable()
export class MyInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    // 在请求发送之前修改请求
    const modifiedReq = req.clone({
      headers: req.headers.set('Authorization', 'Bearer your-token-here')
    });

    return next.handle(modifiedReq).pipe(
      tap(event => {
        if (event instanceof HttpResponse) {
          // 处理响应
          console.log('Response received:', event);
        }
      }),
      catchError((error: HttpErrorResponse) => {
        // 处理错误
        console.error('Request error:', error);
        return throwError(error);
      })
    );
  }
}
```

2. 应用中注册这个拦截器。你可以通过 providers 数组将拦截器提供给依赖注入系统，并使用 HTTP\_INTERCEPTORS 注入令牌。

```javascript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http';
import { AppComponent } from './app.component';
import { MyInterceptor } from './my-interceptor';

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, HttpClientModule],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MyInterceptor,
      multi: true
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule {}

```

## 使用 withInterceptors 和 withInterceptorsFromDi

<pre class="language-javascript"><code class="lang-javascript"><strong>import { Component } from '@angular/core';
</strong>import { HttpClientModule, HttpInterceptorFn, withInterceptors } from '@angular/common/http';

const myInterceptor: HttpInterceptorFn = (req, next) => {
  const modifiedReq = req.clone({
    headers: req.headers.set('Authorization', 'Bearer example-token'),
  });
  return next(modifiedReq);
};

@Component({
  selector: 'app-root',
  template: `&#x3C;h1>Standalone Component with Interceptor&#x3C;/h1>`,
  standalone: true,
  imports: [
    withInterceptors([myInterceptor]), // 配置拦截器
  ],
})
export class AppComponent {}

</code></pre>

```javascript
import { Component } from '@angular/core';
import { HttpClientModule, HTTP_INTERCEPTORS, withInterceptorsFromDI } from '@angular/common/http';
import { MyInterceptor } from './my-interceptor';

@Component({
  selector: 'app-root',
  template: `<h1>Standalone Component with Interceptor</h1>`,
  standalone: true,
  imports: [
    HttpClientModule.withInterceptorsFromDI() // 启用 DI 中的拦截器
  ],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MyInterceptor,
      multi: true
    }
  ]
})
export class AppComponent {}

```
