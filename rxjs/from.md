# from

from 方法可以接受任何可迭代的对象（如数组、字符串、Set、Map，promise 等），并创建一个 Observable，该 Observable 会依次发出可迭代对象中的每个元素，并在最后一个元素发出之后完成。

```javascript
import { from } from 'rxjs';

const array = [1, 2, 3, 4, 5];

// 从数组创建 Observable
const valuesFromArray = from(array);
valuesFromArray.subscribe({
  next: value => console.log(`Next value (Array): ${value}`),
  complete: () => console.log('Completed (Array)')
});

```

<mark style="color:red;">你可以使用 from 将一个 Promise 转换为 Observable。</mark>

```javascript
import { from } from 'rxjs';

const promise = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve('Success!');
    }, 1000);
});

const observableFromPromise = from(promise);

observableFromPromise.subscribe({
    next: value => console.log(`Next value: ${value}`),
    complete: () => console.log('Completed')
});

```
