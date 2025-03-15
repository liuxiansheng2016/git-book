# catchError,retry,retryWhen



## **`catchError()`**

`catchError()` 处理 `Observable` 发生的错误，并允许你提供一个**新的 `Observable` 作为替代**，避免应用崩溃。

**示例：API 请求错误处理**

```ts
import { of, throwError } from 'rxjs';
import { catchError } from 'rxjs/operators';

const apiRequest$ = throwError(() => new Error('请求失败！'));

apiRequest$.pipe(
  catchError(err => {
    console.log('发生错误:', err.message);
    return of('默认数据'); // 返回备用数据
  })
).subscribe(console.log);
```

✅ **效果**：当 API 请求失败时，输出 `'默认数据'`，避免应用崩溃。

***

## **`retry()` 和 `retryWhen()`**&#x20;

<mark style="color:red;">`retry()`</mark> <mark style="color:red;"></mark><mark style="color:red;">操作符会在源 Observable 发出错误时，重新订阅源 Observable 进行重试</mark>

| 操作符           | 作用                          |
| ------------- | --------------------------- |
| `retry(n)`    | **失败后自动重试 n 次**，如果仍然失败则抛出错误 |
| `retryWhen()` | **失败后按照自定义策略重试**（如指数退避）     |

```ts
import { of, throwError } from 'rxjs';
import { retry, catchError } from 'rxjs/operators';

throwError(() => new Error('请求失败！')).pipe(
  retry(3), // 最多重试 3 次
  catchError(err => of('最终失败，返回默认值'))
).subscribe(console.log);
```

如果失败，会自动重试 3 次。

```ts
import { Observable, throwError, of, timer } from 'rxjs';
import { retryWhen, mergeMap } from 'rxjs/operators';

// 模拟一个可能出错的 Observable
const source$ = new Observable<number>((observer) => {
    let count = 0;
    const intervalId = setInterval(() => {
        if (count < 2) {
            observer.error(new Error('Temporary error'));
        } else {
            observer.next(count);
            observer.complete();
        }
        count++;
    }, 1000);

    return () => clearInterval(intervalId);
});

// 使用 retryWhen 操作符进行重试，每次重试间隔 2 秒
const result$ = source$.pipe(
    retryWhen((errors) =>
        errors.pipe(
            mergeMap((error, index) => {
                if (index < 3) {
                    return timer(2000); // 2 秒后重试
                }
                return throwError(error); // 超过 3 次重试，抛出错误
            })
        )
    )
);

result$.subscribe({
    next: (value) => console.log('Received value:', value),
    error: (err) => console.error('Error after retries:', err),
    complete: () => console.log('Observable completed')
});
```

在 `mergeMap` 中，根据重试次数 `index` 决定是否继续重试。如果重试次数小于 3 次，则使用 `timer(2000)` 延迟 2 秒后发出值，触发重新订阅源 Observable 进行重试；如果超过 3 次，则使用 `throwError(error)` 抛出错误，结束重试过程。
