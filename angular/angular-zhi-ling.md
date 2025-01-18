# Angular 指令

Angular 指令是 Angular 框架中的一个重要概念，它们允许你将行为附加到 DOM 元素上。指令可以用来操作 DOM、监听事件、响应数据变化等。Angular 中主要有三种类型的指令：

## **1. 组件（Component）**

* **定义**: 组件是带有模板的指令，用于定义用户界面。
* **特点**:
  * 有模板（`template` 或 `templateUrl`）。
  * 结合 HTML 和业务逻辑。
  * 是 Angular 应用的主要构成部分。
* **示例**:

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-example', // 使用方式 <app-example></app-example>
  template: `<h1>Hello, Angular!</h1>`
})
export class ExampleComponent {}
```

## **2.** 属性型指令（Attribute Directives）：

\
用于改变元素的外观或行为。\
通常以 @Directive 装饰器来定义，并通过属性的形式应用到元素上。\
例如，内置的 ngClass 和 ngStyle 指令。

```javascript

// highlight.directive.ts
import { Directive, ElementRef, HostListener } from '@angular/core';

@Directive({
  selector: '[appHighlight]'
})
export class HighlightDirective {
  constructor(private el: ElementRef) {}

  @HostListener('mouseenter') onMouseEnter() {
    this.highlight('yellow');
  }

  @HostListener('mouseleave') onMouseLeave() {
    this.highlight(null);
  }

  private highlight(color: string) {
    this.el.nativeElement.style.backgroundColor = color;
  }
}

使用： 
<div appHighlight>
  Hover over me to see the effect.
</div>
```

## 3.结构型指令（Structural Directives）：

\
用于在模板中添加或移除 DOM 元素。\
通常以 \* 符号作为前缀，表示它们会影响宿主元素及其子元素。\
例如，内置的 \*ngIf 和 \*ngFor 指令。

```javascript
@Directive({
  selector: '[gdFeatureCheck]'
})
export class FeatureCheckDirective implements OnInit {
  @Input('gdFeatureCheck') featureName: FeatureEnrollmentName | FeatureEnrollmentName[] | any;

  ngOnInit(): void {
  if (
    !this.featureName ||
    this.featureName.length === 0 ||
    this.featureName === '..'
  ) {
    this.viewContainer.clear();
  } else {
    let displayContainer = false;
    if (this.featureName instanceof Array) {
      for (const tempFeature of this.featureName as FeatureEnrollmentName[]) {
        // Further logic to check feature enrollment status
      }
    }
  }
}
}

使用

说明这是一个结构型指令
*gdFeatureCheck=’featureName’ 

```

### 指令类型

1. gdFeatureCheck：用于传递静态值，不涉及动态计算。
2. \[gdFeatureCheck]：用于传递动态值或表达式，会触发变更检测。
3. \*gdFeatureCheck：用于结构型指令，影响 DOM 结构，可以传递静态值或动态值。\


## 获取子组件的实例

使用 @ViewChild 获取单个子组件实例

使用 @ViewChildren 获取多个子组件实例

使用 @ContentChild 和 @ContentChildren 获取内容投影的子组件实例

## @Optional() @SkipSelf()

用于构造函数参数的参数装饰器

Optional

将参数标记为可选依赖项。如果找不到依赖关系，DI框架将提供null。

SkipSelf

它告诉DI框架从父注入器开始依赖项解析。分辨率在注射器层次结构中向上工作，因此不会检查本地注射器的提供者。
