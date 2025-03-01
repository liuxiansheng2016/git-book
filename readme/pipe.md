# pipe

#### 1. 什么是 async 管道（Pipe）？它的作用是什么？

\
`async` 管道是 Angular 内置的一个管道，用于处理异步数据。它可以自动订阅 `Observable` 或 `Promise` 对象，并在数据可用时自动更新视图，同时在组件销毁时自动取消订阅，避免内存泄漏。

**作用**

* **简化异步数据处理**：无需手动订阅和取消订阅 `Observable` 或处理 `Promise` 的解析。
* **自动更新视图**：当异步数据发生变化时，`async` 管道会自动更新绑定到该数据的视图。

**示例**



```
import { Component } from '@angular/core';
import { Observable, of } from 'rxjs';

@Component({
  selector: 'app-async-pipe-example',
  template: `
    <p>{{ asyncData | async }}</p>
  `
})
export class AsyncPipeExampleComponent {
  asyncData: Observable<string> = of('Async data');
}
```

#### 2. 如何在 Angular 中创建和使用自定义管道（Custom Pipe）？

**创建自定义管道步骤**

1. **创建管道类**：使用 `@Pipe` 装饰器定义一个管道类，并实现 `PipeTransform` 接口的 `transform` 方法。
2. **注册管道**：在模块的 `declarations` 数组中注册该管道。

**示例**

```
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'customPipe'
})
export class CustomPipe implements PipeTransform {
  transform(value: string, args?: any): string {
    return value.toUpperCase();
  }
}

```

**使用自定义管道**

```
<p>{{ 'hello' | customPipe }}</p>
```
