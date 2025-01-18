# 将Observable转换成Promise

```javascript
import { Observable } from 'rxjs';
import 'rxjs/add/operator/toPromise'; // RxJS 5.x

const observable: Observable<number> = of(42);

observable.toPromise().then(value => {
    console.log('Value:', value);
}).catch(error => {
    console.error('Error:', error);
});

```

```javascript
import { of, from, throwError } from 'rxjs';
import { firstValueFrom, lastValueFrom } from 'rxjs';

// 使用 firstValueFrom
const observable1 = of(42);

firstValueFrom(observable1).then(value => {
    console.log('First value:', value); // 输出: First value: 42
}).catch(error => {
    console.error('Error:', error);
});

// 使用 lastValueFrom
const observable2 = from([1, 2, 3, 4, 5]);

lastValueFrom(observable2).then(value => {
    console.log('Last value:', value); // 输出: Last value: 5
}).catch(error => {
    console.error('Error:', error);
});

// 如果Observable抛出错误
const observableWithError = throwError(() => new Error('Something went wrong'));

firstValueFrom(observableWithError).then(value => {
    console.log('Value:', value);
}).catch(error => {
    console.error('Error:', error.message); // 输出: Error: Something went wrong
});

```
