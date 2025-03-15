---
description: forkJoin 和 combineLatest 都是 RxJS 中用于 合并多个 Observable 的操作符
---

# forkJoin 和combineLatest



| 操作符             | 触发时机                                  | 适用场景                                   |
| --------------- | ------------------------------------- | -------------------------------------- |
| `forkJoin`      | **等所有 Observable 完成后，一次性返回最后的值**      | **并行执行多个异步任务，等待全部完成后处理结果**（如多个 API 请求） |
| `combineLatest` | **每当任意一个 Observable 发生变化时，都会返回最新组合值** | **需要最新的多个数据流，如表单输入、WebSocket 数据流合并**   |

## forkJoin

当有一组 observables，但你只关心每个 observable 最后发出的值时，此操作符是最适合的。<mark style="color:red;">它可能与 Promise.all 的使用方式类似</mark>

**多个独立 API 请求，必须等所有数据返回后再处理**（如页面初始化）。

```
import { forkJoin, of } from 'rxjs';
import { delay } from 'rxjs/operators';

const api1 = of('用户数据').pipe(delay(2000));
const api2 = of('订单数据').pipe(delay(3000));
const api3 = of('商品数据').pipe(delay(1000));

forkJoin([api1, api2, api3]).subscribe(results => {
  console.log('✅ 所有 API 请求完成:');
  console.log('用户:', results[0]); 
  console.log('订单:', results[1]); 
  console.log('商品:', results[2]);
});

```

## **`combineLatest()`**

**`combineLatest` 适用于需要** **"每次有新数据时，获取最新组合"** 的场景，<mark style="color:red;">只有当</mark> <mark style="color:red;"></mark><mark style="color:red;">**所有的输入流至少发出过一次值**</mark> <mark style="color:red;"></mark><mark style="color:red;">后，它才会开始触发新的值</mark>

* 当任意一个 Observable 发送新值时，就会合并最新值并发出。
* 适用于实时数据流的同步更新（如表单输入）。

假设我们有一个价格输入框和一个数量输入框，每当用户修改任意一个输入框时，都会实时更新总价。

```
import { combineLatest, fromEvent } from 'rxjs';
import { map } from 'rxjs/operators';

const priceInput = document.getElementById('price') as HTMLInputElement;
const quantityInput = document.getElementById('quantity') as HTMLInputElement;

const price$ = fromEvent(priceInput, 'input').pipe(
  map(event => parseFloat((event.target as HTMLInputElement).value) || 0)
);

const quantity$ = fromEvent(quantityInput, 'input').pipe(
  map(event => parseInt((event.target as HTMLInputElement).value) || 1)
);

combineLatest([price$, quantity$]).subscribe(([price, quantity]) => {
  console.log(`💰 总价: ${price * quantity}`);
});

```
