# Library



### D3

#### 选择与操作 DOM 元素

* **`d3.select()`**：选择文档中匹配指定选择器的第一个元素，返回一个包含该元素的选择集。
* **`d3.selectAll()`**：选择文档中匹配指定选择器的所有元素，返回一个包含这些元素的选择集。
* **`selection.attr()`**：设置或获取所选元素的属性。如`selection.attr('width', 100)`可设置元素宽度为 100。
* **`selection.style()`**：设置或获取所选元素的 CSS 样式。例如`selection.style('fill', 'red')`将元素填充颜色设为红色。

#### 数据处理

* **`d3.scaleLinear()`**：创建一个线性比例尺，用于将数据域映射到目标范围。如`const scale = d3.scaleLinear().domain([0, 100]).range([0, 500])`。
* **`d3.scaleOrdinal()`**：创建一个序数比例尺，用于将离散的输入值映射到离散的输出值。
* **`d3.shuffle()`**：打乱数组中元素的顺序。

#### 图表绘制

* **`d3.axisBottom()`**：创建一个底部坐标轴生成器。结合比例尺使用，如`const xAxis = d3.axisBottom(xScale)`。
* **`d3.axisLeft()`**：创建一个左侧坐标轴生成器。
* **`d3.line()`**：创建一个折线生成器函数，用于生成折线图的路径数据。可设置`x`和`y`访问器函数，如`const line = d3.line().x(d => xScale(d.x)).y(d => yScale(d.y))`。
* **`d3.arc()`**：创建一个弧生成器函数，用于绘制饼图、环形图等。

#### 数据绑定

* **`selection.data()`**：将数据绑定到选择的元素上，返回一个更新选择集。
* **`selection.enter()`**：返回一个包含新数据项对应的占位元素的选择集，通常用于创建新元素。
* **`selection.exit()`**：返回一个包含与已删除数据项对应的元素的选择集，用于移除元素。

#### 交互

* **`selection.on()`**：为元素添加事件监听器。如`selection.on('click', function() { console.log('Clicked!'); })`。
* **`d3.event`**：包含当前正在处理的事件的相关信息，在事件处理函数中可获取鼠标位置等信息。

<figure><img src=".gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

### GoJS

#### GoJS 特性

* **性能**：<mark style="color:red;">`<canvas>`</mark> <mark style="color:red;"></mark><mark style="color:red;">提供更高的绘图性能，尤其适合处理大量图形和复杂动画</mark>。
* **内存管理**：`<canvas>` 在处理大量数据和复杂图表时有更好的内存管理能力，因为所有的绘图操作都在内存中完成，然后一次性绘制到屏幕上。
* **灵活性**：提供了更多的低级绘图控制，允许实现更复杂的绘图效果和优化。

#### 基本使用步骤（GoJS）

1. **初始化 Diagram**：
   * 使用 `go.GraphObject.make` 函数创建 GoJS 对象。
   * 设置图表的容器，并配置默认布局为树形布局(TreeLayout)。
   * 允许用户删除和复制节点。
2. **定义节点模板**：
   * 创建节点并设置形状、填充颜色、边框颜色。
   * 添加文本块，并绑定文本到节点的 key 属性。
3. **定义链接模板**：
   * 创建链接并设置路由方式（如正交路由Orthogonal），设置拐角半径。
   * 定义链接的线条宽度和颜色。
4. **添加初始数据**：
   * 创建树形模型(TreeModel)，并添加初始节点数据，包括键(key)、颜色(color)、父节点(parent)等信息。

#### 功能特性

* **拖放操作**：支持节点的拖动。
* **动态更新**：支持运行时动态添加、删除和修改节点及链接。
* **自定义样式**：可自定义节点和链接的样式。
* **动画效果**：支持添加动画效果，使图表更加生动。
* **工具提示**：可为节点和链接添加工具提示。
* **事件处理**：支持处理各种事件，如点击、双击、鼠标悬停等。

通过上述内容可以看出，选择使用哪种技术取决于具体的应用场景和需求。对于需要高效处理大量数据和复杂动画的应用，GoJS 可能是更好的选择；而对于需要高度定制化的复杂图表，D3.js 则更为合适。

```
// 初始化 GoJS Diagram
const myDiagram = new go.Diagram("myDiagramDiv", {
    // 设置默认布局
    layout: new go.TreeLayout(),
    // 允许用户编辑图表
    allowDelete: true,
    allowCopy: true
});

// 定义节点模板
myDiagram.nodeTemplate = new go.Node("Auto",
    new go.Shape("RoundedRectangle",
        { fill: "lightblue", stroke: "black" },
        new go.Binding("figure", "fig") // 将 fig 属性绑定到 figure 属性
    ),
    new go.TextBlock(
        { margin: 8 },
        new go.Binding("text", "key")
    )
);

// 定义链接模板
myDiagram.linkTemplate = new go.Link(
    { routing: go.Link.Orthogonal, corner: 5 },
    new go.Shape({ strokeWidth: 2, stroke: "black" })
);

// 添加一些初始数据
myDiagram.model = new go.TreeModel([
    { key: "A", fig: "Circle", color: "lightblue" },
    { key: "B", fig: "Rectangle", color: "lightgreen", parent: "A" },
    { key: "C", fig: "Ellipse", color: "lightyellow", parent: "A" },
    { key: "D", fig: "Diamond", color: "lightcoral", parent: "B" },
    { key: "E", fig: "Triangle", color: "lightpink", parent: "B" },
    { key: "F", fig: "Hexagon", color: "lightcyan", parent: "C" },
    { key: "G", fig: "Pentagon", color: "lightgoldenrodyellow", parent: "C" }
]);
```

### 可视化：SVG 与 Canvas

**SVG (Scalable Vector Graphics)**

* **基于 XML**：SVG 是一种基于 XML 的矢量图形格式，这意味着它使用文本描述图像。
* **分辨率独立**：由于其矢量性质，SVG 图形可以无损缩放，非常适合高分辨率屏幕和打印。
* **DOM 访问**：每个 SVG 元素都是 DOM 节点，允许单独访问和操作，便于动态更新和交互。
* **适合复杂图形**：适用于创建复杂的、交互性强的图表和图形。

**Canvas**

* **基于像素**：Canvas 是一个位图画布，通过 JavaScript 绘制图形，所有绘图都是基于像素的操作。
* **分辨率依赖**：Canvas 图形在放大时可能会失真，不适合高分辨率屏幕和打印。
* **绘图上下文**：Canvas 提供了一个 2D 或 3D 绘图上下文，所有绘图操作都通过这个上下文进行。

#### Echarts 和 D3.js 对比

* **Echarts**
  * 更加简单易用，适合快速实现数据可视化需求。
* **D3.js**
  * 更加灵活，容易定制，但学习曲线较陡峭。主要用于操作 SVG，但也能用于 Canvas。
  * 适合创建复杂图表如散点图、热力图等。

#### D3.js 与 GoJS 对比

* **D3.js**
  * 主要使用 SVG 进行绘图，适合创建各种复杂的图表。
  * 需要开发者对底层绘图有更深入的理解。
* **GoJS**
  * <mark style="color:red;">主要使用</mark> <mark style="color:red;"></mark><mark style="color:red;">`<canvas>`</mark> <mark style="color:red;"></mark><mark style="color:red;">进行绘图，提供高效的绘图性能，特别适合处理大量图形和复杂动画。</mark>
  * 提供了丰富的内置功能和高层次 API，<mark style="color:red;">使得开发复杂的图表（如流程图、组织结构图）</mark>变得简单快捷。



### Aurelia

Aurelia: 用新一代的 ES 技术向前兼容。没有外部依赖，高度模块化的开发。更强大的双向绑定，可拓展的 HTML。

#### 生命周期

#### DOM 事件

* **Trigger**: 将事件处理程序直接附加到元素。触发事件时，将调用表达式。
* **Delegate**: 将单个处理程序附加到文档，该文档处理冒泡阶段中指定类型的所有事件。
* **Capture**: 将单个事件处理程序附加到文档（或最近的影子 DOM 边界），该处理程序处理捕获阶段中指定类型的所有事件。
* **Call**: 在开发自定义元素或自定义属性时，您可能会遇到一种情况，即您拥有 `@bindable` 需要引用函数的属性。使用 `call` 绑定命令声明函数并将函数传递给 `bindable` 属性。该 `call` 命令优于 `bind` 命令，因为它将在正确的上下文中执行该函数，确保 `this` 是您期望的。

#### Self

```html
mousedown.delegate='onMouseDown($event) & self'
```

使用 self 绑定行为，您可以指定事件处理程序仅响应附加侦听器的目标，而不是其后代。

#### 符号

* **&** 代表一个 BindingBehavior（而不是按位 AND）
* **|** 代表一个 ValueConverter（而不是按位 OR）

#### Model

当我们使用 `model.bind` 时，内容被传递给 `activate`。要定义输入的“值”，请绑定输入的 `model` 属性：`model.bind="product.id"`。

#### Referencing Elements

* `element.ref="expression"`: create a reference to the DOM element (same as `ref="expression`").
* `attribute-name.ref="expression"`: create a reference to a custom attribute's view-model.
* `view-model.ref="expression"`: create a reference to a custom element's view-model.
* `view.ref="expression"`: create a reference to a custom element's view instance (not an HTML Element).
* `controller.ref="expression"`: create a reference to a custom element's controller instance.

#### ValueConverter

1.  创建 value converter。用 `toView` 方法将 value 应用到视图上。

    ```javascript
    import numeral from 'numeral';

    export class CurrencyFormatValueConverter {
      toView(value) {
        return numeral(value).format('($0,0.00)');
      }
    }
    ```
2. 在视图里面 `require` the converters。
3.  最后，我们在绑定中使用管道 `|` 语法应用转换器。

    ```html
    ${currentDate | dateFormat} <br>
    ${netWorth | currencyFormat}
    ```

#### ToView 的参数

1. Convert 的 value 或者 repeat 的数组。
2. 其它参数 `partFilter: 'name' : modelSearch.value` 或者 `{propertyName: 'open_issues', direction: 'descending'}`。

#### FromView

当一个全局参数不能被 Aurelia 观察时，可以用 `signalBindings`。

#### Computer Property

当我们使用藏检查时会调用太多次，所以可以用到 `@computedFrom` decorator。

```javascript
import {computedFrom} from 'aurelia-framework';

export class Person {
  firstName: string = 'John';
  lastName: string = 'Doe';

  @computedFrom('firstName', 'lastName')
  get fullName(): string {
    return `${this.firstName} ${this.lastName}`;
  }
}
```

#### Binding Behaviors

**updateTrigger**

Update trigger allows you to override the input events that cause the element's value to be written to the view-model. The default events are change and input.

Here's how you would tell the binding to only update the model on blur:

```html
<input value.bind="firstName & updateTrigger:'blur'">
```

Update with multiple events:

```html
<input value.bind="firstName & updateTrigger:'blur':'paste'">
```

#### Signal

The signal binding behavior enables you to "signal" the binding to refresh. This is especially useful when a binding result is impacted by global changes that are outside of the observation path.

#### Observing Collections

Use the Collection Observer to observe changes to a collection. Collection types that can be observed are Array, Map, and Set. Create a subscription by providing the collection to observe and a callback function.

```javascript
import {BindingEngine, autoinject, ICollectionObserverSplice} from 'aurelia-framework';

@autoinject
export class App {
  myCollection: Array<string> = ["foo"];

  constructor(private bindingEngine: BindingEngine) {
    let subscription = this.bindingEngine.collectionObserver(this.myCollection)
      .subscribe(this.collectionChanged.bind(this));
  }

  collectionChanged(splices: Array<ICollectionObserverSplice<string>>) {
    // This will fire any time the collection is modified.
  }
}
```

#### 依赖注入

假设我们 `CustomerEditScreen` 需要 `Customer` 通过 Web 从 Web 服务加载实体。我们不希望将 AJAX 实现的所有细节都放在我们的 `CustomerEditScreen` 类中。相反，我们希望将其分解为 `CustomerService` 类，`CustomerEditScreen` 或任何其他类在需要加载时可以使用的类 `Customer`。Aurelia 的依赖注入容器允许您通过声明 `CustomerEditScreen` 需要 `CustomerService` 在创建时注入来实现此目的。

### Jquery

Jquery是一个JavaScript库，有些时候也称为JS框架，目的就是简化原生JS开发，集成了JS的常用功能，类似的框架还有ExtJS，Dojo，Prototype，YUI。

核心函数（全局函数）是jQuery，和$是等价的，传入对应的参数，返回Jquery对象，用jquery对象才能调用jquery的API。

Jquery对象不能直接调用原生对象的任何API，例如innerHTML、click()、focus()。

Jquery对象并不是原生js对象，它是封装了原生对象并且附加了一些功能，产生的一个新的对象实例。

#### Jq的文档就绪事件

写法1：`$(document).ready(回调函数);`

写法2：`$(回调函数);`

window.onload和$(document).ready的区别：

* window.onload：只能注册一次，必须当前html的dom全部加载并且所有的外部资源（图片，音频，视频）全都加载完毕才会触发。
* $(document).ready：注册多次，仅仅html的dom全部加载完毕就会触发，原理是JS的document.onreadystatechange事件。

#### 选择器

* `$(selector)`：选择与给定选择器匹配的所有元素。
  * `$('#id')`：选择具有特定 ID 的元素。
  * `$('.class')`：选择具有特定类名的所有元素。
  * `$('element')`：选择指定类型的元素。
  * `$('parent > child')`：选择父元素下的直接子元素。

#### DOM 操作

* `append(content)`：向每个匹配的元素内部追加内容。
  * `prepend(content)`：向每个匹配的元素内部前置内容。
  * `html(content)`：设置或获取匹配元素的内容。
  * `text(content)`：设置或获取匹配元素的文本内容。
  * `attr(name, value)`：设置或获取属性值。
  * `addClass(className)`：添加类名。
  * `removeClass(className)`：移除类名。
  * `toggleClass(className)`：切换类名。

#### 事件处理

* `click(handler)`：绑定点击事件。
  * `hover(handlerIn, handlerOut)`：绑定鼠标进入和离开事件。
  * `on(event, selector, data, handler)`：绑定事件处理器。
  * `off(event, selector, handler)`：移除事件处理器。

#### 动画效果

* `show(duration, callback)`：显示隐藏的元素。
  * `hide(duration, callback)`：隐藏可见的元素。
  * `fadeIn(duration, callback)`：逐渐显示元素。
  * `fadeOut(duration, callback)`：逐渐隐藏元素。
  * `animate(properties, duration, easing, complete)`：自定义动画效果。

#### Ajax 支持

* `$.ajax(options)`：发送异步 HTTP 请求。
  * `$.get(url, data, success, dataType)`：发送 GET 请求。
  * `$.post(url, data, success, dataType)`：发送 POST 请求。

通过下标访问元素：`eq`返回jq对象，`get`返回js对象。

大多数的选择器语法都有对应的方法实现。

#### 操作属性

* `attr`：如果属性不存在，读取属性，返回undefined，适合非单一属性和自定义属性。
* `prop`：适合单一属性。

#### Jquery的循环遍历语句

* `$.each(对象或者数组,function(index, el) {});`
* `JQ对象.each(function(index, el) {});`

### Electron

#### 主要特性

**1. 跨平台支持**

Electron 支持 Windows、macOS 和 Linux，可以使用相同的代码库构建跨平台应用。

**2. Node.js 集成**

主进程可以直接使用 Node.js 的所有功能，包括文件系统操作、网络请求等。

**3. Chromium 渲染引擎**

渲染进程使用 Chromium 渲染引擎，支持现代 Web 技术，如 HTML5、CSS3 和 JavaScript。

**4. 丰富的 API**

Electron 提供了丰富的 API，用于创建和管理窗口、菜单、系统托盘图标、通知等。

#### win.loadURL 和 win.loadFile

开发阶段：

如果你正在开发一个基于 React、Angular 或其他前端框架的应用，通常会使用 win.loadURL 加载本地开发服务器上的内容。这样可以利用热重载和其他开发工具。

例如，React 应用通常使用 win.loadURL('http://localhost:3000')。

生产阶段：

当你打包应用并发布时，通常会使用 win.loadFile 加载本地文件。这样可以确保应用在没有网络连接的情况下也能正常运行。

例如，打包后的应用通常使用 win.loadFile(path.join(\_\_dirname, 'index.html'))。

#### Demo

#### 主进程 (main.js)

```javascript
const { app, BrowserWindow, ipcMain, dialog } = require('electron');
const path = require('path');

function createWindow() {
  const win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: path.join(__dirname, 'preload.js'),
      // 建议启用 contextIsolation 保证安全
      contextIsolation: true,
      nodeIntegration: false
    }
  });

  win.loadFile('index.html');
}

app.whenReady().then(createWindow);

// 监听渲染进程发来的 "show-dialog" 消息
ipcMain.on('show-dialog', async (event, message) => {
  // 显示一个消息对话框
  const result = await dialog.showMessageBox({
    type: 'info',
    title: '提示',
    message: message || '这是一个默认消息！',
    buttons: ['确定', '取消']
  });
  // 将结果发送回渲染进程，通道名称可以自定义
  event.reply('show-dialog-reply', result);
});

app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') app.quit();
});
```

***

#### 预加载脚本 (preload.js)

在 preload 脚本中，通过 contextBridge 将 ipcRenderer 的 send 和 receive 方法暴露给渲染进程，从而保证安全访问。

```javascript
const { contextBridge, ipcRenderer } = require('electron');

contextBridge.exposeInMainWorld('electronAPI', {
  // 向主进程发送消息
  send: (channel, data) => {
    // 可以设置允许发送的通道列表
    const validChannels = ['show-dialog'];
    if (validChannels.includes(channel)) {
      ipcRenderer.send(channel, data);
    }
  },
  // 接收主进程回复的消息
  receive: (channel, func) => {
    const validChannels = ['show-dialog-reply'];
    if (validChannels.includes(channel)) {
      // 这里使用事件订阅，确保 func 能够接收到消息
      ipcRenderer.on(channel, (event, ...args) => func(...args));
    }
  }
});
```

***

#### 渲染进程 (renderer.js)

在渲染进程中，通过全局暴露的 `electronAPI` 来发送消息和接收主进程回复。下面示例展示了如何点击按钮后，发送 `show-dialog` 消息，并在收到回复后在控制台打印结果。

```javascript
// 假设 index.html 中包含一个按钮，id="showDialogBtn"
const btn = document.getElementById('showDialogBtn');

btn.addEventListener('click', () => {
  // 向主进程发送 "show-dialog" 消息，同时可以传递一个消息字符串
  window.electronAPI.send('show-dialog', '你想显示对话框吗？');
});

// 监听主进程的回复
window.electronAPI.receive('show-dialog-reply', (result) => {
  console.log('对话框结果：', result);
  // 根据需要在页面上展示结果
});
```

***

#### index.html 示例

为了使示例完整，可以创建一个简单的 HTML 页面，包含一个按钮用于触发对话框：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>Electron IPC Demo</title>
</head>
<body>
  <h1>Electron IPC 示例</h1>
  <button id="showDialogBtn">显示对话框</button>
  <script src="renderer.js"></script>
</body>
</html>
```

***

#### 总结

* **主进程 (main.js)** 使用 `ipcMain.on('show-dialog', callback)` 监听渲染进程的消息，调用 `dialog.showMessageBox` 显示对话框，并通过 `event.reply` 将结果返回。
* **预加载脚本 (preload.js)** 使用 `contextBridge.exposeInMainWorld` 暴露 `send` 和 `receive` 方法，确保渲染进程只能访问允许的 IPC 通道。
* **渲染进程 (renderer.js)** 通过 `electronAPI.send` 发送消息，通过 `electronAPI.receive` 接收主进程返回的结果。

#### 主进程与渲染进程通信

**1. `ipcMain` 和 `ipcRenderer`**\
这是 Electron 中最常用的主进程与渲染进程通信的方式，`ipcMain` 用于主进程，`ipcRenderer` 用于渲染进程。

#### preload.js

当 contextIsolation 选项设置为 true 时，渲染进程和主进程之间的通信是隔离的。这提高了应用的安全性，防止潜在的 XSS 攻击。

这时候 ipcRenderer.send 不会生效

```
const { ipcRenderer } = require('electron');

document.getElementById('show-dialog').addEventListener('click', () => {
  ipcRenderer.send('show-dialog');
});

ipcRenderer.on('dialog-response', (event, response) => {
  console.log('Dialog response:', response);
  // 你可以在这里处理对话框的响应，比如更新UI
});

```

在 preload.js 中，使用 contextBridge 暴露一个方法，该方法使用 ipcRenderer.send 发送消息。

```
const { contextBridge, ipcRenderer } = require('electron');

contextBridge.exposeInMainWorld('electronAPI', {
  showDialog: () => ipcRenderer.send('show-dialog'),
  onDialogResponse: (callback) => ipcRenderer.on('dialog-response', callback),
});
```

#### electron-builder&#x20;

是一个非常强大的工具，用于打包和构建 Electron 应用。

```
{
  "name": "electron-demo",
  "version": "1.0.0",
  "description": "A simple Electron demo",
  "main": "main.js",
  "scripts": {
    "start": "electron .",
    "build": "electron-builder"
  },
  "build": {
    "appId": "com.example.electron-demo",
    "productName": "Electron Demo",
    "files": [
      "main.js",
      "index.html",
      "icon.png"
    ],
    "directories": {
      "output": "dist"
    },
    "win": {
      "target": "nsis",
      "icon": "icon.png"
    },
    "mac": {
      "target": "dmg",
      "icon": "icon.png"
    },
    "linux": {
      "target": "AppImage",
      "icon": "icon.png"
    }
  },
  "devDependencies": {
    "electron": "^22.0.0",
    "electron-builder": "^23.0.0"
  }
}

```

#### process.platform：

这是一个 Node.js 提供的属性，返回当前操作系统的平台名称。常见的值包括：

'darwin'：表示 macOS。

'win32'：表示 Windows。

'linux'：表示 Linux。

#### `ipcMain 和ipcRenderer`

ipcMain 和 ipcRenderer 是 Electron 中用于在主进程和渲染进程之间进行通信的两个重要模块。它们通过 Inter-Process Communication (IPC) 机制实现了进程间的双向通信。下面详细介绍这两个模块及其用法。

ipcMain 模块在主进程中使用，用于监听来自渲染进程的消息，并处理这些消息。

ipcRenderer 模块在渲染进程中使用，用于向主进程发送消息，并接收主进程的响应

#### ipcRenderer.invoke 和 ipcRenderer.send

ipcRenderer.send 是一个异步方法，用于从渲染进程向主进程发送消息。主进程可以通过 ipcMain.on 或 ipcMain.addListener 监听这些消息，并进行相应的处理。send 方法不等待主进程的响应，而是立即返回。

ipcRenderer.invoke 是一个同步方法（尽管它是异步的，但会等待主进程的响应），用于从渲染进程向主进程发送消息并等待主进程的响应。主进程可以通过 ipcMain.handle 处理这些请求，并返回结果。invoke 方法会等待主进程的响应，然后返回结果。

#### MessagePort&#x20;

MessagePort 的概念与浏览器中基本一致，它允许在不同的执行上下文（例如主进程与渲染进程、不同窗口、Web Worker 等）之间进行异步消息传

<pre><code>主进程 (main.js)
<strong>const { app, BrowserWindow, MessageChannelMain } = require('electron');
</strong>const path = require('path');

function createWindow() {
  const win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: path.join(__dirname, 'preload.js'),
      contextIsolation: true,  // 确保安全
      nodeIntegration: false
    }
  });

  win.loadFile('index.html');

  // 创建一个消息通道（MessageChannelMain）
  const { port1, port2 } = new MessageChannelMain();

  // 在主进程上监听 port1 的消息
  port1.on('message', (event) => {
    console.log('主进程收到消息：', event.data);
    // 回复消息给渲染进程
    port1.postMessage('Hello from Main Process!');
  });

  // 通过 webContents.postMessage 将 port2 发送给渲染进程
  win.webContents.postMessage('init-port', null, [port2]);
}

app.whenReady().then(createWindow);

app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') app.quit();
});

app.on('activate', () => {
  if (BrowserWindow.getAllWindows().length === 0) createWindow();
});

 //预加载脚本 (preload.js)
const { contextBridge, ipcRenderer } = require('electron');

ipcRenderer.on('init-port', (event) => {
  // event.ports 包含传递过来的 MessagePort 对象
  const port = event.ports[0];

  // 为端口设置监听器
  port.onmessage = (e) => {
    console.log('预加载脚本收到消息：', e.data);
  };

  // 暴露一个 API 给渲染进程
  contextBridge.exposeInMainWorld('electronPort', {
    send: (data) => port.postMessage(data),
    onMessage: (callback) => {
      port.onmessage = (e) => callback(e.data);
    }
  });
});

// 假设 index.html 中已经引入 renderer.js
document.getElementById('sendMessageBtn').addEventListener('click', () => {
  // 向主进程发送消息
  window.electronPort.send('Hello from Renderer Process!');
});

// 监听来自主进程的回复
window.electronPort.onMessage((message) => {
  console.log('渲染进程收到消息：', message);
  // 可以在页面上显示消息，例如：
  const output = document.getElementById('output');
  output.innerText = message;
});

</code></pre>

* **ipcRenderer**
  * 是 Electron 内置的模块，专门用于渲染进程与主进程之间的通信。
  * 提供了像 `send`、`on`、`invoke` 等方法，用于发送消息、监听回复以及进行远程过程调用（RPC）。
  * 适用于大多数主渲染进程间的通信场景。
* **MessageChannelMain**
  * 是基于标准 Web MessageChannel API 的 Electron 版本，专门在主进程中使用。
  * 通过 `MessageChannelMain` 可以创建一对互联的 MessagePort 对象，然后将其中一个端口传递到渲染进程，实现专用的双向通信通道。
  * 适用于需要更细粒度、独立通道通信的场景，可以将特定的通信逻辑隔离开来，且更符合 Web 标准。
