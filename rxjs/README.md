---
description: 一种用于异步编程的库。它基于响应式编程模型
---

# Rxjs

### **RxJS 操作符分类**

RxJS 操作符可以分为以下几大类：

<table data-header-hidden><thead><tr><th width="134"></th><th></th><th width="168.727294921875"></th></tr></thead><tbody><tr><td><strong>类别</strong></td><td><strong>常见操作符</strong></td><td><strong>作用</strong></td></tr><tr><td><strong>创建类</strong></td><td><code>of()</code>, <code>from()</code>, <code>interval()</code>, <code>timer()</code>, <code>fromEvent()</code></td><td>创建新的 Observable</td></tr><tr><td><strong>转换类</strong></td><td><code>map()</code>, <code>pluck()</code>, <code>buffer()</code>, <code>scan()</code></td><td>转换数据</td></tr><tr><td><strong>过滤类</strong></td><td><code>filter()</code>, <code>first()</code>, <code>last()</code>, <code>take()</code>, <code>skip()</code></td><td>过滤流中的数据</td></tr><tr><td><strong>组合类</strong></td><td><code>merge()</code>, <code>concat()</code>, <code>combineLatest()</code>, <code>forkJoin()</code></td><td>组合多个 Observable</td></tr><tr><td><strong>错误处理</strong></td><td><code>catchError()</code>, <code>retry()</code>, <code>retryWhen()</code></td><td>处理流中的错误</td></tr><tr><td><strong>多播类</strong></td><td><code>share()</code>, <code>shareReplay()</code>, <code>publish()</code></td><td>让多个订阅者共享数据流</td></tr><tr><td><strong>时间操作</strong></td><td><code>debounceTime()</code>, <code>throttleTime()</code>, <code>delay()</code>, <code>timeout()</code></td><td>控制数据流的速率</td></tr></tbody></table>
