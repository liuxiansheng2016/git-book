---
description: 'switchMap: 类似于 mergeMap，但当新的 Observable 发出时，它会取消之前的 Observable。 /'
---

# switchMap

<mark style="color:red;">只输出最后一个 Observable 的结果</mark>

```javascript
import { of } from 'rxjs';
import { switchMap } from 'rxjs/operators';

const source = of(1, 2, 3);
const result = source.pipe(
  switchMap(x => of(x, x * 2).pipe(delay(1000)))
);
result.subscribe(x => console.log(x)); // 只输出最后一个 Observable 的结果

```
