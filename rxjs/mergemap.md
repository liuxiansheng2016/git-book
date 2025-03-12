---
description: 它将每个源值投影到一个新的 Observable，并将这些新的 Observable 合并到输出的 Observable 中
---

# mergeMap

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
