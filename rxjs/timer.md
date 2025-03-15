---
description: timer 接收第二个参数，它决定了发出序列值的频率，在本例中我们在1秒发出第一个值
---

# timer 和interval

### timer

```javascript
import { timer } from 'rxjs';

// 只发出一个值，延迟 2 秒后发出
const singleValueSource = timer(2000);
singleValueSource.subscribe(value => console.log(value)); // 输出 0

// 延迟 2 秒后开始，每秒发出一个值
const delayedIntervalSource = timer(2000, 1000);
delayedIntervalSource.subscribe(value => console.log(value)); // 输出 0, 1, 2, ...

```

### interval

```
import { interval } from 'rxjs';

const source = interval(1000); // 每秒发出一个值

source.subscribe(value => console.log(value));
```
