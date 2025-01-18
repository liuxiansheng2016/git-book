---
description: 用于对来自源 observable 的通知执行副作用
---

# Tap

```javascript
import { of, tap, map } from 'rxjs';

of(Math.random()).pipe(
  tap(console.log),
  map(n => n > 0.5 ? 'big' : 'small')
).subscribe(console.log);

```
