# JS

## **常见 JS 兼容性问题**

* **ES6/ESNext 新语法不兼容老版本浏览器。**
* **`Promise`、`async/await` 在 IE/老版本浏览器不支持。**
* **`fetch()` API 及 `XMLHttpRequest` 兼容性问题。**
* **事件绑定方式（`addEventListener` vs `attachEvent`）在老版本 IE 中差异。**
* **`querySelector()`、`classList` 在 IE8 及以下版本中不支持。**



## **JS 兼容性解决方案**

### **使用 Babel 转译 ES6+ 语法**

* Babel 将现代 JavaScript 语法转换为 ES5 语法，兼容大部分浏览器。

### **使用 Polyfill 进行功能补全**

* Polyfill 可以为低版本浏览器提供现代 API 的支持，如 `Promise`、`fetch()`、`Array.prototype.includes` 等。

### **使用 `feature detection`**&#x20;

* 优先检测功能支持情况，而非根据 `user-agent` 判定浏览器。

```js
if ('fetch' in window) {
  fetch('/api/data')
    .then(response => response.json())
    .then(data => console.log(data));
} else {
  // 采用 XMLHttpRequest 作为降级方案
  var xhr = new XMLHttpRequest();
  xhr.open('GET', '/api/data', true);
  xhr.onload = function () {
    if (xhr.status === 200) {
      console.log(JSON.parse(xhr.responseText));
    }
  };
  xhr.send();
}
```
