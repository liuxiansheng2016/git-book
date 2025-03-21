# JavaScript

### 事件流

```
事件捕获阶段：事件从最外层的元素开始，逐层向下传递到目标元素
目标阶段：事件到达目标节点，此时可以处理事件。
事件冒泡阶段：事件从目标节点开始，沿着 DOM 树向上冒泡，直到冒泡到最顶层的 DOM 节点
```

### 事件委托

```
利用事件冒泡的原理，通过父元素来捕获子元素的事件加以处理
```

### 继承

```
原型链继承
构造函数继承
组合式继承
ES6的类
https://blog.csdn.net/weixin_70134200/article/details/131730945
```

### 垃圾回收机制

```

1. **标记-清除（Mark and Sweep）**
   - 从根对象（如全局变量、栈中的局部变量等）(还包括从闭包、事件监听器等可达的对象)出发，递归地遍历所有可达对象，并标记这些对象。除了从根对象出发外，还会追踪所有通过引用链可达的对象。
   - 扫描整个堆内存，释放未被标记的对象所占用的内存。在清除未被标记的对象时，可能会导致内存碎片化，因为释放后的空间可能不连续。

2. **引用计数（Reference Counting）**
   - 每个对象都有一个引用计数器，每当有一个新的引用指向该对象时，计数器加1；每当有一个引用被移除时，计数器减1。当引用计数器为0时，对象被释放。
    - 现代 JavaScript 引擎一般不单独使用引用计数，而是结合其他算法一起使用，以解决循环引用的问题。
   - 缺点：无法处理循环引用的问题。例如两个对象互相引用，但实际上它们已经不再被程序中的其他部分所使用。现代编程语言如Python、Swift等通常会结合使用弱引用或其他机制来解决这个问题。此外，还可以引入“环形检测”机制来处理循环引用的问题。

3. **分代收集（Generational Collection）**
   - 将堆内存分为不同的代（如新生代和老年代），不同代的垃圾回收策略不同。
   - 新生代：频繁进行小规模的垃圾回收，因为新分配的对象往往生命周期较短。通常采用复制算法（Copying Algorithm），即在新生代中，存活的对象会被移动到另一块区域。
   - 老年代：较少进行大规模的垃圾回收，因为老对象往往生命周期较长。可能使用标记-清除或者标记-压缩算法。经过几次垃圾回收后仍然存活的对象会被移动到老年代中，这一过程称为晋升。

4. **标记-压缩**
   - 标记：与标记-清除相同，从根对象出发，递归地遍历所有可达对象，并标记这些对象。
   - 压缩：将存活的对象移动到连续的内存区域，以减少内存碎片。这是为了解决内存碎片化的问题，通过将存活对象集中放置，可以更有效地利用内存空间。

5. **垃圾回收的挑战**
   - 性能开销：垃圾回收过程会消耗 CPU 和内存资源，可能导致应用程序短暂的停顿。为了减少GC对应用性能的影响，现代垃圾收集器支持并发执行（与应用程序同时运行）和并行执行（多线程执行）。
   - 内存碎片：频繁的垃圾回收可能导致内存碎片，影响内存分配效率。特别是对于需要大块连续内存的应用场景，内存碎片问题尤为突出。
   - 循环引用：引用计数机制无法处理循环引用的问题，需要额外的机制来检测和处理。例如，使用弱引用来打破循环引用链，或者通过专门的算法识别和回收循环引用结构。
   - 停顿时间控制：不同的GC算法有不同的停顿时间特性，选择合适的GC算法对于实时性要求较高的应用尤为重要。开发者可以根据应用的具体需求调整垃圾收集策略，以平衡性能与响应时间。
```

### 前端监控系统

### 进程和线程

```
进程是资源分配和独立运行的基本单位，而线程是进程中的执行单元。
进程之间独立运行，线程共享进程的资源。
线程共享相同的地址空间，因此线程间的通信相对容易。	
进程切换的开销较大，线程切换的开销相对较小。
进程之间需要通过进程间通信（IPC）来进行数据交换，线程之间可以直接访问共享的数据
```

### content-type

{% code overflow="wrap" %}
```
Content-Type 是 HTTP 请求头和响应头中常见的字段，用于指明发送到接收方的内容体（body）的媒体类型（MIME 类型）。它告诉客户端或服务器如何解析或处理消息体
常见的 Content-Type 值
1. 文本类型
Content-Type	描述
text/plain	普通文本文件。
text/html	HTML 文档。
text/css	CSS 文件。
text/javascript	JavaScript 文件（通常被 application/javascript 替代）。
text/csv	CSV 文件。
2. 应用程序类型
Content-Type	描述
application/json	JSON 格式的数据。
application/xml	XML 格式的数据。
application/pdf	PDF 文件。
application/x-www-form-urlencoded	表单数据（键值对，URL 编码形式）。
application/javascript	JavaScript 脚本文件。
application/zip	ZIP 压缩文件。
3. 多部分类型
Content-Type	描述
multipart/form-data	表单数据（包含文件上传时使用）。
multipart/alternative	包含多种表示形式的内容（如纯文本和 HTML 版本的电子邮件）。
multipart/mixed	用于包含多种数据类型的内容（如文本和附件）。
4. 图像类型
Content-Type	描述
image/png	PNG 图片。
image/jpeg	JPEG 图片。
image/gif	GIF 图片。
image/webp	WebP 图片。
5. 音视频类型
Content-Type	描述
audio/mpeg	MP3 音频文件。
audio/ogg	OGG 音频文件。
video/mp4	MP4 视频文件。
video/webm	WebM 视频文件。
```
{% endcode %}

### Get/post

```
get是获取数据
Post 提交数据

Get 具有幂等性， 重复获取对资源不会有影响
Get 参数可以 放在url 中，长度有限制，不安全

GET 请求可以被浏览器和代理服务器缓存，提高性能
```

### Symbol

### Object.keys() 和for in for of

{% code overflow="wrap" %}
```
Object.keys()：遍历对象的key,返回一个数组，数组值为对象自有的属性，不会包括继承原型的属性
for in :以任意顺序遍历一个对象的属性，包括自身属性，以及继承自原型的属性
for (var prop in obj) {
  if (obj.hasOwnProperty(prop)) {
    // 处理自身属性
  } else {
    // 处理继承来的属性
  }
}
两种方法都无法遍历到以Symbol为键(key)的属性
for of 只能遍历数组（需要实现iterator接口）
```
{% endcode %}

{% code overflow="wrap" %}
```
Object.create(proto [, propertiesObject]);
⦁使用 Object.create：当需要手动设置对象的原型链时，或者需要更细粒度地控制对象属性时。
⦁使用 new：当需要创建一个构造函数的实例，并且构造函数负责初始化对象状态时。
```
{% endcode %}

### In 和 hasOwnProperty

```
判断对象中是否拥有某个属性
in 包含原型链
```

{% code overflow="wrap" %}
```
运行时 vs 编译时：
instanceof 是在运行时进行类型检查。
is 类型守卫是在编译时进行类型检查，并且可以缩小类型范围。
适用场景：
instanceof 适用于检查对象是否是某个类的实例。
is 类型守卫适用于更复杂的类型检查，特别是在联合类型中区分不同的类型。
类型安全性：
instanceof 只能检查对象是否是某个类的实例，不能用于更复杂的类型检查。
is 类型守卫可以提供更细粒度的类型检查，并且在返回 true 时会自动缩小类型范围，提高类型安全性。
```
{% endcode %}

### 强制类型转换和隐式类型转换

```
String()，Boolean()，Number()
隐式：拼接字符串
```

### 堆和栈

{% code overflow="wrap" %}
```
栈区：只存储局部变量（也包括全局变量）
堆区：只存储对象的实例，每个对象实例在堆中都会自动产生一个唯一的内存地址 堆是用于动态内存分配的区域

数组和函数的引用存储在栈内存中，而对象本身存储在堆内存中
```
{% endcode %}

### 深拷贝和浅拷贝

{% code overflow="wrap" %}
```
深拷贝是指在进行复制操作时，创建一个完全独立的新对象，并递归地复制原始对象及其所有子对象
在浅拷贝中，只有最外层对象被复制，而内部的嵌套对象只是引用而已
https://blog.csdn.net/weixin_44369568/article/details/101515407
```
{% endcode %}

### 判断一个对象是否是数组，如何处理类数组对象

```javascript
1. Array.isArray(obj)
2. Object.prototype.toString.call(obj) === "[object Array]"
3. obj instanceof Array
处理
1. Array.from(arrayLike)
2. Array.prototype.slice.call(arrayLike)
3. [...arrayLike]
const arrayLike = {  0: 'a',  1: 'b',  2: 'c',  length: 3,  [Symbol.iterator]() {    let index = -1;    const data = this;    return {      next() {        index++;        if (index < data.length) {          return { value: data[index], done: false };        } else {          return { done: true };        }
      }
    };
  }
};
const array = Array.from(arrayLike);console.log(array); //

slice 方法：slice 是 JavaScript 数组的一个原型方法，用于提取数组的一部分并返回一个新数组。默认情况下，它只能直接应用于真正的数组对象。
call 方法：call 是 Function.prototype 上的一个方法，它可以改变函数执行时的上下文（即 this 的值）。这意味着你可以指定任意对象作为函数执行时的 this 值。
应用到类数组对象：当你使用 Array.prototype.slice.call(arrayLike) 时，实际上是将 slice 方法的 this 绑定到了 arrayLike 对象上，使得 slice 方法能够像处理普通数组那样处理这个类数组对象。由于 slice 不修改原对象而是返回一个新的数组，所以结果是一个真正的数组，包含了从 arrayLike 提取的所有元素。
```

### 说下你对 Reflect 的理解？为什么会有 Reflect 的出现？Proxy 也简单的说一下？

```
Reflect 和 Proxy 都是用来控制和操作 JavaScript 对象，Reflect 提供了一组标准的方法来执行语言内部的操作，而 Proxy 则允许你自定义这些操作的行为

Reflect对象上可以拿到语言内部的方法。

- 1. 将 Object 对象一些内部的方法，放到 Reflect 对象上。比如 Object.defineProperty
现阶段这些方法存在于 object 和 Reflect 对象上，未来只存在于 Reflect 对象上。

意义：也就是说，从 Reflect 对象上可以拿到语言内部的方法。

-2. 操作对象时出现报错返回 false
比如：Object.defineProperty(obj,name,desc) 在无法定义属性时，会抛出一个错误，而Reflect.defineProperty(obj,name,desc)则会返回 false,这样会更合理一些。
// 使用 Object.defineProperty
try {
  Object.defineProperty(obj, 'name', { value: 'value' });
} catch (e) {
  console.log(e);
}

// 使用 Reflect.defineProperty
if (!Reflect.defineProperty(obj, 'name', { value: 'value' })) {
  console.log('Failed to define property');
}

-3. 让操作对象的编程变为函数式编程
说明：老写法有的是命令式编程，比如下面这个例子

// 命令式编程
'name' in obj;

// 函数式编程
Reflect.has(obj, 'name');

-4. 保持和 Proxy 对象的方法一一对应

说明：Reflect 对象的方法与 Proxy 对象的方法一一对应，只要是 Proxy 对象的方法，就能在 Reflect 对象上找到对应的方法。
const handler = {
    get(target, propertyKey, receiver) {
        // 使用 Reflect.get 来获取目标对象的属性值
        const result = Reflect.get(target, propertyKey, receiver);
        
        if (result === undefined) {
            console.log(`属性 "${propertyKey}" 不存在，返回默认值`);
            return '默认值';
        }
        
        console.log(`访问了属性 "${propertyKey}"`);
        return result;
    },
    set(target, propertyKey, value, receiver) {
        // 使用 Reflect.set 来设置目标对象的属性值
        const success = Reflect.set(target, propertyKey, value, receiver);
        if (success) {
            console.log(`设置了属性 "${propertyKey}" 的值为 "${value}"`);
        } else {
            console.log(`设置属性 "${propertyKey}" 失败`);
        }
        return success;
    }
};

const target = { foo: 'bar' };
const proxy = new Proxy(target, handler);

console.log(proxy.foo); // 输出: 访问了属性 "foo" 和 "bar"
proxy.newProp = 'newValue'; // 输出: 设置了属性 "newProp" 的值为 "newValue"
console.log(proxy.nonExistent); // 输出: 属性 "nonExistent" 不存在，返回默认值 和 默认值

Proxy 是一个构造函数，它能够创建一个代理对象，用于拦截并定义基本操作。

用于修改某些操作的默认行为，等同于在语言层面做出修改。

var proxy = new Proxy(target, handler);
```

### Webpack 打包 速度慢怎么办？

### 微前端

```
微前端是一套架构体系，
Module Federation 是webpack给出的技术方案
1. 技术栈无关
主框架不限制接入应用的技术栈，微应用具备完全自主权
2. 独立开发、独立部署
微应用仓库独立，前后端可独立开发，部署完成后主框架自动完成同步更新
3. 增量升级
在面对各种复杂场景时，我们通常很难对一个已经存在的系统做全量的技术栈升级或重构，而微前端是一种非常好的实施渐进式重构的手段和策略
4. 独立运行时
每个微应用之间状态隔离，运行时状态不共享
```

### 前端工程化

```
模块化
自动化部署
自动化测试
持续集成
https://www.zhihu.com/question/433854153/answer/2925739518?utm_id=0
```

### 封装公共组件需要注意什么？

```
可复用性、可维护性、可测试性和灵活性
```

### 项目开发你是怎么组织 CSS 的？

### 性能优化

```
按需加载（On-demand Loading）
懒加载（Lazy Loading）：当用户滚动到页面特定部分时才加载图片或其他资源，减少初始加载的数据量。
预加载（Preloading）：提前加载用户可能需要但当前未请求的资源，提高用户体验。
滚动加载（Infinite Scrolling）：随着用户向下滚动页面自动加载更多内容，无需手动点击“下一页”。
分页加载（Pagination）：将大量数据分成多个页面展示，每次只加载一部分数据。
浏览器缓存和服务端缓存策略
浏览器缓存
使用 Cache-Control 和 Expires 头来控制资源的有效期。
ETag 和 Last-Modified 可以用来验证资源是否更新。
服务端缓存
CDN 缓存：通过内容分发网络（CDN）加速静态资源的访问速度。
内存缓存：如 Redis 或 Memcached，用于存储频繁访问的数据。
数据库查询结果缓存：减少重复查询带来的负载。
请求合并与 API 端点聚合
请求合并：将多个独立的 HTTP 请求合并为一个请求，减少 HTTP 连接开销。
API 端点聚合：通过单一 API 接口提供多服务数据聚合的功能，简化前端逻辑。
异步加载 JS 资源
使用 <script> 标签的 async 或 defer 属性异步加载脚本，避免阻塞页面渲染。
动态导入（Dynamic Imports）可以按需加载模块。
减少重绘和回流
批处理 DOM 操作：使用文档片段（Document Fragment）进行批量修改，减少对 DOM 树的直接操作。
CSS 动画代替 JavaScript 动画：CSS 动画通常由浏览器优化执行，比 JavaScript 实现的动画效率更高。
节流与防抖
节流（Throttle）：限制某个函数在一定时间内只能执行一次，适用于高频事件如窗口调整大小或滚动。
防抖（Debounce）：确保某段时间内只有最后一次触发有效，适合搜索框输入等场景。
Webpack 打包和压缩
代码分割（Code Splitting）：通过动态导入或入口点拆分，实现按需加载。
Tree Shaking：移除未使用的代码，减小打包体积。
splitChunks：提取公共代码。
TerserPlugin 是 Webpack 中用于压缩 JavaScript 的插件。
```

### 按需加载

```
懒加载
分页加载
异步加载
```

### 说一下减少 dom 数量的办法？一次性给你大量的 dom 怎么优化？

```
一、减少DOM数量的方法
1.可以使用伪元素，阴影实现的内容尽量不使用DOM实现，如清除浮动、样式实现等；
2.按需加载，减少不必要的渲染；
3.结构合理，语义化标签；
二、大量DOM时的优化
当对Dom元素进行一系列操作时，对Dom进行访问和修改Dom引起的重绘和重排都比较消耗性能，所以关于操作Dom,应该从以下几点出发：
1.缓存Dom对象
2.文档片段
3虚拟Dom
```

### CI/CD

### ES6 代码转成 ES5 代码

### commonJS, ES6模块化, UMD,CMD

```
commonJS 一个文件就是一个模块，运行时加载
ES6模块化 编译时加载
CommonJS模块输出的是一个值的拷贝，而ES6模块输出的是值的引用
UMD：通用模块定义，能够在多种模块加载系统（AMD、CommonJS、全局变量）中工作。
CMD：Sea.js 模块定义，支持按需加载和单例模式。
AMD:
异步模块的定义，它是浏览器端模块开发的规范。由于不是js的原生支持，需要用到对应的库函数。Require.js.
```

### 为什么利用多个域名来存储网站资源？

```
浏览器限制：现代浏览器对同一域名下的并发请求数量有限制（通常为6个）。这意味着如果一个页面需要加载大量资源（如图片、CSS、JavaScript文件等），可能会因为达到最大并发连接数而不得不等待前面的请求完成。通过使用多个域名，可以增加总的并发连接数，从而加快资源加载速度。
负载均衡：通过将资源分布到多个域名，可以将流量分散到不同的服务器，避免单个服务器成为瓶颈。
CDN 优化：使用内容分发网络（CDN）可以将资源分布到全球多个节点，利用多个域名可以更好地与CDN结合，进一步优化资源加载速度。
域名隔离：通过使用不同的域名，可以实现资源的隔离，防止跨域问题，提高安全性和稳定性。
```

### 负载均衡

{% code overflow="wrap" %}
```
负载均衡（Load Balancing）是一种计算机技术，用于在多个计算资源（如服务器、网络链接、CPU、硬盘等）之间分配工作负载，以优化资源使用、最大化吞吐量、最小化响应时间，并避免任何单一资源的过载
```
{% endcode %}

### 接口怎么防刷

### Promise状态

### 函数式编程的理解 JavaScript

```
纯函数
柯里化
高阶函数 
优点： 无状态，更优雅的组合
```

### 高阶函数

{% code overflow="wrap" %}
```
是指可以接受一个或多个函数作为参数，并且可以返回一个函数作为结果的函数。在函数式编程中，高阶函数是一个非常重要的概念，它可以帮助我们将代码写得更加简洁、灵活和可复用
```
{% endcode %}

### 说出前端框架设计模式(MVVM 或 MVC)的含义以及原理

### 说一下 jQuery ajax、axios、fetch 的区别

### &#x20;jsonp

```
前端和后端配合实现跨域ajax，需要服务端配合处理回调函数
```

### 介绍下 npm 模块安装机制？输入 npm install 命令敲下回车后它的一个执行流程是怎样的？

### Node.js 支持高并发的理解

```
它的事件驱动、非阻塞I/O模型以及单线程事件循环架构
```

### 请求时是否带Cookie

```
⦁fetch：通过设置 credentials: 'include' 来携带 Cookie。
⦁Axios/ajax：通过设置 withCredentials: true 来携带 Cookie。
⦁服务器端：需要设置 Access-Control-Allow-Origin 为具体的域名，并设置 Access-Control-Allow-Credentials: true。

```

### &#x20;Http 无状态

### &#x20;HTTPS 与 HTTP

{% code overflow="wrap" %}
```
HTTPS是HTTP协议的安全版本，HTTP协议的数据传输是明文的，是不安全的，HTTPS使用了SSL/TLS协议进行了加密处理，相对更安全
```
{% endcode %}

### HTTP/2

{% code overflow="wrap" %}
```
HTTP/1.1
HTTP/1.1 是当前广泛使用的版本，它在 HTTP/1.0 的基础上进行了大量的改进，包括支持持久连接、管道化请求等。
主要特点：
⦁持久连接：默认启用持久连接（keep-alive），可以复用 TCP 连接来发送多个请求。
⦁管道化请求：客户端可以在一个 TCP 连接中连续发送多个请求，而无需等待每个请求的响应。
⦁状态管理：继续支持状态码和状态消息。
⦁请求方法：支持更多的请求方法，如 HEAD、PUT、DELETE 等。
⦁头部信息：支持更丰富的头部信息，包括 Cookie、Content-Type 等。
⦁错误处理：提供了更详细的错误处理机制。
⦁无状态：仍然是无状态协议，但可以通过 Cookie 等机制实现会话管理
HTTP/2
相比于1
二进制协议：HTTP/1.1 版的头信息肯定是文本（ASCII编码），数据体可以是文本，也可以是二进制。HTTP/2 则是一个彻底的二进制协议，头信息和数据体都是二进制，并且统称为”帧”：头信息帧和数据帧。二进制协议解析起来更高效、“线上”更紧凑，更重要的是错误更少。
多路复用：HTTP/2 复用 TCP 连接，在一个连接里，客户端和浏览器都可以同时发送多个请求或回应，而且不用按照顺序一一对应，这样就避免了”队头堵塞”。
头信息压缩：HTTP 协议是没有状态，导致每次请求都必须附上所有信息。请求的很多头字段都是重复的，比如 Cookie，一样的内容每次请求都必须附带，这会浪费很多带宽，也影响速度。HTTP/2 对这一点做了优化，引入了头信息压缩机制。一方面，头信息使用 gzip 或 compress 压缩后再发送。另一方面，客户端和服务器同时维护一张头信息表，所有字段都会存入这个表，产生一个索引号，之后就不发送同样字段了，只需发送索引号。这样对于相同的头部，不必再通过请求发送，只需发送一次。
服务器推送：HTTP/2 允许服务器未经请求，主动向客户端发送资源。
服务器推送
HTTP/3
HTTP/3 是基于 QUIC 协议的新一代 HTTP 协议，
 是一种 UDP 上的传输层协议，设计目标是为了减少延迟并提高安全性。
默认加密
连接迁移：从wifi切换到蜂窝数据是不会中断
减少延迟
数据重传
拥塞控制
HTTP/3 建立在 HTTP/2 的基础上，并且保留了许多 HTTP/2 的特性。同时，HTTP/3 主要是在传输层进行了改进，使用了 QUIC 协议代替 TCP
```
{% endcode %}

### &#x20;http请求由什么组成

```
请求行、请求头部、空行、请求数据。
https://zhuanlan.zhihu.com/p/72736041?utm_id=0
请求行: 由请求方法字段、URL字段和HTTP协议版本字段3个字段组成，
GET /data/info.html HTTP/1.1
请求头：Accept cookie authrization user-agent host

1.请求报文(请求行/请求头/请求数据/空行)
2.响应报文(状态行、消息报头、响应正文)
```

### http请求类型

### 简单请求和非简单请求

{% code overflow="wrap" %}
```
请求方法是以下三种之一：
HEAD
GET
POST
Content-Type 的值仅限于以下三种之一
text/plain
multipart/form-data
application/json
https://baijiahao.baidu.com/s?id=1765836290890433189&wfr=spider&for=pc
```
{% endcode %}

### Https 加密握手的过程

### 什么时候用 TCP，什么时候适合 UDP？

### DNS

```
Domain name system
用来将域名转换为ip的系统
1本地DNS缓存
2本地host查找映射
3本地DNS服务器
4更高级别的DNS服务器发送查询请求
```

### http请求从发起请求到收到响应的全部过程

```
1 输入网址
2 DNS解析
3、建立tcp连接（如果使用的是 HTTPS 协议，则在 TCP 连接建立之后，还需要进行 TLS/SSL 握手以确保通信的安全性。）
4、客户端发送HTPP请求
5、服务器处理请求　
6、服务器响应请求
7、浏览器展示HTML
8、浏览器发送请求获取其他在HTML中的资源
```

### 多路复用：

{% code overflow="wrap" %}
```
⦁为了避免 每次请求就建立一次连接。 采用了keep-alive。 一定时间内让建立一次连接可以多次请求数据。在http1 传输基于文本， 所以所有的数据必须按顺序传输。 HTTP2 采用二进制数据帧和流的概念
HTTP/1.1 的持久连接，请求和响应必须按顺序进行。
⦁HTTP/2 的多路复用还允许同时处理多个请求和响应，显著提高了性能。
```
{% endcode %}

### keep-alive和多路复用

```
HTTP/1.1 的 keep-alive：
连接复用：复用 TCP 连接，但同一时间只能处理一个请求。
半同步：请求按顺序发送和接收。
头阻塞：前面的请求未完成会导致后面的请求阻塞。
并发请求：通常需要开启多个并行的 TCP 连接来加速页面加载。
HTTP/2 的多路复用：
多路复用：在同一 TCP 连接上同时传输多个请求和响应。
无头阻塞：请求和响应可以交错发送和接收。
二进制帧：使用二进制格式传输数据，更紧凑。
头部压缩：使用 HPACK 算法压缩头部，减少传输字节数。
流和帧：请求和响应被分割成多个流和帧，可以交错传输。
```

### Tcp 和UDP

```
传输控制协议
用户数据报协议
tcp 面向连接的协议， UDP无连接的协议
tcp可靠性；确认和重传，数据排序
Tcp 开销大，拥塞机制，流量控制
所以速度更慢
```

### &#x20;三次握手

```
如果使用 两次握手（客户端 SYN → 服务器 SYN+ACK），
可能会出现 服务器认为连接已经建立，但客户端并未确认 的情况，导致服务器资源浪费。
```

### 四次挥手

{% code overflow="wrap" %}
```
双方向确认：
四次挥手过程中，连接的双方都需要确认对方的数据传输已经结束。主动关闭方发送 FIN 表示不再发送数据，而被动关闭方也需要发送 FIN 表示自己的数据传输也已完成。
防止丢失确认：
如果只有三次挥手，那么在被动关闭方发送 FIN 后，如果 ACK 数据包丢失，主动关闭方无法知道被动关闭方是否收到了 FIN。因此，需要第四次挥手来确认被动关闭方的 FIN 已经被主动关闭方收到。
```
{% endcode %}

### 计算机网络七层模型详解

{% code overflow="wrap" %}
```
网络模型的分层主要有两种经典的模型：OSI（Open Systems Interconnection）七层模型和TCP/IP（Transmission Control Protocol/Internet Protocol）模型。
计算机网络五层模型是结合了OSI七层模型和TCP/IP四层模型的特点的一种简化模型
应用层 (http dns)
（ 会话层）
（表示层）数据的格式化加密
传输层（tcp udp）
网络层
数据链路层
物理层(网络设备的物理规格)
```
{% endcode %}

### Websocket和http

```
WebSocket是一种在单个TCP连接上进行全双工通信的协议。它被设计用于替代传统的HTTP请求/响应模型，以实现更高效、实时的通信。
WebSocket使得服务器和客户端可以在建立连接后，双向发送数据，而无需每次发送数据前进行握手，这大大提高了数据传输的效率和速度。

1.2 WebSocket与HTTP的区别

连接方式：HTTP是基于请求/响应的模型，每次通信都需要建立新的连接（无状态的交互），而WebSocket在连接建立后，可以持续进行双向通信。

数据传输：HTTP传输数据时，需要封装成HTTP请求或响应，包含额外的头部信息，而WebSocket传输的数据更轻量(WebSocket 消息头较小)，没有HTTP头部的开销。

实时性：由于WebSocket的持续连接特性，它能够实现真正的实时通信，而HTTP则需要轮询或长轮询来模拟实时性，效率较低。

安全性：HTTPS：HTTP 可以通过 HTTPS 提供加密传输，确保数据的安全性。

WebSocket 可以通过 WSS（WebSocket Secure）提供加密传输，确保数据的安全性。
```

### 对 JavaScript 执行上下文的理解 JavaScript

```
在全局执行上下文中，this的值指向全局对象

在函数执行上下文中，this的值取决于该函数是如何被调用的，
如果它被一个引用对象调用，那么this会被设置成那个对象，否则this的值被设置为全局对象或者undifined,
```

### ES5 模拟实现一下 let、const（https://www.cnblogs.com/bxtfdxg/p/14885568.html）

```
匿名函数和闭包
```

### 暂时性死区

{% code overflow="wrap" %}
```
ES6 规定：如果区块中存在 let 和 const，这个区块对这两个关键字声明的变量，从一开始就形成了封闭作用域。假如尝试在声明前去使用这类变量，就会报错。这一段会报错的区域就是暂时性死区。
https://zhuanlan.zhihu.com/p/438563024?utm_id=0
```
{% endcode %}

### &#x20;异步请求为什么会导致tcp的对头阻塞（https://www.jianshu.com/p/8f64f93d0080）

{% code overflow="wrap" %}
```
http2 的多路复用解决了 http 层面的对头阻塞。HTTP2 不使用管道化的方式，而是引入了帧、消息和数据流等概念，每个请求/响应被称为消息，一个流代表一个完整的请求或者响应。每个消息都被拆分成若干个帧进行传输，每个帧都分配一个序号。不同流的帧可以乱序发送，同一个流的帧需要顺序发送。每个帧在传输是属于一个数据流，而一个连接上可以存在多个流，每个流都可以承载双向的消息。各个帧在流和连接上独立传输，到达之后在组装成消息，这样就避免了请求/响应阻塞。
但是即使是使用 http2，它底层也是使用 tcp，所以它依然存在 tcp 层面的对头阻塞
tcp 的对头阻塞
TCP 的阻塞问题是包括慢启动和拥塞控制（当网络出现拥塞时，TCP 会减小发送窗口大小，这可能导致数据包的发送速率降低。如果最早的几个数据包因为拥塞控制而被延迟，后续的数据包也会受到影响，导致队头阻塞。）、丢包和重传、接收方处理能力不足、缓冲区溢出、应用程序延迟和网络延迟等。通过优化缓冲区大小、应用程序处理逻辑、使用多路复用技术和改进拥塞控制算法等方。
```
{% endcode %}

### &#x20;虚拟列表

{% code overflow="wrap" %}
```
分页加载。实现简单直接，但是在请求下一页时，可能也会打断用户心流，体验不是最佳。
懒加载。实现难度不大，可以解决首屏的压力，但是长时间加载数据，也同样会产生大量元素节点，从而影响应用性能（处理过期节点的销毁问题？）。
虚拟列表。实现难度较大，通过计算滚动视窗，每次只渲染部分元素，既减少了首屏压力，长时间加载也不会有更多的性能负担，可以满足上述大部分场景。
https://juejin.cn/post/6877507011769008135
```
{% endcode %}

### webWorker的优缺点

### 程序设计模式

### &#x20;运营商劫持

### JSON.stringify

&#x20;[https://zhuanlan.zhihu.com/p/478226524?utm\_id=0](https://zhuanlan.zhihu.com/p/478226524?utm_id=0)

### 网页扫码登录的原理

```
1. 生成二维码
2. 用户扫描二维码
3. 验证登录凭证
手机端应用向服务器发送登录凭证，同时附加用户的身份信息（如用户的登录状态或凭证）。
服务器验证：
如果登录凭证有效，并且用户身份信息验证通过，则将登录状态与该凭证绑定。
更新凭证状态（标记为已登录）
4. 网页轮询验证
```

### 原型prototype

```
每个类型（例如Date,Error,Array,Number……包括自定义类型例如Person）都有一个属性，这个属性的名字叫做prototype，这个prototype指向一个对象
_proto__任何对象实例都拥有这个属性，这个属性指向这个对象的类的原型对象
constructor属性的含义就是指向该对象的构造函数，所有函数（此时看成对象了）最终的构造函数都指向Function。

f1.constructor === Foo
```

### 原型链

{% code overflow="wrap" %}
```
当一个对象调用某个成员（属性，方法）首先先查找当前对象实例，没有再查找当前的原型对象，如果还没有，在继续向上层查找，会一直查找到Object类型，如果还没有，直接报错
```
{% endcode %}

获取原型对象的方法

```javascript
//构造函数
function Person(name,age,sex) {
    this.name = name;//属性都添加在构造函数里面
    this.age = age;
    this.sex = sex;
}
//方法都加在原型上
Person.prototype.sayHi = function () {
    console.log("我是原型上面方法");
};

//实例化对象
var per1 = new Person("ZS",18,1);
console.log(per1);

//获取原型对象
console.log(Person.prototype);
console.log(per1.__proto__);
console.log(Object.getPrototypeOf(per1))
```

73\. 作用域链

{% code overflow="wrap" %}
```
一个function中使用一个变量数据，如果当前作用域没有，就会向外层作用域查找，直至查找到全局作用域，要尽量减少对外层作用域的查找，比较耗费资源，尽量用传参来代替作用域查找
```
{% endcode %}

74\. AST（Abstract Syntax Tree）抽象语法树

75\. 浏览器渲染过程概述

```
dom树
css 规则树
渲染树
布局
绘制
光栅化
合成
```

76\. 重绘和回流

```
重绘和回流
·当render tree中的一些元素需要更新属性，而这些属性只是影响元素的外观，风格，而不会影响布局的，比如background-color。则就叫称为重绘。
·当render tree中的一部分(或全部)因为元素的规模尺寸，布局，隐藏等改变而需要重新构建。这就称为回流(reflow）
```

77\. BFC

```
BFC(块级格式化上下文)
用于决定盒子布局以及浮动互相影响范围的一个区域。
Float/position/display 都可以形成
```

78\. 浏览器跨域

```
一个域名下的程序发出一个请求，请求的url是另一个域名下的资源
https://zhuanlan.zhihu.com/p/159060398
```

79\. RN、Weex、Flutter

```
Flutter使用Dart语言，直接编译为本地代码，运行效率高
React Native生态更加强大。丰富的第三方库
```

80\. 浏览器缓存

{% code overflow="wrap" %}
```
强制缓存就是直接从浏览器缓存查找该结果，并根据结果的缓存规则来决定是否使用该缓存的过程。 
控制强制缓存的字段分别是Expires和Cache-Contro
协商缓存就是强制缓存失效后，浏览器携带缓存标识向服务器发起请求，有服务器根据缓存标识决定是否使用缓存的过程 Last-Modified/If-Modified-Since
Etag/If-None-Match
```
{% endcode %}

81\. [节流](https://github.com/lgwebdream/FE-Interview/issues/894)和 防抖

```
防抖：函数防抖是：当你频繁触发后，n秒内只执行一次 如输入
多次触发同一个事件，只执行最后一次操作
节流：在固定的时间内触发事件，每隔n秒触发一次。 如滚动
多次触发同一个事件，只执行第一次操作
```

82\. 闭包

83\. 同步任务和异步任务

{% code overflow="wrap" %}
```
1. 在执行过程中，同步和异步任务分别进入不同的执行场所，同步的是进入主线程，异步的进入Event Table并注册回调函数
2. 指定的事情完成时，Event Tabel 会将这个函数移入Event Quene
3. 主线程内的任务执行完毕时为空，会将Event Queue读取对应函数，进入主线程执行
4. 以上过程重复时，就是Event Loop
```
{% endcode %}

84\. 页面加载过程中发出了哪些事件

```
DOMContentLoaded
onload
Onunload
Readystatechange：在页面的各个状态（包括加载数据等等的过程）发生改变时被触发
```

85\. 宏任务微任务

{% code overflow="wrap" %}
```
异步事件返回结果后会被放到一个任务队列中。然而，根据这个异步事件的类型，这个事件实际上会被对应的宏任务队列或者微任务队列中去。并且在当前执行栈为空的时候，主线程会查看微任务队列是否有事件存在
在代码中的执行顺序，同步任务>微任务>宏任务
```
{% endcode %}

86\. 箭头函数

```
1.箭头函数中的this是在定义函数的时候绑定，而不是在执行函数的时候绑定。
2.导致内部的this就是外层代码块的this。正是因为它没有this，所以也就不能用作构造函数。
3.箭头函数不支持 arguments 对象，但可以通过使用剩余参数（...）来实现类似的功能。
```

87\. 块级作用域

{% code overflow="wrap" %}
```
块级作用域（Block Scope）是 JavaScript 中的一种作用域，它允许变量和常量在代码块（如 {} 包围的代码段）内声明，并且只在该代码块内可见
```
{% endcode %}

88\. Let 和 var

```
Var 声明提升，重复声明，
在全局作用域中使用 var 声明的变量会成为全局对象
let暂时性死区（声明之前不可以访问）
```

89\. 判断数组的方法 和类数组转为数组

```
Instanceof 
Array.isArray()
Object.prototype.toString.call(a) 
a.constructor === Array; 
类数组转为数组
Aarray.from()
Array.prototype.slice.call()
const array = [...arrayLike];
```

90\. 要找到一个对象数组中某个属性的最大值

91\. 伪数组和类数组

{% code overflow="wrap" %}
```
伪数组：通常指的是 arguments 对象，它并不是 Array 的实例，因此不具有数组的方法。
类数组对象：具有 length 属性和索引，但不是 Array 类型的对象。这类对象还包括 NodeList、HTMLCollection 等。
```
{% endcode %}

92\. WebWorker和Service woker

{% code overflow="wrap" %}
```
WebWorker技术是真正意义上的多线程，可以在其他JS脚本运行期间并发同时执行
Service woker基于web worker，不能访问dom
可以拦截当前网站的所有请求，进行判断,如果需要像服务器发起请求就转给服务器，如果可以使用缓存就返回缓存，提高浏览体验。
```
{% endcode %}

93\. Base64/Blob

{% code overflow="wrap" %}
```
Base64是从二进制数据到字符的过程，这些数据需要通过设计用于处理 ASCII 的媒体进行存储和传输。这样是为了保证数据的完整并且不用在传输过程中修改这些数据。
使用 Base64 的好处是能够减少一个图片的 HTTP 请求，然而，与之同时付出的代价则是 CSS 文件体积的增大
Blob
Blob 是浏览器环境中的对象，用于表示不可变的原始数据。常用于处理文件和图像等二进制数据。
```
{% endcode %}

94\. Sso

{% code overflow="wrap" %}
```
SSO的定义是在多个应用系统中，用户只需要登录一次就可以访问所有相互信任的应用系统
SSO 一般都需要一个独立的认证中心（passport），子系统的登录均得通过passport，子系统本身将不参与登录操作
```
{% endcode %}

95\. 数字精度丢失

```
计算机只能用二进制（0或1）表示
0.1 + 0.2 === 0.3 // false
 通过上面的学习，我们知道，在javascript语言中，0.1 和 0.2 都转化成二进制后再进行运算
// 0.1 和 0.2 都转化成二进制后再进行运算
0.00011001100110011001100110011001100110011001100110011010 +
0.0011001100110011001100110011001100110011001100110011010 =
0.0100110011001100110011001100110011001100110011001100111
// 转成十进制正好是 0.30000000000000004
```

97\. TS 新类型

```
枚举 
元祖 元组类型允许表示一个已知元素数量和类型的数组，
Void
Any
```

98\. Async/defer

```
async和defer的相同点

都是异步加载script,加载的过程都不会阻塞html的解析。

不同点

1.async和defer的执行时机不同,async是在加载完后立即执行,执行的过程仍会阻塞后续html的解析。defer是在html解析完,DomCOntentLoaded之前执行。

2.asyny不能保证script标签的执行顺序(谁先加载完谁先执行),await在html解析完之后按顺序执行。

总结

通常情况下defer的使用频率较高,它能保证script之间的变量依赖。

需要注意的是:async script的资源请求时异步的,但script的执行仍然会阻塞后续渲染(单线程),defer是在html渲染完之后执行的所以不会阻塞后续html的解析。
![image](https://github.com/user-attachments/assets/a2e24559-f9ad-423b-aa2f-038458804191)

```

99\. new.target

```
new.target属性允许你检测函数或构造方法是否是通过new运算符被调用的。
```

100\. cookie和session维持登录状态的原理

{% code overflow="wrap" %}
```
cookie机制采用的是在客户端保持状态的方案，而session机制采用的是在服务器端保持状态的方案。
（1）首先用户在客户端浏览器发起登陆请求
（2）登陆成功后，服务端会把用户信息保存在服务端，并返回一个唯一的 session 标识给客户端浏览器。
（3）客户端浏览器会把这个唯一的 session 标识保存在起来（存在cookie中）
（4）以后再次访问 web 应用时，客户端浏览器会把这个唯一的 session 标识带上，这样服务端就能根据这个唯一标识找到用户信息。
```
{% endcode %}

101\. Object.defineProperty 和 Proxy

```
Proxy 是 ES6 引入的一个新特性，它允许你创建一个代理对象，用于拦截对目标对象的操作
Object.defineProperty 是 ES5 中引入的一个方法，用于定义或修改对象的属性，并返回该对象
1.无法监听数组变化
2.只能劫持对象的属性
3.无法检测到对象属性的新增或删除
```

102\. 正向代理和反向代理

```
1.正向代理：如果需要要去访问国外的某些网站，大家可能都会用一个代理工具进行访问
2.正向代理最大的特点是客户端非常明确要访问的服务器地址
3.正向代理，"它代理的是客户端，代客户端发出请求
反向代理的是服务端，代服务端接收请求"
反向代理的作用：
⦁保证内网的安全，通常将反向代理作为公网访问地址，Web服务器是内网
⦁负载均衡，通过反向代理服务器来优化网站的负载
```

103\. 空间复杂度和时间复杂度

### `[0] == true`&#x20;

JavaScript 在进行 `i`<mark style="color:red;">`f(condition)`</mark> <mark style="color:red;"></mark><mark style="color:red;">判断时会将</mark> <mark style="color:red;"></mark><mark style="color:red;">`condition`</mark> <mark style="color:red;"></mark><mark style="color:red;">转换成布尔值然后做判断。</mark><mark style="color:red;">`[0]`</mark> <mark style="color:red;"></mark><mark style="color:red;">是一个非空数组，转换成布尔值是</mark> <mark style="color:red;"></mark><mark style="color:red;">`true`</mark>。但在进行 `==` 比较时，如果类型不同，JavaScript 会尝试将两边转换为相同的类型，通常是数字。具体步骤如下：

```javascript
[0] == true;
// 步骤分解：
// 1. 把 `true` 转化为 `number`，`true` 变成 `1`
[0] == 1;
// 2. 数组 `[0]` 是对象，先尝试 `.valueOf()`，结果还是 `[0]`
// 3. 再尝试 `.toString()`，结果是 `"0"`，类型是 `string`
"0" == 1;
// 4. 把 `"0"` 字符串转化为 `number`，`"0"` 变成 `0`
0 == 1; // 结果是 `false`
```

### `== 和 ===`

<mark style="color:red;">`==`</mark> <mark style="color:red;"></mark><mark style="color:red;">和</mark> <mark style="color:red;"></mark><mark style="color:red;">`===`</mark> <mark style="color:red;"></mark><mark style="color:red;">的主要区别在于是否进行类型转换</mark>

* **`==`（相等运算符）**：在进行比较时会进行类型转换，它会尝试将两个比较的值转换为相同的类型，然后再进行比较。
* **`===`（严格相等运算符）**：在进行比较时不会进行类型转换，也称为严格相等。只有当两个值的类型和值都完全相同时，才会返回 `true`。

```
console.log(null == undefined); 
// 输出: true，在使用 == 时，null 和 undefined 被视为相等
console.log(null === undefined); 
// 输出: false，因为使用 === 时，不会进行类型转换，null 和 undefined 类型不同
```

### \[] ==\[] 为 false；\[] == !\[] 为 true

{% code overflow="wrap" %}
```
！可将变量转换成boolean类型，null、undefined、NaN以及空字符串('')取反都为true，其余都为false
![]变成!Boolean([]), 也就是!true, 也就是false。
对比 [] == false;
对比[] == 0;
对比 "" == 0;
对比Number("") == 0

在JS中，数组是属于引用型数据类型，所以“==”左右两边所表示的实际只是数组的所在的地址而已。在创建一个新数组时，其地址均不相同，因此[]==[]最终返回false 对象的相等是对引用的判断
```
{% endcode %}

105\. 判断NaN (object.is(NaN, NaN)

```
ES6 引入了 Object.is() 方法，它可以用来比较两个值是否严格相等，
```

107\. cookie和token都存放在header里面，为什么只劫持前者

```
CSRF攻击的原因是浏览器会自动带上cookie，而不会带上token；
```

108\. CSRF（跨站请求伪造 Cross-site request forgery）和XSS（跨站脚本攻击Cross Site Scriptin）

109\. 浏览器是怎么对HTML5的离线储存资源进行管理和加载的呢

```
Service Workers 来实现更强大和灵活的离线功能。
```

110\. Web攻击技术

111\. ES6中Class的基本语法及与ES5中Class的区别

```
ES6 Class 是语法糖：
本质上，Class 是对 ES5 构造函数和原型链的封装，简化了语法。
代码风格和可读性：
Class 提供了更清晰、简洁和面向对象的语法，更适合现代开发。
功能更强大：
支持继承、静态方法、访问器等，增强了代码复用性和功能扩展性。
```

112\. [ES6](https://so.csdn.net/so/search?q=ES6\&spm=1001.2101.3001.7020)模板字符串

113\. JavaScript设计为单线程

```
简化并发问题
避免了多线程并发时的竞态条件、死锁和其他问题
多线程操作dom容易产生问题
避免浏览器环境限制
会涉及到线程之间的通信，而这会增加浏览器的复杂度和资源消耗。
```

114\. Foreach return

```
⦁forEach()中的return只会结束此次循环，执行下一次循环
⦁for中的return会直接跳出循环，不再执行下面的循环
```

115\. 函数缓存

```
闭包
柯里化
高阶函数
```

116\. tostring报错

{% code overflow="wrap" %}
```
在JavaScript中，如果你尝试将一个数值（如21）通过`.toString()`方法转换为字符串，通常会得到一个错误。这是因为JavaScript中的点符号“.”通常用于表示字符串连接符，而不是访问某个对象的方法。当你直接写`21.toString()`或者`21.toSring()`时，JavaScript会认为你想要进行字符串连接操作，因此不会调用任何方法。但是，如果你写成`(21).toString()`或者`21..toString()`，其中的小数点“.”会被正确地识别为一个表达式的组成部分，即访问该变量的toString方法。这样就不会报错了。
```
{% endcode %}

117\. 构造函数和普通函数的区别

```
调用方式的区别：
this的指向问题： 
    a 构造函数的this会被绑定到创建的对象的实例上。
    b 普通函数的this指向调用函数的对象，若没有调用的对象，默认的是window。
命名方式：
返回值:
   构造函数：如果构造函数没有显式返回任何对象，则默认返回新创建的对象。如果显式返回了一个对象，则返回该对象；但如果返回的是一个原始类型（如字符串、数字等），则忽略返回值，依旧返回新创建的对象。
function SpecialThing() {
    this.value = 42;
    return { special: 'value' }; // 返回自定义对象
}
const obj = new SpecialThing(); // obj 是 { special: 'value' }
普通函数：可以返回任意类型的值，包括原始类型和对象。如果不显式指定返回值，默认返回 undefined

https://zhuanlan.zhihu.com/p/27093821
```

118\. 内存溢出的场景和原因有哪些？

120\. 如何检测对象是否循环引用？

{% code overflow="wrap" %}
```
使用 WeakSet
通过递归遍历对象，并利用 WeakSet 存储已经访问过的对象。如果某个对象被多次访问，则说明存在循环引用。
function hasCircularReference(obj) {
  const seenObjects = new WeakSet();

  function detect(obj) {
    if (typeof obj !== 'object' || obj === null) {
      return false;
    }
    if (seenObjects.has(obj)) {
      return true;
    }
    seenObjects.add(obj);
    for (const key in obj) {
      if (Object.prototype.hasOwnProperty.call(obj, key)) {
        if (detect(obj[key])) {
          return true;
        }
      }
    }
    return false;
  }

  return detect(obj);
}

// 示例
const a = {};
const b = { a };
a.b = b;

console.log(hasCircularReference(a)); // true

方法 2：尝试 JSON 序列化
使用 JSON.stringify 检测，如果对象有循环引用，JSON.stringify 会抛出错误。
function hasCircularReference(obj) {
  try {
    JSON.stringify(obj);
    return false; // 没有循环引用
  } catch (error) {
    if (error.message.includes('circular')) {
      return true; // 存在循环引用
    }
    throw error; // 其他错误需要抛出
  }
}

// 示例
const a = {};
const b = { a };
a.b = b;

console.log(hasCircularReference(a)); // true
```
{% endcode %}

121\. 前端项目工程的自动化部署

```
1选择 CI/CD 工具(Teamcity)
2设计 Git 流程
分支的设计/代码合并
3.创建 CI/CD Pipeline
3.1 构建阶段
1.拉取代码：从 Git 仓库拉取最新的代码。
2.安装依赖：使用 npm install 或 yarn 安装项目依赖。
3.构建项目：使用 npm run build 或类似命令构建项目。
3.2 测试阶段
1.运行单元测试：使用 Jest、Mocha 等测试框架运行单元测试。
2.运行端到端测试：使用 Cypress 或 Selenium 进行端到端测试。
3.检查代码风格：使用 ESLint 检查代码风格和格式。
4.代码覆盖率报告：生成代码覆盖率报告，并确保达到预定的覆盖率标准。
3.3 部署阶段
1.打包和压缩：将构建好的文件打包和压缩，减少传输时间。
2.上传到服务器：将打包后的文件上传到生产环境的服务器。
3.部署到 CDN：如果使用 CDN，将静态资源上传到 CDN。
4.通知部署完成：通过 Slack、邮件等方式通知团队成员部署已完成。
4. 配置环境变量
```

122\. 页面请求接口大规模并发问题

```
1.服务器端优化：使用负载均衡、数据库优化、缓存层、异步处理等技术手段。
2.客户端优化：实现请求节流与限流、请求合并、本地缓存等机制。
3.服务端优化：引入消息队列、无状态服务、微服务架构等设计理念。
4.监控与告警：建立监控系统和告警机制，确保系统的稳定运行。
```

123\. script 预加载方式

{% code overflow="wrap" %}
```
<link rel="preload"> 是一种现代的预加载技术，它允许浏览器尽早开始加载特定的资源，如 JavaScript 文件、字体、图片
<link rel="prefetch"> 类似于 <link rel="preload">，但它主要用于那些不是立即需要的资源
Service Worker 可以拦截网络请求并缓存资源

目的不同：
preload 旨在尽早加载当前页面中必需的资源，以加快页面加载速度和响应时间。
prefetch 更倾向于优化未来的导航或操作，预先获取那些暂时不需要但将来可能需要用到的资源。
优先级不同：
浏览器会给予 preload 资源更高的优先级，因为它们对当前页面至关重要。
相比之下，prefetch 请求的优先级较低，浏览器只会在空闲时处理这些请求。
时机不同：
preload 在浏览器开始解析文档时就会触发资源下载。
prefetch 可能在页面加载完成之后，当浏览器认为有足够的网络带宽时才会执行。
```
{% endcode %}

124\. 如何优化大规模 dom 操作的场景？

```
缓存 DOM 对象，避免重复查询。
使用文档片段（Document Fragment）进行批量操作，减少重绘和重排。
使用虚拟 DOM 来进行高效的 DOM 操作。

```

125\. 类组件也是function

{% code overflow="wrap" %}
```
class MyClass {  constructor() {    // 构造函数  }
  myMethod() {    // 方法  }
}
console.log(typeof MyClass); // 输出 "function"
无论是类组件还是函数组件，在 JavaScript 中它们都是函数类型。类组件是一个构造函数，而函数组件是一个普通的函数
```
{% endcode %}

126\. cookie 构成部分有哪些

{% code overflow="wrap" %}
```
1. 名称 (Name)
描述：用于标识 Cookie 的唯一性。
示例：name=value 中的 name。
2. 值 (Value)
描述：存储在 Cookie 中的实际数据。
示例：name=value 中的 value。
3. 域名 (Domain)
描述：指定 Cookie 适用于哪些域名。
示例：Domain=.example.com 表示该 Cookie 适用于 .example.com 及其子域。
4. 路径 (Path)
描述：指定 Cookie 应用于网站中的哪些 URL 路径。
示例：
Path=/ 表示该 Cookie 适用于根目录下的所有页面。
默认情况下，Cookie 仅适用于设置它的页面所在的目录及其子目录。
5. 过期时间 (Expires 或 Max-Age)
描述：指定 Cookie 的过期时间。
说明：
Expires：设定具体的日期和时间。
Max-Age：设定相对当前时间的秒数（存活时间）。
未指定过期时间时，Cookie 为会话 Cookie，浏览器关闭时过期。
6. 安全标志 (Secure)
描述：指定 Cookie 是否只能通过 HTTPS 协议传输。
说明：
如果设置了 Secure，Cookie 只能在 HTTPS 连接下发送。
7. HTTPOnly 标志
描述：指定 Cookie 是否只能通过 HTTP 协议访问。
说明：
如果设置了 HttpOnly，JavaScript 无法访问该 Cookie，从而增加安全性（防止 XSS 攻击）。
```
{% endcode %}

127\. F5

{% code overflow="wrap" %}
```
它是一个多功能的应用交付控制器（ADC），可以实现负载均衡、SSL 加密、Web 应用防火墙等多种功能。
```
{% endcode %}

129\. 锁

{% code overflow="wrap" %}
```
锁（Locks）通常用于多线程或多进程编程中，以确保多个线程或进程在同时访问共享资源（如内存中的数组）时不会发生冲突。
```
{% endcode %}

130\. 去重

```
[...new Set(array)];
Array.from(new Set(array));
const array = [1, 2, 2, 3, 4, 4, 5];

const uniqueArray = array.filter((value, index, self) => {
  return self.indexOf(value) === index;
});
console.log(uniqueArray); // 输出 [1, 2, 3, 4, 5]
```

131\. 数组转为字符串 和字符串转为数组

```
1 arr.join() 和
2 arr.toString()
3 reduce() 方法来手动拼接字符串。
const arr = ["Hello", "world", "!"];const str = arr.reduce((acc, curr) => acc + " " + curr, "").trim();
console.log(str); // 输出 Hello world !

Obj.Split(“”)
[...objt]
Array.from(str)
```

132\. RXJS

{% code overflow="wrap" %}
```
RxJS是Angular中一种用于异步编程的库。它基于响应式编程模型，可以帮助您处理异步数据流和事件流。您可以使用Observable和操作符来创建和转换数据流，使用Subject和BehaviorSubject来创建和处理事件流，以及使用Subscription来管理订阅。
```
{% endcode %}

133\. Angular 数据传输

134\. Angular依赖注入

135\. Angular 装饰器

```
类装饰器
@component
@injectable
@derective
属性
@input
@output
方法
@hostlistner
```

136\. Observables和Promises的核心区别是什么？

```
Promises：
返回单个值
不可取消
Observables：
可以使用多个值
可取消
支持map，filter，reduce和类似的操作符
ES 2016提议的功能
使用反应式扩展（RxJS）
根据时间的变化，数组成员可以异步获取
```

137\. 事件驱动（Event-driven）和数据驱动（Data-driven

138\. IntersectionObserver

{% code overflow="wrap" %}
```
用于检测元素是否进入视口（即是否可见）。new IntersectionObserver 构造函数用于创建一个新的 IntersectionObserver 实例，并传入一个回调函数来处理观察到的元素的变化
```
{% endcode %}

139\. 图片加载不出来

{% code overflow="wrap" %}
```
图片地址/图片不存在/浏览器缓存了旧的或损坏的图片/图片格式问损坏/跨域资源共享（CORS）限制导致图片无法加载
```
{% endcode %}

140\. CDN的维护者如何防止自己的图片被别人使用

```
配置防盗链：
1基于 Referer 头：
   配置 CDN 以检查请求的 Referer 头。只有来自特定域名的请求才会被允许。
   基于 IP 地址
2使用token 验证
3  限制下载速度和频率
```

141\. setInterval 的定时可能不准确

```
主要包括事件循环的影响、回调函数的执行时间
```

142\. JSBridge

```
URL Scheme 和 JavaScript Injection
```

143\. EventEmitter

{% code overflow="wrap" %}
```
EventEmitter 是 Angular 中实现父子组件之间通信的一种强大工具。它基于观察者模式，通过 Subject 类实现了事件的发射和监听。通过 @Output 装饰器和模板绑定
```
{% endcode %}

144\. 数据一致性和性能之间

```
，数据一致性和性能之间的权衡需要综合考虑多种技术和策略。通过数据库优化、分布式事务、消息队列、限流和降级、数据一致性模型、分布式锁以及代码层面的优化
```

146\. 面向对象

```
面向对象编程可以看作是使用一系列对象相互协作的软件设计
特性 封装继承多态性
类：抽象上的定义，不具体代表任何一个实例
对象：必须是一个具体的对象实例	
```

148\. Angular 的变更检测

```
Angular 的变更检测是基于事件驱动的
⦁用户输入（如点击、键盘输入等）
⦁AJAX 请求完成
⦁定时器到期（如 setTimeout、setInterval）
⦁自定义事件
变更检测策略（changeDetection）
1默认策略是最常用的策略，Angular 会自动检测组件及其子组件的所有变化
2 OnPush 策略
OnPush 策略是一种优化策略，适用于纯组件（即组件的输出只依赖于输入）。启用 OnPush 策略后，Angular 只在以下情况下才会检查组件的变更：
组件的输入属性发生变化
组件接收到新的事件（如 @Input、@Output）
组件内的异步操作完成（如 Promise、Observable）
要启用 OnPush 策略，可以在组件装饰器中设置 changeDetection 属性：
```

149\. 浏览器的兼容性

150\. Polyfill是一种

```
提供了原生的替代实现，使得现代Web特性在老旧浏览器中得以正常工作
1 手动补丁
2 bable 
```

151\. Csp

```
Angular 应用程序的 index.html 文件中使用 meta 标签来配置 CSP：
unsafe-inline' 允许内联脚本和样式
```

152\. position:sticky

153\. manifest.json

{% code overflow="wrap" %}
```
manifest.json 文件通常用于 web 应用、浏览器扩展和 Node.js项目中，用于包含项目或应用的元数据。
```
{% endcode %}

154.New 和 Object.create

```
New操作符
New操作符创建一个空对象，用this指代该对象。同时继承该函数的原型。属性和方法被加到this 
（1）首先创建了一个新的空对象

（2）设置原型，将对象的原型设置为函数的 prototype 对象。

（3）让函数的 this 指向这个对象，执行构造函数的代码（为这个新对象添加属性）

（4）判断函数的返回值类型，如果是值类型，返回创建的对象。如果是引用类型，就返回这个引用类型的对象。用的对象中。

function myNew(Fn,...args){
   var obj = {};
   var result = Fn.apply(obj, args);
   obj.__proto__ = Fn.prototype;
   return result instanceof Object ? result : obj;

}

function Person(name, age) {
  this.name = name;
  this.age = age;
}

const p1 = myNew(Person, "Alice", 25);
console.log(p1.name); // Alice
console.log(p1.age);  // 25
console.log(p1 instanceof Person); // true



构造函数：如果构造函数没有显式返回任何对象，则默认返回新创建的对象。如果显式返回了一个对象，则返回该对象；但如果返回的是一个原始类型（如字符串、数字等），则忽略返回值，依旧返回新创建的对象。
function SpecialThing() {
    this.value = 42;
    return { special: 'value' }; // 返回自定义对象
}
const obj = new SpecialThing(); // obj 是 { special: 'value' }
```

```
function myCreate(proto) {
  if (typeof proto !== "object" || proto === null) {
    throw new TypeError("Prototype must be a non-null object");
  }
  
  function F() {}  // 创建一个空的构造函数
  F.prototype = proto; // 让它的 prototype 指向传入的 proto
  return new F();  // 通过 new 关键字创建新对象
}

const person = {
  greet: function() {
    console.log("Hello, I am " + this.name);
  }
};

const obj = myCreate(person);
obj.name = "Alice";
obj.greet(); // Hello, I am Alice

console.log(obj.__proto__ === person); // true


```

155\. 前端资源加载失败优化

```
1. 加载失败时，刷新页面 (reload）

2.针对加载失败的文件进行重加载


1.我们可以给 script 标签添加上 onerror 属性，这样在加载失败时触发事件回调，从而捕捉到异常。

借助构建工具 ( 如 webpack 的 script-ext-html-webpack-plugin 插件) ，我们可以轻易地完成对所有 script 标签自动化注入 onerror 标签属性

window.addEventListener
因为 onerror 的事件并不会向上冒泡，window.onerror 接收不到加载失败的错误。冒泡虽不行，但捕获可以！我们可以通过捕获的方式全局监控加载失败的错误，虽然这也监控到了脚本错误，
但通过 !(event instanceof ErrorEvent) 判断便可以筛选出加载失败的错误。
window.addEventListener('error', function(event) {
    // 检查是否为资源加载错误而非脚本运行时错误
    if (!(event instanceof ErrorEvent)) {
        console.log('加载资源出错:', event.target.src || event.target.href);
    }
}, true); // 'true' 表示使用捕获模式
```

### Object.defineProperty 的缺陷

1. **无法检测到对象属性的新增或删除**：
   * 使用 `Object.defineProperty` 不能监听到对象上新添加的属性或者删除现有属性的变化。这意味着，如果你想监控对象的所有变更，你需要预先知道所有可能的属性，并为每个属性设置访问器（getter/setter）。
2. **无法监听数组变化**：
   * 虽然可以通过 `Object.defineProperty` 来定义数组元素的 getter 和 setter，但这不会影响数组原生的方法（如 `push`, `pop`, `shift`, `unshift`, `splice` 等），因此直接修改数组长度或使用这些方法时，无法触发自定义的 getter 或 setter。
3. **只能劫持对象的属性，从而需要对每个对象、每个属性进行遍历**：
   * 如果属性值是对象，则还需要深度遍历这些对象以确保所有嵌套的对象属性都被正确地设置了 getter 和 setter。这使得处理深层次的数据结构变得复杂且耗时。

### 在 Map 中和 For 循环中调用异步函数的区别

* **Map 中的异步调用**：由于 `map()` 方法中的异步调用可以并发执行，所以它们的结果顺序可能与它们在数组中的顺序不同。

```
const arr = [1, 2, 3];

const result = arr.map(async (num) => {
  const res = await fetch(`https://jsonplaceholder.typicode.com/todos/${num}`);
  return res.json();
});

console.log(result); // ❌ [Promise, Promise, Promise]
问题： map() 直接返回 Promise 数组，无法直接获取异步结果。
```

* **For 循环中的异步调用**：使用 `for` 循环配合 `await` 关键字可以确保每个异步操作按顺序执行。当前一个异步操作完成后才会开始下一个操作，保证了顺序性。

### 遍历机制

* **`for in`**：可以遍历对象/数组/字符串，遍历的是索引或对象的属性名。
* **`for of`**：只能遍历数组或类数组对象（可迭代对象），遍历的是值，不能直接遍历普通对象（除非对象实现了 Iterator 接口）。

### Map 和 forEach 的区别

* **`map()`**：会返回一个新的数组，包含通过回调函数处理后的结果。
* **`forEach()`**：没有返回值（返回 `undefined`），主要用于遍历数组并对每个元素执行某些操作，但不会生成新的数组。

### 异步支持

* **`forEach` 和 `map`**：作为同步函数，不支持 `await` 语句；尝试在其中使用 `break` 或 `continue` 会导致错误。
  *   **`forEach()` 和 `map()` 不是循环结构**

      * `forEach()` 和 `map()` **是回调函数的执行方式**，并不是像 `for` 一样的循环体，不能使用 `break` 和 `continue`。

      #### **`forEach()` 不能中途退出**

      * `forEach()` 没有返回值，不能中途跳出，**必须执行所有元素的回调**。
      * 但可以使用 `return` 退出当前回调，**只是跳过当前元素，不会跳出整个遍历**。

      #### **`map()` 需要返回值**

      * `map()` 的回调函数需要返回一个值用于创建新数组。
      * `continue` 需要在 **循环体中使用**，而 `map()` **不是循环结构**，会导致 `SyntaxError`。
* **`for` 循环、`for...in`, `for...of`**：支持 `await`，允许在循环体内等待异步操作完成。`for` 和 `for...of` 可以使用 `break` 和 `continue` 控制流，而 `for...in` 对 `continue` 和 `break` 的支持较弱，可能会被忽略。



### WebWorker多线程

CPU在同一时刻只能运行一个程序（单核），一个应用程序可以由多个独立的进程组成，一个进程可以由多个独立的线程组成，线程是独立的程序的最小单位，可以并发执行。

没有DOM访问权限：Web Worker 在创建时没有访问 DOM 的权限。 需要额外的代码编写。 不支持跨域请求。 JS默认没有真正意义上的多线程，setTimeout setInterval只是模拟了多线程，并不能真正并发执行。如果JS有任何代码在执行中，定时器语言必须等待JS代码执行结束才能执行。

WebWorker技术是真正意义上的多线程，可以在其他JS脚本运行期间并发同时执行，不影响页面效率，一般我们会把比较耗费时间的代码放到WebWorker里面运行。

需要注意：WebWorker代码不能直接操作dom，需要把值回传给主程序（调用postMessage方法），通过主程序的回调函数（onmessage）来操作dom。

### 推送（push）

服务器在客户端没有主动发出请求的情况下，向客户端发送消息。

#### 常见的WEB推送技术

1. 长连接技术，推翻http协议的无状态的特征，不是请求响应结束之后立即断开连接，而是客户端浏览器和服务器端一直保持连接，缺点就是服务器压力太大。
2. HTML5的服务器发送事件（server-sent event），属于单向服务器发送数据到客户端，注意响应的数据格式必须是data:xxxx，否则客户端无法接收。SSE最大的特点就是不需要客户端发送请求，可以实现只要服务器端数据有更新，就可以马上发送到客户端。
3. 短轮询：用定时器每隔一段时间发送一个ajax请求，得到响应，兼容性最好，缺点是耗费很多无用的ajax请求浪费资源，响应的及时性不如真正的推送技术。
4. 长轮询:客户端向服务器发送Ajax请求，服务器接到请求后hold住连接，直到有新消息才返回响应信息并关闭连接，客户端处理完响应信息后再向服务器发送新的请求。
5. WebSocket双向通信的推送，需要服务器本身支持，客户端需要使用WS协议发送请求。完成一次握手的动作，浏览器和服务器就可以创建持久性的链接。
6. WebRTC: WebRTC (Web Real-Time Communications) 是一项实时通讯技术，它允许网络应用或者站点，在不借助中间媒介的情况下，建立浏览器之间点对点（Peer-to-Peer）的连接，实现视频流和（或）音频流或者其他任意数据的传输。WebRTC包含的这些标准使用户在无需安装任何插件或者第三方的软件的情况下，创建点对点（Peer-to-Peer）的数据分享和电话会议成为可能。

### 三层架构

分为:表现层(UI)、业务逻辑层(BLL)、数据访问层。

### MVC

M：model，模型层。代表数据库中的数据源。 V：view，视图层。代表能看到的客户端页面，是根据模型数据创建的。 C：controller，控制层。代表业务逻辑，是模型层和视图层之间的桥梁，建立两者之间的关系。控制层本身是不会处理数据以及输出数据，他只是用来接受请求并决定用那个模型去处理请求，然后再确定用哪个视图来显示返回的数据。

### MVVM

模型视图 视图模型。 数据模型数据双向绑定 的思想为核心。 ViewModel：视图模型，可实现数据的双向绑定，连接View和Model的桥梁，当数据变化时，ViewModel能够监听到数据的变化（通过Data Bindings），自动更新视图，而当用户操作视图，ViewModel也能监听到视图的变化（通过DOM Listeners），然后通知数据做改动，这就实现了数据的双向绑定。

### 跨域请求

一个域名下的程序发出一个请求，请求的URL是另一个域名下的资源，这就是跨域请求，所有的浏览器默认都不允许ajax跨域请求。

不同的域、协议或端口请求一个资源时，资源会发起一个跨域 HTTP 请求。

#### 同源策略的限制包括以下几个方面：

1. **Cookie、LocalStorage和IndexDB等存储在浏览器中的数据**只能被同源网页访问，不能被其他源的网页访问。
2. **XMLHttpRequest和Fetch等网络请求API**只能向同源网址发送请求，不能向其他源发送请求。这样可以防止跨域请求攻击。
3. **DOM操作**也受到同源策略的限制。一个网页只能修改同源网页的DOM，不能修改其他源的DOM。

#### 解决跨域问题的方案

* **在服务器端增加允许跨域请求的响应报头**，设置`Access-Control-Allow-Origin`为`*`。
* **利用jsonp技术**，前端和后端配合实现跨域ajax。所谓的jsonp技术，就是利用浏览器并不禁止外联js是另一个域的js，所以jsonp跨域请求原理就是`<script src="其他域的js">`，而被外联的js是一个函数调用语句，通过实参把数据传递给请求客户端。动态创建script标签，只支持GET请求；存在脚本注入以及跨站请求伪造等安全问题。
* **Postmessage：**&#x8BA9;不同源的 `iframe` 或 `window` 传递消息。
* **Node 中间层代理**
* **Nginx反向代理**
* **Websocket**

### SSR (Server-Side Rendering)

**定义**：将组件或页面通过服务器生成HTML字符串，再发送到浏览器，最后将静态标记“混合”为客户端上完全交互的应用程序。

**优点**：

* 更有利于SEO和首屏渲染。

**缺点**：

* 服务器压力大。
* 开发调试复杂，因为涉及了浏览器及服务器，对于SPA的一些组件的生命周期管理会变得复杂。
* 第三方库使用受限。
* 学习成本较大。

**框架示例**：

* **Nuxt.js**：Vue的SSR方案。
* **Next.js**：一个轻量级的React服务端渲染应用框架。

### SPA (Single Page Web Application)

**工作原理**

1. **用户访问网站时，服务器返回一个基础 `index.html` 页面**。
2. **JavaScript 框架（如 React / Vue / Angular）加载，并通过路由管理页面**。
3. **用户操作（点击链接、切换页面）时，不会触发页面刷新，而是通过 JavaScript 更新内容**。
4. **与服务器的通信采用 AJAX 或 Fetch 请求，仅加载必要的数据**，提高性能。
5. **通过前端路由（如 `react-router` / `Vue Router`）模拟不同页面，使得 URL 变化但不触发页面刷新**。

**优点**：

* 服务器压力小。
* 响应速度快。

**缺点**：

* 不利于SEO。
* 初次加载耗时相对增多。
* 导航不可用，如果一定要导航需要自行实现前进、后退功能。

**页面路由**：通过Ajax+历史记录进行动态页面切换。

**Angular 和 React 主要用于构建单页面应用，但通过Webpack 配置多个入口点，它们也可以很好地支持多页面应用**

### MPA (Multi-Page Application)

**定义**：指一个网站或应用包含多个独立的页面，每个页面都有自己的URL和独立的HTML文件。每次页面跳转时，后台服务器都会给返回一个新的HTML文档。

**优点**：

* 首屏时间快：只经历一次请求。
* 搜索引擎优化效果好：搜索引擎在做网页排名的时候，要根据网页内容才能给网页权重。

**缺点**：

* 切换慢：每次页面跳转都需要重新加载整个页面，包括资源文件（如CSS和JavaScript），这可能导致用户等待时间增加。

### SEO考虑

* **SSR对SEO的优势**：SSR提高了更好的SEO效果。在传统的客户端渲染中，搜索引擎的爬虫只能获取到一个空白的HTML页面，因为实际内容是通过JS动态渲染，这样导致搜索引擎无法正确地进行索引和分析。而通过SSR，搜索引擎获得完整的已渲染页面HTML，这使得搜索引擎能够更好地理解网页内容，提高爬取效率，并有利于网页的排名。\


**JS 引擎主要由两部分组成：**

* **内存堆**\
  内存堆是用于动态分配内存的区域，所有对象、数组和函数等数据都存储在这里。当我们创建变量或对象时，这些数据会分配到内存堆中。
* **调用栈**\
  调用栈是一个数据结构，用于记录程序中正在执行的函数调用。JavaScript 的执行环境只有一个调用栈，这也意味着 JavaScript 是单线程语言，同一时间只能执行一个任务。调用栈按顺序记录当前代码执行的位置，当一个函数被调用时，它会被压入调用栈，函数执行完毕后，再从栈中弹出。

**总结：**

* 由于 JavaScript 只有一个调用栈，因此它只能按顺序执行任务，这也解释了为什么 JavaScript 是单线程的。
* 内存堆用于存储数据，而调用栈则用于记录代码执行的流程和函数调用顺序。这两者协同工作，使得 JavaScript 能够高效地执行代码，同时借助异步编程和事件循环机制来处理并发任务。

### 如何避免内存泄漏？

* **严格声明变量**：始终使用 `let`、`const` 或 `var` 声明变量，避免无意中创建全局变量。这个全局对象通常是 `window`
* **及时清理定时器和回调**：在组件卸载或任务完成后，记得调用 `clearInterval`、`clearTimeout` 等清理定时器。
* **注意闭包的使用**：避免在闭包中引用过多不再需要的数据；在不再需要时，解除引用。
* **管理 DOM 引用**：在移除 DOM 元素前，移除所有与之关联的事件监听器或回调引用。
* **合理设计缓存策略**：使用缓存时，设置合理的失效策略或定期清理缓存中的无用数

