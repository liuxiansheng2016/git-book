# Typescript

## Typescript

### 为什么推荐使用TS <a href="#wei-shen-me-tui-jian-shi-yong-ts" id="wei-shen-me-tui-jian-shi-yong-ts"></a>

主要是静态类型检测，更有利于构建大型项目

### Const <a href="#const" id="const"></a>

类型检查与安全性

当使用 const 修饰变量时，编译器会执行更严格的类型检查，确保该变量在其生命周期内不会被意外修改

#### 内联替换与优化

对于局部 const 变量，编译器通常不会为它们分配实际的内存空间，而是直接在编译时将它们替换为对应的值（即所谓的“常量折叠”），从而避免了运行时访问内存的操作，提高了执行速度 19。此外，由于 const 表达式的值是在编译期确定的，因此可以更容易地进行各种编译优化，如循环展开、函数内联等

```javascript
const int BUFFER_SIZE = 1024;
char buffer[BUFFER_SIZE]; // 编译器可以直接用 1024 替换 BUFFER_SIZE
```

### 元组类型tuple <a href="#yuan-zu-lei-xing-tuple" id="yuan-zu-lei-xing-tuple"></a>

什么是元组类型？其实元组是数组的一种。

表示已知元素数量和类型的数组

`let person:[string,number] = ['邵威儒',28]`

有点类似解构赋值，但是又不完全是解构赋值，比如元组类型必须一一对应上，多了少了或者类型不对都会报错。

元组类型是一个不可变的数组，长度、类型是不可变的。

### 枚举 <a href="#mei-ju" id="mei-ju"></a>

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

### 类 <a href="#lei" id="lei"></a>

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

Void表示没有任何返回值的函数

抽象类是不允许被实例化的：

其次，抽象类中的抽象方法必须被子类实现

“内部模块”现在称做“命名空间”。 “外部模块”现在则简称为“模块”

### 接口 <a href="#jie-kou" id="jie-kou"></a>

**接口** 主要用于描述对象的形状，可以被实现或继承，适合用于描述数据模型或约定

### interface 和 type 的区别 <a href="#interface-he-type-de-qu-bie" id="interface-he-type-de-qu-bie"></a>

1. 定义

type关键字可以定义一个集合，可以包含各种类型的属性和值，以用来描述对象、函数、联合类型、交叉类型等。

interface： 它定义了一个对象的形状，描述了对象应该具有的属性及类型

<mark style="color:red;">Interface只能表示 object、class、function 类型。</mark>

type 还可以用来表示其他的类型，比如基本数据类型、联合类型、交叉类型等

2. 写法

type使用等号来定义类型别名，

而interface使用花括号直接定义接口的成员。

3. 声明合并

type 不支持声明合并

interface支持声明合并

4. 继承

Interface支持继承\
type：可以通过 & 符号创建交叉类型，以组合现有的多种类型

**type和interface的定义**

`type A = string; // 声明了一个类型别名A，同时它的类型等价于string类型`

`type StatusCode = 200 | 301 | 400 | 500 | 502;`

`type PossibleDataTypes = string | number | (() => unknown);`

`type Person = { name: string; age: number; sex: 0 | 1; };`

`type setPoint = (x: number, y: number) => void;`

`// 定义一个【Function】类型`&#x20;

`interface setPoint { (x: number, y: number): void }`

联合类型（ | ）和交叉类型（&）

**Type 组合方式**

```javascript
type Name = {
    name: string;
};

type Age = {
    age: number;
};

type Person = Name & Age;

```

Interface 多次声明来合并声明

```javascript
interface Person{
    name:string;
}

interface Person{
    age:number;
}

// 上面的两个等价于下面这个
interface Person{
    name:string;
    age:number;
}

```

Interface extend继承

```javascript
interface Fruit {
    name: string
}
interface Apple extends Fruit {
    kind: string
}
```

### 泛型 <a href="#fan-xing" id="fan-xing"></a>

泛型可以理解为宽泛的类型，通常用于类和函数

TypeScript 的泛型（Generics）是一种强大的工具，它允许开发者在定义函数、类或接口时使用类型参数，而不是具体的类型。

一个组件可以支持多种类型的数据,为代码添加额外的抽象层和可重用性

// T表示泛型，具体什么类型是调用这个方法的时候决定的。

```javascript
function getData<T>(value:T):T{
 return value;
}
getData<number>(123456);
```

函数声明中的 \<T>:

这是泛型类型的声明部分。它告诉 TypeScript 编译器，这个函数将会使用一个名为 T 的类型参数。T 可以代表任何类型，并且这个类型将在调用函数时由调用者提供或通过上下文推断出来。

参数列表中的 value: T:

在这里，T 指定了函数接受的参数 value 的类型。这意味着传入给 getData 函数的 value 参数可以是任意类型，但是一旦确定了这个类型，它在整个函数内部就只能是这种类型。例如，如果你传递了一个字符串，那么在这个函数的作用域内，T 就是 string 类型。

返回值类型 : T:

最后的 T 定义了函数的返回值类型。这表明 getData 函数将返回与输入参数 value 同一类型的值。因此，如果 value 是一个数字，则返回值也将是一个数字；如果 value 是一个对象，则返回值也是一个相同类型的对象。

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



