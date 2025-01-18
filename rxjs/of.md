# Of

of 方法可以接受一系列的值，并立即创建一个 Observable，该 Observable 会依次发出这些值，并在最后一个值发出之后完成。

```javascript
import { of } from 'rxjs';

const values = of(1, 2, 3, 4, 5);

values.subscribe({
  next: value => console.log(`Next value: ${value}`),
  complete: () => console.log('Completed')
});

```

