# 渲染流程

#### **1. 解析 HTML，构建 DOM 树**

* **浏览器读取 HTML**，将其解析为 **DOM（Document Object Model）**。
* DOM 是 **树状结构**，每个 HTML 标签都是一个 **节点（Node）**。

```html
<body>
    <h1>Hello</h1>
    <p>World</p>
</body>
```

解析后生成的 DOM 结构：

```
├── <html>
    ├── <body>
        ├── <h1>Hello</h1>
        ├── <p>World</p>
```

***

#### **📌 2. 解析 CSS，构建 CSSOM 树**

* **浏览器解析 CSS**，构建 **CSSOM（CSS Object Model）**。
* CSSOM 也是 **树状结构**，用于存储所有的 CSS 规则。

```css
h1 { color: red; }
p { font-size: 16px; }
```

解析后生成的 CSSOM 结构：

```
CSSOM
├── h1 → color: red
├── p → font-size: 16px
```

***

#### **📌 3. 生成 Render Tree**

* **将 DOM 和 CSSOM 结合**，生成 **Render Tree（渲染树）**。
* **Render Tree 只包含可见元素**（`display: none` 的元素不会出现在渲染树中）。

```html
<h1>Hello</h1>
<p style="display: none;">World</p>
```

Render Tree 结构：

```
Render Tree
├── <h1> → color: red
```

***

#### **📌 4. 计算布局（Layout / Reflow）**

在布局（Layout）阶段，浏览器根据 **渲染树** 计算每个元素的 **确切位置和大小**。

📌 **关键点：**

* 影响布局的属性：
  * `width`, `height`
  * `position`, `top`, `left`, `right`, `bottom`
  * `display`, `flexbox`, `grid`

***

#### **📌 5. 绘制（Painting）+ 光栅化（Rasterization）**

* **绘制（Painting）：**
  * 将 Render Tree 的样式信息转换成 **绘制指令**。
  * 例如：“在 (0,0) 位置绘制一个红色的 `h1`”。
* **光栅化（Rasterization）：**
  * **将矢量图形转换为像素（Bitmap）**，生成**纹理（Textures）**。
  * **使用 GPU 进行加速**，提升渲染速度。
  * 例如，`box-shadow` 和 `border-radius` 需要更多计算。

***

#### **📌 6. 合成（Compositing）+ 显示到屏幕**

* **浏览器将页面拆分成多个图层（Layers）。**
* **合成线程（Compositor Thread）** 负责将这些图层 **合成**，并交给 GPU 处理。
* **GPU 负责最终绘制到屏幕上（Frame Buffer）**。

***

### **📌 渲染优化技巧**

✅ **避免不必要的 Reflow**

* 避免频繁修改 `width`、`height`，可以使用 `transform` 代替 `top/left`。

✅ **避免不必要的重绘（Repaint）**

* `background-color` 变化会导致 Repaint，而 `transform: translateX()` 不会。

✅ **利用 GPU 加速**

* `will-change: transform;` 让浏览器提前优化元素的绘制。

✅ **减少 DOM 操作**

* **避免频繁更新 DOM**，可以使用 **虚拟 DOM（Virtual DOM）** 来减少不必要的 Reflow。
