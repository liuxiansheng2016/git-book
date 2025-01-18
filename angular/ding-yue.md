# 订阅

## &#x20;**使用 Subscription 管理多个订阅**

如果你有多个订阅，可以使用 Subscription 类来管理它们

### new Subscription()

```javascript
import { Component, OnInit, OnDestroy } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Subscription } from 'rxjs';

@Component({
  selector: 'app-my-component',
  templateUrl: './my-component.component.html',
  styleUrls: ['./my-component.component.css']
})
export class MyComponent implements OnInit, OnDestroy {
  data1: any;
  data2: any;
  private subscriptions: Subscription = new Subscription();

  constructor(private http: HttpClient) {}

  ngOnInit(): void {
    const subscription1 = this.http.get('https://api.example.com/data1')
      .subscribe(
        (response) => {
          this.data1 = response;
          console.log('Data1:', this.data1);
        },
        (error) => {
          console.error('Error1:', error);
        }
      );

    const subscription2 = this.http.get('https://api.example.com/data2')
      .subscribe(
        (response) => {
          this.data2 = response;
          console.log('Data2:', this.data2);
        },
        (error) => {
          console.error('Error2:', error);
        }
      );

    // 使用 add 方法将订阅添加到 subscriptions 中
    this.subscriptions.add(subscription1);
    this.subscriptions.add(subscription2);
  }

  ngOnDestroy(): void {
    this.subscriptions.unsubscribe();
  }
```

这种声明方式创建了一个新的 Subscription 对象，并将其赋值给 subscriptions 属性。这个 Subscription 对象可以用来管理多个订阅，通过 add 方法将多个订阅添加到这个对象中。

优点：

1. 集中管理：可以将多个订阅集中管理在一个 Subscription 对象中。
2. &#x20;自动取消：当调用 unsubscribe 方法时，所有添加的订阅都会被自动取消。

### Subscription\[] = \[]

```javascript
import { Component, OnDestroy } from '@angular/core';
import { Subscription } from 'rxjs';
import { MyService } from './my.service';

@Component({
  selector: 'app-my-component',
  templateUrl: './my-component.component.html',
  styleUrls: ['./my-component.component.css']
})
export class MyComponent implements OnDestroy {
  private subscriptions: Subscription[] = [];

  constructor(private myService: MyService) {
    // 假设我们有两个需要订阅的 Observable
    const subscription1 = this.myService.getData().subscribe(
      (data) => console.log('Data:', data),
      (error) => console.error('Error:', error)
    );

    const subscription2 = this.myService.getMoreData().subscribe(
      (data) => console.log('More Data:', data),
      (error) => console.error('Error:', error)
    );

    // 将这两个订阅添加到 subscriptions 数组中
    this.subscriptions.push(subscription1, subscription2);
  }

  ngOnDestroy(): void {
    // 当组件销毁时，取消所有订阅
    this.subscriptions.forEach(sub => sub.unsubscribe());
  }
}
```

如果使用  this.subscriptions.push(subscription1);的话，需要遍历所有订阅然后取消

## destroy$？

```javascript
private destroy$: Subject<void> = new Subject<void>();
```

```javascript
ngOnInit(): void {
  this.http.get('https://api.example.com/data')
      .pipe(takeUntil(this.destroy$))
      .subscribe(
          (response) => {
              this.data = response;
              console.log('Data:', this.data);
          },
          (error) => {
              console.error('Error:', error);
          }
      );
}

```

```javascript
ngOnDestroy(): void {
  // 当组件销毁时发出信号，取消所有订阅
  this.destroy$.next();
  this.destroy$.complete();
}

```

\
这里定义了一个名为 destroy$ 的私有属性，类型为 Subject\<void>。void 表示这个 Subject 不会发送任何实际的数据，只是一个信号。

pipe(takeUntil(this.destroy$)) 创建一个新的 Observable，它会在 this.destroy$ 发出信号时停止发出值。

1. 你调用 this.destroy$.next() 来发出一个信号，表示组件即将销毁。
2. 然后调用 this.destroy$.complete() 来完成这个 Subject，确保不会再有新的信号发出。

