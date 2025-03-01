# shareReplay

`shareReplay` 操作符可以让多个订阅者共享同一个 Observable 的执行结果，并且会回放之前的结果给新的订阅者，从而实现缓存效果。

**示例代码**

\
收起typescript

```
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable, Subject, shareReplay } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class CachedDataService {
  private data$: Observable<any>;

  constructor(private http: HttpClient) {}

  getData(url: string): Observable<any> {
    if (!this.data$) {
      this.data$ = this.http.get(url).pipe(
        // 缓存最新的一次请求结果
        shareReplay(1) 
      );
    }
    return this.data$;
  }
}
```

**使用方式**

\
收起typescript

```
import { Component } from '@angular/core';
import { CachedDataService } from './cached-data.service';

@Component({
  selector: 'app-root',
  template: `
    <button (click)="fetchData()">Fetch Data</button>
    <pre>{{ data | json }}</pre>
  `
})
export class AppComponent {
  data: any;

  constructor(private cachedDataService: CachedDataService) {}

  fetchData() {
    const url = 'https://api.example.com/data';
    this.cachedDataService.getData(url).subscribe(result => {
      this.data = result;
    });
  }
}
```
