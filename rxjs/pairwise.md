# pairwise

将前一个值和当前值作为数组发出

```javascript
import { pairwise } from 'rxjs/operators';
import { of } from 'rxjs';

const source = of(1, 2, 3, 4, 5).pipe(pairwise());

source.subscribe(pair => console.log(pair));

```

假设你有一个数字序列，你想计算每对相邻值之间的差值：

```javascript
import { pairwise, map } from 'rxjs/operators';
import { of } from 'rxjs';

const numbers = of(10, 20, 30, 40, 50);

numbers.pipe(
  pairwise(),
  map(([prev, curr]) => curr - prev)
).subscribe(diff => console.log(`Difference: ${diff}`));

```

假设你有一个事件流，你想检测每次值变化的情况：

```javascript
import { pairwise, filter } from 'rxjs/operators';
import { of } from 'rxjs';

const events = of('start', 'loading', 'loading', 'success', 'success', 'error');

events.pipe(
  pairwise(),
  filter(([prev, curr]) => prev !== curr)
).subscribe(pair => console.log(`Changed from ${pair[0]} to ${pair[1]}`));

```
