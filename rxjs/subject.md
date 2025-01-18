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
