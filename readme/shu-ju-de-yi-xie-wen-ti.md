# 数据的一些问题

### **原始类型是** **不可变的（Immutable）**

* **不可变**：指的是 **原始类型的值** **一旦创建就无法更改**。
* &#x20;**值本身** 不可更改，**变量** 可以重新赋值。

```
let str = 'hello';
str[0] = 'H';   // 尝试修改字符串
console.log(str);  // "hello" (未改变)

str = 'world';  // 重新赋值
console.log(str);  // "world"
```

#### JavaScript 中原始类型是不可变的，但为什么 String 可以调用 .toUpperCase()？

* 当调用 `str.toUpperCase()` 时，JavaScript 发生了以下步骤：
  1. **创建包装对象：** `new String(str)` 。
  2. **调用方法：** 调用包装对象上的方法 `toUpperCase()`。
  3. **销毁包装对象：** 立即销毁包装对象，只保留返回的结果。

```
let str = 'hello';
console.log(str.toUpperCase());  // "HELLO"

// 实际等效于
let tempStr = new String('hello');
let result = tempStr.toUpperCase();
tempStr = null;

console.log(result);  // "HELLO"
```

### 使用 `null` 的场景

1. 明确赋值为空对象
2. 初始化变量或占位符

```
let obj = { name: 'Alice' };
obj = null;  // 释放引用，等待 GC（垃圾回收）

let response = null;  // 初始化时为空，等待赋值
```

### `instanceof`

对象 `instanceof` 类 ，如果对象是这个类创建的，返回true，否则为false

#### **`instanceof` 通过原型链进行检查**

* `instanceof` 会沿着 **`object` 的原型链** 向上查找，判断 `object.__proto__`（或 `Object.getPrototypeOf(object)`）是否等于 `Constructor.prototype`

```
function customInstanceOf(obj, Constructor) {
  let prototype = Object.getPrototypeOf(obj); // 获取 obj 的原型
  const prototypeOfConstructor = Constructor.prototype; // 获取构造函数的 prototype

  // 循环向上查找原型链
  while (prototype !== null) {
    if (prototype === prototypeOfConstructor) {
      return true;
    }
    prototype = Object.getPrototypeOf(prototype); // 沿原型链向上查找
  }
  return false;
}

```

### `typeof`&#x20;

`typeof` 可以检测 8 种数据类型：

* `undefined`
* `number`
* `string`
* `boolean`
* `bigint`
* `symbol`
* `object`（包括 `null`、数组和对象）
* `function`

✅ **常见问题：**

* `null` 被检测为 `"object"`
* `NaN` 被检测为 `"number"`
* `function` 被检测为 `"function"`

要判断一个值是否为 `NaN`，推荐使用 `Number.isNaN()`

精确判断类型推荐： `Object.prototype.toString.call(value)`&#x20;

### **`toString()` 的内部机制**

* 每个对象都有 `toString()` 方法，默认继承自 `Object.prototype`。
* 当 `Object.prototype.toString.call()` 被调用时：
  1. 如果是基本类型，返回基本类型的封装对象。
  2. 如果是对象，会调用 `[[Class]]` 内部属性。
  3. `[[Class]]` 属性是 JavaScript 规范中用来表示对象内部分类的机制。
  4. 通过 `Object.prototype.toString` 可以访问 `[[Class]]` 的字符串表示。

### `==` 和 `===`

#### `==`

* 进行 **隐式类型转换**（类型不同会尝试转换后再比较）。
* 只要值相等，不考虑数据类型，也会返回 `true`

```
console.log(1 == "1");       // true
console.log(true == 1);      // true
console.log(false == 0);     // true
console.log(null == undefined);  // true
```

**`===`（严格相等/全等）**

* **特点：**
  * 不进行类型转换，直接比较数据类型和值。

```
console.log(null == undefined); // true

NaN 与任何值都不相等，包括自身。
console.log(NaN == NaN);  // false
```

### `Object.is()`

#### &#x20;`Object.is()` 是 ES6 引入的一个静态方法，用于判断两个值是否 **严格相等**，但在某些特殊情况下的行为与 `===` 不同。

**`Object.is()` 的特殊处理，**`Object.is()` 和 `===` 在其他场景下的行为是完全相同的。

1. **`NaN` 和 `NaN` 比较：** `Object.is(NaN, NaN)` 返回 `true`，而 `NaN === NaN` 返回 `false`。
2. **`+0` 和 `-0` 比较：** `Object.is(+0, -0)` 返回 `false`，而 `+0 === -0` 返回 `true`



### `Symbol`&#x20;

是 ES6 引入的一种 **基本数据类型**，表示独一无二且不可变的标识符。`Symbol` 通过 `Symbol()` 函数创建，不能用 `new` 关键字实例化。可用来防止属性冲突或者定义不可枚举的私有属性

`Symbol` 属性是默认不可枚举的，不会出现在 `for...in` 和 `Object.keys()` 中。通过 `Object.getOwnPropertySymbols()` 才能访问。

#### **`Symbol.for()` 和 `Symbol.keyFor()`**

* **`Symbol.for()`**：创建一个全局共享的 `Symbol`，如果已存在相同键的 `Symbol`，则返回已有的。

```javascript
const sym1 = Symbol.for('shared');
const sym2 = Symbol.for('shared');

console.log(sym1 === sym2);  // true (共享 Symbol)
```

* **`Symbol.keyFor()`**：获取通过 `Symbol.for()` 创建的 `Symbol` 的键。

```javascript
const sym = Symbol.for('myKey');
console.log(Symbol.keyFor(sym));  // 'myKey'
```

```
const sym1 = Symbol();
const sym2 = Symbol();

console.log(sym1 === sym2);  // false (独一无二)

const sym1 = Symbol('foo');
const sym2 = Symbol('foo');

console.log(sym1 === sym2);  // false
```

### BigInt

`BigInt` 是 ES11（ES2020）引入的新数据类型，用于表示任意精度的整数。

适用于

* 需要进行大整数的精确计算时。
* 在区块链、加密算法、金融交易系统中。
* 需要存储超过 `Number.MAX_SAFE_INTEGER` 的数据时。

#### **`BigInt` 和 `Number` 的区别**

`BigInt` 只支持 **整数运算**，不支持与 `Number` 混合运算。

```
const bigInt1 = 1234567890123456789012345678901234567890n;
const bigInt2 = BigInt('1234567890123456789012345678901234567890');

console.log(bigInt1 === bigInt2);  // true

console.log(big1 + 1);  // TypeError: Cannot mix BigInt and other types
```

| 特性                | `Number`    | `BigInt`       |
| ----------------- | ----------- | -------------- |
| 表示范围              | ±(2^53 - 1) | 理论上无限大         |
| 类型                | 浮点数         | 任意精度的整数        |
| 运算支持              | 基本数学运算      | 仅支持整数运算        |
| 兼容性               | 所有浏览器       | ES2020 及以上版本支持 |
| 是否可以与 `Number` 运算 | ❌ 不可直接混合运算  | ❌ 需要转换         |

### 原始值包装对象

* `String` → `string` 的包装对象
* `Number` → `number` 的包装对象
* `Boolean` → `boolean` 的包装对象

```
const strObj = new String('hello');

console.log(typeof strObj);      // "object"
console.log(strObj instanceof String);  // true
console.log(strObj == 'hello');  // true (值相等)
console.log(strObj === 'hello'); // false (引用不同)
```

### `Object.create(null)` 和 `{}` 的区别

* `Object.create(null)` 创建一个 **纯净的对象**，不继承任何原型链（`Object.prototype`）
  * 创建安全的字典对象，防止原型污染。
  * 适合存储键值对的场景，避免继承链污染。
* 创建的对象 **没有 `__proto__` 属性**，因此无法使用 `toString()`、`hasOwnProperty()` 等方法。
* 使用 `{}` 创建的对象，默认继承自 `Object.prototype`，有原型方法

```
const obj = Object.create(null);

console.log(obj.toString);  // undefined
console.log(Object.getPrototypeOf(obj));  // null

const obj2 = {};
console.log(obj2.toString());  // [object Object]
console.log(obj2.hasOwnProperty('key'));  // false
```

### `?.` 和 `??` 运算符

`??` 只在 **`null` 或 `undefined`** 时才使用默认值，与 `||` 的区别在于不会将 `false`、`0`、`NaN` 视为 `false`。

`?.` 处理嵌套对象访问

```
let user = {
  settings: {
    theme: null
  }
};

let theme = user.settings?.theme ?? 'light';
console.log(theme);  // "light"
```

### isNaN()和Number.isNaN()

#### **`isNaN()`：**

* 会对值进行 **类型转换** 后再判断是否为 `NaN`。
* 会导致一些非数字值错误地被判断为 `NaN`

```
console.log(isNaN('hello'));      // true ❗️
console.log(Number.isNaN('hello'));// false ✅

console.log(isNaN(undefined));    // true ❗️
console.log(Number.isNaN(undefined));// false ✅
```

### JSON.stringify()

#### **解决**JSON.stringify()**循环引用的方法**

`JSON.stringify()` 不能直接处理 **循环引用**，会抛出 `TypeError`。

* **使用 `JSON.stringify()` 的 `replacer` 参数手动处理：**

```javascript
javascript复制编辑const obj = {};
obj.self = obj;

const json = JSON.stringify(obj, (key, value) => {
  if (value === obj) {
    return '[Circular Reference]';
  }
  return value;
});

console.log(json);  // '{"self":"[Circular Reference]"}'
```

#### `JSON.stringify()` 处理 `null` 和 `undefined` 的区别？

```
let obj = { a: null, b: undefined };
console.log(JSON.stringify(obj)); // '{"a":null}'
```

\
在 `JSON.stringify()` 中，`undefined` 会被忽略，转换为 `null` 时出现在数组中。

```
console.log(JSON.stringify({ a: undefined, b: 'hello' }));  // '{"b":"hello"}'
console.log(JSON.stringify([undefined, 'hello']));         // '[null,"hello"]'
```

`NaN` 会被转换为 `null`。

```
console.log(JSON.stringify({ a: NaN, b: 'hello' }));  // '{"a":null,"b":"hello"}'
console.log(JSON.stringify([NaN, 'hello']));         // '[null,"hello"]'
```

可以在 `JSON.stringify()` 中使用 `replacer` 参数。替换 `undefined` 为 `null` 或其他占位符。值类型和引用类型

### **`Object.freeze()和 Object.seal()`**

* 完全冻结对象，不允许修改、添加或删除属性。
* 适用于创建不可变的常量或配置对象。

#### **`Object.seal()`**

* 只禁止添加或删除属性，但允许修改已有属性。
* 适用于需要防止对象扩展但允许数据更新的场景。

#### **3. 嵌套对象注意事项**

* `Object.freeze()` 和 `Object.seal()` 不会自动冻结或密封嵌套对象，需要手动递归处理。

如果希望冻结嵌套对象，需要进行 **深度冻结**。

```
function deepFreeze(obj) {
  Object.keys(obj).forEach(key => {
    if (typeof obj[key] === 'object' && obj[key] !== null) {
      deepFreeze(obj[key]);
    }
  });
  return Object.freeze(obj);
}

const obj = {
  user: {
    name: 'Alice',
    details: {
      age: 30
    }
  }
};

deepFreeze(obj);

obj.user.name = 'Bob';       // ❗️ 无效
obj.user.details.age = 40;   // ❗️ 无效
console.log(obj);
// 输出：{ user: { name: 'Alice', details: { age: 30 } } }


const person = { name: 'John', age: 30 };
Object.seal(person);

person.age = 35;        // ✅ 修改成功
person.city = 'Paris';  // ❗️ 无效
delete person.name;     // ❗️ 无效

console.log(person);
// 输出：{ name: 'John', age: 35 }

```

### `void 0`

```
typeof undefined   // "undefined"
typeof void 0      // "undefined"

console.log(undefined === void 0);  // true ✅
```

`void` 运算符的常见使用场景

1. 通过 `void 0` 来安全获取 `undefined`，防止 `undefined` 被意外篡改：
2. 返回 `undefined` 作为箭头函数的返回值

```
const doNothing = () => void 0;
console.log(doNothing());   // undefined ✅
```

3. 用于 `href="javascript:void(0)"` 阻止页面跳转

### 常见判断空对象的方法

1. `Object.keys(obj)` 返回对象自身 **可枚举属性的键数组**。如果 `length === 0`，表示对象没有可枚举的自身属性，即为空对象。仅检查 **可枚举属性**，如果对象的属性是 **不可枚举** 的，则无法检测出来

```
const obj = {};
Object.defineProperty(obj, 'a', {
  value: 1,
  enumerable: false
});

console.log(Object.keys(obj).length === 0);   // true ❗️ 但 obj 不是空对象
```

2.  `Object.getOwnPropertyNames(obj)` 会返回对象的 **所有自身属性（包括不可枚举属性）**

    <pre class="language-javascript"><code class="lang-javascript">const obj = {};
    <strong>console.log(Object.getOwnPropertyNames(obj).length === 0);   // true 
    </strong></code></pre>
3. **`Reflect.ownKeys(obj).length === 0`**（更全面）

```
const obj = {};
console.log(Reflect.ownKeys(obj).length === 0);   // true ✅
```

4. JSON.stringify(obj) === '{}' 无法处理 **不可枚举属性** 和 **Symbol 属性**

#### **判断 `null` 和 `undefined` 时避免报错**

```javascript
const obj = null;
console.log(obj && Object.keys(obj).length === 0);  // false ✅
```

### 隐式转换

在JavaScript中，不同类型的值在进行相加操作时，会根据类型的不同进行转换和处理。 // !可将变量转换成boolean类型，null、undefined、NaN以及空字符串('')取反都为true，其余都为false。

#### 示例代码

```javascript

console.log(!null); // true
console.log(!undefined); // true
console.log(!NaN); // true
console.log(!''); // true

// [] + {} "[object Object]"
console.log([] + {}); // "[object Object]"

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

#### 说明

* `[] + {}` 结果为 `"[object Object]"`，这是因为数组和对象的相加在JavaScript中被转换为字符串操作。
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

[更多详细内容](https://2ality.com/2012/01/object-plus-object.html) [参考博客](https://blog.csdn.net/jian_zi/article/details/105137258)

### **将多层嵌套数组平铺成一层数组的方法**

`flat()` 可以将嵌套数组展开到指定的层级，`flat(Infinity)` 可以完全展开多层嵌套数组。

`reduce()` 遍历数组，递归检查是否为数组，如果是则继续展开

```
function flattenArray(arr) {
  return arr.reduce(
    (acc, val) => acc.concat(Array.isArray(val) ? flattenArray(val) : val),
    []
  );
}

const arr = [1, [2, [3, [4, 5]]], 6];
console.log(flattenArray(arr));  // [1, 2, 3, 4, 5, 6]
```

`toString()` + `split()`

```
const arr = [1, [2, [3, [4, 5]]], 6];
const flattened = arr.toString().split(',').map(Number);
console.log(flattened);  // [1, 2, 3, 4, 5, 6]
```

使用 `JSON.stringify()` + `replace()`

```
const arr = [1, [2, [3, [4, 5]]], 6];
const flattened = JSON.stringify(arr)
  .replace(/\[|\]/g, '')   // 去掉方括号
  .split(',')              // 转换为字符串数组
  .map(Number);            // 转换回数字
console.log(flattened);  // [1, 2, 3, 4, 5, 6]
```

### **`WeakMap` 和 `WeakSet` 的区别**

***

| 特性     | `WeakMap`        | `WeakSet`     |
| ------ | ---------------- | ------------- |
| 数据存储   | 键值对（`key-value`） | 仅存储对象         |
| 键或值    | 只能使用对象作为键        | 只能存储对象        |
| 引用类型   | 弱引用              | 弱引用           |
| 是否可迭代  | ❌ 不可迭代           | ❌ 不可迭代        |
| 垃圾回收行为 | 键被回收时自动释放键值对     | 对象被回收时自动移除    |
| 常用场景   | 私有数据、缓存、关联数据     | 记录对象状态、避免重复操作 |

通过 `WeakSet`&#x20;

记录哪些对象被访问或处理过，不会影响垃圾回收。

可用于防止对相同对象执行重复操作，例如防止重复发送通知。

```
const processed = new WeakSet();

function process(obj) {
  if (processed.has(obj)) {
    console.log('Already processed');
    return;
  }

  // 处理对象
  processed.add(obj);
  console.log('Processing...');
}

const obj1 = { id: 1 };
process(obj1);  // "Processing..."
process(obj1);  // "Already processed"

obj1 = null;  // 被垃圾回收，WeakSet 自动移除引用

```

WeakMap

使用 `WeakMap` 来实现 **对象缓存**，当对象被垃圾回收时，缓存数据也随之消失。

将对象的私有数据存储在 `WeakMap` 中，实现数据的 **隐藏和封装**。

```
const cache = new WeakMap();

function fetchData(obj) {
  if (!cache.has(obj)) {
    const data = { value: Math.random() };  // 模拟数据
    cache.set(obj, data);
  }
  return cache.get(obj);
}

const obj1 = {};
console.log(fetchData(obj1));  // { value: 0.1234 }

obj1 = null;  // 释放对象，WeakMap 自动清理数据

```
