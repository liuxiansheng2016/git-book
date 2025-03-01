# 数据传输

### 父到子

输入属性（@Input）

### 子到父

使用 `@Output` 装饰器可以在子组件向父组件传递事件。子组件通过 `EventEmitter` 发出事件

```
// 子组件
import { Component, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'app-child',
  template: '<button (click)="sendMessage()">Send Message</button>'
})
export class ChildComponent {
  @Output() messageEvent = new EventEmitter<string>();

  sendMessage() {
    this.messageEvent.emit('Hello from child');
  }
}

// 父组件模板
<app-child (messageEvent)="receiveMessage($event)"></app-child>
```

#### 服务（Services）

使用服务可以在多个组件之间共享数据。服务可以通过依赖注入（DI）系统在组件中注入，并提供数据和方法。

#### 路由参数（Route Parameters）

\
storage

RXjs 发布订阅

```
import { Injectable } from '@angular/core';
import { Subject } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class MessageService {
  private msg = new Subject<string>();

  public sendMsg(message: string) {
    this.msg.next(message);
  }

  public getMsg() {
    return this.msg.asObservable();
}
}
```

