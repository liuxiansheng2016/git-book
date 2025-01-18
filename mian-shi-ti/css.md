# CSS

选择器优先级

```
选择器优先级：id选择器>类选择器>元素选择器   行内样式>内嵌样式>外联样式
元素选择器 1
类选择器 10
Id选择器 100
行内样式 1000
!important 权重最高
```

css盒子模型

{% code overflow="wrap" %}
```
一个完整的盒子模型是由四部分组成，内容 padding（内边距）  border（边框） margin（外边距）
盒子模型的怪异模式
在标准模式的盒子模型中，padding和border都是向外扩展空间，并不会占据内容本身的空间
在怪异模式的盒子模型中，padding和border是占据内容本身的空间，并不会向外扩展
产生怪异模式的情况：
1.不写doctype文档类型并且在低版本的IE的浏览器下
2.在CSS3中用box-sizing属性设置值为border-box

```
{% endcode %}

link 和@import 区别

{% code overflow="wrap" %}
```
1.link 是 XHT ML 标签，除了加
载 CSS 外，还可以定义 RSS 等其他事务；@import 属于 CSS 范畴，只能加载 CSS。
2.link 引用 CSS 时，在页面载入时同时加载；@import 需要页面网页完全载入以后加载。
3.link 是 XHTML 标签，无兼容问题；@import 是在 CSS2.1 提出的，低版本的浏览器不支持。
@import 通常用于在同一样式表文件内部引入其他样式表，特别是在复杂的项目中，需要组织和模块化样式表文件时
```
{% endcode %}

怎么组织CSS

```
/可扩展、模块化
https://blog.csdn.net/clhq71932/article/details/100254226
```

CSS加载造成的阻塞优化

{% code overflow="wrap" %}
```
不会阻塞dom树的解析，会阻塞dom树的渲染
https://blog.csdn.net/qq_41462647/article/details/130161709
当 <link rel="stylesheet"> 标签出现在文档的 <head> 部分时，CSS 的加载会阻塞 JavaScript 的执行。
```
{% endcode %}

移动端页面布局

```
响应式设计：
使用媒体查询（Media Queries）
百分比布局：
使用 width: 100% 或 height: auto 进行宽度或高度的自适应
弹性单位：
使用 rem 和 em 替代固定单位 px。
Flexbox 布局：
常用于实现水平和垂直居中、等分布局。
工具与框架：
使用框架如 Bootstrap 或 TailwindCSS。
```

Flex auto

```
flex grow 0 有空间也不放大
flex shrink 1 没空间缩小
flex basic auto 占据的主轴空间
```

空元素（void elements）是指没有闭合标签的元素。

```
<br/>
<img/>
```

HTML/XML/XHTML

```
⦁HTML：超文本标记语言，是语法较为松散的、不严格的Web语言；
⦁XML：可扩展的标记语言，主要用于存储数据和结构，可扩展；
⦁XHTML：可扩展的超文本标记语言，基于XML，作用与HTML类似，但语法更严格
```

css样式匹配规则

```
在css样式表中，为了提高元素匹配效率，样式匹配是从右到左进行匹配的
```

Canvas 和 HTML/CSS

{% code overflow="wrap" %}
```
Canvas
直接像素绘制：Canvas 是一个绘图设备，它允许你通过 JavaScript 直接绘制图像和图形到画布上。所有的图形操作都是通过 CanvasRenderingContext2D 或 WebGL 接口完成的。
GPU 加速：Canvas 可以利用 GPU 加速来渲染图形，尤其是在使用 WebGL 时，图形渲染效率更高。
实时更新：Canvas 的图形是通过脚本实时生成的，因此非常适合用于动态和实时更新的内容，如游戏、动画等
对比
⦁Canvas 更适合用于图形密集型应用，如游戏、图表、动画等，尤其是需要高性能和实时更新的场景。
⦁HTML/CSS 更适合用于结构化内容和布局，如网页设计、文档显示等，尤其在需要复杂的交互逻辑和事件处理时。
```
{% endcode %}

prefetch和preload

```
使用 <link rel="preload">和<link rel="prefetch"> 提前加载关键资源
将关键样式内联
延迟加载非关键 CSS
⦁prefetch：用于提前加载后续页面的资源，提高用户体验。
⦁preload：用于提前加载当前页面的关键资源，提高页面加载速度。
```

CSS Modules

<pre data-overflow="wrap"><code><strong>CSS Modules 是一种 CSS 样式的模块化方法，它能够让你避免样式冲突，并且使 CSS 代码更加可维护。
</strong>作用域限制： .module.css 文件中的样式是局部作用域的，即它们只会应用到被引用的组件或元素中，而不会全局影响页面上的其他元素。这意味着，CSS 选择器（如类名、ID 等）只会在组件内部生效，不会与其他组件的样式冲突。
自动生成唯一类名： 当你使用 CSS Modules 时，构建工具（如 Webpack）会自动将类名转换为唯一的哈希值。这样，即使你在不同的组件中使用相同的类名，它们也不会相互影响。例如：
/* styles.module.css */
.button {
  background-color: blue;
}
编译后的 CSS 可能变成：
/* styles.module.css */
.button_button_abc123 {
  background-color: blue;
}
import styles from './styles.module.css';

function Button() {
  return &#x3C;button className={styles.button}>Click Me&#x3C;/button>;
}
</code></pre>

Document type

{% code overflow="wrap" %}
```
文档类型声明是HTML和XML文档的重要组成部分，用于指定文档的类型和版本，影响浏览器的解析模式和渲染行为
<!DOCTYPE html>
这是HTML5的文档类型声明，是最常用的形式。
```
{% endcode %}

href和src的区别

```
<link rel="stylesheet" type="text/css" href="styles.css">
用途：
href 主要用于指定链接和外部资源的 URL。
src 主要用于指定嵌入资源的 URL。
行为：
href 通常用于导航和引用外部资源，用户点击链接后会跳转到目标 URL。
src 用于直接加载和显示资源，如图像、脚本、视频等。
加载时机：
href 指定的资源通常在用户触发事件（如点击链接）时加载。
src 指定的资源通常在页面加载时立即加载。
```

Dom 和 bom



onload 和 DOMContentLoaded

```
onload 事件在页面的所有资源（包括图片、脚本、样式表等）完全加载完毕后触发
DOMContentLoaded在初始 HTML 文档加载和解析完成后触发
```

垂直margin失效问题的解决办法

```
1.父元素有Overflow: hidden
2.父元素有border
3.父元素position设置absolute或者fixed，或者float浮动，脱离文档输出流
4.父元素Display: inline-block

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Example</title>
    <style>
        .parent {
            margin-top: 200px;
            width: 200px;
            height: 200px;
            background: red;
            display: inline-block;
        }

        .child {
            margin-top: 50px;
            width: 100px;
            height: 100px;
            background: black;
        }
    </style>
</head>
<body>
    <div class="parent">
        <div class="child">Box-2</div>
    </div>
</body>
</html>
```

浮动元素会导致父元素高度塌陷

```
浮动布局中，父元素的内容高度默认是被子元素撑开的，但子元素浮动后，便脱离了文档流，因此无法撑起父元素，从而导致父元素高度丢失
1.父容器设置固定高度
2.最后一个浮动子元素的后面，加上一个任意块级元素应用clear清除浮动样式
3.父元素应用overflow:hidden样式，在IE6下需要用zoom:1样式做兼容
4.用伪元素动态的在父元素的最后输出一个空内容，在空内容上应用清除浮动样式

```
