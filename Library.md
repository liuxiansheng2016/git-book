## D3

## angular JS

## GOJS

## Aurelia

Aurelia: 用新一代的 ES 技术向前兼容。没有外部依赖，高度模块化的开发。更强大的双向绑定，可拓展的 HTML。

### 生命周期

### DOM 事件
- **Trigger**: 将事件处理程序直接附加到元素。触发事件时，将调用表达式。
- **Delegate**: 将单个处理程序附加到文档，该文档处理冒泡阶段中指定类型的所有事件。
- **Capture**: 将单个事件处理程序附加到文档（或最近的影子 DOM 边界），该处理程序处理捕获阶段中指定类型的所有事件。
- **Call**: 在开发自定义元素或自定义属性时，您可能会遇到一种情况，即您拥有 `@bindable` 需要引用函数的属性。使用 `call` 绑定命令声明函数并将函数传递给 `bindable` 属性。该 `call` 命令优于 `bind` 命令，因为它将在正确的上下文中执行该函数，确保 `this` 是您期望的。

### Self
```html
mousedown.delegate='onMouseDown($event) & self'
```
使用 self 绑定行为，您可以指定事件处理程序仅响应附加侦听器的目标，而不是其后代。

### 符号
- **&** 代表一个 BindingBehavior（而不是按位 AND）
- **|** 代表一个 ValueConverter（而不是按位 OR）

### Model
当我们使用 `model.bind` 时，内容被传递给 `activate`。要定义输入的“值”，请绑定输入的 `model` 属性：`model.bind="product.id"`。

### Referencing Elements
- `element.ref="expression"`: create a reference to the DOM element (same as `ref="expression`").
- `attribute-name.ref="expression"`: create a reference to a custom attribute's view-model.
- `view-model.ref="expression"`: create a reference to a custom element's view-model.
- `view.ref="expression"`: create a reference to a custom element's view instance (not an HTML Element).
- `controller.ref="expression"`: create a reference to a custom element's controller instance.

### ValueConverter
1. 创建 value converter。用 `toView` 方法将 value 应用到视图上。
   ```javascript
   import numeral from 'numeral';

   export class CurrencyFormatValueConverter {
     toView(value) {
       return numeral(value).format('($0,0.00)');
     }
   }
   ```
2. 在视图里面 `require` the converters。
3. 最后，我们在绑定中使用管道 `|` 语法应用转换器。
   ```html
   ${currentDate | dateFormat} <br>
   ${netWorth | currencyFormat}
   ```

### ToView 的参数
1. Convert 的 value 或者 repeat 的数组。
2. 其它参数 `partFilter: 'name' : modelSearch.value` 或者 `{propertyName: 'open_issues', direction: 'descending'}`。

### FromView
当一个全局参数不能被 Aurelia 观察时，可以用 `signalBindings`。

### Computer Property
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

### Binding Behaviors

#### updateTrigger
Update trigger allows you to override the input events that cause the element's value to be written to the view-model. The default events are change and input.

Here's how you would tell the binding to only update the model on blur:
```html
<input value.bind="firstName & updateTrigger:'blur'">
```
Update with multiple events:
```html
<input value.bind="firstName & updateTrigger:'blur':'paste'">
```

### Signal
The signal binding behavior enables you to "signal" the binding to refresh. This is especially useful when a binding result is impacted by global changes that are outside of the observation path.

### Observing Collections
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

### 依赖注入
假设我们 `CustomerEditScreen` 需要 `Customer` 通过 Web 从 Web 服务加载实体。我们不希望将 AJAX 实现的所有细节都放在我们的 `CustomerEditScreen` 类中。相反，我们希望将其分解为 `CustomerService` 类，`CustomerEditScreen` 或任何其他类在需要加载时可以使用的类 `Customer`。Aurelia 的依赖注入容器允许您通过声明 `CustomerEditScreen` 需要 `CustomerService` 在创建时注入来实现此目的。

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


