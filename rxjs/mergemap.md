# Map相关

## map

* **作用**：对 Observable **每个值** 进行转换，返回的是**普通值**（非 Observable）。
* **适用场景**：简单的数据转换，如对流中的数据进行数学运算、格式化等。

```typescript
import { of } from 'rxjs';
import { map } from 'rxjs/operators';

of(1, 2, 3).pipe(
  map(x => x * 10)  // 乘以 10
).subscribe(console.log);  // 输出: 10, 20, 30
```

| 操作符 | **特点** | **适用场景** |
| --- | ------ | -------- |

| `mergeMap` | **并发执行**，不会取消之前的请求，多个请求可以同时进行 | **高并发**（如批量 API 请求、并行处理任务） |
| ---------- | ----------------------------- | -------------------------- |

| `switchMap` | **切换到最新请求**，取消前一个未完成的请求，始终执行最新的 | 搜索输入、轮询、避免过时请求 |
| ----------- | ------------------------------- | -------------- |

| `concatMap` | **按顺序执行**，当前请求完成后才会执行下一个，严格按顺序 | 表单提交、文件上传、确保顺序 |
| ----------- | ------------------------------ | -------------- |

## mergeMap

它将每个源值投影到一个新的 Observable，并将这些新的 Observable 合并到输出的 Observable 中

假设你有一个用户 ID 列表，你需要根据这些 ID 获取用户的详细信息。你可以使用 mergeMap来实现这一点：

* **作用**：<mark style="color:red;">**并发执行**</mark><mark style="color:red;">内部 Observable</mark>，**不等待上一个完成**。
* **适用场景**：多个异步任务同时执行（如并发 HTTP 请求）。

```javascript
import { from, of } from 'rxjs';
import { mergeMap, delay } from 'rxjs/operators';

const userIds = [1, 2, 3];

from(userIds).pipe(
  mergeMap(id => fetchUserDetails(id))
).subscribe(console.log);

function fetchUserDetails(id: number) {
  console.log(`🔵 获取用户 ${id} 的详情`);
  return of(`📢 用户 ${id} 详情`).pipe(delay(2000)); // 模拟 2 秒 API 响应
}


```

## switchMap&#x20;

switchMap: 类似于 mergeMap，但当新的 Observable 发出时，它会取消之前的 Observable。\ <mark style="color:red;">只输出最后一个 Observable 的结果</mark>

```javascript
import { fromEvent, of } from 'rxjs';
import { switchMap, tap, delay } from 'rxjs/operators';

const button = document.getElementById('loadDataButton') as HTMLButtonElement;

fromEvent(button, 'click').pipe(
  tap(() => console.log('🟡 按钮点击')),
  switchMap(() => fetchData()) // 取消前一个请求，获取最新数据
).subscribe(result => console.log(`✅ 数据加载完成: ${result}`));

function fetchData() {
  console.log(`🔵 发送 API 请求`);
  return of(`📢 服务器数据`).pipe(delay(2000)); // 模拟 2 秒 API 响应
}

```

适用于如搜索、表单提交、实时更新

## **`concatMap()`**

* **作用**：**严格按顺序执行**，每个内部 Observable 必须**等上一个完成**。
* **适用场景**：需要保持**严格的顺序**，如**依赖前一个任务的结果**。

```typescript
   const userIds = [1, 2, 3];
    
    from(userIds).pipe(
      concatMap(id => fetchUserDetails(id))
    ).subscribe(console.log);
    
    function fetchUserDetails(id: number) {
      console.log(`🔵 获取用户 ${id} 的详情`);
      return of(`📢 用户 ${id} 详情`).pipe(delay(2000)); // 模拟 2 秒 API 响应
    }
```
