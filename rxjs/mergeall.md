---
description: 假设你有一个发出多个 Observables 的 Observable，你需要将这些 Observables 的值合并到一个单一的 Observable 中
---

# mergeAll 和concatAll

## mergeAll

```javascript
import { of, interval } from 'rxjs';
import { mergeAll, take } from 'rxjs/operators';

const higherOrderObservable = of(
  interval(1000).pipe(take(3)), // 每隔1秒发出0, 1, 2
  interval(500).pipe(take(2))  // 每隔500毫秒发出0, 1
);

higherOrderObservable.pipe(mergeAll()).subscribe(value => console.log(value));
// 输出: 0, 0, 1, 1, 2 (具体顺序取决于两个内部Observables的执行速度)
```

## **concatAll**

假设你有一个发出多个 Observable 的 Observable，你需要按顺序处理这些 Observable：

```javascript
ngOnInit() {
  const requests = of(
    of(1, 2, 3),
    of(4, 5, 6)
  );

  // 使用 concatAll 将多个请求按顺序连接
  //如果不使用concatAll，返回的是两个observable
  const result = requests.pipe(concatAll());

  result.subscribe(data => console.log(data)); // 1,2,3,4,5,6
}

```

## **concatAll 和mergeAll**&#x20;

1. concatAll 适合于需要保持内部 Observables 顺序的情况，它会确保按照它们被发出的顺序依次执行。
2. mergeAll 适用于需要同时并发处理多个内部 Observables 的情况，它可以将所有内部 Observable 的输出合并到一个单一的流中，但不保证值的顺序。
