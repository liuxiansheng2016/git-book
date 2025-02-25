# Typescript

## Typescript

### 为什么推荐使用TS <a href="#wei-shen-me-tui-jian-shi-yong-ts" id="wei-shen-me-tui-jian-shi-yong-ts"></a>

主要是静态类型检测，更有利于构建大型项目

### 类 <a href="#lei" id="lei"></a>

<mark style="color:red;">类是一种面向对象编程的概念，它是对象的蓝图，描述了对象的属性和方法</mark>。TypeScript 中的类支持传统的面向对象特性<mark style="color:red;">，如封装、继承和多态</mark>。

抽象类

<pre class="language-javascript"><code class="lang-javascript">abstract class Animal { 
    // 抽象方法 
    abstract makeSound(): void;
<strong>    // 非抽象方法
</strong>    move(): void {
        console.log('Moving...');
    }
}
</code></pre>

&#x20;实现抽象类

```typescript
class Dog extends Animal {
    makeSound(): void {
        console.log('Bark!');
    }
}

class Cat extends Animal {
    makeSound(): void {
        console.log('Meow!');
    }
}

```

<mark style="color:red;">如果子类继承的是一个抽象类，子类必须实现父类里的抽象方法</mark>，不然的话不能实例化，会报错。

**Public private protected修饰符**

**private**：

* 只能在声明它的类内部访问。
* 不能被子类继承或访问。

**protected**：

* <mark style="color:red;">可以在声明它的类及其子类中访问。</mark>
* 可以被子类继承和访问。

Constructor方法，类被实例化一次调用一次

包含构造函数的派生类必须调用super()，它会执行基类的构造方法和其他方法。

接口声明的方法 应用的类必须调用\
类型声明文件里面只有类型代码，没有具体的代码实现

抽象类是不允许被实例化的：

其次，抽象类中的抽象方法必须被子类实现

“内部模块”现在称做“命名空间”。 “外部模块”现在则简称为“模块”

### 模块（Module）

TypeScript 中的模块是<mark style="color:red;">相关变量、函数、类和接口的集合</mark>

**导出和导入模块**\
使用 `export` 关键字将模块中的变量、函数、类等导出，使用 `import` 关键字将其他模块中的内容导入\
每个模块可以有一个默认导出，使用 `export default` 关键字。在<mark style="color:red;">导入默认导出时，可以使用任意名称。</mark>

```
export default class Person {}
import MyPerson from './person';
```

### 常见的 TS 基本数据类型

#### boolean/number/string/null /undefined /unknown/any/void/enum/tuple

**`void`** 类型通常用于表示一个函数没有返回值，也能用于变量声明，<mark style="color:red;">`void`</mark> <mark style="color:red;"></mark><mark style="color:red;">类型的变量只能被赋值为</mark> <mark style="color:red;"></mark><mark style="color:red;">`undefined`</mark> （在非严格空检查模式下还能赋值为 `null` ）

```
// 定义一个无返回值的函数，返回值类型为 void
function printMessage(message: string): void {
    console.log(message);
    // 没有显式的 return 语句，或者 return 后面不跟表达式
    // 隐式返回 undefined
}

// 严格空检查模式下
let emptyValue: void = undefined;
```

**`any`** 类型的变量可以被赋予任意类型的值，而不会受到类型检查的限制。

**`unknown`** 类型也表示任意类型，但它比 `any` 类型更安全。与 `any` 不同的是，在对 `unknown` 类型的值进行操作之前，需要先进行类型检查，以确保操作的安全性

```
let bar: unknown = 222; // OK 
console.log(bar.msg); // Error
let foo: any = 123;
console.log(foo.msg); // 符合TS的语法


let value: unknown;
value = 10;

// 直接操作会报错
// let result = value + 5; 

// 需要先进行类型检查
if (typeof value === 'number') {
    let result: number = value + 5;
    console.log(result); 
}
```

**`never`** 类型表示永远不会出现的值的类型。通常用于以下两种情况：

* 函数永远不会返回值，例如函数抛出异常或进入无限循环。
* 类型系统中的不可能类型，例如联合类型中没有任何可能的值。

```
// 函数永远抛出异常，返回值类型为 never
function throwError(message: string): never {
    throw new Error(message);
}

// 函数进入无限循环，返回值类型为 never
function infiniteLoop(): never {
    while (true) {}
}

// 不可能类型的示例
type NonExistentUnion = string & number; 
// NonExistentUnion 的类型为 never，因为字符串和数字类型没有交集
```

#### `keyof` 关键字

<mark style="color:red;">`keyof`</mark> <mark style="color:red;"></mark><mark style="color:red;">关键字用于获取一个类型的所有属性名组成的联合类型</mark>

```
// 定义一个接口
interface User {
    name: string;
    age: number;
    email: string;
}

// 使用 keyof 获取 User 接口的所有属性名组成的联合类型
type UserKeys = keyof User; 
// UserKeys 的类型为 'name' | 'age' | 'email'

// 可以使用 UserKeys 作为类型注解
function getUserProperty(user: User, key: UserKeys) {
    return user[key];
}
```

#### `in` 关键字

\
在 TypeScript 中，`in` 关键字主要用于映射类型和条件类型中，<mark style="color:red;">用于遍历一个类型的属性名</mark>。在映射类型中，`in` 用于遍历 `keyof` 得到的属性名联合类型，对每个属性进行操作。

```
// 定义一个接口
interface Product {
    id: number;
    name: string;
    price: number;
}

// 使用映射类型将 Product 接口的所有属性变为可选属性
type PartialProduct = {
    [P in keyof Product]?: Product[P];
};

const partialProduct: PartialProduct = {
    name: 'Smartphone'Partial
};
```

### 类型注解与类型推断

**类型注解**\
开发者可以在代码中显式地为变量、函数参数、返回值等添加类型注解，告诉 TypeScript 编译器这些变量或值应该是什么类型。例如：

```

// 函数参数和返回值的类型注解
function add(a: number, b: number): number {
    return a + b;
}
在上述代码中，num 变量被显式注解为 number 类型，add 函数的参数 a 和 b 以及返回值都被注解为 number 类型。
```

**类型推断**\
TypeScript 编译器能够根据变量的初始值、上下文等信息自动推断出变量的类型，而不需要开发者显式地添加类型注解。例如：

```
// 类型推断
function multiply(a: number, b: number) {
    return a * b;
}
// 编译器会自动推断 multiply 函数的返回值类型为 number
```

### **`readonly和`**`const` 的区别

* **`const`**：主要用于声明常量变量，侧重于变量引用的不可变性。对于基本数据类型，值不能改变；对于引用类型，引用不能改变，但对象内部的属性可以改变。
* **`readonly`**：主要用于属性，侧重于属性值的不可变性，强调属性一旦初始化就不能再被重新赋值。

```
class Car {
    readonly brand: string;
    constructor(brand: string) {
        this.brand = brand;
    }
}
const myCar = new Car('Toyota');
// myCar.brand = 'Honda'; 
// 报错：Cannot assign to 'brand' because it is a read - only property.
```

### 元组类型tuple <a href="#yuan-zu-lei-xing-tuple" id="yuan-zu-lei-xing-tuple"></a>

什么是元组类型？其实元组是数组的一种。

表示已知元素数量和类型的数组

`let person:[string,number] = ['邵威儒',28]`

有点类似解构赋值，但是又不完全是解构赋值，比如元组类型必须一一对应上，多了少了或者类型不对都会报错。

元组类型是一个不可变的数组，长度、类型是不可变的。

### 枚举 <a href="#mei-ju" id="mei-ju"></a>

TypeScript 的枚举类型为开发者提供了一种方便的方式来管理一组相关的常量

#### **数字枚举**

最简单的枚举形式是数字枚举，其中每个成员默认从 0 开始自动递增。例如：

```javascript
enum Direction {
  Up,
  Down,
  Left,
  Right
}
```

#### **字符串枚举**

```typescript
enum Color {
    Red = "RED",
    Green = "GREEN",
    Blue = "BLUE"
}
```



### 接口 <a href="#jie-kou" id="jie-kou"></a>

**接口** 主要用于描述对象的形状，可以被实现或继承，适合用于描述数据模型或约定

### interface 和 type 的区别 <a href="#interface-he-type-de-qu-bie" id="interface-he-type-de-qu-bie"></a>

`interface` 和 `type` 都可用于定义类型

<mark style="color:red;">interface： 它定义了一个对象的形状，描述了对象应该具有的属性及类型</mark>

<mark style="color:red;">Interface只能表示 object、class、function 类型。</mark>

<mark style="color:red;">type 还可以用来表示其他的类型，比如基本数据类型、联合类型、交叉类型等</mark>

#### 语法差异

* **`interface`**：使用 `interface` 关键字来定义，其语法更接近传统面向对象编程中接口的定义方式，通过声明属性和方法的签名来描述对象的形状。

```typescript
interface Person {
    name: string;
    age: number;
    sayHello(): void;
}
```

* **`type`**：使用 `type` 关键字定义，它的语法更加灵活，可以定义基本类型、联合类型、交叉类型、元组类型等多种类型。

```typescript
type PersonType = {
    name: string;
    age: number;
    sayHello: () => void;
};
```

#### 扩展方式

* **`interface`**：可以使用 `extends` 关键字实现继承，从而扩展已有的接口。这使得代码结构更加清晰，符合面向对象编程中接口继承的概念。

```typescript
interface Animal {
    species: string;
}

interface Dog extends Animal {
    bark(): void;
}
```

* **`type`**：使用交叉类型（`&`）来实现类似扩展的功能。交叉类型可以将多个类型合并成一个新的类型，包含所有类型的属性。

```typescript
type AnimalType = {
    species: string;
};

type DogType = AnimalType & {
    bark(): void;
};
```

#### 重复定义

* **`interface`**：可以对同一个 `interface` 进行多次定义，TypeScript 会自动将这些定义合并。这在扩展第三方库或者模块化开发中非常有用。

```typescript
interface User {
    name: string;
}

interface User {
    age: number;
}

// 合并后的 User 接口包含 name 和 age 属性
const user: User = { name: 'John', age: 30 };
```

* **`type`**：不允许重复定义相同名称的 `type`，否则会导致编译错误。每个 `type` 定义必须是唯一的。

```typescript
type Point = {
    x: number;
};

// 报错：Duplicate identifier 'Point'.
// type Point = {
//     y: number;
// };
```

#### 定义类型的范围

* **`interface`**：主要用于定义对象类型、函数类型、类的实现契约等，侧重于描述对象的结构和行为。

```typescript
// 定义对象类型
interface Config {
    apiKey: string;
    timeout: number;
}

// 定义函数类型
interface SumFunction {
    (a: number, b: number): number;
}
```

* **`type`**：可以定义更广泛的类型，除了对象类型外，还可以定义基本类型、联合类型、交叉类型、元组类型、字面量类型等。

```typescript
// 定义基本类型
type ID = number | string;

// 定义联合类型
type Result = 'success' | 'error';

// 定义元组类型
type Coordinate = [number, number];
```

#### 与类的关系

* **`interface`**：可以被类实现（`implements`），用于约束类的结构，确保类包含接口中定义的所有属性和方法。

```typescript
interface Shape {
    area(): number;
}

class Circle implements Shape {
    constructor(private radius: number) {}
    area() {
        return Math.PI * this.radius * this.radius;
    }
}
```

* **`type`**：一般不能被类直接实现，但可以通过将类型定义转换为接口的方式间接实现。

```typescript
type ShapeType = {
    area(): number;
};

// 不能直接使用 implements ShapeType
// 可以通过将其转换为接口来实现
interface ShapeInterface extends ShapeType {}

class Square implements ShapeInterface {
    constructor(private side: number) {}
    area() {
        return this.side * this.side;
    }
}
```

#### 总结

* 如果需要定义对象的结构、进行接口继承和合并，或者用于类的实现契约，使用 `interface` 更为合适。
* <mark style="color:red;">如果需要定义基本类型、联合类型、交叉类型、元组类型等复杂类型</mark>，或者在类型定义中使用类型别名，使用 `type` 会更灵活。

### 泛型 <a href="#fan-xing" id="fan-xing"></a>

泛型可以理解为宽泛的类型，通常用于类和函数

TypeScript 的泛型（Generics）是一种强大的工具<mark style="color:red;">，它允许开发者在定义函数、类或接口时使用类型参数，而不是具体的类型。</mark>

一个组件可以支持多种类型的数据,为代码添加额外的抽象层和可重用性

// T表示泛型，具体什么类型是调用这个方法的时候决定的。

```javascript
function getData<T>(value:T):T{
 return value;
}
getData<number>(123456);
```

函数声明中的 \<T>:

这是泛型类型的声明部分。它告诉 TypeScript 编译器，这个函数将会使用一个名为 <mark style="color:red;">T 的类型参数</mark>。T 可以代表任何类型，并且这个类型将在调用函数时由调用者提供或通过上下文推断出来。

参数列表中的 value: T:

在这里，T 指定了函数接受的参数 value 的类型。这意味着传入给 getData 函数的 value 参数可以是任意类型，但是一旦确定了这个类型，它在整个函数内部就只能是这种类型。例如，如果你传递了一个字符串，那么在这个函数的作用域内，T 就是 string 类型。

返回值类型 : T:

最后的 T 定义了函数的返回值类型。这表明 getData 函数将返回与输入参数 value 同一类型的值。因此，如果 value 是一个数字，则返回值也将是一个数字；如果 value 是一个对象，则返回值也是一个相同类型的对象。

### 使用泛型约束

<mark style="color:red;">你可以使用</mark> <mark style="color:red;"></mark><mark style="color:red;">`extends`</mark> <mark style="color:red;"></mark><mark style="color:red;">关键字来定义一个泛型约束</mark>，从而限制传入的类型必须符合某个结构或接口。这不仅限于类类型，也可以是任何具有兼容结构的类型。

**基本语法**

```
function getProperty<T extends object, K extends keyof T>(obj: T, key: K): T[K] {
    return obj[key];
}
```

当我们希望泛型类型参数具有某些属性时

```
interface HasLength {
    length: number;
}

function logLength<T extends HasLength>(arg: T): void {
    console.log(`The length is ${arg.length}`);
}

logLength("Hello"); // 正确，字符串有 length 属性
logLength([1, 2, 3]); // 正确，数组有 length 属性
// logLength(42); // 错误，数字没有 length 属性

// 使用泛型约束，确保 T 是 Animal 或其子类
function performAction<T extends Animal>(animal: T) {
    animal.eat();
    // 如果 T 是 Dog 类型，还可以调用 bark 方法
    if (animal instanceof Dog) {
        animal.bark();
    }
}
```

### 条件类型（Conditional Types）

&#x20;JavaScript 中的三元运算符，格式为 `T extends U ? X : Y`，表示如果 `T` 是 `U` 的子类型，那么结果类型为 `X`，否则为 `Y`

```
// 定义一个条件类型，根据传入的类型判断是否为数组类型
type IsArray<T> = T extends any[] ? true : false;

// 使用 IsArray 条件类型进行类型判断
type Result1 = IsArray<number[]>; 
// Result1 的类型为 true
type Result2 = IsArray<number>; 
// Result2 的类型为 false
```

#### 结合泛型使用 条件类型经常和泛型一起使用，以实现更灵活的类型操作。

```
// 定义一个条件类型，用于提取函数的返回值类型
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

// 定义一个函数
function add(a: number, b: number): number {
    return a + b;
}

// 使用 ReturnType 条件类型获取 add 函数的返回值类型
type AddReturnType = ReturnType<typeof add>; 
// AddReturnType 的类型为 number
```

\
这里，`ReturnType<T>` 是一个条件类型，它会判断 `T` 是否为函数类型。如果是，<mark style="color:red;">使用</mark> <mark style="color:red;"></mark><mark style="color:red;">`infer`</mark> <mark style="color:red;"></mark><mark style="color:red;">关键字推断函数的返回值类型并赋值给</mark> <mark style="color:red;"></mark><mark style="color:red;">`R`</mark>，最终结果类型为 `R`；如果 `T` 不是函数类型，结果类型为 `never`。

### 类型断言 <a href="#lei-xing-duan-yan" id="lei-xing-duan-yan"></a>

可以用来手动指定一个值具体的类型，即允许变量从一种类型更改为另一种类型。

"尖括号" 语法：<类型>值

```javascript
let someValue: any = 'this is a string'
let strLength: number = (someValue as string).length
```

as 语法：值 as 类型

```javascript
let someValue: any = 'this is a string'
let strLength: number = (someValue as string).length
```

### `Partial<T>`、`Required<T>`、`Readonly<T>`、`Pick<T, K>` 和 `Omit<T, K>`

&#x20;是非常实用的工具类型，它们基于泛型构建，用于对已有的类型进行转换和操作

```
interface User {
    name: string;
    age: number;
    email: string;
}

// 使用 Partial<User> 将 User 类型的所有属性变为可选属性
type PartialUser = Partial<User>;

const partialUser: PartialUser = {
    // 可以只提供部分属性，甚至不提供任何属性
    name: 'John'
};

interface Product {
    id: number;
    name: string;
    price: number;
    description: string;
}

// 从 Product 类型中移除 description 属性来构造新类型
type ProductSummary = Omit<Product, 'description'>;

const productSummary: ProductSummary = {
    id: 1,
    name: 'Smartphone',
    price: 999
};
```

### 配置tsconfig.json <a href="#pei-zhi-tsconfigjson" id="pei-zhi-tsconfigjson"></a>

首先我们要生成一个tsconfig.json来告诉ts-loader怎样去编译这个ts代码

`tsc --init`

会在项目中生成了一个tsconfig.json文件，接下来进入这个文件，来修改相关配置

```json
// tsconfig.json
{
// 编译选项
"compilerOptions": {
    "target": "es5", // 编译成es5语法
    "module": "commonjs", // 模块的类型
    "outDir": "./dist", // 编译后的文件目录
    "sourceMap": true, // 生成sourceMap方便我们在开发过程中调试
    "noImplicitAny": true, // 每个变量都要标明类型
    "jsx": "react", // jsx的版本,使用这个就不需要额外使用babel了，会编译成React.createElement
},
// 为了加快整个编译过程，我们指定相应的路径
"include": [
    "./src/**/*"
 ]
}
```



### 数组的类型 <a href="#shu-zu-de-lei-xing" id="shu-zu-de-lei-xing"></a>

最简单的方法是使用\[类型+方括号]表示法

`Let Fibonacci: number[] = [1,1,2,3,5]`

数组泛型表示数组

`Let Fibonacci: Array<number> = [1,2,3]`

一个比较常见的做法是，用 any 表示数组中允许出现任意类型：

`let list: any[] = ['Xcat Liu', 25, { website: '`[`http://xcatliu.com`](http://xcatliu.com/)`' }];`

### 重载 <a href="#zhong-zai" id="zhong-zai"></a>

允许一个函数接受不同数量或类型的参数时，作出不同的处理。

比如，我们需要实现一个函数 reverse，输入数字 123 的时候，输出反转的数字 321，输入字符串 'hello' 的时候，输出反转的字符串 'olleh'。

利用联合类型，我们可以这么实现：

这时，我们可以使用重载定义多个 reverse 的函数类型：

```javascript
function reverse(x: number): number;
function reverse(x: string): string;

function reverse(x: number | string): number | string {
  if (typeof x === 'number') {
    return Number(x.toString().split('').reverse().join(''));
  } else if (typeof x === 'string') {
    return x.split('').reverse().join('');
  }
}
```

此时可以使用类型断言，将 something 断言成 string：

```javascript
function getLength(something: string | number): number {
  if ((<string>something).length) {
    return (<string>something).length;
  } else {
    return something.toString().length;
  }
}
```

### 重写 <a href="#zhong-xie" id="zhong-xie"></a>

当父类和其[派生类](https://so.csdn.net/so/search?q=%E6%B4%BE%E7%94%9F%E7%B1%BB\&spm=1001.2101.3001.7020%22%20%5Ct%20%22https://blog.csdn.net/qq_24518001/article/details/_blank)拥有同一个方法时，这种情况就是方法的重写

```javascript
class Person {
    major() {
        console.log('计算机专业');
    }
}

class Student extends Person {
    constructor() {
        super();
    }

    major() {
        console.log('航天工程专业');
        super.major();
    }

    sex() {
    }
}

var student = new Student();
student.major();
// 航天工程专业
// 计算机专业
```

### 三斜线指令 <a href="#san-xie-xian-zhi-ling" id="san-xie-xian-zhi-ling"></a>

管理模块之间的依赖关系

引入库的类型定义

三斜线指令以 /// \<reference ... /> 开头，放在文件的顶部。

替代方案<mark style="color:red;">：在现代 TypeScript 项目中，通常使用 tsconfig.json 文件中的 types 和 lib 字段来管理类型定义，三斜线指令的使用频率逐渐减少</mark>。

引用类型定义文件 假设你有一个自定义的类型定义文件 myDefinitions.d.ts，你可以使用 /// 来引用它。

引用第三方库的类型定义 假设你使用了 node 库，你可以使用 /// 来引用 @types/node 包的类型定义

```javascript
/// <reference path="./definitions/myDefinitions.d.ts" />

function useCustomType(custom: CustomType) {
    console.log(custom.name);
}
```

引用第三方库的类型定义 假设你使用了 node 库，你可以使用 /// 来引用 @types/node 包的类型定义

```javascript
/// <reference types="node" />
import fs from 'fs';

fs.readFile('file.txt', 'utf8', (err, data) => {
    if (err) throw err;
    console.log(data);
});
```

### Mixin <a href="#mixin" id="mixin"></a>

<mark style="color:red;">由于TypeScrip中的类不支持多继承</mark>，所以引入了混合（Mixin）的特性，可以间接实现多继承的效果

使用mixin

```javascript
// 声明跨界车类Crossover，继承Car和Suv,既可以上班代步，也可以山地越野
class Crossover extends Vehicle {
}

// 这里需要创建需要合并类（Crossover）的同名接口，并继承多个类Car、Suv
interface Crossover extends Car, Suv {
}

applyMixins(Crossover, [Car, Suv])

let crossover = new Crossover()

// 该混合函数摘自TypeScript官方手册，如果derivedCtor和constructors类中存在同名方法，则后者覆盖前者
function applyMixins(derivedCtor: any, constructors: any[]) {
    constructors.forEach((baseCtor) => {
        Object.getOwnPropertyNames(baseCtor.prototype).forEach((name) => {
            Object.defineProperty(
                derivedCtor.prototype,
                name,
                Object.getOwnPropertyDescriptor(baseCtor.prototype, name) || Object.create(null)
            );
        });
    });
}

```



