---
description: 装饰器（Decorators）是一种元编程工具，用于修改或注解类、方法、属性和参数
---

# 装饰器 decorator

### 类装饰器

1. @Component\
   用于定义 Angular 组件。组件是 Angular 应用的基本构建块，包含模板、样式和类。
2. @NgModule\
   定义模块。
3. @directive\
   用于定义指令。指令用于在模板中添加行为。

```javascript
import { Directive, ElementRef, Renderer2, HostListener } from '@angular/core';

@Directive({
  selector: '[appHighlight]'
})
export class HighlightDirective {
  constructor(private el: ElementRef, private renderer: Renderer2) {}

  @HostListener('mouseenter') onMouseEnter() {
    this.renderer.setStyle(this.el.nativeElement, 'backgroundColor', 'yellow');
  }

  @HostListener('mouseleave') onMouseLeave() {
    this.renderer.removeStyle(this.el.nativeElement, 'backgroundColor');
  }
```

4. @Injectable\
   用于定义服务。服务通常用于提供可重用的功能，如数据访问、业务逻辑等。

```javascript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class DataService {
  getData() {
    return 'Some data';
  }
}

```

### 属性装饰器

1. @Input  / @output\
   定义输入/输出属性\
   允许父组件向子组件传递数据。/ 允许父组件向子组件传递数据。
2. @ViewChild\
   用于获取组件或指令的实例。

### &#x20;方法装饰器

1. @HostListener\
   用于在指令或组件中监听宿主元素的事件。
2.  @HostBinding\
    &#x20;绑定到宿主元素的属性\


    通常会与 `@HostListener` 结合使用来处理事件\
    这段代码实现了当鼠标悬停在宿主元素上时添加 `hovered` 类，并在鼠标移开时移除该类。

    ```javascript

    import { HostBinding, HostListener, Directive } from '@angular/core';

    @Directive({
      selector: '[appHover]'
    })
    export class HoverDirective {
      @HostBinding('class.hovered') isHovered = false;

      @HostListener('mouseenter')
      onMouseEnter() {
        this.isHovered = true;
      }

      @HostListener('mouseleave')
      onMouseLeave() {
        this.isHovered = false;
      }
    }
    ```

### 参数装饰器

参数装饰器可用于修饰类方法的参数。

### 怎么自定义装饰器

1. 装饰器的结构

装饰器函数接受一个或多个参数，具体取决于装饰器的类型：

* &#x20;类装饰器：接受一个参数，即类的构造函数。

```javascript
function LogClass(target: Function) {
  console.log(`Class ${target.name} is created.`);
}

@LogClass
export class MyService {
  constructor() {}
}

Class MyService is created.
```

* 属性装饰器：接受两个参数，即目标对象（类的原型或类本身）和属性名

```javascript
function ReadOnly(target: any, propertyKey: string) {
  Object.defineProperty(target, propertyKey, {
    writable: false,
  });
}

export class MyComponent {
  @ReadOnly
  name: string = 'Angular';
}

const component = new MyComponent();
component.name = 'React'; // 会抛出错误，因为属性是只读的
```

* 方法装饰器：接受三个参数，即目标对象（类的原型或类本身）、方法名和方法描述符。

```typescript
export function LogMethod(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
  const originalMethod = descriptor.value;
  descriptor.value = function (...args: any[]) {
    console.log(`Method ${propertyKey} is called with arguments:`, args);
    const result = originalMethod.apply(this, args);
    console.log(`Method ${propertyKey} returned:`, result);
    return result;
  };
  return descriptor;
}

@LogMethod
toggle(event:any) {
   console.log('Toggle event:', event);
}

Method toggle is called with arguments: ['124']
select-form.component.ts:96 Toggle event: 124
log.ts:7 Method toggle returned: undefined
```

* 参数装饰器：接受三个参数，即目标对象（类的原型或类本身）、方法名或属性名和参数索引。

```javascript
function LogParameter(target: any, propertyKey: string, parameterIndex: number) {
  console.log(`Parameter in ${propertyKey} at index ${parameterIndex}`);
}

export class MyService {
  greet(@LogParameter name: string) {
    console.log(`Hello, ${name}!`);
  }
}

Parameter in greet at index 0
```



2. 装饰器的执行时机

装饰器在编译时被调用，而不是在运行时。这意味着装饰器可以用来修改类的定义，而不会影响运行时的性能。

3. 装饰器的返回值

· 类装饰器：可以返回一个新的构造函数，以替换原来的类。

· 方法装饰器：可以返回一个新的方法描述符，以替换原来的方法。

· 属性装饰器：通常不返回值，因为它们主要用于修改属性的行为。

· 参数装饰器：通常不返回值，因为它们主要用于添加元数据。

4. 属性描述符的结构

一个属性描述符通常具有以下属性：

* **`value`**: 属性的值。对于方法而言，这是实际的方法函数。
* **`writable`**: 如果为 `true`，则属性的值可以通过赋值操作改变；如果为 `false`，则不能。
* **`enumerable`**: 如果为 `true`，则属性会在遍历对象属性时出现（例如，在 `for...in` 循环中或通过 `Object.keys()` 方法）。如果为 `false`，则不会出现在这些遍历操作中。
* **`configurable`**: 如果为 `true`，则属性可以被删除，并且其属性描述符可以被更改。如果为 `false`，则不允许删除属性或更改它的属性描述符（除了将 `writable` 从 `true` 改为 `false`）。

对于访问器属性（即有 getter 或 setter 的属性），属性描述符还包含：

* **`get`**: 获取属性值的函数（即 getter）。如果没有 getter，则此属性为 `undefined`。
* **`set`**: 设置属性值的函数（即 setter）。如果没有 setter，则此属性为 `undefined`。







