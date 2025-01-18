# ng-container 和ng-template

## ng-container&#x20;

是 Angular 提供的一个特殊的容器标签，它允许你在模板中创建逻辑分组而不向最终的 DOM 添加额外的节点。这意味着 ng-container 在渲染时不会出现在页面上，也不会影响样式或布局。

ng-container 还可以与 ngTemplateOutlet 结合使用来递归调用模板，这有助于减少不必要的 DOM 元素嵌套，并且能够更灵活地传递参数给子模板

## ng-template

另一方面，ng-template 是一个用于定义模板片段的 Angular 指令。这些模板片段本身并不会直接显示在页面上，而是作为模板的一部分被引用和重用。ng-template 的内容只有在通过特定的指令（如 \*ngIf、\*ngFor 或 ngTemplateOutlet）显式渲染时才会出现在视图中

```html
<ng-template #loading>
  <div class="waiting-wrap">
    <div class="spinner">loading...</div>
  </div>
</ng-template>

<!-- 使用ngTemplateOutlet将模板插入到DOM -->
<ng-container *ngTemplateOutlet="loading"></ng-container>

```

ng-template 可以用来创建复杂的条件渲染逻辑，比如根据上下文的不同显示不同部分的内容。通过 context 参数，还可以向模板传递数据，使模板变得更加动态和交互性强

```html
<ng-template #introduce let-data>
  <p>Name: {{ data.name }}, Description: {{ data.description }}</p>
</ng-template>

<div>
  <ng-container *ngTemplateOutlet="introduce; context: {data: partyA}"></ng-container>
  <ng-container *ngTemplateOutlet="introduce; context: {data: partyB}"></ng-container>
</div>

```

## ngTemplateOutlet

ngTemplateOutlet 是 Angular 框架中的一个内置结构指令，<mark style="color:red;">它允许开发者动态地插入由 ng-template 定义的模板内容</mark>。这个功能特别有用，尤其是在需要创建高度可定制或复用的组件时。通过 ngTemplateOutlet，你可以将一个提前准备好的 TemplateRef 插入到页面指定的位置，并且可以传递上下文数据给该模板以实现更加灵活的内容展示

在父组件中定义一个模板，并在子组件中渲染它：

```html
<!-- parent.component.html -->
<app-child>
  <ng-template #customContent let-item="item">
    <p>Custom content for item: {{ item }}</p>
  </ng-template>
</app-child>

```

```html
<!-- child.component.html -->
<div *ngIf="template; else defaultContent">
  <ng-container [ngTemplateOutlet]="template" [ngTemplateOutletContext]="{ $implicit: 'someItem' }"></ng-container>
</div>

<ng-template #defaultContent>
  <p>Fallback content when no template is provided.</p>
</ng-template>

```

parent.component.html 中定义了一个名为 customContent 的模板，并通过 \<app-child> 标签将其传递给了子组件。子组件接收到这个模板后，在其自己的模板中使用了 ngTemplateOutlet 来渲染父组件传递过来的内容。同时，还设置了上下文对象 { $implicit: 'someItem' }，使得父组件定义的模板能够获取到传递的数据 item。

<mark style="color:red;">$implicit 等于let-item</mark>\ <mark style="color:red;">$implicit 是一个特殊的上下文变量名称，它用于 ngTemplateOutlet 和其他结构指令中，当定义模板引用变量时，可以用来表示默认传递的数据值</mark>

除了 $implicit，你也可以添加其他键值对到上下文对象中，以便在模板中使用不同的局部变量名进行绑定。

```html
<!-- child.component.html -->
<ng-container [ngTemplateOutlet]="template" [ngTemplateOutletContext]="{ item: 'anItem', anotherKey: 'anotherValue' }"></ng-container>

然后在模板中就可以像这样访问:

<!-- parent.component.html -->
<ng-template #customContent let-item="item" let-another="anotherKey">
  <p>Item: {{ item }} and Another Value: {{ another }}</p>
</ng-template>

```
