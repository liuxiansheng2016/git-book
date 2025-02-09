
##ES6

### AST抽象语法树（Abstract Syntax Tree）
- **解析和验证**：通过解析源代码，将其转换为AST之后，可以对代码进行验证和静态分析。这包括检查语法错误、类型错误、变量引用等，并发现潜在的问题或优化机会。

- **优化和转换**：AST可以用于执行各种优化操作，例如消除冗余代码、提取共享表达式、内联函数调用等。它还能够进行代码转换，例如将ES6代码转换为ES5兼容的代码、将模板编译为渲染函数等。

- **生成代码**：从AST中可以再次生成目标代码，如JavaScript、HTML、CSS等。这使得可以将源代码翻译为其他语言、在不同平台上执行代码等。

### 将ES6代码转换成es5的实现思路
- 将代码解析成抽象语法树，即AST

- 对AST进行处理，这个阶段可以对Es6进行相应转换，即转成es5代码

- 根据处理后的AST再生成代码字符串

### 基础
let和const,变量的解构，字符串的拓展（模板字符串），数组的拓展，字符串的拓展， symbol类型，迭代器：array，map,set,for in ,for of”,promise, async, generator.

Bable转码let 给var加_

let 和 const: let具有块级作用域的特性，

for 循环时产生的闭包 可以使用let

### 箭头函数

箭头函数提供了一种更简洁的语法来编写函数，并且其`this`值是在定义时确定的，而不是在执行时。

```javascript
// 定义一个箭头函数
const arrowFunc = (...args) => {
    console.log(args);
};

arrowFunc(1, 2, 3); // 输出: [1, 2, 3]
```

- **特点**:
  - `this`绑定到定义时的作用域，而非调用时。
  - 不支持`arguments`对象，但可以使用剩余参数(`...`)替代。
  - 不能作为构造函数使用，即不能用`new`关键字实例化。

### 泛型

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

// 使用泛型保留类型信息
function identity<T>(arg: T): T {
    return arg;
}
```

- **好处**: 提高代码重用性，同时确保传入和返回的数据类型一致。

### Symbol

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

- **特点**:
  - 每个`Symbol`值都是独一无二的。
  - 可以通过`Symbol.for()`创建共享的`Symbol`值。
  - `Symbol`不能与`new`一起使用，因为它不是对象。

### Reflect 和 Proxy 

#### Reflect 对象

`Reflect` 是一个内置的对象，它提供了一系列方法，这些方法与 `Proxy` 处理器方法相对应。通过 `Reflect`，可以更方便地执行语言内部的操作，并且提供了更一致的错误处理机制。

#### 主要特点和用途

1. **将 Object 对象的一些内部方法移到了 Reflect 上**

   - 这使得一些操作更加直观和函数式，例如属性定义、删除等。
   
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

   - 这种设计允许代码在不使用 `try...catch` 的情况下处理失败的情况，使错误处理更加合理和简便。
   
3. **让操作对象的编程变为函数式编程**

   - 传统上的一些命令式操作可以通过 `Reflect` 方法以函数式的方式实现，这不仅提高了代码的可读性，还增强了代码的一致性和可维护性。
   
   ```javascript
   // 命令式编程
   'name' in obj;

   // 函数式编程
   Reflect.has(obj, 'name');
   ```

4. **保持和 Proxy 对象的方法一一对应**

   - `Reflect` 提供的方法与 `Proxy` 的处理器方法是一一对应的，这意味着你可以使用 `Reflect` 来简化对目标对象的操作，尤其是在编写代理逻辑时。

### Proxy 对象

`Proxy` 对象用于定义基本操作的自定义行为（如属性查找、赋值等），从而修改某些默认行为或添加额外的功能。

#### 示例：结合 `Reflect` 使用 `Proxy`

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

- 在这个例子中，我们创建了一个代理来拦截对 `target` 对象的 `get` 和 `set` 操作。通过使用 `Reflect`，我们可以轻松地调用原始操作并根据结果进行相应的处理。
  
#### 总结

- **`Reflect`** 提供了一组标准的方法来执行语言内部的操作，使得对对象的操作更加一致和可预测。它不仅可以避免抛出异常，还能让代码看起来更像函数式编程风格。
  
- **`Proxy`** 允许你自定义对象的基本操作，从而改变其默认行为或增加额外功能。结合 `Reflect` 使用，可以让代理逻辑更加简洁明了。

这两者的结合使用极大地提升了 JavaScript 处理对象的能力，无论是对于数据验证、日志记录还是权限控制等方面都有很大的帮助。

### Array 新特性

#### Array.of方法用于将一组值，转换为数组。

console.log(Array.of(1, 2, 3)); // [1, 2, 3]

console.log(Array.of('a', 'b', 'c')); // ['a', 'b', 'c']

console.log(Array.of(7)); // [7]

Array.from方法用于将两类对象转为真正的数组：类似数组的对象（array-like object）和可遍历（iterable）的对象（包括 ES6 新增的数据结构 Set 和 Map）。

#### Array.from()

`Array.from()`方法从类似数组或可迭代对象创建一个新的浅拷贝数组实例。

```javascript
console.log(Array.from('foo')); // 输出: ["f", "o", "o"]
console.log(Array.from([1, 2, 3], x => x + x)); // 输出: [2, 4, 6]
```

#### flat() 方法

`flat()`方法用于将嵌套的数组“拉平”，变成一维数组。

```javascript
console.log([1, 2, [3, 4]].flat()); // 输出: [1, 2, 3, 4]
console.log([1, [2, [3]]].flat(Infinity)); // 输出: [1, 2, 3]
```

#### flatMap() 方法

`flatMap()`方法首先映射每个元素，然后将结果扁平化为一个新数组。

```javascript
console.log([1, 2, 3].flatMap(x => [x * 2])); // 输出: [2, 4, 6]
```

### Set 和 Map 数据结构

#### Set

`Set` 是一种新的数据结构，它类似于数组，但是成员的值都是唯一的，没有重复的值。

- **基本属性和方法**

  - `Set.prototype.size`: 返回集合中的元素总数。
  - 操作方法：
    - `add(value)`: 添加某个值，返回 Set 结构本身。
    - `delete(value)`: 删除某个值，返回一个布尔值表示删除是否成功。
    - `has(value)`: 返回一个布尔值，表示该值是否为 Set 的成员。
    - `clear()`: 清除所有成员，无返回值。
  - 遍历操作：
    - `keys()`, `values()`: 由于 Set 结构没有键名，只有键值，因此这两个方法的行为完全一致，都返回值的遍历器。
    - `entries()`: 返回键值对的遍历器（对于 Set 来说，每个键值对是相同的）。
    - `forEach(callbackFn, thisArg?)`: 使用回调函数遍历每个成员。

```javascript
let set = new Set([1, 2, 3]);
set.add(4);
console.log(set.has(4)); // true
set.delete(2);
console.log(set); // Set {1, 3, 4}
```

#### Map

`Map` 是一种键值对的集合，其中的键可以是任意类型的数据。这与传统的对象不同，后者只能使用字符串作为键。

- **基本属性和方法**

  - `size`: 返回 Map 实例的成员总数。
  - 方法：
    - `set(key, value)`: 设置键名（key）对应的键值（value），然后返回 Map 构造函数生成的对象。
    - `get(key)`: 获取 key 对应的键值，如果找不到 key，返回 undefined。
    - `delete(key)`: 删除 key，成功返回 true，失败返回 false。
    - `has(key)`: 判断是否有 key 存在。
    - `clear()`: 清空 Map 实例的所有成员。
  - 遍历操作：
    - `keys()`: 返回键名的遍历器。
    - `values()`: 返回键值的遍历器。
    - `entries()`: 返回键值对的遍历器。
    - `forEach(callbackFn, thisArg?)`: 使用回调函数遍历每个成员。

```javascript
let map = new Map();
map.set('name', 'Alice');
console.log(map.get('name')); // "Alice"
console.log(map.has('age')); // false
```

### Promise 对象

`Promise` 是处理异步编程的一种解决方案，有三种状态：`Pending`（进行中）、`Fulfilled`（已完成）、`Rejected`（已失败）。

- **主要方法**

  - `Promise.all(iterable)`: 当所有 promises 都完成时调用。
  - `Promise.race(iterable)`: 当其中一个 promise 完成或拒绝时立即调用。
  - `Promise.allSettled(iterable)`: 等待所有 promises 完成，无论结果如何。
  - `Promise.resolve(value)`: 返回一个新的 Promise 实例，并且会立即变为 Resolved 状态。
  - `Promise.reject(reason)`: 返回一个带有拒绝原因的新 Promise 实例。

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
- **‌Promise的主要缺点包括‌：**

-‌ 链式调用的可读性‌：虽然Promise通过链式调用提高了代码的可读性，但当链过长时，可能会导致代码难以理解和维护‌

‌- 无法取消‌：Promise对象在创建后会立即执行，并且无法中途取消。这意味着如果有一个长时间运行的异步操作，我们无法在需要时停止它‌

‌- 无法得知当前状态‌：Promise对象有三种状态：pending(进行中)、fulfilled(成功)和rejected(失败)。然而，一旦Promise的状态从pending变为fulfilled或rejected，我们就无法再获取到其当前的状态或进度。这意味着我们无法得知异步操作何时完成，或者已经完成了多少
‌
-‌ 错误处理不够灵活‌：如果不设置回调函数，Promise内部抛出的错误不会反应到外部。如果忘记添加catch方法，Promise内部的错误可能会被忽略，这可能导致难以调试的问题

- **‌实现promise all**
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
### Generator 函数

Generator 函数是一个可以暂停执行和恢复执行的函数，使用 `function*` 语法声明，内部使用 `yield` 表达式来暂停函数的执行。

- **特点**
  - 可以通过 `next()` 方法控制执行流程。
  - `yield` 表达式用于暂停 Generator 函数的执行。
  - `async/await` 是基于 Generator 和 Promise 的更高层次抽象。

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
### Iterator（遍历器）

在 ES6 中，引入了 `Iterator` 这一机制，它提供了一种统一的接口，使得各种不同的数据结构可以通过该接口完成遍历操作。任何数据结构只要部署了 `Iterator` 接口，就可以通过一致的方式依次处理该数据结构的所有成员。

##### 核心概念

- **`Symbol.iterator`**：这是 `Iterator` 接口的默认方法名。如果一个数据结构具有这个属性，则认为它是“可遍历的”（iterable）。
- **迭代过程**：调用 `Symbol.iterator` 方法会返回一个迭代器对象，该对象必须包含一个名为 `next` 的方法。每次调用 `next()` 方法都会返回一个 `{ value, done }` 形式的对象，其中 `value` 是当前元素的值，`done` 是一个布尔值，表示是否遍历结束。

##### 示例：自定义数组的 `@@iterator` 方法

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

#### 获取迭代器对象

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
