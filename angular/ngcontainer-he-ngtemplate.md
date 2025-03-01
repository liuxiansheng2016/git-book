# 模板

## `ng-content`&#x20;

是 Angular 中的一个投影标签，用于将父组件中的内容投影到子组件的指定位置。它可以实现内容的复用和自定义，使得子组件可以灵活地展示不同的内容。例如：\
收起typescript

```
// 子组件
import { Component } from '@angular/core';

@Component({
  selector: 'app-child',
  template: `
    <div>
      <h2>Child Component</h2>
      <ng-content></ng-content>
    </div>
  `
})
export class ChildComponent {}

// 父组件模板
<app-child>
  <p>This is content from the parent component.</p>
</app-child>
```

\
在上述例子中，父组件中的 `<p>` 标签内容会被投影到子组件的 `ng-content` 位置。

## ng-container&#x20;

`ng-container` 是一个逻辑容器，它本身不会在 DOM 中创建任何元素，主要用于分组和应用指令，而不会引入额外的 HTML 标签。例如，当需要在不引入额外元素的情况下使用 `*ngIf` 和 `*ngFor` 指令时，可以使用 `ng-container`：

```
<ng-container *ngFor="let item of items">
  <p *ngIf="item.isVisible">{{ item.name }}</p>
</ng-container>
```

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

## 获取子组件的实例

使用 @ViewChild 获取单个子组件实例

使用 @ViewChildren 获取多个子组件实例

使用 @ContentChild 和 @ContentChildren 获取内容投影的子组件实例

### **ContentChild**：

* 用于访问通过 `<ng-content>` 投影进来的子组件或元素。

```
import { Component, ContentChild, AfterContentInit } from '@angular/core';
import { ChildComponent } from './child.component';

@Component({
  selector: 'app-parent',
  template: `
    <ng-content></ng-content>
  `
})
export class ParentComponent implements AfterContentInit {
  @ContentChild(ChildComponent) child: ChildComponent;

  ngAfterContentInit() {
    console.log(this.child);
  }
}

```

### **ViewChild**：

用于访问组件模板中的子组件或元素。

```
import { Component, ViewChild, AfterViewInit } from '@angular/core';
import { ChildComponent } from './child.component';

@Component({
  selector: 'app-parent',
  template: `
    <app-child></app-child>
  `
})
export class ParentComponent implements AfterViewInit {
  @ViewChild(ChildComponent) child: ChildComponent;

  ngAfterViewInit() {
    console.log(this.child);
  }
}
```
