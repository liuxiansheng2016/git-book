---
description: 发出给定的第一个值
---

# startWith

```javascript
// RxJS v6+
import { startWith } from 'rxjs/operators';
import { of } from 'rxjs';

// Emit (1, 2, 3)
const source = of(1, 2, 3);
// Start with 0
const example = source.pipe(startWith(0));
// Output: 0, 1, 2, 3
const subscribe = example.subscribe(val => console.log(val));

```
