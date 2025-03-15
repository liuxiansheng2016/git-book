# Observable和promise

## 将Observable转换成Promise

```javascript
import { Observable } from 'rxjs';
import 'rxjs/add/operator/toPromise'; // RxJS 5.x

const observable: Observable<number> = of(42);

observable.toPromise().then(value => {
    console.log('Value:', value);
}).catch(error => {
    console.error('Error:', error);
});

```

```javascript
import { of, from, throwError } from 'rxjs';
import { firstValueFrom, lastValueFrom } from 'rxjs';

// 使用 firstValueFrom
const observable1 = of(42);

firstValueFrom(observable1).then(value => {
    console.log('First value:', value); // 输出: First value: 42
}).catch(error => {
    console.error('Error:', error);
});

// 使用 lastValueFrom
const observable2 = from([1, 2, 3, 4, 5]);

lastValueFrom(observable2).then(value => {
    console.log('Last value:', value); // 输出: Last value: 5
}).catch(error => {
    console.error('Error:', error);
});

// 如果Observable抛出错误
const observableWithError = throwError(() => new Error('Something went wrong'));

firstValueFrom(observableWithError).then(value => {
    console.log('Value:', value);
}).catch(error => {
    console.error('Error:', error.message); // 输出: Error: Something went wrong
});

```

## Observable 和 Promise 有什么区别？

| **特性**        | **Observable**                  | **Promise**                  |
| ------------- | ------------------------------- | ---------------------------- |
| **是否惰性执行**    | **是**（只有订阅时才执行）                 | **否**（创建后立即执行）               |
| **是否可以返回多个值** | **是**（可推送多个值）                   | **否**（只能返回一个值）               |
| **是否可以取消**    | **是**（调用 `unsubscribe()`）       | **否**（无法取消）                  |
| **是否支持操作符**   | **是**（`map`、`filter`、`merge` 等） | **否**（只能通过 `.then()` 进行链式调用） |
| **是否支持多播**    | **是**（通过 `Subject` 实现）          | **否**（只能是单播）                 |

✅ **简单理解：**

* **Promise 适用于一次性异步操作**（如 HTTP 请求）。
* **Observable 适用于处理多个值的数据流**（如用户输入、WebSocket 数据等）

## 将Promise 转换为 Observable

### 使用 `from()` 函数

```javascript
const { from } = require('rxjs');

// 创建一个 Promise
const myPromise = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve('Promise resolved successfully');
    }, 2000);
});

// 将 Promise 转换为 Observable
const observableFromPromise = from(myPromise);

// 订阅 Observable
observableFromPromise.subscribe({
    next: (value) => console.log('Received value:', value),
    error: (err) => console.error('Error:', err),
    complete: () => console.log('Observable completed')
});
```

### 使用 `defer()` 函数（可选）

\
`defer()` 函数允许你在订阅时动态创建 `Observable`，当你需要根据不同条件创建不同的 `Promise` 时，使用 `defer()` 会很有用。

```javascript
const { defer } = require('rxjs');

// 创建一个返回 Promise 的函数
function createPromise() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve('Promise resolved using defer');
        }, 2000);
    });
}

// 使用 defer 将返回 Promise 的函数转换为 Observable
const observableFromDefer = defer(() => createPromise());

// 订阅 Observable
observableFromDefer.subscribe({
    next: (value) => console.log('Received value:', value),
    error: (err) => console.error('Error:', err),
    complete: () => console.log('Observable completed')
});
```
