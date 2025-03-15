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

### **`fromEvent 和 from`**

**`fromEvent()`**：主要用于将事件源（如 DOM 事件、Node.js 事件等）转换为 Observable。它会监听指定的事件源，当事件发生时，就会将该事件对象作为值发出，适用于处理各种事件流，比如用户的点击、键盘输入、滚动等事件。

**`from()`**：可以将多种不同类型的数据结构或异步操作转换为 Observable。这些数据结构包括数组、类数组对象、可迭代对象（如 Set、Map），异步操作包括 Promise、异步迭代器等，适用于将现有的数据或异步结果转换为可观察的序列。

