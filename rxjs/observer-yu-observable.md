# Observer 与 Observable

### **Observer**

定义：一个 Observer 是一个拥有三个可选方法的对象或实现这些方法的函数，用于接收来自 Observable 的通知：

· next(value)：当 Observable 发出一个值时调用。

· error(error)：当 Observable 遇到错误并终止时调用。

· complete()：当 Observable 正常完成且不再发出更多值时调用。

作用：Observer 负责处理从 Observable 接收到的数据或事件，并对这些数据或事件做出响应。它订阅了 Observable 后，就会开始监听由该 Observable 发出的通知。

### **Observable**

定义：Observable 是一个可以被订阅的对象，它代表了一个可能异步发生的值或事件序列。一旦有观察者订阅它，Observable 就会开始发送通知给这个观察者。

生产者与消费者：<mark style="color:red;">Observable 是生产者，负责生成数据流；而 Observer 是消费者，负责消费这些数据。</mark>

```javascript
const observer = {
  next: (x: any) => console.log('Observer got a next value: ' + x),
  error: (err: any) => console.error('Observer got an error: ' + err),
  complete: () => console.log('Observer got a complete notification'),
};

const myObservable = new Observable((subscriber) => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.next(3);
  // 完成通知
  subscriber.complete();
});

myObservable.subscribe(observer);

```

Observer 写法

```javascript
myObservable.subscribe({
    next: value => console.log(value),
    error: err => console.error('Error: ' + err),
    complete: () => console.log('Completed')
});

myObservable.subscribe(
    value => console.log(value), // next
    err => console.error('Error: ' + err), // error
    () => console.log('Completed') // complete
);

//可以同时处理 next、error、complete。
const subject = new Subject<number>();
subject.subscribe({
  next: (value) => console.log('Next:', value),
  error: (err) => console.error('Error:', err),
  complete: () => console.log('Complete'),
});

subject.next(42);    // 输出: Next: 42
subject.error('Oops'); // 输出: Error: Oops
subject.complete();  // 输出: Complete

//仅能处理 next。
const subject = new Subject<number>();
subject.subscribe((value) => console.log('Next:', value));
subject.next(42); // 输出: Next: 42

```

### **使用静态创建方法**

RxJS 提供了多个静态方法来创建特定类型的 Observables，例如从数组、Promise、事件等创建。

1. &#x20;from: 将任何可迭代对象或 Promise 转换为 Observable。
2. of: 创建一个发出一系列指定值的 Observable。
3. &#x20;interval: 创建一个每隔一定时间发出递增数值的 Observable。
4. &#x20;timer: 创建一个在指定延迟后开始发出值的 Observable。
