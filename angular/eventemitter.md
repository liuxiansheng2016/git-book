---
description: >-
  EventEmitter 的基本原理是观察者模式（Observer
  Pattern）。观察者模式定义了对象之间的一对多依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都会得到通知并自动更新。
---

# EventEmitter



在 Angular 中，EventEmitter 主要用于父子组件之间的通信。以下是一些关键点：

## **定义 EventEmitter：**

使用 @Output 装饰器将 EventEmitter 标记为输出属性。

EventEmitter 是一个 RxJS 的 Subject，它可以用来发射事件。

## **发射事件：**

通过调用 emit 方法来发射事件，并传递事件数据。

```javascript
import { Output } from '@angular/core';

@Component({
  selector: 'app-child-component',
  template: `
    <button (click)="emitEvent()">Emit Event</button>
  `,
  styles: []
})
export class ChildComponent {
  @Output() customEvent = new EventEmitter<string>();

  emitEvent() {
    this.customEvent.emit('Hello from Child Component');
  }
}
```

## **监听事件：**

父组件通过模板绑定来监听子组件的 EventEmitter 事件。

```javascript
<app-child-component (customEvent)="onChildEvent($event)"></app-child-component>
```

## **实现**EventEmitter 类

1. &#x20;EventEmitter 类\
   继承自 RxJS 的 Subject 类。Subject 是一个特殊的 Observable，它既是 Observer 又是 Observable，可以用来在多个订阅者之间共享数据。

```javascript
import { Subject } from 'rxjs';

export class EventEmitter<T> {
  private subject = new Subject<T>();

  // 发出事件
  emit(value: T) {
    this.subject.next(value);
  }

  // 订阅事件
  subscribe(observerOrNext?: any, error?: any, complete?: any) {
    return this.subject.subscribe(observerOrNext, error, complete);
  }
}
```

```javascript
```
