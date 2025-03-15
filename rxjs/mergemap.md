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

## mergeMap

它将每个源值投影到一个新的 Observable，并将这些新的 Observable 合并到输出的 Observable 中

假设你有一个用户 ID 列表，你需要根据这些 ID 获取用户的详细信息。你可以使用 mergeMap来实现这一点：

```javascript
ngOnInit() {
  const userIds = [1, 2, 3, 4];
  const result = of(...userIds).pipe(
    mergeMap((userId) =>
      this.httpClient.get(
        `https://jsonplaceholder.typicode.com/users/${userId}`
      )
    )
  );

  result.subscribe((user) => console.log(user));
}

```

## switchMap&#x20;

switchMap: 类似于 mergeMap，但当新的 Observable 发出时，它会取消之前的 Observable。\ <mark style="color:red;">只输出最后一个 Observable 的结果</mark>

```javascript
import { of } from 'rxjs';
import { switchMap } from 'rxjs/operators';

const source = of(1, 2, 3);
const result = source.pipe(
  switchMap(x => of(x, x * 2).pipe(delay(1000)))
);
result.subscribe(x => console.log(x)); // 只输出最后一个 Observable 的结果
```
