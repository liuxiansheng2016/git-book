# Interval

```javascript
import { interval } from 'rxjs';

const source = interval(1000); // 每秒发出一个值

source.subscribe(value => console.log(value));

```
