# concatWith和concatAll

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
