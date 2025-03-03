# Js

## JS的数据类型划分

### 基本数据类型（原始数据类型）

* undefined 一般表示变量已声明但未赋值，或者函数没有返回值

```typescript
function printMessage(message: string): void {
    console.log(message);
    // 没有 return 语句
}

const messageResult = printMessage('Hello, TypeScript!');
console.log(messageResult); 
// 输出: undefined
```

* null 空对象（代表空指针）

```
typeof null 返回 "object"
```

* number 数字型 例如 100 3.14
* string 字符型（字符串）常量需要用双引号或者单引号括起 例如 "中国" 'abcd' 空字符串"" ''
* boolean 布尔值 就两个取值 true代表真 false代表假

### 引用数据类型

* object 对象类型

Js的数据类型是弱类型，不是像java那种强类型，变量必须显示的声明数据类型

* 强类型举例： int i = 10; String s = "abcd"; double d = 3.14;
* 弱类型举例：var i = 10; var s = "abcd"; var d = 3.14;

`instanceof`运算符： 对象 `instanceof` 类 ，如果对象是这个类创建的，返回true，否则为false

### 值类型和引用类型

**值类型：**

1. 保存与复制的是值本身
2. 使用typeof检测数据的类型
3. 基本数据类型是值类型

**引用类型：**

1. 保存和赋值的是指向对象的一个指针。
2. 使用instanceof检测数据类型
3. 使用new()方法构造出的对象是引用型

## JavaScript 中的变量在内存中的具体存储形式是什么

原始类型包括但不限于 number、string、boolean、null、undefined 和 bigint（ES10 引入的新类型）。这些类型的值在内存中直接存储。

* 原始类型的值直接存储在栈内存中。
* 访问速度快，因为栈内存具有连续性和固定大小的特点。
* 引用类型包括但不限于 object、array 和 function。引用类型的变量实际上存储的是指向实际对象的引用（地址）

### 堆内存（Heap Memory）

* 存储引用类型：引用类型的对象存储在堆内存中。
* 优点：存储空间大，适合存储大对象。

### 栈（Stack）

* 栈区：只存储局部变量（也包括全局变量）
* 堆区：只存储对象的实例，每个对象实例在堆中都会自动产生一个唯一的内存地址

### 堆和栈的区别

1. 数据结构：栈和堆都是内存数据结构，栈是一种线性结构，堆是一种树形结构。
2. 内存分配方式：栈采用的是静态内存分配，系统在编译阶段就确定了分配给栈的内存空间，而且栈内存的释放是由系统自动完成的。而堆采用的是动态内存分配，程序在运行时可以向操作系统请求动态分配一段空间，然后使用完之后再手动释放。
3. 存储内容：栈中存储的是函数的调用和局部变量，而堆中存储的是对象的实例。
4. 访问方式：栈是一种后进先出（LIFO）的数据结构，只有栈顶的元素可以被访问和操作，而堆是一棵树形结构，其存储的元素可以被通过指针或引用访问。
5. 内存分配效率：栈的内存分配效率比较高，因为栈是一种内存结构，其内存块相互紧凑，可以直接通过指针操作，而堆的内存分配效率相对低一些，因为需要手动分配和释放内存空间。

对象之间赋值赋的是引用地址，基本类型赋值赋的是值本身。

### 同步任务 和异步任务

1. 在执行过程中，同步和异步任务分别进入不同的执行场所，同步的是进入主线程，异步的进入Event Table并注册函数
2. 指定的事情完成时（如定时器到期、Promise 解析完成），Event Tabel 会将这个函数移入Event Quene
3. 主线程内的任务执行完毕时为空，会将Event Queue读取对应函数，进入主线程执行
4. 以上过程重复时，就是Event Loop

<figure><img src=".gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

* 不同类型的异步任务会进入各自的队列：Promise、process.nextTick 进入微任务队列，而 setTimeout、setInterval 和（在 Node.js 中）setImmediate 则属于宏任务。

```
console.log('script start');

setTimeout(() => {
    console.log('setTimeout 1');
}, 0);

Promise.resolve().then(() => {
    console.log('Promise 1');
}).then(() => {
    console.log('Promise 2');
});

setTimeout(() => {
    console.log('setTimeout 2');
}, 0);

console.log('script end');
```

* **执行同步代码**
  * `console.log('script start')`
  * `setTimeout 1` 被加入 **宏任务队列**
  * `Promise 1` 加入 **微任务队列**
  * `Promise 2` 加入 **微任务队列**
  * `setTimeout 2` 被加入 **宏任务队列**
  * `console.log('script end')`
* **执行微任务**
  * 执行 `Promise 1`
  * 执行 `Promise 2`
* **执行宏任务（第一个 `setTimeout`）**
  * `console.log('setTimeout 1')`
* **执行宏任务（第二个 `setTimeout`）**
  * `console.log('setTimeout 2')`



### `setTimeout` 推迟执行

1. **事件循环延迟**：JavaScript 运行在一个单线程环境中，这意味着在同一时间内只能执行一个任务。如果在调用栈中有其他长时间运行的任务正在执行，那么即使定时器到期了，回调也不会立即执行，而是要等到当前任务完成后才会被处理
2. **浏览器最小时间间隔**：大多数浏览器对 `setTimeout` 和 `setInterval` 的最小时间间隔有一个下限，通常是 4 毫秒。这意味着即使你设置了小于 4 毫秒的时间间隔，浏览器也会将其调整为至少 4 毫秒
3. **累积误差**：对于 `setInterval` 来说，由于每次回调执行所需的时间可能不同，这会导致累积误差，即后续的执行可能会越来越偏离预期的时间点
4. **页面不可见时的行为**：当页面处于后台或标签页不可见时，浏览器为了节省资源，可能会暂停或减缓定时器的执行频率
5. **跨浏览器兼容性和实现差异**

JavaScript 运行在**单线程**环境下，所有任务都要排队执行。如果**主线程有任务执行时间过长**，`setTimeout` 可能会被推迟执行。

```
setTimeout(() => console.log("⏳ 这里应该在 1 秒后执行"), 1000);

// 模拟主线程任务执行 3 秒
const start = Date.now();
while (Date.now() - start < 3000) {}
console.log("主线程任务完成");
```

* `setTimeout(1000)` 计划在 1 秒后执行
* 但主线程被 `while` 循环**阻塞 3 秒**，所以 `setTimeout` 只能等到主线程空闲后才执行。

解决方案

1. Web Worker **不受 UI 线程阻塞**的影响，可以更稳定地执行定时任务
2. 使用 `Date.now()` 计算实际误差，并调整下一次 `setTimeout`，可以减少累积误差。



## 原型prototype

<mark style="color:red;">每个类型（例如Date,Error,Array,Number......包括自定义类型例如Person）都有一个属性，这个属性的名字叫做prototype，这个prototype指向一个对象，这个对象就是这个类的原型对象</mark>，里面默认有两个属性，constructor，_proto_，如果把属性或者方法定义到类的原型对象中，不管创建多少个对象，这些属性和方法仅存在一次，比较节省内存，一般来说，只 会把方法定义到原型对象中。不放属性，因为属性值一样，无法实现面向对象。

prototype属性的作用就是让该函数所实例化的对象们都可以找到公用的属性和方法

_**proto**_ <mark style="color:red;">任何对象实例都拥有这个属性，这个属性指向这个对象的类的原型对象</mark>，不建议在程序中直接使用\_\_proro\_\_这个属性引用，很多浏览器不允许直接调用。

<pre class="language-javascript" data-overflow="wrap"><code class="lang-javascript"><strong>function Foo (){...}
</strong><strong>f1 = new Foo();
</strong>即f1.__proto__ === Foo.prototype。
</code></pre>

**prototype** 属性的作用就是让该函数所实例化的对象们都可以找到公用的属性和方法，即f1._proto_ === Foo.prototype。

<mark style="color:red;">**constructor**</mark> <mark style="color:red;"></mark><mark style="color:red;">属性的含义就是指向该对象的构造函数</mark>，所有函数（此时看成对象了）最终的构造函数都指向Function。

```
f1.constructor === Foo
```

constructor：此属性只有原型对象才有，它默认指回prototype属性所在的构造函数。

原型对象本身的类型是一个Object类的对象

### **原型链**：

基本原理就是对象的\_\_proto\_\_，当一个对象调用某个成员（属性，方法）的时候，首先先查找当前对象实例，没有再查找当前的原型对象，如果还没有，在继续向上层查找，会一直查找到Object类型，如果还没有，直接报错

### In 和 hasOwnProperty

in语法：检测某个对象中（包括原型对象）是否拥有某个成员 '属性或者方法名称'in 对象

hasOwnProperty方法：判断一个对象中（不包括原型对象）是否拥有某个成员

如果希望任何一个对象都能拥有某个方法，那么这个方法应该定义到Object类的原型中，但是这样做对团队开发会有影响。

### **作用域链**：

一个function中使用一个变量数据，如果当前作用域没有，就会向外层作用域查找，直至查找到全局作用域，要尽量减少对外层作用域的查找，比较耗费资源，尽量用传参来代替作用域查找。

**继承**：一个类（子类）从另一个类（父类）中获得属性和方法，优点就是避免重复定义同样的属性和方法。继承实现：原型链，和 （基于伪装call的继承方式）构造器

**isPrototypeOf**方法：判断当前对象是否继承了某个类的原型

**propertyIsEnumerable**方法：判断对象的某个成员是否允许被遍历，默认的成员都是可以被遍历的，如果不想被遍历，需要对成员单独做特殊设置。

## 作用域

<mark style="color:red;">作用域决定了变量和函数的使用范围（可访问性</mark>（即可见性和生命周期）。JavaScript 中主要有三种作用域：

1. **全局作用域**：在任何函数之外声明的变量拥有全局作用域。这些变量可以在整个代码中被访问。
2. **局部作用域（函数作用域）**：在函数内部声明的变量只能在该函数内部访问。每个函数创建一个新的作用域。
3. **块级作用域**：由 `{}` 定义的语句块（如 `if` 条件、循环等）内声明的变量仅在该块内有效。ES6 引入的 `let` 和 `const` 关键字支持块级作用域。

变量如果没有加var声明，即时写在function内部，也算全局变量。

### 块级作用域的函数声明

#### 示例代码

```javascript
if (true) {
  a = 10;
  console.log(a, window.a); // 10 undefined

  function a() {};
  console.log(a, window.a); // 10 10
}

if (true) {
  console.log(a, window.a); // ƒ a(){} undefined

  function a() {};
  a = 10;
  console.log(a, window.a); // 10 ƒ a(){}
}
```

在块级作用域中默认声明的变量，只有代码执行到声明语句之后，才可以进行访问，否则会报错。块级作用域中默认声明的变量会被提升到全局作用域。

ECMAScript的作用域只有两种：全局作用域和函数作用域。但ES6的到来，为我们提供了“块级作用域”。凡是带 `{}` 的都是块级作用域。

`window.a` 只有等块级作用域中函数声明的定义的那行代码执行过之后，才会被映射到全局作用域。

### 作用域链

当在某个function使用某个的变量时候，首先先查看当前function作用域中是否有这个变量，如果有，直接使用，如果没有，就会像上层（外层）作用域查找，直至全局作用域。

### 生命周期

变量出现在内存开始，到从内存中销毁为止，中间的过程，就是一个变量的生命周期，一个局部变量的生命周期从函数调用开始，到函数执行完毕，全局变量的生命周期是整个js程序结束。

## 避免全局变量

* 立即执行函数
* 声明命名空间
* 模块化

## 立即执行函数表达式（IIFE）

### 错误示例

```javascript
function (){ /* code */ }() // 错误
```

在JavaScript中，遇到`function`关键字时，默认会将其视为函数声明。由于函数声明需要一个名称，并且不能直接跟一对括号来立即调用它，因此上述代码会导致语法错误。

即使加上了函数名`foo`，这个例子也会出错。这里的括号`()`被解释为分组操作符而不是函数调用的一部分，因为JavaScript期望一个函数声明或表达式而非这种形式。正确的形式应为：

```javascript
function foo { /* code */ }

() // 错误
```

### 正确形式

要创建一个立即执行函数表达式（IIFE），你需要确保`function`关键字被视为函数表达式的一部分。这可以通过将整个函数包裹在括号内实现，如下所示：

```javascript
(function() { /* code */ })(); // 正确
```

或者

```javascript
(function() { /* code */ }()); // 也是正确的
```

### 立即执行函数的作用

* 避免全局变量污染 IIFE允许你定义不需要命名的函数并立即执行它们，这样就不会向全局作用域添加额外的变量或函数名。
* 创建独立的作用域 IIFE内部形成了一个新的作用域，这意味着你可以在此范围内定义局部变量，这些变量对外部是不可访问的，从而实现封装和隐藏数据的效果。
* 封装变量 可以用来封装变量，防止它们泄露到全局作用域，同时提供了一种组织代码的方式。
* 立即执行函数的参数 如果IIFE需要使用外部的全局变量，可以通过参数传递给IIFE，这种方式可以提高代码的可维护性和清晰度。例如：

```javascript
(function(j) {
    // 在这里可以使用 j
})(i); // i 是实参，j 是形参
```

* 立即执行函数的返回值 IIFE可以有返回值，但是通常情况下，除非特别需要，否则很少关注IIFE的返回值。如果你确实需要从IIFE获取返回值，可以直接将其赋值给一个变量：

```javascript
const result = (function() {
    return "This is the result";
})();
console.log(result); // 输出: This is the result
```

## JavaScript 变量提升与作用域

### 声明提升

**声明提升（Hoisting）** 是 JavaScript 中的一个重要概念，<mark style="color:red;">指的是在代码执行之前，JavaScript 引擎会将所有变量和函数声明“提升”到其作用域的顶部</mark>

1. <mark style="color:red;">变量提升只会提升变量名的声明，而不会提升变量的赋值初始化。</mark>
2. 函数提升优先于变量提升，即函数提升在变量提升之上。
3. <mark style="color:red;">函数声明会将函数提升，函数表达式只会将变量var提升。</mark>
4. **`let` 和 `const`**：虽然 `let` 和 `const` 声明的变量也会被提升，但是在声明之前访问这些变量会导致引用错误（ReferenceError），这是因为它们存在暂时性死区（Temporal Dead Zone, TDZ），直到变量声明的实际位置

```javascript
console.log(foo); // 输出: [Function: foo]
var foo = 1;
function foo() {
    console.log('I am a function');
}
console.log(foo); // 输出: 1
```

#### 示例 1

```javascript
{
    function foo() {}
    foo = 1;
}
console.log(foo);
```

* **预期输出**：根据之前的知识，预期输出应该是 `1`。
* **实际输出**：实际输出是 `function foo() {}`。
* **解释**：这是因为函数声明会被提升到其所在作用域的顶部，而变量赋值 (`foo = 1;`) 不会被提升。

#### 示例 2

```javascript
{
    foo = 1;
    function foo() {}
}
console.log(foo);
```

* **预期输出**：输出是什么？
* **实际输出**：输出是 `1`。
* **解释**：在这个例子中，变量赋值 `foo = 1;` 发生在函数声明之前。由于函数声明会被提升到块级作用域的顶部，但变量赋值会覆盖函数引用，因此 `foo` 的值被设置为 `1`。

### 函数提升

1. **函数声明的提升**：函数声明会被提升到其所在作用域的顶部。
2. **执行顺序**：当代码执行时，它首先遇到已经被提升的函数声明。
3. **变量赋值**：变量赋值按它们被编写的顺序发生，并且可以覆盖具有相同标识符的函数声明。

这解释了为什么在第一个示例中，`foo` 指向函数，而在第二个示例中，`foo` 是数字 `1`。

#### 示例代码

```javascript
function fn() {
  getValue = function () { console.log(1); };
  return this;
}

fn.getValue = function () { console.log(2); };
fn.prototype.getValue = function () { console.log(3); };
var getValue = function () { console.log(4); };
function getValue() { console.log(5); }
var getValue = function () { console.log(4); };
function getValue() { console.log(5); }

// 请写出以下输出结果：
getValue(); // 4
fn().getValue(); // 1
getValue(); // 1
new fn.getValue(); // 2
new fn().getValue(); // 3
```



函数声明中，整个函数体也都会被提升，而函数表达式中只会提升名称。

#### This 例子

#### 示例代码

```javascript
var x = 3;
let result = {
  x: 2,
  baz: {
    x: 1,
    bar: function() {
      return this.x;
    }
  }
};

let go = result.baz.bar;
console.log(result.baz.bar()); // 1
console.log(go()); // 3
```

## 面向对象编程

* 类：抽象上的定义，不具体代表任何一个实例
* 对象：必须是一个具体的对象实例
* 类和对象是一对多关系，一个类可以创建多个对象实例，通过类创建对象的过程叫做实例化

## this关键字

代表一个对象，不同的情况下，this代表的对象也不同

* 函数调用function：this永远代表window全局对象
* 构造函数调用function：this永远代表当前创建的对象
* 对象实例方法调用function：this永远代表当前的对象

## 执行上下文

* 执行上下文： 是评估和执行js代码环境的抽象概念,每当js代码在运行的时候，它都在执行上下文中运行。
* 全局执行上下文： 基础的上下文， 创建全局的window对象，并设置this等于这个全局对象。一个程序中只有一个全局执行上下文
* 函数执行上下文：每当函数调用时，都会为函数创建一个新的上下文

在全局执行上下文中，this的值指向全局对象

在函数执行上下文中，this的值取决于该函数是如何被调用的， 如果它被一个引用对象调用，那么this会被设置成那个对象，否则this的值被设置为全局对象或者undifined,

匿名函数的执行环境具有全局性，因此其 this 对象通常指向 window。

匿名函数最大的用途是创建闭包，并且还可以构建命名空间，以减少全局变量的使用。从而使用闭包模块化代码，减少全局变量的污染。

```
Let foo = {

Baz: function(){

   Console.log(this)

}

}

Foo.baz() //”this” 引用’foo’因为 ‘baz’ 被对象’foo’调用

Let bar = foo.baz; bar(); //’this’指向全局window对象,因为没有指定引用对象
```

调用setTimeout的对象是window.

箭头函数的this始终指向函数定义时的this,

## Array数组

* 伪数组：通常指的是 arguments 对象，它具有数组的一些特征，但不是真正的数组。
* 类数组对象：具有 length 属性和索引，但不是 Array 类型的对象。这类对象还包括 NodeList、HTMLCollection 等

### 创建数组对象的方式：

1. `var 变量 = new Array();` 基本语法
2. `var 变量 = new Array(值1，值2，......);` 直接初始化元素值
3. `var 变量 = [值1，值2，......];` 等价于上面语句，是字面量写法

### 数组对象的属性方法

* length属性：返回数组的元素个数。
* push方法：在数组末尾添加元素。
* pop方法：删除数组末尾元素。
* unshift方法：在数组头部添加元素。
* shift方法：删除数组头部的元素。
* splice方法：删除数组指定的元素，也可以用于替换或插入元素。
* concat方法：合并另一个数组。
* join方法：按照指定的分隔符（默认逗号）将数组元素拼接为一个字符串。
* reverse方法：颠倒数组中元素的顺序。
* slice方法：根据起始和终止下标截取返回数组的一部分，不会修改原始数组，而是返回一个浅拷贝的新数组。
* sort方法：
  * 无参：默认按照数组中的元素的asc码来排序。
  * 传参：传入一个function定义排序规则。

改变原有数组的方法：push, pop, Shift, splice, sort, reverse

### 数组排序算法

* 冒泡排序、选择排序、插入排序、希尔排序、快速排序等。

冒泡排序核心思想：通过多次遍历数组，每次比较相邻的元素，如果顺序错误则交换它们，直到数组有序。

```js
for(var i = 0; i < a.length; i++){
    for(var j = 0; j < a.length - 1 - i; j++){
        if(a[j] > a[j+1]){
            var temp = a[j];
            a[j] = a[j+1];
            a[j+1] = temp;
        }
    }
}
```

## String字符串对象常用属性和方法

* charAt方法：获取字符串string的第n个字符。
* charCodeAt方法：获取字符串string的第n个字符的Unicode编码。
* fromCharCode方法：从指定编码创建一个新的字符串。
* concat方法：连接字符串，不如使用+操作符方便。
* indexOf方法：查找子字符串的起始位置，未找到返回-1。
* lastIndexOf方法：同上，但从后向前查找。
* length属性：返回字符串长度。
* substr方法：基于指定的开始位置和长度提取子字符串。
* substring方法：类似substr，但第二个参数是结束位置。
* toLowerCase, toUpperCase方法：转换大小写。
* split方法：按指定分隔符分割字符串成数组。

## Math数学对象常用属性和方法

* floor方法：向下取整。
* round方法：四舍五入取整。
* ceil方法：向上取整。
* random方法：生成\[0,1)之间的随机小数。

### 任意两整数间的随机整数公式:

parseInt((大值 - 小值 + 1) \* Math.random() + 小值)

## Date对象常用方法

* 构造函数：new Date() 默认当前系统时间，也可接受日期字符串或具体的时间参数。
* getFullYear/setFullYear方法：推荐使用此方法替代getYear/setYear以避免兼容性问题。
* getMonth/setMonth方法：月份数字范围为0到11。
* getDate/setDate方法：设置或获取一个月中的哪一天。
* getHours/setHours方法：设置或获取小时。
* getMinutes/setMinutes方法：设置或获取分钟。
* getSeconds/setSeconds方法：设置或获取秒。
* getDay方法：返回星期几，数字范围为0（周日）到6（周六）。
* getTime/setTime方法：获取或设置自1970年1月1日以来的毫秒数。

### 常用内置函数：

parseInt：字符串转为整数

parseFloat：字符串转为浮点数

isNaN：判断是否是一个非数字

eval：动态执行JavaScript语句字符串

escape/ unescape：基本编码解码，是unicode编码，不是url编码

encodeURI/ decodeURI：url编码解码，常用

encodeURIComponent/ decodeURIComponent：url编码解码，和上面的不同的是，&=?也会被编码

## 字符相加

在JavaScript中，不同类型的值在进行相加操作时，会根据类型的不同进行转换和处理。 // !可将变量转换成boolean类型，null、undefined、NaN以及空字符串('')取反都为true，其余都为false。

### 示例代码

```javascript

console.log(!null); // true
console.log(!undefined); // true
console.log(!NaN); // true
console.log(!''); // true

// [] + {} "[object Object]"
console.log([] + {}); // "[object Object]"

// {} + [] => + [] 0
console.log({} + []); // 0

// 通过Number()将值转换为数字
console.log(Number(undefined)); // NaN
console.log(Number({})); // NaN

console.log(1 + 'string'); // '1string'
console.log(1 + undefined); // NaN
console.log(1 + null); // 1
console.log(1 + [2,3]); // "12,3"
console.log(1 + {name: 'andyyou'}); // "1[object Object]"
console.log(1 + true); // 2

var a = (2, 3, 5);
console.log(a); // 5

console.log(Math.max([2,3,4,5])); // NaN

console.log(0.1 + 0.2); // 0.30000000000000004
```

### 说明

* `[] + {}` 结果为 `"[object Object]"`，这是因为数组和对象的相加在JavaScript中被转换为字符串操作。
* `{}` + `[]` 结果为 `0`，因为会被解释为 `+ []`，而空数组转换成数字后为 `0`。
* `Number(undefined)` 和 `Number({})` 都会返回 `NaN`，因为它们不能被转换成有效的数字。
* 当数字与字符串相加时，数字会被转换为字符串。
* 与 `undefined` 相加会返回 `NaN`。
* 与 `null` 相加时，`null` 被转换为 `0`。
* 与数组相加时，数组会被转换为字符串。
* 与对象相加时，对象会被转换为字符串 `[object Object]`。
* `1 + true` 结果为 `2`，因为 `true` 被转换为 `1`。
* 逗号运算符返回最后一个表达式的值。
* `Math.max` 不能直接处理数组，需要使用 `Math.max.apply(null, [2,3,4,5])`。
* 浮点数计算可能会出现精度问题，如 `0.1 + 0.2` 结果为 `0.30000000000000004`。

[更多详细内容](https://2ality.com/2012/01/object-plus-object.html) [参考博客](https://blog.csdn.net/jian_zi/article/details/105137258)\
\


## 深克隆浅克隆

<mark style="color:red;">**浅拷贝**</mark> <mark style="color:red;"></mark><mark style="color:red;">与</mark> <mark style="color:red;"></mark><mark style="color:red;">**深拷贝**</mark> <mark style="color:red;"></mark><mark style="color:red;">是两种不同的复制对象的方法，它们的主要区别在于对嵌套对象的处理方式。</mark>

**浅克隆**

浅拷贝是创建一个新对象或数组，新对象或数组会复制原对象或数组的一层属性。对于基本数据类型的属性，会直接复制其值；而对于引用数据类型的属性，只会复制其引用，即新对象和原对象中的引用数据类型属性会指向同一个内存地址。

* **特点**：
  * 修改浅拷贝中引用类型属性的内容，会影响原对象。
  * 浅拷贝通常速度快，占用内存少。

#### 浅克隆

**1. 使用 `Object.assign()`**

```javascript
// 定义原始对象 obj1
let obj1 = { a: 1, b: { c: 2 } };
// 使用 Object.assign() 方法进行浅克隆，将 obj1 的属性复制到一个空对象中
let obj2 = Object.assign({}, obj1);

// 验证浅克隆，修改 obj2 中 b 属性的 c 值
obj2.b.c = 3;
console.log(obj1.b.c); // 输出 3，说明修改 obj2 影响了 obj1
```

**2. 使用扩展运算符 (`...`)**

```javascript
// 定义原始对象 obj1
let obj1 = { a: 1, b: { c: 2 } };
// 使用扩展运算符进行浅克隆
let obj2 = { ...obj1 };

// 验证浅克隆，修改 obj2 中 b 属性的 c 值
obj2.b.c = 3;
console.log(obj1.b.c); // 输出 3，说明修改 obj2 影响了 obj1
```

**3. 自定义浅克隆方法**

```javascript
// 在 Object 类的原型中添加一个克隆方法
Object.prototype.clone = function () {
    // 根据情况创建数组对象或者普通 object 对象
    let obj = this instanceof Array ? [] : {};
    // 遍历当前对象的成员，赋给新对象
    for (let prop in this) {
        obj[prop] = this[prop];
    }
    return obj;
};

// 使用自定义浅克隆方法
let obj1 = { a: 1, b: { c: 2 } };
let obj2 = obj1.clone();

// 验证浅克隆，修改 obj2 中 b 属性的 c 值
obj2.b.c = 3;
console.log(obj1.b.c); // 输出 3，说明修改 obj2 影响了 obj1
```

#### 深克隆

深拷贝会递归复制对象及其所有嵌套的对象，生成一个完全独立的新对象，

**1. 使用 `JSON.parse(JSON.stringify())`**

```javascript
// 定义原始对象 obj1
let obj1 = { a: 1, b: { c: 2 } };
// 使用 JSON.parse(JSON.stringify()) 进行深克隆
let obj2 = JSON.parse(JSON.stringify(obj1));

// 验证深克隆，修改 obj2 中 b 属性的 c 值
obj2.b.c = 3;
console.log(obj1.b.c); // 输出 2，说明修改 obj2 不影响 obj1
```

**2. 自定义深克隆方法**

```javascript
// 在 Object 类的原型中添加一个深克隆方法
Object.prototype.clone = function () {
    // 根据情况创建一个数组或者普通 object 对象
    let obj = this instanceof Array ? [] : {};
    // 遍历当前对象的成员，赋给新对象
    for (let prop in this) {
        // 如果当前成员是对象类型
        if (typeof this[prop] === 'object' && this[prop]!== null) {
            obj[prop] = this[prop].clone();
        } else {
            obj[prop] = this[prop];
        }
    }
    return obj;
};

// 使用自定义深克隆方法
let obj1 = { a: 1, b: { c: 2 } };
let obj2 = obj1.clone();

// 验证深克隆，修改 obj2 中 b 属性的 c 值
obj2.b.c = 3;
console.log(obj1.b.c); // 输出 2，说明修改 obj2 不影响 obj1
```

3.第三方库（如 `lodash）`

#### 注意事项

* **`JSON.parse(JSON.stringify())`**：该方法有一些局限性，比如不能处理函数、`undefined`、`Symbol` 等类型，并且不会复制对象的原型链。
* **自定义方法**：通过递归调用 `clone` 方法实现深克隆，但要注意循环引用的问题，上述代码没有处理循环引用，实际使用时可能需要额外处理。

## 节流与防抖（throttle 和 debounce）

**Throttle**

规定一个单位时间，在这个单位时间内，只能有一次触发事件的回调函数执行。

**场景**：监听滚动事件，比如是否滑到底部自动加载更多，用 `throttle` 来判断。

**实现思路**：设置一个参数，执行完毕才为 `true`，如果为 `false` 则不继续执行。

```javascript
// 节流 throttle 代码：
function throttle(fn, delay) {
    let canRun = true; // 通过闭包保存一个标记
    return function () {
        // 在函数开头判断标记是否为 true，不为 true 则 return
        if (!canRun) return;
        // 立即设置为 false
        canRun = false;
        // 将外部传入的函数的执行放在 setTimeout 中
        setTimeout(() => {
            // 最后在 setTimeout 执行完毕后再把标记设置为 true(关键)表示可以执行下一次循环了。
            // 当定时器没有执行的时候标记永远是 false，在开头被 return 掉
            fn.apply(this, arguments);
            canRun = true;
        }, delay);
    };
}

function sayHi(e) {
    console.log('节流：', e.target.innerWidth, e.target.innerHeight);
}
```

**Debounce**

在事件被触发 `n` 秒后再执行，如果在这 `n` 秒内又被触发，则重新计时，触发多次只执行一次（如 `input` 输入、`onresize` 事件）。

**实现思路**：每次触发事件时设置一个延迟调用方法，并且取消之前的延时调用方法。

```javascript
// 防抖 debounce 代码：
function debounce(fn, delay) {
    var timeout = null; // 创建一个标记用来存放定时器的返回值
    return function (e) {
        // 每当用户输入的时候把前一个 setTimeout clear 掉
        clearTimeout(timeout);
        // 然后又创建一个新的 setTimeout, 这样就能保证 interval 间隔内如果时间持续触发，就不会执行 fn 函数
        timeout = setTimeout(fn, delay);
    };
}

// 处理函数
function handle() {
    console.log('防抖：', Math.random());
}
```

上述代码分别实现了节流和防抖函数。节流函数 `throttle` 确保在指定的时间间隔内，回调函数只能执行一次；防抖函数 `debounce` 则会在事件停止触发一段时间后才执行回调函数，如果在这段时间内再次触发事件，会重新计时。

## 事件

### 事件流：事件传播的顺序

* 捕获型：从父元素传播到子元素（注意：IE不支持）。
* 冒泡型：从子元素传播到父元素（默认行为）。

### 阻止事件冒泡

* W3C标准写法：\
  event.stopPropagation();
* IE专用写法：\
  event.cancelBubble = true;
* 兼容写法：\
  e.stopPropagation ? e.stopPropagation() : e.cancelBubble = true;

### 阻止事件的默认行为

* W3C标准写法：\
  event.preventDefault();
* IE专用写法：\
  event.returnValue = false;
* 行内事件处理，直接在最后加上return false;就可以阻止事件默认行为。
* 代码事件处理中，直接写上return false;也可以阻止事件的默认行为。

### 事件委托

利用事件冒泡的原理，通过父元素来捕获子元素的事件加以处理。这种方法可以有效地减少事件处理器的数量，提高性能。

如果一个元素的子元素触发了一个事件，这个事件会沿着DOM树向上冒泡，直到文档根节点。因此，我们可以将事件监听器绑定到父元素上，而不是直接在每个子元素上绑定事件处理器

* **性能提升**：减少需要添加事件监听器的数量，特别是当有大量动态添加或移除的元素时。
* **简化代码**：不需要为每个新添加的元素单独设置事件监听器。
* **内存效率**：由于减少了事件监听器的数量，降低了内存占用。

```
<ul id="myList">
    <li>Item 1</li>
    <li>Item 2</li>
    <li>Item 3</li>
</ul>

<script>
document.getElementById('myList').addEventListener('click', function(event) {
    if (event.target && event.target.nodeName == 'LI') { // 检查事件目标是否是 LI 元素
        alert('You clicked on list item: ' + event.target.textContent);
    }
});
</script>
```

### 浏览器的事件流

1. 事件捕获阶段：事件从最外层的祖先元素开始向下传播直到目标元素。
2. 处于目标阶段：事件到达目标元素本身。
3. 事件冒泡阶段：事件从目标元素开始向上传播回最外层的祖先元素

## 正则表达式的方法

* test()：判断字符串是否匹配模式。返回true或false。
* exec()：返回一个对象，包含三项数据------匹配的字符串、下标和原始字符串。如果找不到匹配，则返回null。匹配的字符串以数组形式返回，如果有子表达式，每个子表达式匹配的内容也会添加到数组中。

String中支持正则表达式的方法

* replace(要替换的字符串或者模式, 替换为的字符串)：替换字符串，默认只会替换第一个匹配项。若要全局替换，必须使用正则表达式配合g参数。
* match()：返回一个数组，存储所有匹配的值。如果没有匹配项，返回null。
* search()：返回第一个匹配内容的下标。如果没有匹配项，返回-1。

正则表达式元字符

### 转义与边界匹配

* \：转义字符。
* ^：表示开始（例如 /^a/ 匹配"ab"，但不匹配"bab"）。
* $：表示结束。

### 字符集合

* .：表示任意字符，包括中文。
* \[abc]：表示a、b、c中的任一字符。
* \[^abc]：表示不是a、b、c中的任何一个字符。
* \[a-z]：小写字母；\[A-Z]：大写字母；\[a-zA-Z]：所有字母（也可以是特定范围如\[c-e]、\[X-Z]）。
* \[0-9]：数字。
* \[a-zA-Z0-9]：字母和数字。
* \[^a-z]、\[^A-Z]、\[^0-9]：非字母、非数字。

### 特殊字符集

* \s：空白（空格、制表位、换行、换页、回车）。
* \S：非空白。
* \d：数字，等价于\[0-9]。
* \D：非数字，等价于\[^0-9]。
* \w：字母、数字或下划线，等价于\[0-9a-zA-Z\_]。
* \W：非字母、数字、下划线。

### 匹配次数

* \*：0次到多次。
* +：1次到多次。
* ?：0次或1次。
* {n}：正好n次。
* {n,}：至少n次。
* {n,m}：n次到m次。

### 其他元字符

* |：逻辑或（需要打小括号来分组）。
* (表达式)：子表达式，用于改变优先级或捕获组。
* ?：在某些情况下用于非贪婪模式，尽可能少地匹配，通常与replace方法一起使用。

## Cookie

Cookie：通用的客户端数据存储技术，在浏览器端以纯文本的键值对形式存储数据，必须基于服务器运行实现（本地运行无法实现）

**Cookie分为两种形式：**

**会话级**：存储在浏览器的会话进程中，生命周期和会话一致

**硬盘级**：永久存储在浏览器的缓存文件夹中，是一个文本文件，但是需要设置失效时间，否则默认就是会话级。失效时间必须以;expires=GMT时间格式

一个域写入的cookie数据，只能被相同的域读取，不能被其他的域读取，例如百度无法读取搜狐写入的cookie，搜狐无法读取新浪写入的cookie，如果写入的是中文数据，需要进行编码，然后读取的时候进行解码。

Cookie: 同源限制，大小限制，服务器和浏览器传递，每次请求都会把cookie放到请求报头发送到服务器。

Cookie在浏览器中有容量限制，有条数限制，每次网络请求都会放到请求报头中一起发给服务器。

JS中操作cookie的语句：document.cookie属性

## 数据交换标准

1. 字符串用特殊符号分割
2. CSV格式
3. Xml格式
4. Json格式

Xml（Extensive Makeup Language）可扩展的标记语言，特点是自定义标签，而不是像html那种固定标签，主要用途做通用数据交换格式和配置文件，缺点是标签本身占用流量太多，解析xml数据比较困难 xml多用于做配置文件

**语法特点：**

1. 文件头部声明
2. 标签区分大小写
3. 文档中必须且只能有一个根元素
4. 属性值必须加双引号或者单引号
5. 如果有应用dtd或者schema规则文件，必须符合dtd或者schema规则
6. Dtd是早期规则语法
7. Schema是后期规则语法，和dtd语法并存

JSON：基于JavaScript语言的轻量级的数据交换格式(JavaScript Object Notiation)，最常见的就是和后台的数据通信

基本语法特点：就是和字面量的对象语法非常类似，用{}表示一个json对象，用\[]表示json数组对象，但是属性名称需要加双引号，只能有属性，不能有方法

Json字符串转换为json对象： .parse() //ie8或以上支持

Json字符串转换为json对象：eval( '(' + json字符串 + ')' ) //ie6或以上支持

Eval：动态执行字符串

Json对象转换为json字符串：JSON.stringify() //ie8或以上支持

## 函数高级扩展

函数本身就是一个对象，可以作为参数传递，也可以作为返回值返回

重载的概念：就是方法（函数）的名称相同，参数列表不同

在JS中没有重载这一说，重复的函数或者方法声明，以最后声明的为准

arguments对象：在function内部可以获得一个arguments 对象，通过这个对象就可以获得所有的实参数据，这个对象是一个类数组

获得形参的数量：函数对象.length

函数对象的call方法: 函数对象.call(上下文对象,可变长参数......)，改变函数的调用者对象的上下文对象

函数对象的apply方法：函数对象.apply(上下文对象,参数数组......)，功能同上

//类数组转换为数组

var arg = Array.prototype.slice.call(arguments);

callee是arguments对象的一个对象，通过callee可以调用本身的函数，简单的说，callee代表当前函数本身

caller是函数对象的一个属性对象，永远代表调用了当前函数的函数对象

一个function中的变量，不论声明在什么位置，在实际运行期间，都会提升到函数的开始位置声明

## 闭包

一个正常的function调用，里面所有的局部变量在function调用结束之后，都会被销毁，但是如果局部变量被内层作用域所引用，即时外层函数执行完毕也不释放，这就是闭包，缺点是会造成内存泄露（内存使用完不及时释放，就叫内存泄露）

[http://www.ruanyifeng.com/blog/2009/08/learning\_javascript\_closures.html](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)

### 闭包的作用

* **延长变量的生命周期**\
  因为内部函数持有对外部函数作用域变量的引用，即使外部函数已经返回，这些变量也不会被垃圾回收，从而持续存在于内存中。
* **实现数据的私有化**\
  可以将一些变量封装在外部函数作用域中，只能通过内部函数访问和修改，外部无法直接更改这些变量，起到保护数据的作用。
* **创建高阶函数**\
  在函数式编程中，闭包常被用来返回一个新的函数，或者将函数作为参数传递，用于灵活地组合功能。

```
function createCounter() {
  let count = 0; // 外部函数的局部变量

  // 内部函数可以访问外部函数的变量 count
  return function() {
    count++;
    console.log(`当前计数: ${count}`);
  };
}

const counterA = createCounter();
counterA(); // 当前计数: 1
counterA(); // 当前计数: 2

const counterB = createCounter();
counterB(); // 当前计数: 1

```

上面是一个简单的闭包示例，演示如何通过闭包实现计数器，并使 `count` 变量在外部无法直接访问

```
// 高阶函数：接收一个 factor，返回一个新的函数 multiplier
function createMultiplier(factor) {
  // 这里形成了闭包，内部函数引用了外部函数的变量 factor
  return function (num) {
    return factor * num;
  };
}

// 使用示例
const double = createMultiplier(2);  // 生成一个“翻倍”的函数
console.log(double(5)); // 输出 10
console.log(double(10)); // 输出 20

const triple = createMultiplier(3);  // 生成一个“三倍”的函数
console.log(triple(5)); // 输出 15
console.log(triple(10)); // 输出 30

```

### **缺点：**

* **内存占用和内存泄漏**\
  闭包会让其捕获的外部变量在内存中一直存在，即使外部函数已经返回。如果不慎管理，可能导致内存泄漏，特别是在长生命周期或大量创建闭包时。
* **性能开销**\
  闭包在创建时需要维护额外的作用域链，这可能带来一定的性能开销。在高频率调用的场景下，过多的闭包可能影响性能。
* **代码可读性和调试难度**\
  过度使用闭包可能会使代码变得难以理解和维护。闭包中的变量共享和依赖关系有时不易追踪，调试时可能会遇到作用域混淆的问题。

### 怎么避免

#### 1. 减少不必要的嵌套函数

如果可能的话，尽量减少嵌套函数的使用，尤其是那些捕获外部变量的函数。这不仅可以使代码更加清晰易读，还能避免潜在的闭包相关问题。

#### 2. 使用局部作用域限制变量生命周期

通过缩小变量的作用范围，可以减少它们被闭包引用的机会，从而降低内存泄漏的风险。ES6 引入的 `let` 和 `const` 关键字支持块级作用域，可以帮助实现这一点。

{% code overflow="wrap" %}
```
function doSomething() {
    if (true) {
        let x = 10; // 使用 let 限制 x 的作用域为 if 块内
        console.log(x);
    }
    // x 在这里不可用
}
```
{% endcode %}

3.及时清理不再需要的引用

```
function createFunctionWithCounter() {
    let counter = 0;

    function incrementCounter() {
        return ++counter;
    }

    // 返回的对象包含方法和一个清除引用的方法
    return {
        increment: incrementCounter,
        cleanup: () => { counter = null; } // 清理引用
    };
}

const func = createFunctionWithCounter();
console.log(func.increment()); // 输出: 1
func.cleanup(); // 手动清理，帮助 GC 回收资源
```

#### 4. 使用即时函数（IIFE）来创建独立作用域

即时函数表达式（IIFE）可以在不污染全局命名空间的情况下创建独立的作用域，有助于管理变量的作用范围并减少意外的闭包形成。

```
javascript深色版本(function() {    let localVariable = 'I am scoped locally';    console.log(localVariable);})();
```

## `call()`、`apply()` 和 `bind()` 的区别

`call`、`apply` 和 `bind` 方法都用于改变函数执行时的上下文（即 `this` 的值），但它们之间有一些关键的区别。

**都是用来重定义 this 这个对象的！**

### `call()`

* **用途**：立即调用一个函数，并允许你指定 `this` 的值（即函数运行时所使用的上下文），以及以参数列表的形式传递参数。
* **语法**：`function.call(thisArg, arg1, arg2, ...)`
*   **示例**：

    ```javascript
    const obj = { num: 40 };
    function add(a, b) {
        return this.num + a + b;
    }
    console.log(add.call(obj, 1, 2)); // 输出 43
    ```

### `apply()`

* **用途**：与 `call()` 类似，但它接受一个数组或类数组对象作为参数列表传递给目标函数。
* **语法**：`function.apply(thisArg, [argsArray])`
*   **示例**：

    ```javascript
    const obj = { num: 40 };
    function add(a, b) {
        return this.num + a + b;
    }
    console.log(add.apply(obj, [1, 2])); // 输出 43
    ```
* **注意**：第二个参数必须是一个数组或类数组对象。

### `bind()`

* **用途**：不会立即执行函数，而是返回一个新的函数，并且可以预先设定原函数的 `this` 值以及部分或全部参数。这使得它非常适合用于创建函数的特定版本以便后续使用。
* **语法**：`function.bind(thisArg[, arg1[, arg2[, ...]]])`
*   **示例**：

    ```javascript
    const obj = { num: 40 };
    function add(a, b) {
        return this.num + a + b;
    }
    const boundAdd = add.bind(obj, 1);
    console.log(boundAdd(2)); // 输出 43
    ```
* **特点**：返回的是一个新的函数，因此可以在需要的时候延迟执行。

### 总结

| 方法        | 是否立即执行 | 参数形式 | 主要用途                           |
| --------- | ------ | ---- | ------------------------------ |
| `call()`  | 是      | 参数列表 | 立即改变 `this` 并传入参数列表执行函数        |
| `apply()` | 是      | 数组   | 立即改变 `this` 并通过数组传入参数执行函数      |
| `bind()`  | 否      | 参数列表 | 创建一个新函数，其 `this` 已被固定，并可预设部分参数 |

每种方法都有其适用场景，理解它们之间的差异可以帮助您更灵活地控制函数的行为和上下文。

### 手写 bind

多次绑定 bind 是无效的

```
Function.prototype.bind = function (context){
    var self = this;
    return function() {
        self.apply(context, arguments);
    }
}
```

## WEB本地存储

为了代替Cookie实现客户端数据存储，相对于Cookie的区别是，不会在每次请求的时候，把数据自动发送到服务器端，节省流量。

* **localStorage方式**：永久性存储，没有失效时间。
* **sessionStorage方式**：会话级存储，会话关闭就销毁。localStorage 与 sessionStorage 的唯一区别是 localStorage 属于永久性存储，而 sessionStorage 属于当会话结束的时候会被清空。

### **1. sessionStorage vs localStorage 对比**

| **特性**     | **sessionStorage**                                    | **localStorage**                                  |
| ---------- | ----------------------------------------------------- | ------------------------------------------------- |
| **作用域**    | 仅限当前标签页（Tab）                                          | 所有同源页面共享                                          |
| **存储时长**   | **会话级别**，关闭浏览器/标签页即清除                                 | **永久** 存储，除非手动清除                                  |
| **跨标签页共享** | ❌ **不共享**（即使同一域名，新开标签页也无法访问）                          | ✅ **可共享**（同源下所有标签页都能访问）                           |
| **数据大小限制** | 一般 **5MB**（不同浏览器可能有所不同）                               | 一般 **5MB**（同 `sessionStorage`）                    |
| **适用场景**   | **临时存储**，如表单填写状态、会话数据                                 | **长期存储**，如用户偏好设置、缓存数据                             |
| **API**    | `sessionStorage.setItem()`、`sessionStorage.getItem()` | `localStorage.setItem()`、`localStorage.getItem()` |

`storage` 事件是 **`localStorage` 专属** 的事件，它在 **同源的多个标签页（Tab）或窗口** 之间同步数据变化。\
\
和Cookie一样，WEB本地存储的数据同源限制。

### 相关的API

* **length属性**：返回数据的条数。
* **setItem()**：设置或增加数据。
* **removeItem()**：删除指定key的数据。
* **getItem()**：返回指定key的值。
* **key()**：返回指定下标的key。

**应用缓存**：利用html5设定某些文件缓存，需要通过单独的配置文件进行配置。如果缓存的文件进行了修改，必须修改配置文件，否则文件在浏览器端不更新。

## IndexedDB

本地数据库，存储空间大，不仅存储字符串。搜索和索引功能，支持事务（transaction）。

## Service Worker

基于web worker，不能访问dom。可以拦截当前网站的所有请求，进行判断。如果需要向服务器发起请求就转给服务器，如果可以使用缓存就返回缓存，提高浏览体验。



## **解决跨域问题的方案**

* **在服务器端增加允许跨域请求的响应报头**，设置`Access-Control-Allow-Origin`为`*`。
* **利用jsonp技术**，前端和后端配合实现跨域ajax。所谓的jsonp技术，就是利用浏览器并不禁止外联js是另一个域的js，所以jsonp跨域请求原理就是`<script src="其他域的js">`，而被外联的js是一个函数调用语句，通过实参把数据传递给请求客户端。动态创建script标签，只支持GET请求；存在脚本注入以及跨站请求伪造等安全问题。
* **Postmessage** 通过 `window.postMessage()` 让不同源的 `iframe` 或 `window` 传递消息。
* **Node 中间层代理**
* **Nginx反向代理**
* **Websocket** （适用于双向通信）

### &#x20;**处理跨域时的 `credentials`（携带 Cookies）**

默认情况下，跨域请求不会携带 `cookies`，如果需要携带，需要：

1. **后端允许 `credentials`（设置 `Access-Control-Allow-Credentials: true`）**
2. **前端 `fetch` 需要开启 `credentials: "include"`**

📌 **示例：前端 fetch**

```javascript
fetch("https://api.example.com/data", {
    method: "GET",
    credentials: "include", // 允许携带 Cookie
})
    .then(response => response.json())
    .then(data => console.log(data));
```

📌 **示例：Axios**

```javascript
axios.get("https://api.example.com/data", { withCredentials: true })
    .then(response => console.log(response.data));
```

📌 **示例：后端（Node.js + Express）**

```javascript
app.use(cors({
    origin: "https://your-frontend.com",
    credentials: true
}));
```

🚨 **注意**

* `credentials: "include"` 不能与 `Access-Control-Allow-Origin: "*"` 一起使用，需要指定具体的域名。

### **🔹 结论**

| **场景**            | **前端需要做的事情**                                                            |
| ----------------- | ----------------------------------------------------------------------- |
| 服务器已配置 CORS       | 直接请求即可                                                                  |
| 需要携带 `cookies`    | `credentials: "include"` & 服务器 `Access-Control-Allow-Credentials: true` |
| 避免 `OPTIONS` 预检请求 | 确保 `Content-Type` 简单，避免不必要的请求头                                          |
| 后端不支持 CORS        | 代理服务器（Vue/React 配置 `proxy`）                                             |



### nodejs中间层代理

利用http.request方法来转发请求

```
const http = require('http');

// 创建一个HTTP服务器
const proxyServer = http.createServer((req, res) => {
    // 目标服务器的选项
    const options = {
        hostname: 'example.com', // 您希望代理访问的目标主机名
        port: 80,                // 目标服务器端口
        path: req.url,           // 请求路径
        method: req.method,      // HTTP方法
        headers: req.headers     // 原始请求头
    };

    // 向目标服务器发起请求
    const proxyRequest = http.request(options, (proxyResponse) => {
        // 将响应头转发给客户端
        res.writeHead(proxyResponse.statusCode, proxyResponse.headers);
        // 将响应数据流转发给客户端
        proxyResponse.pipe(res);
    });

    // 处理错误情况
    proxyRequest.on('error', (err) => {
        console.error(`代理请求出错: ${err.message}`);
        res.end();
    });

    // 将请求体（如果有）转发给目标服务器
    req.pipe(proxyRequest);
});

// 监听端口
const PORT = 3000;
proxyServer.listen(PORT, () => {
    console.log(`代理服务器正在监听端口 ${PORT}`);
});
```

http-proxy-middleware 是一个非常流行的 Node.js 中间件，用于简化 HTTP 请求的代理

```
// 引入必要的模块
const express = require('express');
const { createProxyMiddleware } = require('http-proxy-middleware');

// 创建一个Express应用程序
const app = express();

// 定义代理中间件
const apiProxy = createProxyMiddleware({
    target: 'http://example.com', // 目标服务器的地址
    changeOrigin: true,           // 更改主机头为代理目标URL
    pathRewrite: {
        '^/api' : '/',           // 重写路径，例如将 /api 路径前缀移除
    },
    onProxyReq: (proxyReq, req, res) => {
        console.log('发送请求到:', proxyReq.path);
    },
    onProxyRes: (proxyRes, req, res) => {
        console.log('从目标接收响应:', proxyRes.statusCode);
    },
    onError: (err, req, res) => {
        res.status(500).send('请求代理出错');
    }
});

// 使用代理中间件
app.use('/api', apiProxy); // 将所有以 /api 开头的请求代理到目标服务器

// 启动服务器
const PORT = 3000;
app.listen(PORT, () => {
    console.log(`代理服务器正在监听端口 ${PORT}`);
});
```

### Nginx 反向代理配置

Nginx 是一个高性能的 HTTP 和反向代理服务器。使用 Nginx 作为反向代理，可以将客户端请求转发到后端服务器，并将响应返回给客户端，而客户端并不知道实际处理请求的服务器是哪一个。以下是配置 Nginx 反向代理的基本步骤和一个简单的例子。

**步骤**

1. 安装 Nginx：确保您的系统上已经安装了 Nginx。如果尚未安装，可以根据您操作系统的不同选择合适的方法进行安装。例如，在基于 Debian 的系统上（如 Ubuntu），可以通过以下命令安装： sudo apt update sudo apt install nginx
2. 编辑 Nginx 配置文件：通常位于 /etc/nginx/nginx.conf 或者 /etc/nginx/sites-available/default。您可以直接编辑默认配置文件或者创建一个新的配置文件。
3. 设置反向代理配置：在配置文件中添加或修改 server 块来定义如何处理特定域名或 IP 地址的请求。
4. 测试配置并重启 Nginx：每次修改完配置文件后，建议先测试配置是否正确，然后重启 Nginx 使更改生效。

bash sudo nginx -t sudo systemctl restart nginx

**基本例子** 下面是一个基本的例子，展示如何将所有对 yourdomain.com/api/ 的请求转发到运行在 localhost:3000 上的应用程序：

```
Nginx
server {
    listen 80;
    server_name yourdomain.com;

    location /api/ {
        proxy_pass http://localhost:3000/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # 如果需要，还可以为其他路径设置不同的代理或其他处理方式
    location / {
        # 这里可以放置静态文件服务、另一个代理等
        root /var/www/html;
        index index.html;
    }
}
```

**在这个例子中：**

listen 80; 表示监听标准HTTP端口80。 server\_name yourdomain.com; 定义了这个server块应该响应哪个域名的请求。 location /api/ { ... } 块指定了对于以 /api/ 开头的所有请求，Nginx 应该将其转发到 http://localhost:3000/。注意这里的 proxy\_pass URL 后面有一个斜杠 /，这意味着 /api/ 路径部分会被去掉后再拼接到目标URL后面。 proxy\_set\_header 指令用于设置转发请求时添加或修改HTTP头部信息，以便后端服务器能够获取客户端的真实信息。

### **区别**&#x20;

设置 Access-Control-Allow-Origin 为 \*：

优点：简单快捷，适用于简单的应用场景。

缺点：安全性较低，不适合涉及敏感数据的场景。

Node 中间层代理：

优点：灵活性高，可以在代理层进行复杂的逻辑处理。

缺点：增加了一层代理，可能会引入性能开销，系统复杂性增加。

Nginx 反向代理：

优点：高性能，配置灵活，适合处理大量并发请求和静态资源。

缺点：配置复杂，需要编写和维护配置文件

## 模块化

在ES6之前，社区制定了若干模块加载方案，其中最著名的有CommonJS和AMD两种规范。

* CommonJS 主要应用于服务器端（如Node.js环境）。
* AMD（Asynchronous Module Definition），则多用于浏览器端。

### CommonJS

CommonJS是一种主要用于Node.js环境的模块化规范。ES6的设计思想倾向于静态化，即尽可能在编译时确定模块的依赖关系、输入输出变量等信息。相比之下，CommonJS和AMD模块依赖关系以及输入输出变量只能在运行时确定。

例如，在CommonJS中，<mark style="color:red;">一个模块实质上是一个对象，当导入时需要查找该对象的属性。比如从fs模块中整体加载（即加载fs的所有方法），生成一个对象\_fs，</mark>然后从这个对象中读取特定的方法。这种加载方式被称为“<mark style="color:red;">运行时加载</mark>”，因此无法进行编译时的“静态优化”。

### ES6模块

ES6模块不是对象，而是通过export命令显式指定输出的内容，并且这些输出是静态定义的。这样做的好处是可以实现“编译时加载”，即只加载实际需要的部分。例如，<mark style="color:red;">如果只需要从fs模块加载3个方法，则只会加载这3个方法，其他方法不会被加载。这种方式称为“编译时加载”</mark>，但这也意味着你不能像对待对象那样引用整个模块。

使用export default时，对应的import语句不需要使用大括号。这是因为默认导出只能有一个。 如果不使用export default，则需要使用大括号来明确指出导入哪些具体成员。 本质上，export default就是输出一个名为default的变量或方法，允许为它取任意名字。

### 加载机制对比

* <mark style="color:red;">CommonJS: 输出的是值的一个拷贝</mark>，这意味着一旦输出了一个值，模块内部的变化就不会影响到这个值。
* ES6模块: 输出的是值的引用，实现了动态引用，所以模块内部的变化可以反映到外部。

### CommonJS vs AMD

**CommonJS**

定义：每个单独文件就是一个模块。 输出：通过module.exports对象作为唯一出口。 加载：使用require()方法加载模块。

**AMD (Asynchronous Module Definition)**

目标：解决浏览器端模块开发的问题，特别是处理多个JavaScript文件之间的依赖关系，并避免js文件加载阻塞页面渲染。 工具：Require.js，它定义了全局函数define来定义模块，以及异步加载的require()函数。

**CMD (Common Module Definition)** 浏览器端的实现之一是Sea.js，其设计目标类似于Require.js，但在模块定义和加载机制上有自己的特点，比如使用sea.use()来加载模块。



## 继承

通过子类的原型prototype对象实例化来实现

```
function Parent() {
    this.name = 'Parent';
}

Parent.prototype.sayHello = function() {
    console.log('Hello');
};

function Child() {
    this.name = 'Child';
}

Child.prototype = new Parent();

var child = new Child();
child.sayHello(); // Hello
```

缺点是：子类之间相互影响。所有子对象共享同一个原型对象，对原型对象的修改会影响到所有子对象

### 构造函数式继承

构造函数继承是通过在子构造函数中调用父构造函数来实现继承。在构造函数继承中，通过在子构造函数中使用\*\*call()或apply()\*\*方法，将父构造函数的上下文设置为子对象的上下文，从而实现继承。

```
function Parent(name) {
  this.name = name;
}

Parent.prototype.sayName = function() {
  console.log(this.name);
};

function Child(name) {
  Parent.call(this, name); // 调用父类构造函数
}

let child1 = new Child('Alice');
let child2 = new Child('Bob');

child1.sayName(); // 输出: Alice
child2.sayName(); // 输出: Bob
```

使通过this创建的属性和方法在子类中复制一份，因为是单独复制的，所以各个实例化的子类互不影响。<mark style="color:red;">Parent.call(this ,name),所以父类的原型方法自然不会被子类继承</mark>，而如果要想被子类继承就必须要放在构造函数中。

### 组合式继承

但是父类的构造函数会被创建两次（一次是在设置原型时，一次是在创建子对象时）

```
function Parent(name) {
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}

Parent.prototype.sayName = function() {
    console.log(this.name);
};

function Child(name, age) {
    Parent.call(this, name); // 第二次调用Parent构造函数
    this.age = age;
}

// 直接将Parent.prototype赋值给Child.prototype，这会导致Parent构造函数被第二次调用
Child.prototype = new Parent(); 

Child.prototype.constructor = Child; 
Child.prototype.sayAge = function() {
    console.log(this.age);
};
```

### 寄生组合继承

```
function inheritPrototype(subType, superType) {
  let prototype = Object.create(superType.prototype); // 创建父类原型的副本
  prototype.constructor = subType; // 修复构造函数引用
  subType.prototype = prototype; // 将子类原型指向这个副本
}

function SuperType(name) {
  this.name = name;
}

SuperType.prototype.sayName = function() {
  console.log(this.name);
};

function SubType(name, age) {
  SuperType.call(this, name); // 继承父类的实例属性
  this.age = age;
}

inheritPrototype(SubType, SuperType); // 继承父类的原型方法

SubType.prototype.sayAge = function() {
  console.log(this.age);
};

let instance = new SubType('Alice', 25);
instance.sayName(); // 输出: Alice
instance.sayAge(); // 输出: 25
```

### ES6中的继承 extends

在 ES2015 中有了 class 语法糖，有了 extends、super、static 这样的关键字，更像强类型语言中的“类”了。

## Ajax

使用 Ajax 发起 HTTP 请求的步骤如下：

1. 创建 `XMLHttpRequest` 对象。
2. 创建一个新的 HTTP 请求，并指定 HTTP 请求的方法、URL 及验证信息（是否支持异步，默认为 true）。
3. 设置响应 HTTP 请求状态变化的函数。
4. 发送 HTTP 请求。

示例代码：

```javascript
let xhr = new XMLHttpRequest();

xhr.open("GET", url, true);
xhr.responseType = "json";
xhr.setRequestHeader("Accept", "application/json");

xhr.onreadystatechange = function() {
  if (xhr.readyState === 4 && xhr.status === 200) {
    console.log(xhr.response);
  }
};

xhr.onerror = function() {
  console.error("Request error");
};

xhr.send(null);
```

Ajax 是异步 JavaScript 和 XML，用于在 Web 页面中实现异步数据交互。

## Fetch

`Fetch` 是 JavaScript 中用于发起 HTTP 请求的库。与 Ajax 相比，Fetch 有以下不同：

1. **API 设计风格**
   * Fetch API：采用 Promise 的模式来处理异步操作，更加符合现代 JavaScript 的编程习惯。Fetch API 的设计简洁明了，易于理解和使用。
   * AJAX：通常依赖于 `XMLHttpRequest` 对象，需要处理多个状态码和事件。尽管 `XMLHttpRequest` 也可以使用 Promise 包装，但其原生 API 更加繁琐。
2. **返回值**
   * Fetch API：总是返回一个 Promise，无论请求是否成功，都需要通过 `.then()` 方法来处理结果。如果请求失败，会返回一个带有错误状态码的 Response 对象。（当接收到错误的 HTTP 状态码时，返回的 Promise 不会标记为 reject，只有网络故障和请求被阻止才会标记为 reject）
   * AJAX：使用 `XMLHttpRequest` 时，可以通过 `onreadystatechange` 事件来处理请求完成的状态变化，也可以使用 `responseText` 或 `responseXML` 属性来获取响应内容。
3. **全局错误处理**
   * Fetch API：如果请求过程中出现了网络错误或者其他类型的错误，Promise 会被 reject，因此可以通过 `.catch()` 来处理这些错误。
   * AJAX：需要为 `XMLHttpRequest` 设置 `onerror` 和 `onabort` 事件来处理错误。

其他区别：

* 缺少拦截器：Fetch 没有内置的请求和响应拦截器。
* 手动处理 JSON：需要手动将响应数据转换为 JSON。
* 错误处理：Fetch 不会因为 HTTP 错误状态码（如 404 或 500）而 reject Promise，需要手动检查响应状态。

```
// GET 请求
fetch('https://jsonplaceholder.typicode.com/posts')
  .then(response => {
    if (!response.ok) {
      throw new Error('Network response was not ok ' + response.statusText);
    }
    return response.json();
  })
  .then(data => console.log(data))
  .catch(error => console.error('There has been a problem with your fetch operation:', error));

// POST 请求
fetch('https://jsonplaceholder.typicode.com/posts', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    title: 'foo',
    body: 'bar',
    userId: 1,
  }),
})
  .then(response => {
    if (!response.ok) {
      throw new Error('Network response was not ok ' + response.statusText);
    }
    return response.json();
  })
  .then(data => console.log(data))
  .catch(error => console.error('There has been a problem with your fetch operation:', error));
```

## Axios

Axios 是另一个用于发起 HTTP 请求的库，具有以下优点和缺点：

### 优点

* 跨浏览器兼容性：支持所有现代浏览器，包括 IE11。
* Promise-based：基于 Promise，使得异步操作更加简洁和易于处理。
* 拦截器：可以设置请求和响应的拦截器，方便进行请求和响应的预处理。
* 自动转换：自动将响应数据转换为 JSON（如果响应类型为 JSON）。
* 支持多种请求方法：GET、POST、PUT、DELETE 等。
* 错误处理：提供了统一的错误处理机制。
* 取消请求：支持取消请求，可以用于处理用户取消操作或超时情况。

### 缺点

* 需要额外安装：需要通过 npm 或其他包管理工具安装。
* 体积较大：相比原生的 Fetch，Axios 的体积稍大一些。

```
const axios = require('axios');

// GET 请求
axios.get('https://jsonplaceholder.typicode.com/posts')
  .then(response => {
    console.log(response.data);
  })
  .catch(error => {
    console.error('There has been a problem with your axios operation:', error);
  });

// POST 请求
axios.post('https://jsonplaceholder.typicode.com/posts', {
  title: 'foo',
  body: 'bar',
  userId: 1,
})
  .then(response => {
    console.log(response.data);
  })
  .catch(error => {
    console.error('There has been a problem with your axios operation:', error);
  });
```

## 函数式编程

函数式编程（Functional Programming, FP）是一种编程范式，它将计算机程序视为数学函数的求值，并避免了改变状态和可变数据。以下是函数式编程的主要概念和特点：

1. **纯函数（Pure Functions）**：
   * 纯函数是指那些给定相同的输入总是返回相同的结果，并且不会产生副作用的函数。这意味着它们不依赖于外部状态（如全局变量），也不会修改外部状态。
2. **不可变性（Immutability）**：
   * 在函数式编程中，一旦创建了一个对象或变量，就不能再对其进行修改。如果需要更新数据，必须创建新的数据结构来表示变化，而不是直接修改原有的数据。
3. **高阶函数（Higher-Order Functions）**：
   * 高阶函数是可以接受其他函数作为参数、或者返回一个函数作为结果的函数。这种特性使得代码更加灵活和可重用。
   * 一等函数：在函数式编程中，函数被视为“第一等公民”，这意味着函数可以像其他数据一样传递、返回或存储。
4. **函数组合（Function Composition）**：
   * 函数可以组合在一起形成更复杂的操作。通过组合简单的函数来构建复杂的功能，可以提高代码的可读性和可维护性。
5. **递归（Recursion）**：
   * 由于函数式编程避免使用循环和可变状态，因此常用递归来实现迭代逻辑。递归函数是调用自身的函数。
6. **柯里化（Currying）**：
   * 柯里化是一种将带有多个参数的函数转换为一系列仅接受单个参数的函数的技术。这有助于提高代码的灵活性和复用性。
7. **惰性求值（Lazy Evaluation）**：
   * 惰性求值是指推迟表达式的计算直到真正需要其结果的时候。这种方式可以优化性能并允许处理无限序列。

## 特点

* **声明式编程风格**：强调“做什么”而不是“怎么做”。代码通常更加简洁和直观。
* **易于测试和调试**：因为函数没有副作用，所以更容易预测行为，也更容易进行单元测试。
* **并行与并发支持**：由于减少了共享状态和可变数据，编写并行或并发程序变得更加容易。
* **模块化设计**：鼓励小而专一的函数设计，有利于代码的重用和扩展。

函数式编程不仅是一种编程技术，也是一种思维方式，可以帮助开发者写出更清晰、更可靠的代码。尽管函数式编程在某些场景下可能不如命令式编程直观，但它提供的抽象层次和编程模型对于解决复杂问题非常有用。常见的函数式编程语言包括Haskell、Lisp、Erlang等，同时现代的多范式语言如JavaScript、Python和Java也提供了对函数式编程的支持。

### 柯里化

柯里化主要用于将接受多个参数的函数转换为接受单个参数的函数，并返回一个新函数，该新函数接受余下的参数并返回结果。

### 示例代码

```javascript
// 普通的add函数
function add(x, y) {
  return x + y;
}

// Currying后
function curryingAdd(x) {
  return function (y) {
    return x + y;
  }
}

console.log(add(1, 2)); // 输出: 3
console.log(curryingAdd(1)(2)); // 输出: 3
```

更多内容可以参考 [这篇文章](https://www.jianshu.com/p/2975c25e4d71)。

## async defer

### async和defer的相同点

都是异步加载script，加载的过程都不会阻塞html的解析。

### 不同点

1. async和defer的执行时机不同，async是在加载完后立即执行，执行的过程仍会阻塞后续html的解析。defer是在html解析完，DomContentLoaded之前执行。
2. async不能保证script标签的执行顺序（谁先加载完谁先执行），defer在html解析完之后按顺序执行。

### 总结

通常情况下defer的使用频率较高，它能保证script之间的变量依赖。

需要注意的是：async script的资源请求时异步的，但script的执行仍然会阻塞后续渲染（单线程），defer是在html渲染完之后执行的所以不会阻塞后续html的解析。



## REM

css中的body中先全局声明font-size=62.5%，这里的%的算法和rem一样。

因为100%=16px，1px=6.25%，所以10px=62.5%，

这是的1rem=10px，所以12px=1.2rem。px与rem的转换通过10就可以得来，很方便了吧！

## 使用方法

注意，rem是只相对于根元素html的font-size，即只需要设置根元素的font-size，其他元素使用rem单位设置成相应的百分比即可；

一般情况下，是这样子使用的：

```css
html {
  font-size: 62.5%;
}

body {
  font-size: 12px;
  font-size: 1.2rem;
}

p {
  font-size: 14px;
  font-size: 1.4rem;
}
```

## Vite 特性整理

### 1. 原生 ES 模块支持

* **无打包启动**：Vite 利用了浏览器对原生 ES 模块的支持，开发过程中无需打包整个项目。这意味着启动开发服务器时，Vite 只需读取和解析模块图，而不是像 Webpack 那样将所有文件打包成一个或多个 bundle。
* **按需编译**：只有在实际请求某个模块时，Vite 才会编译和转换该模块。这大大减少了启动时间和热更新的时间。

### 2. 开发服务器优化

* **快速冷启动**：由于 Vite 使用了原生 ES 模块，开发服务器的启动时间非常短，通常只需几秒钟。
* **热模块替换（HMR）**：Vite 提供了高效的热模块替换（HMR）机制，可以在不刷新页面的情况下实时更新代码。HMR 的实现更加精细，只更新实际发生变化的部分，而不是整个模块。

### 3. 依赖预构建

* **预构建依赖**：Vite 会自动预构建项目中的依赖项，将它们转换为 ES 模块格式。这样可以避免在开发过程中重复编译第三方库，进一步加快了开发速度。
* **缓存机制**：预构建的依赖会被缓存起来，下次启动时可以直接使用，进一步减少了启动时间。

### 4. 内置开发服务器

* **内置开发服务器**：Vite 自带了一个开发服务器，基于 Node.js 的 esbuild 和 rollup，提供了开箱即用的开发环境。开发服务器支持多种开发工具和插件，如 TypeScript、CSS 预处理器等。
* **自动重启**：开发服务器会监听文件变化，自动重启以反映最新的代码变更。

### 5. 生产构建优化

* **高效构建**：虽然 Vite 在开发模式下不进行打包，但在生产构建时，Vite 会使用 Rollup 进行高效的打包。Rollup 的构建速度通常比 Webpack 更快，尤其是在处理大型项目时。
* **Tree Shaking**：Vite 通过 Rollup 进行 Tree Shaking，移除未使用的代码，减小最终的包体积。

## 实现地图功能

### 1. 数据准备

* **地图数据**
  * 矢量数据：使用 GeoJSON 格式的数据，包含点、线、多边形等地理要素。
  * 栅格数据：使用 TIFF、PNG、JPEG 等格式的卫星图像或地图瓦片。
* **数据来源**
  * 公开数据：可以从 OpenStreetMap、Natural Earth 等网站获取免费的地理数据。
  * 自采数据：使用 GPS 设备或无人机采集数据。

### 2. 前端开发

* **选择框架**
  * 纯 JavaScript：使用原生 JavaScript 和 HTML5 Canvas 或 SVG。
  * Web GIS 框架：使用 Leaflet、OpenLayers 等成熟的地图库。
* **基本地图显示**
  *   使用 Leaflet：

      1. 引入 Leaflet：

      ```html
      <link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css"/>
      <script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>
      ```

      2. 创建地图容器：

      ```html
      <div id="map" style="height: 500px;"></div>
      ```

      3. 初始化地图：

      ```javascript
      var map = L.map('map').setView([51.505, -0.09], 13);
      L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
          attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
      }).addTo(map);
      ```
* **使用Canvas**
  * GeoJSON 是一种强大且灵活的地理数据格式，大多数现代地图库（如 Leaflet、OpenLayers、Mapbox）都支持直接使用 GeoJSON 数据。
  * `coordinates` 是一个包含多个数值的数组，表示经度和纬度。
  * GeoJSON 对象可以包含以下几种类型：
    * 几何对象（Geometry）：表示地理形状，如点、线、多边形等。
    * 特征（Feature）：包含几何对象和属性信息。
    * 特征集合（Feature Collection）：包含多个特征对象。

这些步骤和说明旨在提供一个清晰的指导，帮助理解如何利用 Vite 加速前端开发流程以及如何使用 Leaflet 或其他地图库来展示地图数据。

## 设计模式

### 代理模式

**定义**：为其他对象提供一种代理以控制对这个对象的访问。

**静态代理实现步骤**：

1. 定义一个接口或抽象类（服务类接口）。
2. 创建具体的服务类（实现服务接口的具体类）。
3. 创建代理类，持有具体服务类的实例，并在代理类中实现与服务类相同的接口。
4. 在客户端代码中使用代理类。

```java
public class Client {
    public static void main(String[] args) {
        Subject subject = new ConcreteSubject(); // 具体类
        Proxy proxy = new Proxy(subject); // 创建代理并传入具体类
        proxy.request(); // 使用代理请求
    }
}
```

**与装饰器模式的区别**：

* **装饰器模式**：侧重于动态地给对象添加行为或职责，而无需修改其结构。
* **代理模式**：主要用于控制对对象的访问，可能用于延迟初始化、访问控制等。

### 单例模式

确保一个类仅有一个实例，并提供一个全局访问点。通常通过私有化构造函数和提供一个静态方法来获取唯一实例。

```java
public class Singleton {
    private static Singleton instance;
    
    private Singleton() {}
    
    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

**注意**：`Synchronized`保证了线程安全，但在高并发情况下性能可能不是最优，可以考虑双重检查锁定或静态内部类等方式优化。

### 工厂模式

#### 简单工厂模式

通过一个工厂类根据传入参数决定创建哪种类的实例。

```java
public class Demo {
    public static void main(String[] args) {
        PhoneFactory factory = new PhoneFactory();
        Phone miPhone = factory.makePhone("MiPhone");
        Phone iPhone = factory.makePhone("iPhone");
    }
}
```

#### 工厂方法模式

定义一个创建对象的接口，但由子类决定要实例化的类是哪一个。这样，工厂方法模式让类的实例化推迟到子类。

```java
public class Demo {
    public static void main(String[] args) {
        AbstractFactory miFactory = new XiaoMiFactory();
        AbstractFactory appleFactory = new AppleFactory();
        miFactory.makePhone();
        appleFactory.makePhone();
    }
}
```

### 抽象工厂模式

提供一系列相关或相互依赖对象的创建，而不指定它们具体的类。

### 观察者模式与发布订阅模式的区别

* **耦合度**：观察者模式中，观察者和主题直接依赖；发布订阅模式中，发布者和订阅者通过消息中间件解耦。
* **灵活性**：观察者模式适合简单的依赖关系；发布订阅模式适用于更复杂的场景，支持更灵活的消息传递机制。
* **消息传递**：观察者模式中，消息传递是直接的；发布订阅模式中，消息通过中间件间接传递。
* **适用场景**：观察者模式适用于如GUI控件事件处理等简单依赖场景；发布订阅模式适用于需要解耦的复杂系统，如事件驱动系统、消息队列等。

以上是对原内容进行了细化和必要的补充，以便更加清晰准确地描述各个设计模式的特点和实现方式。

## 数据结构的选择指南

在实际应用中，选择哪种数据结构取决于具体的业务逻辑和技术要求。以下是一些考虑因素：

* 性能需求：根据操作频率（如查找、插入、删除）来选择最优化的数据结构。
* 内存限制：某些数据结构可能占用更多内存，因此要考虑系统的资源约束。
* 并发控制：如果涉及到多线程或分布式环境下的并发访问，确保所选数据结构支持必要的同步机制。
* 扩展性和维护性：随着项目的成长，代码的可读性和易于维护也是重要的考量因素。
* **数组、堆、栈**：侧重于数据的存储和访问方式，堆和栈分别有各自的应用场景（如优先级处理和后进先出逻辑）。
* **树形结构与图形结构**：前者用于表现明显的层次关系（如组织架构），后者用于描述复杂的、多对多的节点关系（如社交网络）。
* **HashMap**：高效的键值对存储容器，内部基于数组与链表（或树）的组合，使用时需要关注容量和负载因子。
* **链表**：提供高效的插入和删除操作，但查找效率较低；包括单向链表、双向链表和循环链表三种常见形式。
* **数组与链表**：各有优缺点，选择时需根据具体场景考虑内存连续性、查找、插入、删除等操作的效率。
* **二叉树**：作为树形结构的一种特殊形式，广泛用于实现搜索、排序和数据组织等功能。\


## **Serverless 架构**

* **概念**：\
  开发者无需关注底层服务器的运维，云服务提供商负责基础设施管理，开发者只专注于业务逻辑。
* **层次划分**：
  * **FaaS（Function as a Service）**
    * 基于事件触发（如文件上传、HTTP 请求等），执行单个函数。
    * 无状态、按实际使用时间和资源付费。
    * 示例：AWS Lambda。
  * **BaaS（Backend as a Service）**
    * 提供数据库、身份验证、推送通知等后端服务，通过简单 API 或 SDK 调用。
  * **PaaS（Platform as a Service）**
    * 提供完整的开发与部署平台（操作系统、数据库、网络等），支持自动部署和 CI/CD，高度可定制。

***

**Serverless 产品**

* **概念**：\
  指无需理解或管理服务器的产品，用户按需使用、按量付费。
* **特点**：\
  包含存储、计算等多种产品，其中典型的计算产品就是云函数。

***

**总结**

* **FaaS** 专注于单个函数的事件驱动执行；
* **BaaS** 整合了多种后端服务；
* **PaaS** 则提供了一个完整的开发和部署环境。
* 无论是架构还是产品，Serverless 都强调开发者无需管理服务器，专注于业务逻辑，并按实际使用付费。

## PWA&#x20;

* **渐进增强**：PWA 可以在任何支持现代 Web 技术的浏览器中运行，并且能够根据浏览器的支持程度逐步提供相应的功能。即使在不支持某些高级特性的旧浏览器中，也能保证基本的可用性，为所有用户提供一致的核心体验。
* **响应式设计**：PWA 具备良好的响应式布局，能够自适应不同的设备屏幕尺寸和分辨率，无论是在手机、平板还是电脑上，都能提供流畅、舒适的浏览和交互体验。
* **离线支持**：借助 Service Worker 技术，PWA 可以在离线状态下继续运行部分功能。Service Worker 能够拦截网络请求，将关键资源（如 HTML、CSS、JavaScript、图片等）缓存到本地，当设备离线时，应用可以直接从本地缓存中获取资源，从而实现离线浏览和操作。
* **推送通知**：PWA 支持向用户发送推送通知，就像原生应用一样。这使得开发者可以及时向用户传达重要信息、提醒、活动通知等，增加用户与应用的互动和粘性。
* **添加到主屏幕**：用户可以将 PWA 添加到设备的主屏幕，形成一个类似原生应用的图标。点击该图标可以直接启动应用，无需通过浏览器访问，为用户提供了便捷的访问方式

#### 技术组成

* **Service Worker**：这是 PWA 的核心技术之一，它是一种在浏览器后台运行的脚本，独立于网页，能够拦截网络请求、管理缓存、处理推送通知等。通过 Service Worker，PWA 可以实现离线支持和后台同步等功能。
* **Web App Manifest**：这是一个 JSON 文件，用于描述 PWA 的基本信息，如应用名称、图标、启动页面、主题颜色等。它告诉浏览器如何将 PWA 以类似原生应用的方式展示给用户，包括添加到主屏幕后的外观和行为。
* **HTTPS**：为了确保用户数据的安全和隐私，PWA 必须通过 HTTPS 协议进行访问。HTTPS 可以防止数据在传输过程中被窃取或篡改，同时也是使用 Service Worker 和推送通知等功能的必要条件。
