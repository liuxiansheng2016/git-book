---
description: "当有一组 observables，但你只关心每个 observable 最后发出的值时，此操作符是最适合的。它可能与\_Promise.all\_的使用方式类似"
---

# forkJoin

```
ngOnInit() {
    const source1 = of(1, 2, 3);
    const source2 = of(4, 5, 6);

    const combined$ = forkJoin([source1, source2]);

    combined$.subscribe(results => {
        console.log(results[0]); // 3
        console.log(results[1]); // 6
    });
}

```
