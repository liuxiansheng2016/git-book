# 流

#### **Angular 中的流（Stream）概念**

在 Angular 中，**流（Stream）** 主要指的是 **RxJS（Reactive Extensions for JavaScript）** 提供的 **Observable（可观察对象）**，它是一种**基于推送（push）的异步数据流**。流的核心概念包括 **数据源（Producer）**、**订阅者（Subscriber）** 和 **运算符（Operators）**。

***

### **1. 什么是流（Stream）？**

流可以理解为**一系列连续发生的事件或数据**，这些数据可以在**同步或异步**的情况下发生。Angular 通过 **RxJS** 实现流式数据处理，核心是 `Observable`（可观察对象）。

#### **流的特点**

1. **数据随时间推送（push-based）**
   * 传统 `Promise` 只会返回**一个值**，而 `Observable` 可以**推送多个值**。
2. **懒执行（Lazy Execution）**
   * 只有当\*\*订阅（subscribe）\*\*后，`Observable` 才会开始发送数据。
3. **可组合（Composable）**
   * 通过**RxJS 操作符**（如 `map`、`filter`、`mergeMap`）对流进行转换和组合。

***

### **2. Angular 中的流**

在 Angular 中，流的典型应用场景包括：

* **HTTP 请求（HttpClient）**
* **事件处理（如点击、输入框变化等）**
* **表单验证（Reactive Forms）**
* **状态管理（如 NgRx、BehaviorSubject）**
* **路由参数监听（ActivatedRoute）**

***

### **3. `Observable`（可观察对象）**

#### **创建 `Observable`**

Angular 中的流通常由 **`Observable`** 创建，例如：

```typescript
import { Observable } from 'rxjs';

// 创建一个简单的 Observable
const stream$ = new Observable(observer => {
  observer.next(1);
  observer.next(2);
  observer.next(3);
  observer.complete(); // 结束流
});

// 订阅 Observable
stream$.subscribe(value => console.log(value));
```

**输出**

```
1
2
3
```

**解释**

* `next(value)`：推送数据
* `complete()`：结束流，不再推送新数据

***

### **4. `Subject`（主题）**

`Subject` 既是 **`Observable`** 也是 **`Observer`**，可以手动推送数据：

```typescript
import { Subject } from 'rxjs';

const subject$ = new Subject<number>();

// 订阅流
subject$.subscribe(value => console.log('Subscriber 1:', value));
subject$.subscribe(value => console.log('Subscriber 2:', value));

// 发送数据
subject$.next(1);
subject$.next(2);
```

**输出**

```
Subscriber 1: 1
Subscriber 2: 1
Subscriber 1: 2
Subscriber 2: 2
```

**特点**

* **多个订阅者可以同时接收相同的数据**
* **手动推送数据（不像 `Observable` 那样被动推送）**

#### **BehaviorSubject**

`BehaviorSubject` 具有 **“初始值”**，订阅时会立即获得最后一个值：

```typescript
import { BehaviorSubject } from 'rxjs';

const behaviorSubject$ = new BehaviorSubject<number>(0);

behaviorSubject$.subscribe(value => console.log('Subscriber:', value));

behaviorSubject$.next(1);
behaviorSubject$.next(2);
```

**输出**

```
Subscriber: 0  // 初始值
Subscriber: 1
Subscriber: 2
```

***

### **5. Angular 中的流应用**

#### **（1）HttpClient**

Angular 的 `HttpClient` 会返回 `Observable`：

```typescript
import { HttpClient } from '@angular/common/http';
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-users',
  template: `<div *ngFor="let user of users">{{ user.name }}</div>`
})
export class UsersComponent implements OnInit {
  users: any[] = [];

  constructor(private http: HttpClient) {}

  ngOnInit() {
    this.http.get<any[]>('https://jsonplaceholder.typicode.com/users')
      .subscribe(data => this.users = data);
  }
}
```

* `http.get()` 返回 `Observable`
* 通过 `subscribe()` 处理数据

***

#### **（2）事件流（DOM 事件）**

Angular 的 `fromEvent()` 允许监听 DOM 事件：

```typescript
import { Component, ElementRef, AfterViewInit, ViewChild } from '@angular/core';
import { fromEvent } from 'rxjs';
import { debounceTime } from 'rxjs/operators';

@Component({
  selector: 'app-search',
  template: `<input #searchInput type="text" placeholder="Search...">`
})
export class SearchComponent implements AfterViewInit {
  @ViewChild('searchInput', { static: true }) input!: ElementRef;

  ngAfterViewInit() {
    fromEvent(this.input.nativeElement, 'input')
      .pipe(debounceTime(300))
      .subscribe(event => console.log((event.target as HTMLInputElement).value));
  }
}
```

* `fromEvent()` 监听输入框变化
* `debounceTime(300)` 限制 300ms 内的高频输入，避免频繁触发

***

#### **（3）路由参数监听**

```typescript
import { ActivatedRoute } from '@angular/router';
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-product',
  template: `<p>Product ID: {{ productId }}</p>`
})
export class ProductComponent implements OnInit {
  productId!: string;

  constructor(private route: ActivatedRoute) {}

  ngOnInit() {
    this.route.params.subscribe(params => {
      this.productId = params['id'];
    });
  }
}
```

* `ActivatedRoute.params` 返回 `Observable`
* 订阅 `params` 以监听路由参数变化

***

#### **（4）RxJS 操作符**

RxJS 提供大量操作符，如：

| 操作符            | 作用             |
| -------------- | -------------- |
| `map`          | 转换数据           |
| `filter`       | 过滤数据           |
| `mergeMap`     | 处理嵌套流（如请求 API） |
| `debounceTime` | 限制高频触发         |
| `switchMap`    | 取消旧请求，保留最新请求   |

**示例：HTTP 搜索**

```typescript
search(term: string) {
  return this.http.get(`https://api.example.com/search?q=${term}`)
    .pipe(
      debounceTime(300),  // 避免频繁请求
      switchMap(response => response) // 保留最新请求
    );
}
```

***

### **6. 总结**

#### ✅ **Angular 中的流概念**

* **流（Stream）** 是 **数据随时间变化的序列**，如 HTTP 响应、用户输入等。
* **`Observable`** 是 Angular 处理流的核心，支持**异步数据处理**。
* **`Subject` / `BehaviorSubject`** 允许手动推送数据，并支持**多订阅者**。
* **RxJS 操作符（`map`、`filter`、`switchMap`）** 可用于流的转换与组合。

Angular 通过 **RxJS** 强化了流式编程，使得**数据流管理更高效、更可组合**
