# debounceTime/throttleTime

**示例：使用`debounceTime`防抖**

假设我们有一个搜索框，希望用户停止输入1秒后再进行搜索：

```
import { Component, OnInit } from '@angular/core';
import { fromEvent, Subscription } from 'rxjs';
import { debounceTime, map } from 'rxjs/operators';

@Component({
  selector: 'app-search',
  template: `<input type="text" placeholder="搜索..." #searchBox>`
})
export class SearchComponent implements OnInit, OnDestroy {
  private subscription: Subscription;

  constructor() {}

  ngOnInit(): void {
    const searchBox = document.querySelector('input') as HTMLInputElement;
    
    this.subscription = fromEvent(searchBox, 'input').pipe(
      map(event => (event.target as HTMLInputElement).value),
      debounceTime(1000) // 等待1秒钟
    ).subscribe(value => 
      console.log(`Searching for ${value}...`)
    );
  }

  ngOnDestroy(): void {
    if (this.subscription) {
      this.subscription.unsubscribe();
    }
  }
}
```

#### 使用节流（Throttle）

节流是指在规定的时间间隔内只允许事件触发一次，即使在这个时间间隔内事件被多次触发。适用于需要限制事件频率但又不想错过每一个事件的情况，比如窗口调整大小或滚动事件。

**示例：使用`throttleTime`节流**

假设我们需要监听窗口大小调整，并且希望每500毫秒最多响应一次：

```
import { Component, OnInit, OnDestroy } from '@angular/core';
import { fromEvent, Subscription } from 'rxjs';
import { throttleTime } from 'rxjs/operators';

@Component({
  selector: 'app-resize-listener',
  template: `<p>监听窗口大小变化</p>`
})
export class ResizeListenerComponent implements OnInit, OnDestroy {
  private subscription: Subscription;

  constructor() {}

  ngOnInit(): void {
    this.subscription = fromEvent(window, 'resize').pipe(
      throttleTime(500) // 每500毫秒最多响应一次
    ).subscribe(event => 
      console.log(`Window resized to ${window.innerWidth}x${window.innerHeight}`)
    );
  }

  ngOnDestroy(): void {
    if (this.subscription) {
      this.subscription.unsubscribe();
    }
  }
}
```
