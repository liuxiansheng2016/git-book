# Subject

Subject：是一个可以被多个观察者订阅的可观察对象，同时它可以向观察者发送值。

BehaviorSubject：始终保持最新的值，并且新订阅的观察者可以接收到这个最新的值。

Subject 是一个特殊的对象，它同时具备 Observable 和 Observer 的特性。

具体来说，Subject 可以作为 Observable 被多个订阅者订阅，同时也可以作为 Observer 接收数据并将其广播给所有订阅者。

```javascript
import { Injectable } from '@angular/core';
import { Subject } from 'rxjs/internal/Subject';

@Injectable({
  providedIn: 'root'
})
export class MessageService {
  private msg = new Subject<String>();

  public sendMsg(message: String) {
    this.msg.next(message);
  }

  public getMsg() {
    return this.msg;
    //  return this.msg.asObservable(); // 返回可订阅的 Observable
  }
}

```

订阅消息

```javascript
ngOnInit() {
    this.messageService.getMsg.subscribe(data => {
        // businessCode
    })
}

```

发送消息

```javascript
this.messageService.sendMsg('hello world');
```

#### `asObservable()`&#x20;

是 `Subject` 类的一个方法，它的主要作用是将 `Subject` 转换为一个普通的 `Observable` 对象

* `getMessage()` 返回的是 `Subject`，而不是 `Observable`。
* 任何订阅它的组件都可以 **直接调用 `.next(value)` 发送消息**，这可能会导致：
  * 某些组件 **误修改数据流**，造成数据混乱。
  * 违背**单一数据流**的原则（不希望消费者也能充当生产者）。

## `BehaviorSubject` 的特点：

* **初始值**：当创建 `BehaviorSubject` 实例时，必须指定一个初始值。任何新订阅者都会立即收到这个初始值或最近一次发出的值。
* **状态记忆**：即使没有新的值被发出，订阅者也会得到最后一次发出的值

```
const behaviorSubject = new Rx.BehaviorSubject(0); // 初始值为 0

behaviorSubject.subscribe(val => console.log('Subscriber A:', val));

behaviorSubject.next(1);
behaviorSubject.next(2);

behaviorSubject.subscribe(val => console.log('Subscriber B:', val)); // Subscriber B 接收 2

behaviorSubject.next(3);
```

## &#x20;Subject 和 Observable 的区别是什么？

\
Subject 既是 Observable 又是 Observer，它可以同时发出值和被订阅。与普通 Observable 的区别在于：

### 1. 单播和多播

**Observable**：通常是单播的。每个 `Observer` 订阅 `Observable` 时，`Observable` 会为该订阅者独立执行其内部的逻辑，即每个订阅者都会得到一份独立的数据副本。例如，使用 `interval()` 创建的 `Observable`，每个订阅者都会有自己独立的计数序列。

```
import { Observable } from 'rxjs';

const observable = new Observable(observer => {
  console.log('Observable 被调用');
  observer.next(Math.random()); // 生成一个随机数
});

observable.subscribe(value => console.log('Subscriber 1:', value));
observable.subscribe(value => console.log('Subscriber 2:', value));
```

输出（每个订阅者都会收到不同的值）：

```
Observable 被调用
Subscriber 1: 0.6789

Observable 被调用
Subscriber 2: 0.1234
```

每次订阅都会创建一个新的执行上下文，所以 console.log('Observable 被调用') 执行两次。

**Subject**：是多播的。多个 `Observer` 可以订阅同一个 `Subject`，并且 `Subject` 发出的值会同时推送给所有订阅者，它们共享同一个数据流。如前面 `Subject` 的示例代码所示，两个 `Observer` 都会收到 `subject` 发出的相同的值。

```
const { Subject } = require('rxjs');

// 创建一个 Subject 实例
const subject = new Subject();

// 订阅 Subject
subject.subscribe({
    next: (value) => console.log('Observer 1 received:', value)
});

subject.subscribe({
    next: (value) => console.log('Observer 2 received:', value)
});

// 手动发出值
subject.next(1);
subject.next(2);
```

上述代码创建了一个 `Subject` 实例 `subject`，然后有两个 `Observer` 订阅了它。接着通过调用 `subject.next()` 方法手动发出值，这两个 `Observer` 都会收到这些值。

### **2. 值的发出方式**

* 普通 `Observable` **不能** 直接通过 `.next(value)` 手动推送数据，<mark style="color:red;">数据流是在创建</mark> <mark style="color:red;"></mark><mark style="color:red;">`Observable`</mark> <mark style="color:red;"></mark><mark style="color:red;">时</mark><mark style="color:red;">**定义**</mark><mark style="color:red;">的，所有数据的推送逻辑必须写在</mark> <mark style="color:red;"></mark><mark style="color:red;">`Observable`</mark> <mark style="color:red;"></mark><mark style="color:red;">内部。</mark>不能在外部调用 `next(value)` 推送新数据。

```javascript
import { Observable } from 'rxjs';

const observable = new Observable(observer => {
  observer.next('数据1');
  observer.next('数据2');
  observer.complete(); // 结束数据流
});

// 订阅
observable.subscribe(value => console.log(value));
```

* **Subject**：可以手动控制值的发出，通过调用 `next()`、`error()` 和 `complete()` 方法来分别发出值、抛出错误和发送完成信号。如前面 `Subject` 示例中，通过 `subject.next()` 手动发出值。

### **3. 初始化与状态管理**

* **Observable**：一般不具备状态管理功能，它只是按照内部逻辑发出值。每次订阅时，`Observable` 的执行都是独立的，不依赖于之前的订阅情况。
* **Subject**：某些类型的 `Subject`（如 `BehaviorSubject`、`ReplaySubject`）具有状态管理能力。`BehaviorSubject` 会记住最后发出的值，新的订阅者会立即收到这个值；`ReplaySubject` 可以记住过去发出的多个值，新的订阅者会收到这些历史值。
