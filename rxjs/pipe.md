---
description: pipe 方法是一个函数，它接受一系列的操作符作为参数，并返回一个新的操作符
---

# Pipe

* `pipe()` 组合多个操作符。
* **使用 `pipe()` 好处：** 遵循函数式编程、易于组合、不修改原始 Observabl

```javascript
import { ajax } from 'rxjs/ajax';
import { delay, filter } from 'rxjs/operators';

const request$ = ajax.getJSON('https://api.example.com/data');

request$.pipe(
  delay(1000), // 延迟 1 秒
  filter(response => response !== '') // 过滤掉空响应
).subscribe({
  next: response => console.log('Response:', response),
  error: error => console.error('Error:', error),
  complete: () => console.log('Completed')
});

```

```javascript
import { fromEvent } from 'rxjs';
import { debounceTime, map } from 'rxjs/operators';

const inputElement = document.getElementById('input');

const input$ = fromEvent(inputElement, 'input');

input$.pipe(
  debounceTime(300), // 延迟 300 毫秒
  map(event => (event.target as HTMLInputElement).value.toUpperCase()) // 转换为大写
).subscribe(value => {
  console.log('Input:', value);
});

```
