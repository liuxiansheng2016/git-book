# pipe

#### 内置Pipes

1. **DatePipe**：根据区域设置规则格式化日期值。
   * 使用示例：`{{ today | date:'yyyy-MM-dd' }}`
2. **UpperCasePipe**：将文本转换为全大写。
   * 使用示例：`{{ 'hello world' | uppercase }}`
3. **LowerCasePipe**：将文本转换为全小写。
   * 使用示例：`{{ 'HELLO WORLD' | lowercase }}`
4. **CurrencyPipe**：将数字格式化为货币字符串。
   * 使用示例：`{{ salary | currency:'USD':'symbol':'1.2-2' }}`
5. **DecimalPipe**：根据指定的本地环境格式化数字。
   * 使用示例：`{{ price | number:'3.2-2' }}`
6. **PercentPipe**：将数字格式化为百分比字符串。
   * 使用示例：`{{ chance | percent }}`
7. **SlicePipe**：创建一个新列表或字符串，它是原列表或字符串的一个子集。
   * 使用示例：`{{ ['a', 'b', 'c', 'd'] | slice:1:3 }}`
8. **JsonPipe**：将值转换为JSON字符串格式，主要用于调试目的。
   * 使用示例：`<pre>{{ data | json }}</pre>`
9. **AsyncPipe**：订阅Observable或Promise，并将发出的最后一个值直接应用于模板。
   * 使用示例：`{{ observable$ | async }}`

#### 1. 什么是 async 管道（Pipe）？它的作用是什么？

\
`async` 管道是 Angular 内置的一个管道，用于处理异步数据。它可以自动订阅 `Observable` 或 `Promise` 对象，并在数据可用时自动更新视图<mark style="color:red;">，同时在组件销毁时自动取消订阅</mark>，避免内存泄漏。

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
