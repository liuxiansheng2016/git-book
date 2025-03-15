# shareReplay 和 share

`shareReplay` 操作符可以让多个订阅者共享同一个 Observable 的执行结果，并且会回放之前的结果给新的订阅者，从而实现缓存效果。

### `shareReplay` 具体使用

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

### **`share()`**

* **共享数据流**，但 **不缓存** 任何已发送的数据。
* 只有 **当前活跃的订阅者** 才能接收到数据。
* **新订阅者不会收到过去的数据**，只会收到订阅之后的数据。

**示例：`share()`**

```typescript
import { interval } from 'rxjs';
import { share, take } from 'rxjs/operators';

const shared$ = interval(1000).pipe(take(5), share());

shared$.subscribe(value => console.log('🅰️ 订阅者 A:', value));

setTimeout(() => {
  shared$.subscribe(value => console.log('🅱️ 订阅者 B:', value));
}, 2500); // 2.5 秒后订阅
```

**🔹 可能的输出**

```
yaml复制代码🅰️ 订阅者 A: 0
🅰️ 订阅者 A: 1
🅱️ 订阅者 B: 2  <-- 订阅者 B 只收到 2 之后的数据
🅰️ 订阅者 A: 2
🅰️ 订阅者 A: 3
🅱️ 订阅者 B: 3
🅰️ 订阅者 A: 4
🅱️ 订阅者 B: 4
```

&#x20;`B` **错过了 0 和 1**，因为 `share()` **不会缓存历史数据**。

***

### **`shareReplay(bufferSize)`**

* **共享数据流**，但 **会缓存历史数据**。
* **新订阅者可以收到过去的数据**（取决于 `bufferSize`）。
* 适用于 **缓存 HTTP 请求结果**、**保持最近的 UI 状态**。

```typescript
import { interval } from 'rxjs';
import { shareReplay, take } from 'rxjs/operators';

const sharedReplay$ = interval(1000).pipe(take(5), shareReplay(2)); // 缓存最近 2 个数据

sharedReplay$.subscribe(value => console.log('🅰️ 订阅者 A:', value));

setTimeout(() => {
  sharedReplay$.subscribe(value => console.log('🅱️ 订阅者 B:', value));
}, 2500); // 2.5 秒后订阅
```

**🔹 可能的输出**

```
🅰️ 订阅者 A: 0
🅰️ 订阅者 A: 1
🅰️ 订阅者 A: 2
🅱️ 订阅者 B: 1  <-- 订阅者 B 收到 1（缓存）
🅱️ 订阅者 B: 2  <-- 订阅者 B 收到 2（缓存）
🅰️ 订阅者 A: 3
🅱️ 订阅者 B: 3
🅰️ 订阅者 A: 4
🅱️ 订阅者 B: 4
```

👉 `B` **收到 1 和 2**，因为 `shareReplay(2)` **缓存了最近 2 个值**。

***

#### **📌 `share()` vs `shareReplay()` 对比**

| 特性                | `share()`          | `shareReplay(bufferSize)` |
| ----------------- | ------------------ | ------------------------- |
| **数据是否共享**        | ✅ 是                | ✅ 是                       |
| **是否缓存历史数据**      | ❌ 否                | ✅ 是                       |
| **新订阅者能否收到过去的数据** | ❌ 不能               | ✅ 取决于 `bufferSize`        |
| **适用场景**          | 实时流（WebSocket、事件流） | 需要回放历史数据（HTTP 缓存、状态共享）    |

***

#### **🚀 什么时候用 `shareReplay()`？**

1.  **HTTP 请求缓存**

    ```typescript
    const cachedRequest$ = this.http.get('/api/data').pipe(shareReplay(1));
    ```

    * 让多个订阅者共享同一个 HTTP 请求结果，而不会重复请求后端。
2.  **状态管理**

    ```typescript
    const userStatus$ = someEvent$.pipe(shareReplay(1));
    ```

    * 让新订阅的组件也能获取最新的状态，而不会错过数据。
3. **表单自动填充**
   * 保持最近一次的输入值，以便新的订阅者能获取最新的数据。
