# debounce() 和 debounceTime()

### **`debounce`**

```javascript
// RxJS v6+
import { of, timer } from 'rxjs';
import { debounce } from 'rxjs/operators';

// 发出四个字符串
const example = of('WAIT', 'ONE', 'SECOND', 'Last will display');
/*
  只有在最后一次发送后再经过一秒钟，才会发出值，并抛弃在此之前的所有其他值
*/
const debouncedExample = example.pipe(debounce(() => timer(1000)));
/*
  在这个示例中，所有的值都将被忽略，除了最后一个
  输出： 'Last will display'
*/
const subscribe = debouncedExample.subscribe(val => console.log(val));

```

### **`debounce()` 和 `debounceTime()` 的区别**

这两个 RxJS 操作符都用于**防抖（Debounce）**，即**抑制短时间内频繁触发的事件**，仅在**事件间隔达到一定时间后**才执行最新的事件。

| 操作符                           | 作用                            |
| ----------------------------- | ----------------------------- |
| `debounceTime(time)`          | 在**指定的时间间隔**内如果没有新的值，则发出最新的值。 |
| `debounce(() => timer(time))` | **基于回调函数**，可以动态控制**防抖时间**。    |
