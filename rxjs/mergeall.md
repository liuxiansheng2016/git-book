---
description: 假设你有一个发出多个 Observables 的 Observable，你需要将这些 Observables 的值合并到一个单一的 Observable 中
---

# mergeAll

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
