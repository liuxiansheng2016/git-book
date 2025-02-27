# 兼容性

## **CSS 样式显示不一致**

* **使用 CSS 重置样式**：引入通用的 CSS 重置样式，如 `normalize.css`，消除不同浏览器的默认样式差异，确保元素在不同浏览器中有一致的基础样式。
* **添加浏览器前缀**：对于一些新的 CSS3 特性，不同浏览器可能需要不同的前缀来支持。例如，`-webkit-` 用于 Safari 和 Chrome，`-moz-` 用于 Firefox，`-ms-` 用于 IE 等。可以使用工具如 Autoprefixer 自动添加这些前缀。
* **进行浏览器特定的样式调整**：通过媒体查询和浏览器检测，针对不同浏览器编写特定的 CSS 样式。例如，使用 `@supports` 来检测浏览器是否支持某个 CSS 特性，然后提供替代方案。

## **JavaScript 功能无法正常使用**

* **使用 Babel 进行代码转换**：Babel 是一个 JavaScript 编译器，可以将新的 JavaScript 代码转换为向后兼容的版本，确保在旧版浏览器中也能正常运行。
* **引入 Polyfill**：对于一些浏览器不支持的新特性，可以引入相应的 Polyfill 库来提供替代实现。例如，`es6-promise` 库可以为不支持 `Promise` 的浏览器提供 `Promise` 功能。
* **进行浏览器特性检测**：在代码中使用特性检测来判断浏览器是否支持某个功能，然后根据检测结果执行不同的代码逻辑。

```
// 检测浏览器是否支持 Promise
if ('Promise' in window) {
    // 使用 Promise
    const promise = new Promise((resolve, reject) => {
        resolve('Success');
    });
    promise.then(result => console.log(result));
} else {
    // 提供替代方案
    console.log('Promise is not supported');
}
```

## 确保移动端页面在不同分辨率和屏幕尺寸设备及不同浏览器中有一致显示效果的方法

