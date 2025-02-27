# Es6



## 基础

let和const,变量的解构，字符串的拓展（模板字符串），数组的拓展，字符串的拓展，symbol类型，迭代器：array，map,set,for in ,for of”,promise, async, generator。

Bable转码let 给var加\_

#### 将ES6代码转换成es5的实现思路

* 将代码解析成抽象语法树，即AST
* 对AST进行处理，这个阶段可以对Es6进行相应转换，即转成es5代码
* 根据处理后的AST再生成代码字符串

let 和 const

**`let`**：具有块级作用域，存在暂时性死区，不允许在同一作用域内重复声明变量

**`const`**：除了具有 `let` 的特性外，还用于声明常量，一旦赋值不能重新赋值，但对于引用类型可以修改其内部属性或元素

for 循环时产生的闭包 可以使用let

模拟let

```
(function() {
    var c = 3;
    console.log(c); // 1
})();

console.log(c); // c is not defined
```

模拟const

```
function _const(key, value) {
    window[key] = value;
    Object.defineProperty(window, key, {
        enumerable: false,
        configurable: false,
        get: function() {
            return value;
        },
        set: function(newValue) {
            if (newValue !== value) {
                throw TypeError("这是只读变量，不可修改");
            } else {
                return value;
            }
        },
    });
}

```

暂时性死区

```
var tmp = 123;

if (true) {
    tmp = 'abc'; // ReferenceError
    let tmp;
}
```

解构

```
const person = {
    name: 'John',
    age: 30,
    address: {
        street: '123 Main St',
        city: 'New York',
        state: 'NY'
    }
};

// 嵌套对象解构
const { name, age, address: { street, city, state } } = person;

console.log(name); // 输出: John
console.log(age);  // 输出: 30
console.log(street); // 输出: 123 Main St
console.log(city); // 输出: New York
console.log(state); // 输出: NY

// 嵌套数组解构
const [first, [second, third], fourth] = nestedArray;

console.log(first); // 输出: 1
console.log(second);  // 输出: 2
console.log(third); // 输出: 3
console.log(fourth); // 输出: 4
```

#### Rest 参数（Rest Parameters）

<mark style="color:red;">Rest 参数允许我们将一个不定数量的参数表示为一个数组</mark>。它通常用于函数声明中，当你不确定会传递多少个参数给函数时特别有用。Rest 参数必须是函数定义中的最后一个参数。

```
function sum(...numbers) {
  return numbers.reduce((acc, curr) => acc + curr, 0);
}

console.log(sum(1, 2, 3)); // 输出: 6
console.log(sum(1, 2, 3, 4, 5)); // 输出: 15
```

#### Spread 操作符（Spread Operator）

\
Spread 操作符允许你将一个可迭代对象（如数组、字符串、Set 等）展开为多个独立的元素。它可以用于函数调用、数组字面量和对象字面量等场景。

## super

<mark style="color:red;">它让子类能够方便地访问和调用父类的属性、方法和构造函数</mark>

1. 在子类的构造函数里，`super` 可用于调用父类的构造函数，以完成父类属性的初始化。子类定义了构造函数时，必须调用 `super`

```
// 定义父类
class Animal {
    constructor(name) {
        this.name = name;
    }
}

// 定义子类
class Dog extends Animal {
    constructor(name, breed) {
        // 调用父类的构造函数
        super(name);
        this.breed = breed;
    }
}

// 创建 Dog 类的实例
const myDog = new Dog('Buddy', 'Golden Retriever');
console.log(myDog.name); // 输出: Buddy
console.log(myDog.breed); // 输出: Golden Retriever
```

2. #### 在子类方法中调用父类方法
3. <mark style="color:red;">在子类的构造函数中，</mark><mark style="color:red;">`super`</mark> <mark style="color:red;"></mark><mark style="color:red;">必须在使用</mark> <mark style="color:red;"></mark><mark style="color:red;">`this`</mark> <mark style="color:red;"></mark><mark style="color:red;">之前调用。这是因为在调用</mark> <mark style="color:red;"></mark><mark style="color:red;">`super`</mark> <mark style="color:red;"></mark><mark style="color:red;">之前，</mark><mark style="color:red;">`this`</mark> <mark style="color:red;"></mark><mark style="color:red;">还未被正确初始化</mark>

## 箭头函数

箭头函数提供了一种更简洁的语法来编写函数，并且其`this`值是在定义时确定的，而不是在执行时。

```javascript
// 定义一个箭头函数
const arrowFunc = (...args) => {
    console.log(args);
};

arrowFunc(1, 2, 3); // 输出: [1, 2, 3]
```

* **特点**:
  * `this`绑定到定义时的作用域，而非调用时。
  * 不支持`arguments`对象，但可以使用剩余参数(`...`)替代。
  * 不能作为构造函数使用，即不能用`new`关键字实例化。

## 泛型

泛型允许函数或组件支持多种数据类型，同时保持类型安全。

```typescript
// 不使用泛型
function identity(arg: number): number {
    return arg;
}

// 使用any类型（丢失类型信息）
function identityAny(arg: any): any {
    return arg;
}

因此，我们需要一种方法使返回值的类型与传入参数的类型是相同的。 这里，我们使用了 类型变量，它是一种特殊的变量，只用于表示类型而不是值。
// 使用泛型保留类型信息
function identity<T>(arg: T): T {
    return arg;
}
```

* **好处**: 提高代码重用性，同时确保传入和返回的数据类型一致。

## Symbol

ES6引入了新的原始数据类型`Symbol`，表示独一无二的值。

```javascript
let s = Symbol();
console.log(s); // 输出一个唯一的symbol值

const sym = Symbol('foo');
console.log(sym.description); // 输出: "foo"

let a = {};
a[s] = "hello";
console.log(a[s]); // 输出: "hello"
```

* **特点**:
  * 每个`Symbol`值都是独一无二的。
  * 可以通过`Symbol.for()`创建共享的`Symbol`值。
  * `Symbol`不能与`new`一起使用，因为它不是对象。

## Reflect 和 Proxy

<mark style="color:red;">`Proxy`</mark> <mark style="color:red;"></mark><mark style="color:red;">主要用于拦截和自定义对象的基本操作，而</mark> <mark style="color:red;"></mark><mark style="color:red;">`Reflect`</mark> <mark style="color:red;"></mark><mark style="color:red;">则提供了一系列与对象交互的统一方法</mark>

### Reflect 对象

`Reflect` 是一个内置的对象，它提供了一系列方法，这些方法与 `Proxy` 处理器方法相对应，与 `Object` 的某些方法功能相似。通过 `Reflect`，可以更方便地执行语言内部的操作，并且提供了更一致的错误处理机制。

### 主要特点和用途

1.  **将 Object 对象的一些内部方法移到了 Reflect 上**

    * 这使得一些操作更加直观和函数式，例如属性定义、删除等。

    ```javascript
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
    ```
2. **操作对象时出现报错返回 false 而不是抛出异常**
   * 这种设计允许代码在不使用 `try...catch` 的情况下处理失败的情况，使错误处理更加合理和简便。
3.  **让操作对象的编程变为函数式编程**

    * 传统上的一些命令式操作可以通过 `Reflect` 方法以函数式的方式实现，这不仅提高了代码的可读性，还增强了代码的一致性和可维护性。

    ```javascript
    // 命令式编程
    'name' in obj;

    // 函数式编程
    Reflect.has(obj, 'name');
    ```
4. **保持和 Proxy 对象的方法一一对应**
   * `Reflect` 提供的方法与 `Proxy` 的处理器方法是一一对应的，这意味着你可以使用 `Reflect` 来简化对目标对象的操作，尤其是在编写代理逻辑时。

### Proxy 对象

`Proxy` 是 ES6 引入的一个特性，<mark style="color:red;">它用于创建一个对象的代理</mark>，<mark style="color:red;">从而可以对该对象的基本操作</mark>（如属性查找、赋值、枚举、函数调用等）<mark style="color:red;">进行拦截和自定义处理</mark>。通过 `Proxy`，可以在不修改原始对象的基础上，对对象的行为进行扩展和控制。

#### **应用场景**

1\. 数据验证

2\. 访问控制

### 示例：结合 `Reflect` 使用 `Proxy`

```javascript
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
```

* 在这个例子中，我们创建了一个代理来拦截对 `target` 对象的 `get` 和 `set` 操作。通过使用 `Reflect`，我们可以轻松地调用原始操作并根据结果进行相应的处理。

### 总结

* **`Reflect`** 提供了一组标准的方法来执行语言内部的操作，使得对对象的操作更加一致和可预测。它不仅可以避免抛出异常，还能让代码看起来更像函数式编程风格。
* **`Proxy`** 允许你自定义对象的基本操作，从而改变其默认行为或增加额外功能。结合 `Reflect` 使用，可以让代理逻辑更加简洁明了。

这两者的结合使用极大地提升了 JavaScript 处理对象的能力，无论是对于数据验证、日志记录还是权限控制等方面都有很大的帮助。

## Array 新特性

### Array.of

方法用于将一组值，转换为数组。

console.log(Array.of(1, 2, 3)); // \[1, 2, 3]

console.log(Array.of('a', 'b', 'c')); // \['a', 'b', 'c']

console.log(Array.of(7)); // \[7]

Array.from方法用于将两类对象转为真正的数组：类似数组的对象（array-like object）和可遍历（iterable）的对象（包括 ES6 新增的数据结构 Set 和 Map）。

### Array.from()

`Array.from()`方法从类似数组或可迭代对象创建一个新的浅拷贝数组实例。

```javascript
console.log(Array.from('foo')); // 输出: ["f", "o", "o"]
console.log(Array.from([1, 2, 3], x => x + x)); // 输出: [2, 4, 6]
```

### flat() 方法

`flat()`方法用于将嵌套的数组“拉平”，变成一维数组。

```javascript
console.log([1, 2, [3, 4]].flat()); // 输出: [1, 2, 3, 4]
console.log([1, [2, [3]]].flat(Infinity)); // 输出: [1, 2, 3]
```

### flatMap() 方法

`flatMap()`方法首先映射每个元素，然后将结果扁平化为一个新数组。

```javascript
console.log([1, 2, 3].flatMap(x => [x * 2])); // 输出: [2, 4, 6]
```

## Set 和 Map 数据结构

### Set

`Set` 是一种新的数据结构，它类似于数组，但是成员的值都是唯一的，没有重复的值。

* **基本属性和方法**
  * `Set.prototype.size`: 返回集合中的元素总数。
  * 操作方法：
    * `add(value)`: 添加某个值，返回 Set 结构本身。
    * `delete(value)`: 删除某个值，返回一个布尔值表示删除是否成功。
    * `has(value)`: 返回一个布尔值，表示该值是否为 Set 的成员。
    * `clear()`: 清除所有成员，无返回值。
  * 遍历操作：
    * `keys()`, `values()`: 由于 Set 结构没有键名，只有键值，因此这两个方法的行为完全一致，都返回值的遍历器。
    * `entries()`: 返回键值对的遍历器（对于 Set 来说，每个键值对是相同的）。
    * `forEach(callbackFn, thisArg?)`: 使用回调函数遍历每个成员。

```javascript
let set = new Set([1, 2, 3]);
set.add(4);
console.log(set.has(4)); // true
set.delete(2);
console.log(set); // Set {1, 3, 4}
```

### Map

`Map` 是一种键值对的集合，其中的键可以是任意类型的数据。这与传统的对象不同，后者只能使用字符串作为键。

* **基本属性和方法**
  * `size`: 返回 Map 实例的成员总数。
  * 方法：
    * `set(key, value)`: 设置键名（key）对应的键值（value），然后返回 Map 构造函数生成的对象。
    * `get(key)`: 获取 key 对应的键值，如果找不到 key，返回 undefined。
    * `delete(key)`: 删除 key，成功返回 true，失败返回 false。
    * `has(key)`: 判断是否有 key 存在。
    * `clear()`: 清空 Map 实例的所有成员。
  * 遍历操作：
    * `keys()`: 返回键名的遍历器。
    * `values()`: 返回键值的遍历器。
    * `entries()`: 返回键值对的遍历器。
    * `forEach(callbackFn, thisArg?)`: 使用回调函数遍历每个成员。

```javascript
let map = new Map();
map.set('name', 'Alice');
console.log(map.get('name')); // "Alice"
console.log(map.has('age')); // false
```

### Weakmap

只接受对象作为键名

`WeakMap` 是一个键值对的集合，其中的键必须是对象，而值可以是任意类型。这些键是弱引用的，<mark style="color:red;">意味着如果对象没有其他引用，垃圾回收器会自动回收这些对象，即使它们作为</mark> <mark style="color:red;"></mark><mark style="color:red;">`WeakMap`</mark> <mark style="color:red;"></mark><mark style="color:red;">的键存在</mark>。

### Weakset

Weakset结构和set结构类似；区别在于

1\.    weakset结构只能是对象。

2\.    weakset中的引用都是弱引用；只要这些对象在外部消失，它在 WeakSet 里面的引用就会自动消失。(所以weakset不可以遍历)

## Promise 对象

`Promise` 是处理异步编程的一种解决方案，有三种状态：`Pending`（进行中）、`Fulfilled`（已完成）、`Rejected`（已失败）。

* **主要方法**
  * `Promise.all(iterable)`: 当所有 promises 状态都变成fulfilled或者某一个状态变成rejected时调用。
  * `Promise.race(iterable)`: 当其中一个 promise 完成或拒绝时立即调用。
  * `Promise.allSettled(iterable)`: 等待所有 promises 完成，无论结果如何。
  * `Promise.resolve(value)`: 返回一个新的 Promise 实例，并且会立即变为 Resolved 状态。
  * `Promise.reject(reason)`: 返回一个带有拒绝原因的新 Promise 实例。

```javascript
function timeout(ms) {
    return new Promise((resolve) => {
        setTimeout(resolve, ms, "done");
    });
}

timeout(100).then(value => console.log(value)); // 输出: "done"
```

```
new Promise((resolve, reject) => {
    console.log('Promise executor starts');
    resolve('Success!');
    console.log('After resolve inside executor');
});

new Promise((resolve, reject) => {
    console.log('Promise executor starts');
    return resolve('Success!');
    console.log('After resolve inside executor');
});
promise resolve调用后，后面的代码还是会会继续执行
resolve 只是改变了 Promise 的状态，并不会中断或提前结束执行器函数中的后续代码。
```

* **‌Promise的主要缺点包括‌：**

-‌ 链式调用的可读性‌：虽然Promise通过链式调用提高了代码的可读性，但当链过长时，可能会导致代码难以理解和维护‌

‌- 无法取消‌：Promise对象在创建后会立即执行，并且无法中途取消。这意味着如果有一个长时间运行的异步操作，我们无法在需要时停止它‌

‌- 无法得知当前状态‌：Promise对象有三种状态：pending(进行中)、fulfilled(成功)和rejected(失败)。然而，一旦Promise的状态从pending变为fulfilled或rejected，我们就无法再获取到其当前的状态或进度。这意味着我们无法得知异步操作何时完成，或者已经完成了多少 ‌&#x20;

-‌ 错误处理不够灵活‌：如果不设置回调函数，Promise内部抛出的错误不会反应到外部。如果忘记添加catch方法，Promise内部的错误可能会被忽略，这可能导致难以调试的问题

### Promise.race()：

如果参数中某个promise解决或拒绝，返回的 promise就会解决或拒绝。

### Promise.allSettled()

也就是说当Promise全部处理完成后我们可以拿到每个Promise的状态

### 写法

```
New Promise((resolve, reject) => {
  setTimeout(() => {}, 5000)
})
省略 reject的写法
New Promise(resolve ={
})
```

这张图片展示了一段 JavaScript 代码，该代码使用了 `Promise` 对象来处理异步操作。以下是代码的详细解释：

#### 代码结构

1. **`Promise.resolve(promise)`**:
   * `Promise.resolve(promise)` 会将 `promise` 转换为一个 `Promise`。
     * 如果 `promise` 本身已经是一个 `Promise`，则直接返回它。
     * 如果 `promise` 是一个非 `Promise` 值，则返回一个解析为该值的 `Promise`。
   * 这一步确保我们处理的是一个 `Promise`，即使传入的是一个非 `Promise` 值。
2. **`.then(onFulfilled, onRejected)`**:
   * `.then` 方法用于注册 `Promise` 的成功和失败回调。
     * `onFulfilled` 是成功回调，当 `Promise` 成功解析时调用。
     * `onRejected` 是失败回调，当 `Promise` 被拒绝时调用。

#### 代码详解

```javascript
Promise.resolve(promise).then(
    (value) => {
        results[index] = value;
        completed++;
        if (completed === promises.length) {
            resolve(results);
        }
    },
    (error) => {
        reject(error);
    }
);
```

* **`Promise.resolve(promise)`**: 将 `promise` 转换为一个 `Promise`。
* **`.then(onFulfilled, onRejected)`**:
  * **`onFulfilled` 回调**:
  * **`onRejected` 回调**:
    * `(error) => { reject(error); }`: 当 `Promise` 被拒绝时，执行这个回调函数，并通过 `reject(error)` 抛出错误。

总结

这段代码的主要目的是处理多个 `Promise` 的结果，并在所有 `Promise` 都成功解析后收集结果。具体步骤如下：

1. 使用 `Promise.resolve` 确保 `promise` 是一个 `Promise`。
2. 注册成功和失败回调：
   * 成功时，更新结果数组并检查是否所有 `Promise` 都已完成。
   * 失败时，抛出错误。

这种模式常用于处理并发请求或异步操作，确保所有操作完成后进行后续处理。

* **‌实现promise all**

```
function promiseAll(promises) {
    return new Promise((resolve, reject) => {
        if (!Array.isArray(promises)) {
            return reject(new TypeError('Argument is not an array'));
        }

        let resolvedCounter = 0;
        const result = new Array(promises.length);
        for (let i = 0; i < promises.length; i++) {
            Promise.resolve(promises[i])
                .then(value => {
                    resolvedCounter++;
                    // 按照原始顺序保存结果
                    result[i] = value;
                    if (resolvedCounter === promises.length) {
                        resolve(result);
                    }
                })
                .catch(error => {
                    // 如果任意一个 Promise 被拒绝，则整个 Promise.all 被拒绝
                    reject(error);
                });
        }
    });
}

// 测试代码
const p1 = Promise.resolve(3);
const p2 = 42;
const p3 = new Promise((resolve, reject) => {
    setTimeout(resolve, 100, 'foo');
});

promiseAll([p1, p2, p3]).then(values => {
    console.log(values); // [3, 42, "foo"]
}).catch(error => {
    console.error(error);
});
```

‌

## Async 和 await

* `async` 函数返回一个 `Promise`。
* `await` 表达式会使异步函数暂停执行，直到 `Promise` 解析完成。

#### 异步非阻塞的本质

JavaScript 在浏览器或 Node.js 环境中是单线程的，并且采用了事件循环机制来管理异步任务。这意味着所有的 JavaScript 代码都在一个线程上运行，但是通过事件循环和回调队列，可以有效地处理 I/O 密集型任务（如网络请求、文件系统访问等）而不阻塞主线程。

* 当一个函数被标记为 `async` 时，它会自动返回一个 `Promise`，并且可以在其内部使用 `await` 来暂停执行直到 `Promise` 被解决（即完成或拒绝）。
* <mark style="color:red;">使用</mark> <mark style="color:red;"></mark><mark style="color:red;">`await`</mark> <mark style="color:red;"></mark><mark style="color:red;">暂停的是当前异步函数的执行流程，而不是整个主线程。</mark>这意味着其他代码（包括其他异步操作或者定时器等）仍然可以在后台继续运行

## Generator 函数

Generator 函数是一个可以暂停执行和恢复执行的函数，使用 `function*` 语法声明，内部使用 `yield` 表达式来暂停函数的执行。

* **特点**
  * 可以通过 `next()` 方法控制执行流程。
  * `yield` 表达式用于暂停 Generator 函数的执行。
  * `async/await` 是基于 Generator 和 Promise 的更高层次抽象。

```javascript
function* helloWorldGenerator() {
    yield 'hello';
    yield 'world';
    return 'ending';
}

let hw = helloWorldGenerator();
console.log(hw.next()); // { value: 'hello', done: false }
console.log(hw.next()); // { value: 'world', done: false }
console.log(hw.next()); // { value: 'ending', done: true }
console.log(hw.next()); // { value: undefined, done: true }
```

```
function runGenerator(genFunc, ...args) {
  const gen = genFunc(...args);

  function handleResult(next) {
    if (next.done) return Promise.resolve(next.value);
    return Promise.resolve(next.value)
      .then(
        res => handleResult(gen.next(res)),
        err => handleResult(gen.throw(err))
      );
  }

  try {
    return handleResult(gen.next());
  } catch (err) {
    return Promise.reject(err);
  }
}

function* fetchDataGenerator(url) {
  const response = yield fetch(url);
  if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
  const data = yield response.json();
  return data;
}

runGenerator(fetchDataGenerator, 'https://api.example.com/data')
  .then(data => console.log(data))
  .catch(error => console.error('Error fetching data:', error));

```

## Iterator（遍历器）

在 ES6 中，引入了 `Iterator` 这一机制，它提供了一种统一的接口，使得各种不同的数据结构可以通过该接口完成遍历操作。任何数据结构只要部署了 `Iterator` 接口，就可以通过一致的方式依次处理该数据结构的所有成员。

#### 核心概念

* <mark style="color:red;">**`Symbol.iterator`**</mark><mark style="color:red;">：这是</mark> <mark style="color:red;"></mark><mark style="color:red;">`Iterator`</mark> <mark style="color:red;"></mark><mark style="color:red;">接口的默认方法名</mark>。如果一个数据结构具有这个属性，则认为它是“可遍历的”（iterable）。
* **迭代过程**：<mark style="color:red;">调用</mark> <mark style="color:red;"></mark><mark style="color:red;">`Symbol.iterator`</mark> <mark style="color:red;"></mark><mark style="color:red;">方法会返回一个迭代器对象，该对象必须包含一个名为</mark> <mark style="color:red;"></mark><mark style="color:red;">`next`</mark> <mark style="color:red;"></mark><mark style="color:red;">的方法。每次调用</mark> <mark style="color:red;"></mark><mark style="color:red;">`next()`</mark> <mark style="color:red;"></mark><mark style="color:red;">方法都会返回一个</mark> <mark style="color:red;"></mark><mark style="color:red;">`{ value, done }`</mark> <mark style="color:red;"></mark><mark style="color:red;">形式的对象，</mark>其中 `value` 是当前元素的值，`done` 是一个布尔值，表示是否遍历结束。

```
const range = {
    start: 1,
    end: 5,
    // 实现 Symbol.iterator 方法
    [Symbol.iterator]() {
        let current = this.start;
        return {
            // 实现 next() 方法
            next: () => {
                if (current <= this.end) {
                    return { value: current++, done: false };
                }
                return { value: undefined, done: true };
            }
        };
    }
};

// 使用 for...of 循环迭代自定义可迭代对象
for (const num of range) {
    console.log(num);
}
// 输出:1 2 3 4 5
```

#### 示例：自定义数组的 `@@iterator` 方法

下面的例子展示了如何为数组自定义 `Iterator` 接口，并使用 `for...of` 循环进行遍历。

```javascript
let arr = [1, 2, 3];

// 自定义数组的 @@iterator 方法
arr[Symbol.iterator] = function* () {
    let index = 0;
    while (index < this.length) {
        yield this[index++];
    }
};

// 使用 for...of 循环遍历数组
for (let value of arr) {
    console.log(value); // 输出: 1, 2, 3
}
```

在这个例子中：

1. 我们为数组 `arr` 定义了一个新的 `Symbol.iterator` 方法，这是一个生成器函数（`function*`），用于定义自定义的迭代逻辑。
2. 在生成器函数内部，我们使用 `yield` 关键字来逐个返回数组中的元素。
3. 使用 `for...of` 循环可以自动调用 `Symbol.iterator` 方法并遍历数组中的所有元素。

### 获取迭代器对象

除了直接使用 `for...of` 循环，你也可以手动获取迭代器对象，并显式地调用 `next()` 方法来进行遍历。

```javascript
let arr = [1, 2, 3];
let iterator = arr[Symbol.iterator]();

console.log(iterator.next()); // { value: 1, done: false }
console.log(iterator.next()); // { value: 2, done: false }
console.log(iterator.next()); // { value: 3, done: false }
console.log(iterator.next()); // { value: undefined, done: true }
```

这里，我们通过调用数组的 `Symbol.iterator` 方法获取到了该数组对应的迭代器对象，然后通过多次调用 `next()` 方法逐步遍历数组中的每个元素。

这种方式不仅适用于数组，还适用于其他内置的可迭代对象，如字符串、集合（Set）、映射（Map）等。通过实现 `Symbol.iterator` 方法，开发者可以为自定义的数据结构添加迭代能力，从而支持 `for...of` 循环和扩展运算符（`...`）等功能。
