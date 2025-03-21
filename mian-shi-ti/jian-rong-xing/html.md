# HTML

### **常见 HTML 兼容性问题**

* **HTML5 新标签不支持**：如 `<article>`、`<section>`、`<nav>`、`<header>` 等标签在 IE8 及以下版本中无效。
* **`input` 类型不支持**：如 `date`、`email`、`range` 等新类型在部分浏览器中无效。
* **HTML5 新特性（如 `audio`、`video`、`canvas`、`placeholder` 等）在低版本浏览器无法使用。**



### **使用 HTML5 Shiv/HTML5 Shim**

* 解决 HTML5 新标签在 IE8 及以下版本中的支持问题。

```html
<!--[if lt IE 9]>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/html5shiv/3.7.3/html5shiv.min.js"></script>
<![endif]-->
```

### **使用 Modernizr 进行特性检测**

* `Modernizr` 允许在不同浏览器中检测 HTML5 和 CSS3 的支持情况，根据情况加载降级方案。

```bash
安装 modernizr
npm install modernizr --save-dev
```

```html
<script src="modernizr.js"></script>
<script>
  if (Modernizr.canvas) {
    console.log('Canvas is supported');
  } else {
    console.log('Canvas is not supported');
  }
</script>
```

### **为 HTML5 标签设置 `display: block`**

* 解决 IE8 及以下版本中 HTML5 标签无法识别的问题。

```css
article, aside, details, figcaption, figure, footer, header, main, nav, section, summary {
  display: block;
}
```
