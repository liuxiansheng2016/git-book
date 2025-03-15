# mergeWith 和concatWith

## mergeWith

```
import { interval, mergeWith } from 'rxjs';
import { take } from 'rxjs/operators';

// 创建两个 Observable
const source1$ = interval(1000).pipe(take(3));
const source2$ = interval(500).pipe(take(3));

// 使用 mergeWith 合并两个 Observable
const merged$ = source1$.pipe(mergeWith(source2$));

// 订阅合并后的 Observable
merged$.subscribe((value) => console.log('mergeWith result:', value));
```

* `source1$` 是一个每隔 1 秒发出一个值，共发出 3 个值的 Observable。
* `source2$` 是一个每隔 0.5 秒发出一个值，共发出 3 个值的 Observable。
* 使用 `mergeWith` 操作符将 `source1$` 和 `source2$` 合并成一个新的 Observable `merged$`。
* <mark style="color:red;">订阅</mark> <mark style="color:red;"></mark><mark style="color:red;">`merged$`</mark> <mark style="color:red;"></mark><mark style="color:red;">后，会看到两个 Observable 发出的值交错输出，因为它们是并行执行的。</mark>

## **concatWith**

将当前的 Observable 与另一个 Observable 连接起来，只有当前一个 Observable 完成后，才会开始订阅下一个 Observable。

```javascript
ngOnInit() {
  const source1 = of(1, 2, 3);
  const source2 = of(4, 5, 6);

  const result = source1.pipe(concatWith(source2));
  result.subscribe((x) => console.log(x)); // 输出: 1, 2, 3, 4, 5, 6
}

```

