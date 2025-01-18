---
description: CDK 不仅包括一些可以直接使用的 UI 组件，更重要的是它提供了许多底层服务和工具，
---

# Angular CDK

Angular CDK (Component Dev Kit) 是 Angular 团队提供的一套工具和构建块，旨在帮助开发者更轻松地创建高质量的 Angular 组件。CDK 不是一个独立的库，而是作为 @angular/cdk 包的一部分发布，它提供了许多底层功能，这些功能在 Angular Material 中被广泛使用，但也可以独立于 Material 使用。



#### 主要特点

基础组件：

1\. CDK 提供了一些基础组件，如 Overlay、Portal、Scrolling 等，这些组件可以用来构建复杂的 UI 组件。

* cdk/overlay：用于处理弹出层、对话框等覆盖物相关的逻辑。
* cdk/drag-drop：支持拖拽交互。
* cdk/a11y：提供无障碍辅助功能。
* cdk/layout：提供布局相关服务，例如断点检测

2\. 例如，Overlay 可以用来创建浮动面板（如对话框、弹出菜单等），而 Portal 可以用来将内容动态插入到 DOM 中。

```javascript
import { Component, Injectable, Injector, TemplateRef } from '@angular/core';
import { Overlay, OverlayRef, OverlayConfig, PositionStrategy } from '@angular/cdk/overlay';
import { ComponentPortal } from '@angular/cdk/portal';

@Injectable()
export class MenuService {
  constructor(private overlay: Overlay, private injector: Injector) {}

  openMenu(template: TemplateRef<any>, origin: HTMLElement) {
    const positionStrategy = this.overlay.position()
      .flexibleConnectedTo(origin)
      .withPositions([
        { originX: 'end', originY: 'bottom', overlayX: 'start', overlayY: 'top' },
        { originX: 'start', originY: 'bottom', overlayX: 'end', overlayY: 'top' }
      ]);

    const overlayConfig = new OverlayConfig({
      hasBackdrop: true,
      backdropClass: 'custom-backdrop-class',
      panelClass: 'custom-panel-class',
      positionStrategy,
      scrollStrategy: this.overlay.scrollStrategies.reposition()
    });

    const overlayRef: OverlayRef = this.overlay.create(overlayConfig);
    const portal = new ComponentPortal(MenuComponent, null, this.injector);
    overlayRef.attach(portal);

    overlayRef.backdropClick().subscribe(() => overlayRef.detach());
  }
}

```

