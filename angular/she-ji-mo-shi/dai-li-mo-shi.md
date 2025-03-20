# 代理模式

当你想要对一个业务类进行一些横切关注点（cross-cutting concerns）的增强而不直接修改原类代码时，\*\*代理模式（Proxy Pattern）\*\*是一个非常合适的选择。代理模式允许你提供一个替代对象来控制对原对象的访问，从而可以在请求到达原对象之前或之后执行额外的操作。

#### 代理模式的应用场景

* **增加请求与响应的日志**：可以通过代理在方法调用前后记录日志。
* **增加权限校验**：可以在实际方法调用前通过代理检查用户权限。
* **增加远程请求对象封装**：可以使用代理将本地接口调用转换为远程服务调用，而对外保持接口的一致性。



在 JavaScript/TypeScript 或者 Node.js 环境中实现代理模式有几种常见的方式：

**动态代理**

1.  **ES6 Proxy 对象**：JavaScript 提供了内置的 `Proxy` 构造函数，可以用来创建一个代理对象，拦截并自定义基本操作（如属性查找、赋值、枚举、函数调用等）。

    ```javascript
    const handler = {
      get: function(target, name) {
        console.log(`Getting ${name}`);
        return target[name];
      },
      apply: function(target, thisArg, argumentsList) {
        console.log(`Calling ${target.name} with`, argumentsList);
        return target.apply(thisArg, argumentsList);
      }
    };

    const target = {
      message: 'Hello, world!',
      greet: function(name) { return `${this.message}, ${name}`; }
    };

    const proxy = new Proxy(target, handler);

    console.log(proxy.greet('Alice')); // 输出日志并执行原始方法
    ```
2. **使用库如 `node-proxy`**：对于更复杂的需求，可能需要使用第三方库提供的功能更强的代理机制。

**静态代理**

静态代理是指手动创建一个代理类来包装目标对象，适用于已知需要增强的功能。

```javascript
class BusinessClass {
  doSomething() {
    console.log("Doing something...");
  }
}

class BusinessClassProxy {
  constructor(businessClass) {
    this.businessClass = businessClass;
  }

  doSomething() {
    console.log("Logging before method call.");
    // 权限校验逻辑
    if (this.checkPermission()) {
      this.businessClass.doSomething();
    } else {
      console.log("Permission denied");
    }
    console.log("Logging after method call.");
  }

  checkPermission() {
    // 模拟权限校验
    return true;
  }
}

const businessClass = new BusinessClass();
const proxy = new BusinessClassProxy(businessClass);
proxy.doSomething();
```

#### 总结

通过代理模式，你可以有效地分离横切关注点，避免在核心业务逻辑中混入诸如日志记录、权限校验等功能。这样不仅使代码更加清晰和易于维护，而且增强了灵活性，比如可以轻松地添加新的横切功能或者移除不再需要的功能。此外，这种做法遵循了面向切面编程（AOP）的原则，有助于提高软件设计的质量。
