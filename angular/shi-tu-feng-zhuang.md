---
description: 用于控制组件样式的可见性和作用域
---

# 视图封装



## Angular 提供了三种视图封装模式，

_**1.1 Emulated（模拟）**_

这是默认的视图封装模式。在这种模式下，Angular 会模拟 Shadow DOM 的行为，通过为组件的样式添加唯一的选择器前缀，确保组件的样式不会影响其他组件。

_**1.2 None（无）**_

在这种模式下，组件的样式将直接应用于全局样式表，没有任何作用域限制。这意味着组件的样式可能会与其他组件的样式发生冲突。

**1.3 ShadowDom（Shadow DOM）**

在这种模式下，组件的样式将完全封装在 Shadow DOM 中，确保组件的样式不会影响其他组件，也不会被其他组件的样式所影响。这种模式需要浏览器支持 Shadow DOM。\
\
::host 用于选择组件的宿主元素，即组件的最外层标签。

```
@Component({
  selector: 'app-my-component',
  template: `<p>这是一个没有封装模式的组件。</p>`,
  styles: [`p { color: blue; }`],
  encapsulation: ViewEncapsulation.None
})
export class MyComponentWithoutEncapsulation {}
```

\
\
