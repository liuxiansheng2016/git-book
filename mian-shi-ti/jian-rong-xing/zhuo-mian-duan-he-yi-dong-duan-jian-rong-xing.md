# 桌面端和移动端兼容性

## 基础原则

### 响应式设计（Responsive Design）

* 使用 **媒体查询（Media Queries）**。
* 使用 **弹性布局（Flexbox）和 CSS Grid**。
* 采用 **流式布局**，让页面随着视口大小调整。
* **图片与视频的自适应加载**。

### 渐进增强（Progressive Enhancement）与优雅降级（Graceful Degradation）



## **常见 CSS 兼容性问题**

* 不同设备下的 **字体大小、间距、边距不一致**。
* **Flexbox 和 Grid 布局** 在部分老版本浏览器中不兼容。
* `vh`、`vw` 单位在 iOS Safari 中可能导致渲染异常。
* CSS 动画、`backdrop-filter`、`object-fit` 等属性在部分设备无效。

## **解决方案**

### **使用媒体查询进行响应式设计**

```
@media screen and (max-width: 768px) 
@media screen and (min-width: 768px) and (max-width: 1024px) 
@media screen and (min-width: 1024px) {
```

### 使用弹性布局（Flexbox）或 CSS Grid

### 使用 `rem` 和 `em` 作为字体和间距单位

### **处理 iOS Safari 的 `vh` 单位 Bug**

```css
css复制编辑/* 修正 iOS Safari 对于 vh 的处理 */
html,
body {
  height: -webkit-fill-available;
  min-height: 100%;
}
```

## **常见 HTML 兼容性问题**

* HTML5 新标签如 `<article>`、`<section>`、`<nav>` 等在 IE8 及以下版本不支持。
* `<input type="date">`、`<input type="range">` 等在部分移动端浏览器不生效。
* `<picture>`、`<source>` 标签在不支持的浏览器中无法切换图片资源。

### 引入 HTML5 Shiv/HTML5 Shim 处理老浏览器兼容性

### 使用 `picture` 和 `srcset` 提供不同分辨率的图片

### 使用 `<meta viewport>` 进行移动端页面优化

```
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
```

### 屏幕**适配**

\
对于高清屏幕（如 Retina 屏幕），为了使页面元素显示得更加清晰，可以根据设备的像素比进行缩放设置。例如：\
收起html

```
<script>
    const scale = 1 / window.devicePixelRatio;
    document.write('<meta name="viewport" content="width=device-width, initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no">');
</script>
```

\
通过 JavaScript 获取设备的像素比，然后<mark style="color:red;">动态设置</mark> <mark style="color:red;"></mark><mark style="color:red;">`viewport`</mark> <mark style="color:red;"></mark><mark style="color:red;">的缩放比例</mark>，确保页面元素在高清屏幕上以合适的大小显示。
