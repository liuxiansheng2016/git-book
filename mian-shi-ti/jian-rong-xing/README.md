# 兼容性



## **CSS 样式显示不一致**

* **使用 CSS 重置样式**：引入通用的 CSS 重置样式，如 `normalize.css`，消除不同浏览器的默认样式差异，确保元素在不同浏览器中有一致的基础样式。
* **添加浏览器前缀**：对于一些新的 CSS3 特性，不同浏览器可能需要不同的前缀来支持。例如，`-webkit-` 用于 Safari 和 Chrome，`-moz-` 用于 Firefox，`-ms-` 用于 IE 等。可以使用工具如 Autoprefixer 自动添加这些前缀。
* **进行浏览器特定的样式调整**：通过媒体查询和浏览器检测，针对不同浏览器编写特定的 CSS 样式。例如，使用 `@supports` 来检测浏览器是否支持某个 CSS 特性，然后提供替代方案。

```
/* 使用 @supports 检测是否支持 flexbox */
@supports (display: flex) {
    .container {
        display: flex;
    }
}

@supports not (display: flex) {
    .container {
        /* 提供替代布局方案 */
        display: block;
    }
}
```

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

**媒体查询**：使用 CSS 媒体查询根据不同的屏幕宽度和设备特性应用不同的样式

**弹性布局**：使用 CSS3 的弹性布局（Flexbox）和网格布局（Grid）来实现自适应的页面布局，使元素能够根据屏幕大小自动调整位置和大小。

采用 `rem/vw` 进行等比例缩放

使用 `meta viewport` 进行视口控制

#### **高清屏幕适配** 对于高清屏幕（如 Retina 屏幕），为了使页面元素显示得更加清晰，可以根据设备的像素比进行缩放设置。例如： 收起html

```
<script>
    const scale = 1 / window.devicePixelRatio;
    document.write('<meta name="viewport" content="width=device-width, initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no">');
</script>
```

\
通过 JavaScript 获取设备的像素比，然后<mark style="color:red;">动态设置</mark> <mark style="color:red;"></mark><mark style="color:red;">`viewport`</mark> <mark style="color:red;"></mark><mark style="color:red;">的缩放比例</mark>，确保页面元素在高清屏幕上以合适的大小显示。







| `position: fixed;` 在 iOS 失效 | 使用 `transform` 触发 GPU 或 `overflow: hidden` 方案 |
| --------------------------- | --------------------------------------------- |

| `flexbox` 在旧版浏览器不兼容 | 添加 `-webkit-box` 前缀 |
| ------------------- | ------------------- |

| `vh` 计算不准 | 用 `window.innerHeight` 计算 |
| --------- | ------------------------- |

| `fetch` 不兼容 | 引入 `whatwg-fetch` polyfill |
| ----------- | -------------------------- |

| `WebP` 在 iOS 端不兼容 | `picture` 标签提供备用格式 |
| ----------------- | ------------------ |

| 旧版浏览器不支持 `grid` | 使用 `@supports` 或 `Modernizr` 检测 |
| --------------- | ------------------------------- |

| 响应式适配 | `meta viewport` + `rem`/`vw` 方案 |
| ----- | ------------------------------- |

