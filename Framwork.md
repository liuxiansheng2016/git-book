## D3

## angular JS

## GOJS

## Jquery

Jquery是一个JavaScript库，有些时候也称为JS框架，目的就是简化原生JS开发，集成了JS的常用功能，类似的框架还有ExtJS，Dojo，Prototype，YUI。

核心函数（全局函数）是jQuery，和$是等价的，传入对应的参数，返回Jquery对象，用jquery对象才能调用jquery的API。

Jquery对象不能直接调用原生对象的任何API，例如innerHTML、click()、focus()。

Jquery对象并不是原生js对象，它是封装了原生对象并且附加了一些功能，产生的一个新的对象实例。

### Jq的文档就绪事件

写法1：`$(document).ready(回调函数);`

写法2：`$(回调函数);`

window.onload和$(document).ready的区别：

- window.onload：只能注册一次，必须当前html的dom全部加载并且所有的外部资源（图片，音频，视频）全都加载完毕才会触发。
- $(document).ready：注册多次，仅仅html的dom全部加载完毕就会触发，原理是JS的document.onreadystatechange事件。

### 选择器

- `$(selector)`：选择与给定选择器匹配的所有元素。
  - `$('#id')`：选择具有特定 ID 的元素。
  - `$('.class')`：选择具有特定类名的所有元素。
  - `$('element')`：选择指定类型的元素。
  - `$('parent > child')`：选择父元素下的直接子元素。

### DOM 操作

- `append(content)`：向每个匹配的元素内部追加内容。
  - `prepend(content)`：向每个匹配的元素内部前置内容。
  - `html(content)`：设置或获取匹配元素的内容。
  - `text(content)`：设置或获取匹配元素的文本内容。
  - `attr(name, value)`：设置或获取属性值。
  - `addClass(className)`：添加类名。
  - `removeClass(className)`：移除类名。
  - `toggleClass(className)`：切换类名。

### 事件处理

- `click(handler)`：绑定点击事件。
  - `hover(handlerIn, handlerOut)`：绑定鼠标进入和离开事件。
  - `on(event, selector, data, handler)`：绑定事件处理器。
  - `off(event, selector, handler)`：移除事件处理器。

### 动画效果

- `show(duration, callback)`：显示隐藏的元素。
  - `hide(duration, callback)`：隐藏可见的元素。
  - `fadeIn(duration, callback)`：逐渐显示元素。
  - `fadeOut(duration, callback)`：逐渐隐藏元素。
  - `animate(properties, duration, easing, complete)`：自定义动画效果。

### Ajax 支持

- `$.ajax(options)`：发送异步 HTTP 请求。
  - `$.get(url, data, success, dataType)`：发送 GET 请求。
  - `$.post(url, data, success, dataType)`：发送 POST 请求。

通过下标访问元素：`eq`返回jq对象，`get`返回js对象。

大多数的选择器语法都有对应的方法实现。

### 操作属性

- `attr`：如果属性不存在，读取属性，返回undefined，适合非单一属性和自定义属性。
- `prop`：适合单一属性。

### Jquery的循环遍历语句

- `$.each(对象或者数组,function(index, el) {});`
- `JQ对象.each(function(index, el) {});`


