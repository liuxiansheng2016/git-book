# 设计模式



Refer to [url](https://zhuanlan.zhihu.com/p/672502223)

## 行为型设计模式

描述多个类或对象之间怎样==相互协作==共同完成单个对象都无法单独完成的任务

1. &#x20;**模版方法模式**

描述： 模板方法模式的核心思想是封装行为中的不变部分，同时允许可变部分通过子类来进行扩展。

在前端开发中，<mark style="color:red;">模板方法模式通常用于处理页面的渲染和事件处理</mark>。例如，我们可以定义一个基础的页面渲染算法，并在其中定义一些抽象方法，如初始化数据、绑定事件、渲染模板等，然后在子类中实现这些具体操作。<mark style="color:red;">这样可以使得我们在开发页面时，只需要关注具体的业务逻辑，而不用过多关注页面的渲染细节。</mark>

Angular 的生命周期钩子（如 ngOnInit, ngOnDestroy 等）采用了模板方法模式。这些钩子方法在组件的生命周期中自动调用，开发者可以在这些方法中添加自定义逻辑。

&#x20; 2\.   观察者模式（Observer Pattern）

&#x20;Angular 广泛使用 RxJS 库来实现观察者模式。RxJS 提供了 Observable 和 Subject 等类，用于处理异步数据流和事件。

在 RxJS 中，观察者模式通过 Observable 和 Observer 来实现。

· Observable（可观察对象）：一个可以发送数据流的对象。它可以发送多种类型的通知，包括 next（发送数据）、error（发送错误）和 complete（发送完成通知）。

· Observer（观察者）：一个可以接收 Observable 发送的通知的对象。它包含三个回调函数：next、error 和 complete

```javascript
import { Observable, Observer } from 'rxjs';

// 创建一个 Observable
const myObservable = new Observable((observer: Observer<string>) => {
  observer.next('Hello');
  observer.next('World');
  observer.complete();
});

// 创建一个 Observer
const myObserver: Observer<string> = {
  next: (value) => console.log('Next:', value),
  error: (error) => console.error('Error:', error),
  complete: () => console.log('Completed')
};

// 订阅 Observable
myObservable.subscribe(myObserver);
```

_**发布-订阅模式**_ &#x20;

Subject 类实现了发布订阅模式。Subject 是一个特殊的 Observable，它既可以作为 Observable 来订阅，也可以作为 Observer 来接收数据。

```javascript
import { Injectable } from '@angular/core';
import { Subject } from 'rxjs/internal/Subject';

@Injectable({
  providedIn: 'root'
})
export class MessageService {
  private msg = new Subject<String>();

  public sendMsg(message: String) {
    this.msg.next(message);
  }

  public getMsg() {
    return this.msg;
  }
}
```

3. &#x20;策略模式

策略模式用于定义一系列算法，并将每一个算法封装起来，使它们可以互换。Angular 中的路由守卫（Guards）就是一个典型的例子

## 创建型设计模式

1. &#x20;**单例模式（Singleton Pattern）**

Angular 的服务默认是单例的，即在整个应用中只有一个实例。这通过在服务的 @Injectable 装饰器中设置 providedIn: 'root' 来实现。

```javascript
如何在 TypeScript 中实现单例模式
class Logger {
    private static instance: Logger;

    private constructor() {
        // 私有构造函数，防止外部实例化
    }

    public static getInstance(): Logger {
        if (!Logger.instance) {
            Logger.instance = new Logger();
        }
        return Logger.instance;
    }

    log(message: string) {
        console.log(`[LOG] ${message}`);
    }
}

const logger1 = Logger.getInstance();
const logger2 = Logger.getInstance();

logger1.log('This is a log message.');
logger2.log('This is another log message.');
```

&#x20;

2. **工厂模式（Factory Pattern）**

描述：它是专门定义一个类，依据参数的不同，来负责创建其它类的实例，被创建的实例通常都具有共同的父类

1\. 创建服务：定义你需要创建的不同服务。

2\. 创建工厂函数：编写一个工厂函数，根据条件返回不同的服务实例。

3\. 注册提供者：在模块中注册工厂提供者。

4\. 在组件中使用服务：在组件中注入并使用服务。

**简单工厂**

简单工厂模式是最基本的形式

简单工厂其实不是一个设计模式，反而比较像是一种编程习惯。

简单工厂模式（Simple Factory Pattern）也称为静态工厂模式，它是专门定义一个类，依据参数的不同，来负责创建其它类的实例，被创建的实例通常都具有共同的父类。之所以称作静态工厂模式，

假设我们有一个应用，需要创建不同类型的日志记录器（例如，文件日志记录器和数据库日志记录器）。

```javascript
import { Component, inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Component({
  selector: 'app-root',
  template: `<h1>Hello, {{ title }}!</h1>`
})
export class AppComponent {
  title = 'Angular App';
  private http = inject(HttpClient);

  ngOnInit() {
    this.http.get('https://api.example.com/data').subscribe(data => {
      console.log(data);
    });
  }
}


// 日志记录器接口
interface Logger {
  log(message: string): void;
}

// 文件日志记录器
class FileLogger implements Logger {
  log(message: string) {
      console.log(`File Logger: ${message}`);
  }
}

// 数据库日志记录器
class DatabaseLogger implements Logger {
  log(message: string) {
      console.log(`Database Logger: ${message}`);
  }
}

// 简单工厂类
class LoggerFactory {
  static createLogger(type: string): Logger {
      switch (type) {
          case 'file':
              return new FileLogger();
          case 'database':
              return new DatabaseLogger();
          default:
              throw new Error('Unknown logger type');
      }
  }
}

// 使用简单工厂模式
const fileLogger = LoggerFactory.createLogger('file');
fileLogger.log('This is a file log.');

const databaseLogger = LoggerFactory.createLogger('database');
databaseLogger.log('This is a database log.');
```

**工厂方法**

定义了一个创建对象的抽象接口（类或接口中的方法）==并不是代码中的interface==，但由子类决定要实例化的类 是哪一个。工厂方法把实例化推迟到子类

提供一个接口，让子类决定实例化哪一个类。

```javascript
// 车辆接口
interface Vehicle {
  start(): void;
}

// 汽车类
class Car implements Vehicle {
  start() {
      console.log('Car started.');
  }
}

// 摩托车类
class Motorcycle implements Vehicle {
  start() {
      console.log('Motorcycle started.');
  }
}

// 车辆工厂接口
interface VehicleFactory {
  createVehicle(): Vehicle;
}

// 汽车工厂
class CarFactory implements VehicleFactory {
  createVehicle(): Vehicle {
      return new Car();
  }
}

// 摩托车工厂
class MotorcycleFactory implements VehicleFactory {
  createVehicle(): Vehicle {
      return new Motorcycle();
  }
}

// 使用工厂方法模式
const carFactory = new CarFactory();
const car = carFactory.createVehicle();
car.start();

const motorcycleFactory = new MotorcycleFactory();
const motorcycle = motorcycleFactory.createVehicle();
motorcycle.start();
```

**抽象工厂**

· 工厂方法模式适合于创建单一类型的产品，通过子类来决定具体的实现。

· 抽象工厂模式适合于创建一系列相关的产品，通过具体的工厂类来创建这些产品。

&#x20;

## 结构型设计模式

1. **代理模式**

描述：当我们想要对一个业务类进行某些横切性的增强时，例如：增加请求与响应 的日志、增加权限校验、增加远程请求对象封装等等。我们可以采用代理模式去实现， 而不需要修改原有的类。

2. **组合模式**

描述：用于将对象组合成树形结构以表示“部分-整体”的层次结构。

在 Angular 中，你可以利用组合模式来构建复杂的 UI 组件或数描述：它允许向一个现有的对象动态地添加新的功能，同时不改变其结构。它是继承的一种替代方案，可以实现在运行时动态地扩展对象的行为，而无需修改原有代码。

假设我们要构建一个简单的文件系统，其中包含文件和文件夹。文件夹可以包含文件和其他文件夹，形成一个树形结构。

1、Component：组合模式中的“根节点”，可以是接口、抽象类、普通类，该类中定义了其子类的所有共性内容，并且该类中还存在着用于访问和管理它子部件的方法。

2、Leaf：组合中的叶子节点，也就是最末端的节点，该节点下不会再有子节点。

3、Composite：非叶子节点，它的作用是存储子部件，并且在Composite中实现了对子部件的相关操作。

3. **装饰器模式（Decorator Pattern）**

用于修改或注解类、方法、属性和参数

4. **适配器模式**

模式描述：适配器模式允许你将一个类的接口转换成客户端期望的另一个接口。在 Angular 中，你可以使用适配器模式来处理不同数据源的格式转换。\


```javascript
class OldApiService {
  async getData(): Promise<{ id: number; name: string }> {
      // 模拟旧 API 数据
      return { id: 1, name: 'Old API Data' };
  }
}

class NewApiService {
  async getData(): Promise<{ id: number; title: string }> {
      // 模拟新 API 数据
      return { id: 2, title: 'New API Data' };
  }
}

const oldApi = new OldApiService();
const newApi = new NewApiService();

const adapter = new DataAdapter(oldApi, newApi);

(async () => {
  const oldData = await adapter.getUnifiedData('old');
  console.log(oldData); // 输出: { id: 1, name: 'Old API Data' }

  const newData = await adapter.getUnifiedData('new');
  console.log(newData); // 输出: { id: 2, name: 'New API Data' }
})();
```

