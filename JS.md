## Script

### JS的数据类型划分

#### 基本数据类型（原始数据类型）

- undefined 未定义
- null 空对象（代表空指针）
- number 数字型 例如 100 3.14
- string 字符型（字符串）常量需要用双引号或者单引号括起 例如 "中国" 'abcd' 空字符串"" ''
- boolean 布尔值 就两个取值 true代表真 false代表假

#### 引用数据类型

- object 对象类型

Js的数据类型是弱类型，不是像java那种强类型，变量必须显示的声明数据类型

- 强类型举例： int i = 10; String s = "abcd"; double d = 3.14;
- 弱类型举例：var i = 10; var s = "abcd"; var d = 3.14;

`instanceof`运算符： 对象 `instanceof` 类 ，如果对象是这个类创建的，返回true，否则为false

#### 值类型和引用类型

**值类型：**

1. 保存与复制的是值本身

2. 使用typeof检测数据的类型

3. 基本数据类型是值类型

**引用类型：**

1. 保存和赋值的是指向对象的一个指针。

2. 使用instanceof检测数据类型

3. 使用new()方法构造出的对象是引用型

### JavaScript 中的变量在内存中的具体存储形式是什么

原始类型包括但不限于 number、string、boolean、null、undefined 和 bigint（ES10 引入的新类型）。这些类型的值在内存中直接存储。

- 原始类型的值直接存储在栈内存中。
- 访问速度快，因为栈内存具有连续性和固定大小的特点。
- 引用类型包括但不限于 object、array 和 function。引用类型的变量实际上存储的是指向实际对象的引用（地址）

#### 堆内存（Heap Memory）

- 存储引用类型：引用类型的对象存储在堆内存中。
- 优点：存储空间大，适合存储大对象。

#### 栈（Stack）

- 栈区：只存储局部变量（也包括全局变量）
- 堆区：只存储对象的实例，每个对象实例在堆中都会自动产生一个唯一的内存地址

#### 堆和栈的区别

1. 数据结构：栈和堆都是内存数据结构，栈是一种线性结构，堆是一种树形结构。
2. 内存分配方式：栈采用的是静态内存分配，系统在编译阶段就确定了分配给栈的内存空间，而且栈内存的释放是由系统自动完成的。而堆采用的是动态内存分配，程序在运行时可以向操作系统请求动态分配一段空间，然后使用完之后再手动释放。
3. 存储内容：栈中存储的是函数的调用和局部变量，而堆中存储的是对象的实例。
4. 访问方式：栈是一种后进先出（LIFO）的数据结构，只有栈顶的元素可以被访问和操作，而堆是一棵树形结构，其存储的元素可以被通过指针或引用访问。
5. 内存分配效率：栈的内存分配效率比较高，因为栈是一种内存结构，其内存块相互紧凑，可以直接通过指针操作，而堆的内存分配效率相对低一些，因为需要手动分配和释放内存空间。

对象之间赋值赋的是引用地址，基本类型赋值赋的是值本身。

### 变量的作用域：变量的使用范围

<https://segmentfault.com/a/1190000014741472#articleHeader6>

分为两种作用域：

- 全局变量：属于window对象（全局对象，是作用域中最大的对象），在整个js程序中都可以使用，声明在function的外部，尽可能少用全局变量，会造成全局污染。
- 局部变量：属于某个function范围，仅限于在function内部使用

JS中没有块级作用域这一说，最小的作用域就是一个function，声明在function里面的变量包括参数都是属于局部变量。

变量如果没有加var声明，即时写在function内部，也算全局变量。

#### 作用域链

当在某个function使用某个的变量时候，首先先查看当前function作用域中是否有这个变量，如果有，直接使用，如果没有，就会像上层（外层）作用域查找，直至全局作用域。

#### 生命周期

变量出现在内存开始，到从内存中销毁为止，中间的过程，就是一个变量的生命周期，一个局部变量的生命周期从函数调用开始，到函数执行完毕，全局变量的生命周期是整个js程序结束。

### 面向对象编程

- 类：抽象上的定义，不具体代表任何一个实例
- 对象：必须是一个具体的对象实例
- 类和对象是一对多关系，一个类可以创建多个对象实例，通过类创建对象的过程叫做实例化

### this关键字

代表一个对象，不同的情况下，this代表的对象也不同

- 函数调用function：this永远代表window全局对象
- 构造函数调用function：this永远代表当前创建的对象
- 对象实例方法调用function：this永远代表当前的对象

### 执行上下文

- 执行上下文： 是评估和执行js代码环境的抽象概念,每当js代码在运行的时候，它都在执行上下文中运行。

- 全局执行上下文： 基础的上下文， 创建全局的window对象，并设置this等于这个全局对象。一个程序中只有一个全局执行上下文

- 函数执行上下文：每当函数调用时，都会为函数创建一个新的上下文

在全局执行上下文中，this的值指向全局对象

在函数执行上下文中，this的值取决于该函数是如何被调用的，
如果它被一个引用对象调用，那么this会被设置成那个对象，否则this的值被设置为全局对象或者undifined,

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

### Array数组

- 伪数组：通常指的是 arguments 对象，它具有数组的一些特征，但不是真正的数组。
- 类数组对象：具有 length 属性和索引，但不是 Array 类型的对象。这类对象还包括 NodeList、HTMLCollection 等

#### 创建数组对象的方式：

1. `var 变量 = new Array();` 基本语法
2. `var 变量 = new Array(值1，值2，......);` 直接初始化元素值
3. `var 变量 = [值1，值2，......];` 等价于上面语句，是字面量写法

#### 数组对象的属性方法

- length属性：返回数组的元素个数。
- push方法：在数组末尾添加元素。
- pop方法：删除数组末尾元素。
- unshift方法：在数组头部添加元素。
- shift方法：删除数组头部的元素。
- splice方法：删除数组指定的元素，也可以用于替换或插入元素。
- concat方法：合并另一个数组。
- join方法：按照指定的分隔符（默认逗号）将数组元素拼接为一个字符串。
- reverse方法：颠倒数组中元素的顺序。
- slice方法：根据起始和终止下标截取返回数组的一部分，不会修改原始数组，而是返回一个浅拷贝的新数组。
- sort方法：
  - 无参：默认按照数组中的元素的asc码来排序。
  - 传参：传入一个function定义排序规则。

改变原有数组的方法：push, pop, Shift, splice, sort, reverse

#### 数组排序算法

- 冒泡排序、选择排序、插入排序、希尔排序、快速排序等。

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


### String字符串对象常用属性和方法

-   charAt方法：获取字符串string的第n个字符。
-   charCodeAt方法：获取字符串string的第n个字符的Unicode编码。
-   fromCharCode方法：从指定编码创建一个新的字符串。
-   concat方法：连接字符串，不如使用+操作符方便。
-   indexOf方法：查找子字符串的起始位置，未找到返回-1。
-   lastIndexOf方法：同上，但从后向前查找。
-   length属性：返回字符串长度。
-   substr方法：基于指定的开始位置和长度提取子字符串。
-   substring方法：类似substr，但第二个参数是结束位置。
-   toLowerCase, toUpperCase方法：转换大小写。
-   split方法：按指定分隔符分割字符串成数组。

### Math数学对象常用属性和方法

-   floor方法：向下取整。
-   round方法：四舍五入取整。
-   ceil方法：向上取整。
-   random方法：生成[0,1)之间的随机小数。

#### 任意两整数间的随机整数公式:

parseInt((大值 - 小值 + 1) * Math.random() + 小值)

### Date对象常用方法

-   构造函数：new Date() 默认当前系统时间，也可接受日期字符串或具体的时间参数。
-   getFullYear/setFullYear方法：推荐使用此方法替代getYear/setYear以避免兼容性问题。
-   getMonth/setMonth方法：月份数字范围为0到11。
-   getDate/setDate方法：设置或获取一个月中的哪一天。
-   getHours/setHours方法：设置或获取小时。
-   getMinutes/setMinutes方法：设置或获取分钟。
-   getSeconds/setSeconds方法：设置或获取秒。
-   getDay方法：返回星期几，数字范围为0（周日）到6（周六）。
-   getTime/setTime方法：获取或设置自1970年1月1日以来的毫秒数。

#### 常用内置函数：

parseInt：字符串转为整数

parseFloat：字符串转为浮点数

isNaN：判断是否是一个非数字

eval：动态执行JavaScript语句字符串

escape/ unescape：基本编码解码，是unicode编码，不是url编码

encodeURI/ decodeURI：url编码解码，常用

encodeURIComponent/ decodeURIComponent：url编码解码，和上面的不同的是，&=?也会被编码

### 事件

#### 事件流：事件传播的顺序

-   捕获型：从父元素传播到子元素（注意：IE不支持）。
-   冒泡型：从子元素传播到父元素（默认行为）。

#### 阻止事件冒泡

-   W3C标准写法：\
    event.stopPropagation();
-   IE专用写法：\
    event.cancelBubble = true;
-   兼容写法：\
    e.stopPropagation ? e.stopPropagation() : e.cancelBubble = true;

#### 阻止事件的默认行为

-   W3C标准写法：\
    event.preventDefault();
-   IE专用写法：\
    event.returnValue = false;
-   行内事件处理，直接在最后加上return false;就可以阻止事件默认行为。
-   代码事件处理中，直接写上return false;也可以阻止事件的默认行为。

#### 事件委托

利用事件冒泡的原理，通过父元素来捕获子元素的事件加以处理。这种方法可以有效地减少事件处理器的数量，提高性能。

#### 浏览器的事件流

1.  事件捕获阶段：事件从最外层的祖先元素开始向下传播直到目标元素。
2.  处于目标阶段：事件到达目标元素本身。
3.  事件冒泡阶段：事件从目标元素开始向上传播回最外层的祖先元素

### 正则表达式的方法

-   test()：判断字符串是否匹配模式。返回true或false。
-   exec()：返回一个对象，包含三项数据------匹配的字符串、下标和原始字符串。如果找不到匹配，则返回null。匹配的字符串以数组形式返回，如果有子表达式，每个子表达式匹配的内容也会添加到数组中。

String中支持正则表达式的方法

-   replace(要替换的字符串或者模式, 替换为的字符串)：替换字符串，默认只会替换第一个匹配项。若要全局替换，必须使用正则表达式配合g参数。
-   match()：返回一个数组，存储所有匹配的值。如果没有匹配项，返回null。
-   search()：返回第一个匹配内容的下标。如果没有匹配项，返回-1。

正则表达式元字符

#### 转义与边界匹配

-   \：转义字符。
-   ^：表示开始（例如 /^a/ 匹配"ab"，但不匹配"bab"）。
-   $：表示结束。

#### 字符集合

-   .：表示任意字符，包括中文。
-   [abc]：表示a、b、c中的任一字符。
-   [^abc]：表示不是a、b、c中的任何一个字符。
-   [a-z]：小写字母；[A-Z]：大写字母；[a-zA-Z]：所有字母（也可以是特定范围如[c-e]、[X-Z]）。
-   [0-9]：数字。
-   [a-zA-Z0-9]：字母和数字。
-   [^a-z]、[^A-Z]、[^0-9]：非字母、非数字。

#### 特殊字符集

-   \s：空白（空格、制表位、换行、换页、回车）。
-   \S：非空白。
-   \d：数字，等价于[0-9]。
-   \D：非数字，等价于[^0-9]。
-   \w：字母、数字或下划线，等价于[0-9a-zA-Z_]。
-   \W：非字母、数字、下划线。

#### 匹配次数

-   *：0次到多次。
-   +：1次到多次。
-   ?：0次或1次。
-   {n}：正好n次。
-   {n,}：至少n次。
-   {n,m}：n次到m次。

#### 其他元字符

-   |：逻辑或（需要打小括号来分组）。
-   (表达式)：子表达式，用于改变优先级或捕获组。
-   ?：在某些情况下用于非贪婪模式，尽可能少地匹配，通常与replace方法一起使用。

### Cookie

Cookie：通用的客户端数据存储技术，在浏览器端以纯文本的键值对形式存储数据，必须基于服务器运行实现（本地运行无法实现）

**Cookie分为两种形式：**

**会话级**：存储在浏览器的会话进程中，生命周期和会话一致

**硬盘级**：永久存储在浏览器的缓存文件夹中，是一个文本文件，但是需要设置失效时间，否则默认就是会话级。失效时间必须以;expires=GMT时间格式

一个域写入的cookie数据，只能被相同的域读取，不能被其他的域读取，例如百度无法读取搜狐写入的cookie，搜狐无法读取新浪写入的cookie，如果写入的是中文数据，需要进行编码，然后读取的时候进行解码。

Cookie: 同源限制，大小限制，服务器和浏览器传递，每次请求都会把cookie放到请求报头发送到服务器。

Cookie在浏览器中有容量限制，有条数限制，每次网络请求都会放到请求报头中一起发给服务器。

JS中操作cookie的语句：document.cookie属性


### 数据交换标准

1.  字符串用特殊符号分割
2.  CSV格式
3.  Xml格式
4.  Json格式

Xml（Extensive Makeup Language）可扩展的标记语言，特点是自定义标签，而不是像html那种固定标签，主要用途做通用数据交换格式和配置文件，缺点是标签本身占用流量太多，解析xml数据比较困难 xml多用于做配置文件

**语法特点：**

1.  文件头部声明 <?xml version="1.0" encoding="UTF-8"?>
2.  标签区分大小写
3.  文档中必须且只能有一个根元素
4.  属性值必须加双引号或者单引号
5.  如果有应用dtd或者schema规则文件，必须符合dtd或者schema规则

1.  Dtd是早期规则语法
2.  Schema是后期规则语法，和dtd语法并存

JSON：基于JavaScript语言的轻量级的数据交换格式(JavaScript Object Notiation)，最常见的就是和后台的数据通信

基本语法特点：就是和字面量的对象语法非常类似，用{}表示一个json对象，用[]表示json数组对象，但是属性名称需要加双引号，只能有属性，不能有方法

Json字符串转换为json对象： .parse() //ie8或以上支持

Json字符串转换为json对象：eval( '(' + json字符串 + ')' ) //ie6或以上支持

Eval：动态执行字符串

Json对象转换为json字符串：JSON.stringify() //ie8或以上支持

### 函数高级扩展

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

### 闭包

一个正常的function调用，里面所有的局部变量在function调用结束之后，都会被销毁，但是如果局部变量被内层作用域所引用，即时外层函数执行完毕也不释放，这就是闭包，缺点是会造成内存泄露（内存使用完不及时释放，就叫内存泄露）

<http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html>

**闭包用途：**

1\. 就是能够读取其他函数内部变量的函数。

2\. 变量始终保持在内存中

**缺点：**

1.内存泄漏

2.会在父函数外部改变内部变量的值

例子
```
　　function f1(){

　　　　var n=999;

　　　　nAdd=function(){n+=1}

　　　　function f2(){

　　　　　　alert(n);

　　　　}

　　　　return f2;

　　}

　　var result=f1();

　　result(); // 999

　　nAdd();

　　result(); // 1000
```

### 原型prototype

**每个类型（例如Date,Error,Array,Number......包括自定义类型例如Person）都有一个属性，这个属性的名字叫做prototype，这个prototype指向一个对象，这个对象就是这个类的原型对象**，里面默认有两个属性，constructor，_proto_，如果把属性或者方法定义到类的原型对象中，不管创建多少个对象，这些属性和方法仅存在一次，比较节省内存，一般来说，只 会把方法定义到原型对象中。不放属性，因为属性值一样，无法实现面向对象。

**_proto_** 任何对象实例都拥有这个属性，这个属性指向这个对象的类的原型对象，不建议在程序中直接使用__proro__这个属性引用，很多浏览器不允许直接调用。

**prototype** 属性的作用就是让该函数所实例化的对象们都可以找到公用的属性和方法，即f1._proto_ === Foo.prototype。

**constructor** 属性的含义就是指向该对象的构造函数，所有函数（此时看成对象了）最终的构造函数都指向Function。

f1.constructor === Foo

constructor：此属性只有原型对象才有，它默认指回prototype属性所在的构造函数。

原型对象本身的类型是一个Object类的对象

**原型链**：基本原理就是对象的__proto__，当一个对象调用某个成员（属性，方法）的时候，首先先查找当前对象实例，没有再查找当前的原型对象，如果还没有，在继续向上层查找，会一直查找到Object类型，如果还没有，直接报错

#### In 和 hasOwnProperty

in语法：检测某个对象中（包括原型对象）是否拥有某个成员 '属性或者方法名称'in 对象

hasOwnProperty方法：判断一个对象中（不包括原型对象）是否拥有某个成员

如果希望任何一个对象都能拥有某个方法，那么这个方法应该定义到Object类的原型中，但是这样做对团队开发会有影响。

**作用域链**：一个function中使用一个变量数据，如果当前作用域没有，就会向外层作用域查找，直至查找到全局作用域，要尽量减少对外层作用域的查找，比较耗费资源，尽量用传参来代替作用域查找。

**继承**：一个类（子类）从另一个类（父类）中获得属性和方法，优点就是避免重复定义同样的属性和方法。继承实现：原型链，和 （基于伪装call的继承方式）构造器

**isPrototypeOf**方法：判断当前对象是否继承了某个类的原型

**propertyIsEnumerable**方法：判断对象的某个成员是否允许被遍历，默认的成员都是可以被遍历的，如果不想被遍历，需要对成员单独做特殊设置。

### `call()`、`apply()` 和 `bind()` 的区别

在 JavaScript 中，`call()`、`apply()` 和 `bind()` 都是用于控制函数执行时的 `this` 值的方法，但它们之间有一些关键的区别。

**都是用来重定义 this 这个对象的！**

#### `call()`

- **用途**：立即调用一个函数，并允许你指定 `this` 的值（即函数运行时所使用的上下文），以及以参数列表的形式传递参数。
- **语法**：`function.call(thisArg, arg1, arg2, ...)`
- **示例**：
    ```javascript
    const obj = { num: 40 };
    function add(a, b) {
        return this.num + a + b;
    }
    console.log(add.call(obj, 1, 2)); // 输出 43
    ```

#### `apply()`

- **用途**：与 `call()` 类似，但它接受一个数组或类数组对象作为参数列表传递给目标函数。
- **语法**：`function.apply(thisArg, [argsArray])`
- **示例**：
    ```javascript
    const obj = { num: 40 };
    function add(a, b) {
        return this.num + a + b;
    }
    console.log(add.apply(obj, [1, 2])); // 输出 43
    ```
- **注意**：第二个参数必须是一个数组或类数组对象。

#### `bind()`

- **用途**：不会立即执行函数，而是返回一个新的函数，并且可以预先设定原函数的 `this` 值以及部分或全部参数。这使得它非常适合用于创建函数的特定版本以便后续使用。
- **语法**：`function.bind(thisArg[, arg1[, arg2[, ...]]])`
- **示例**：
    ```javascript
    const obj = { num: 40 };
    function add(a, b) {
        return this.num + a + b;
    }
    const boundAdd = add.bind(obj, 1);
    console.log(boundAdd(2)); // 输出 43
    ```
- **特点**：返回的是一个新的函数，因此可以在需要的时候延迟执行。

#### 总结

| 方法 | 是否立即执行 | 参数形式 | 主要用途 |
| --- | --- | --- | --- |
| `call()` | 是 | 参数列表 | 立即改变 `this` 并传入参数列表执行函数 |
| `apply()` | 是 | 数组 | 立即改变 `this` 并通过数组传入参数执行函数 |
| `bind()` | 否 | 参数列表 | 创建一个新函数，其 `this` 已被固定，并可预设部分参数 |

每种方法都有其适用场景，理解它们之间的差异可以帮助您更灵活地控制函数的行为和上下文。

#### 手写 bind

多次绑定 bind 是无效的

```
Function.prototype.bind = function (context){
    var self = this;
    return function() {
        self.apply(context, arguments);
    }
}
```

### WEB本地存储

为了代替Cookie实现客户端数据存储，相对于Cookie的区别是，不会在每次请求的时候，把数据自动发送到服务器端，节省流量。

- **localStorage方式**：永久性存储，没有失效时间。
- **sessionStorage方式**：会话级存储，会话关闭就销毁。localStorage 与 sessionStorage 的唯一区别是 localStorage 属于永久性存储，而 sessionStorage 属于当会话结束的时候会被清空。

和Cookie一样，WEB本地存储的数据同源限制。

#### 相关的API

- **length属性**：返回数据的条数。
- **setItem()**：设置或增加数据。
- **removeItem()**：删除指定key的数据。
- **getItem()**：返回指定key的值。
- **key()**：返回指定下标的key。

**应用缓存**：利用html5设定某些文件缓存，需要通过单独的配置文件进行配置。如果缓存的文件进行了修改，必须修改配置文件，否则文件在浏览器端不更新。

### IndexedDB
本地数据库，存储空间大，不仅存储字符串。搜索和索引功能，支持事务（transaction）。

### Service Worker
基于web worker，不能访问dom。可以拦截当前网站的所有请求，进行判断。如果需要向服务器发起请求就转给服务器，如果可以使用缓存就返回缓存，提高浏览体验。


### WebWorker多线程
CPU在同一时刻只能运行一个程序（单核），一个应用程序可以由多个独立的进程组成，一个进程可以由多个独立的线程组成，线程是独立的程序的最小单位，可以并发执行。

没有DOM访问权限：Web Worker 在创建时没有访问 DOM 的权限。
需要额外的代码编写。
不支持跨域请求。
JS默认没有真正意义上的多线程，setTimeout setInterval只是模拟了多线程，并不能真正并发执行。如果JS有任何代码在执行中，定时器语言必须等待JS代码执行结束才能执行。

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
M：model，模型层。代表数据库中的数据源。
V：view，视图层。代表能看到的客户端页面，是根据模型数据创建的。
C：controller，控制层。代表业务逻辑，是模型层和视图层之间的桥梁，建立两者之间的关系。控制层本身是不会处理数据以及输出数据，他只是用来接受请求并决定用那个模型去处理请求，然后再确定用哪个视图来显示返回的数据。

### MVVM
模型视图 视图模型。 数据模型数据双向绑定 的思想为核心。
ViewModel：视图模型，可实现数据的双向绑定，连接View和Model的桥梁，当数据变化时，ViewModel能够监听到数据的变化（通过Data Bindings），自动更新视图，而当用户操作视图，ViewModel也能监听到视图的变化（通过DOM Listeners），然后通知数据做改动，这就实现了数据的双向绑定。

### 跨域请求

一个域名下的程序发出一个请求，请求的URL是另一个域名下的资源，这就是跨域请求，所有的浏览器默认都不允许ajax跨域请求。

不同的域、协议或端口请求一个资源时，资源会发起一个跨域 HTTP 请求。

#### 同源策略的限制包括以下几个方面：

1. **Cookie、LocalStorage和IndexDB等存储在浏览器中的数据**只能被同源网页访问，不能被其他源的网页访问。
2. **XMLHttpRequest和Fetch等网络请求API**只能向同源网址发送请求，不能向其他源发送请求。这样可以防止跨域请求攻击。
3. **DOM操作**也受到同源策略的限制。一个网页只能修改同源网页的DOM，不能修改其他源的DOM。

#### 解决跨域问题的方案

- **在服务器端增加允许跨域请求的响应报头**，设置`Access-Control-Allow-Origin`为`*`。
- **利用jsonp技术**，前端和后端配合实现跨域ajax。所谓的jsonp技术，就是利用浏览器并不禁止外联js是另一个域的js，所以jsonp跨域请求原理就是`<script src="其他域的js">`，而被外联的js是一个函数调用语句，通过实参把数据传递给请求客户端。动态创建script标签，只支持GET请求；存在脚本注入以及跨站请求伪造等安全问题。
- **Postmessage**
- **Node 中间层代理**
- **Nginx反向代理**
- **Websocket**

##### nodejs中间层代理
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
##### Nginx 反向代理配置

Nginx 是一个高性能的 HTTP 和反向代理服务器。使用 Nginx 作为反向代理，可以将客户端请求转发到后端服务器，并将响应返回给客户端，而客户端并不知道实际处理请求的服务器是哪一个。以下是配置 Nginx 反向代理的基本步骤和一个简单的例子。

**步骤**
1. 安装 Nginx：确保您的系统上已经安装了 Nginx。如果尚未安装，可以根据您操作系统的不同选择合适的方法进行安装。例如，在基于 Debian 的系统上（如 Ubuntu），可以通过以下命令安装：
sudo apt update
sudo apt install nginx

2. 编辑 Nginx 配置文件：通常位于 /etc/nginx/nginx.conf 或者 /etc/nginx/sites-available/default。您可以直接编辑默认配置文件或者创建一个新的配置文件。

3. 设置反向代理配置：在配置文件中添加或修改 server 块来定义如何处理特定域名或 IP 地址的请求。

4. 测试配置并重启 Nginx：每次修改完配置文件后，建议先测试配置是否正确，然后重启 Nginx 使更改生效。

bash
sudo nginx -t
sudo systemctl restart nginx

**基本例子**
下面是一个基本的例子，展示如何将所有对 yourdomain.com/api/ 的请求转发到运行在 localhost:3000 上的应用程序：
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

listen 80; 表示监听标准HTTP端口80。
server_name yourdomain.com; 定义了这个server块应该响应哪个域名的请求。
location /api/ { ... } 块指定了对于以 /api/ 开头的所有请求，Nginx 应该将其转发到 http://localhost:3000/。注意这里的 proxy_pass URL 后面有一个斜杠 /，这意味着 /api/ 路径部分会被去掉后再拼接到目标URL后面。
proxy_set_header 指令用于设置转发请求时添加或修改HTTP头部信息，以便后端服务器能够获取客户端的真实信息。

**区别**
设置 Access-Control-Allow-Origin 为 *：

优点：简单快捷，适用于简单的应用场景。

缺点：安全性较低，不适合涉及敏感数据的场景。

Node 中间层代理：

优点：灵活性高，可以在代理层进行复杂的逻辑处理。

缺点：增加了一层代理，可能会引入性能开销，系统复杂性增加。

Nginx 反向代理：

优点：高性能，配置灵活，适合处理大量并发请求和静态资源。

缺点：配置复杂，需要编写和维护配置文件

### 模块化
在ES6之前，社区制定了若干模块加载方案，其中最著名的有CommonJS和AMD两种规范。

- CommonJS 主要应用于服务器端（如Node.js环境）。
- AMD（Asynchronous Module Definition），则多用于浏览器端。
  
#### CommonJS
CommonJS是一种主要用于Node.js环境的模块化规范。ES6的设计思想倾向于静态化，即尽可能在编译时确定模块的依赖关系、输入输出变量等信息。相比之下，CommonJS和AMD模块依赖关系以及输入输出变量只能在运行时确定。

例如，在CommonJS中，一个模块实质上是一个对象，当导入时需要查找该对象的属性。比如从fs模块中整体加载（即加载fs的所有方法），生成一个对象_fs，然后从这个对象中读取特定的方法。这种加载方式被称为“运行时加载”，因此无法进行编译时的“静态优化”。

#### ES6模块
ES6模块不是对象，而是通过export命令显式指定输出的内容，并且这些输出是静态定义的。这样做的好处是可以实现“编译时加载”，即只加载实际需要的部分。例如，如果只需要从fs模块加载3个方法，则只会加载这3个方法，其他方法不会被加载。这种方式称为“编译时加载”，但这也意味着你不能像对待对象那样引用整个模块。

使用export default时，对应的import语句不需要使用大括号。这是因为默认导出只能有一个。
如果不使用export default，则需要使用大括号来明确指出导入哪些具体成员。
本质上，export default就是输出一个名为default的变量或方法，允许为它取任意名字。

#### 加载机制对比
- CommonJS: 输出的是值的一个拷贝，这意味着一旦输出了一个值，模块内部的变化就不会影响到这个值。
- ES6模块: 输出的是值的引用，实现了动态引用，所以模块内部的变化可以反映到外部。

#### CommonJS vs AMD

**CommonJS**

定义：每个单独文件就是一个模块。
输出：通过module.exports对象作为唯一出口。
加载：使用require()方法加载模块。

**AMD (Asynchronous Module Definition)**

目标：解决浏览器端模块开发的问题，特别是处理多个JavaScript文件之间的依赖关系，并避免js文件加载阻塞页面渲染。
工具：Require.js，它定义了全局函数define来定义模块，以及异步加载的require()函数。

**CMD (Common Module Definition)**
浏览器端的实现之一是Sea.js，其设计目标类似于Require.js，但在模块定义和加载机制上有自己的特点，比如使用sea.use()来加载模块。

### 避免全局变量
- 立即执行函数

- 声明命名空间

- 模块化

### 立即执行函数表达式（IIFE）

#### 错误示例

```javascript
function (){ /* code */ }() // 错误
```
在JavaScript中，遇到`function`关键字时，默认会将其视为函数声明。由于函数声明需要一个名称，并且不能直接跟一对括号来立即调用它，因此上述代码会导致语法错误。

即使加上了函数名`foo`，这个例子也会出错。这里的括号`()`被解释为分组操作符而不是函数调用的一部分，因为JavaScript期望一个函数声明或表达式而非这种形式。正确的形式应为：

```javascript
function foo { /* code */ }

() // 错误
```

#### 正确形式

要创建一个立即执行函数表达式（IIFE），你需要确保`function`关键字被视为函数表达式的一部分。这可以通过将整个函数包裹在括号内实现，如下所示：

```javascript
(function() { /* code */ })(); // 正确
```
或者
```javascript
(function() { /* code */ }()); // 也是正确的
```

#### 立即执行函数的作用

- 避免全局变量污染
IIFE允许你定义不需要命名的函数并立即执行它们，这样就不会向全局作用域添加额外的变量或函数名。

- 创建独立的作用域
IIFE内部形成了一个新的作用域，这意味着你可以在此范围内定义局部变量，这些变量对外部是不可访问的，从而实现封装和隐藏数据的效果。

- 封装变量
可以用来封装变量，防止它们泄露到全局作用域，同时提供了一种组织代码的方式。

- 立即执行函数的参数
如果IIFE需要使用外部的全局变量，可以通过参数传递给IIFE，这种方式可以提高代码的可维护性和清晰度。例如：

```javascript
(function(j) {
    // 在这里可以使用 j
})(i); // i 是实参，j 是形参
```

- 立即执行函数的返回值
IIFE可以有返回值，但是通常情况下，除非特别需要，否则很少关注IIFE的返回值。如果你确实需要从IIFE获取返回值，可以直接将其赋值给一个变量：

```javascript
const result = (function() {
    return "This is the result";
})();
console.log(result); // 输出: This is the result
```
### JavaScript 变量提升与作用域

#### 声明提升
1. 变量提升只会提升变量名的声明，而不会提升变量的赋值初始化。
2. 函数提升优先于变量提升，即函数提升在变量提升之上。
3. 函数声明会将函数提升（包括声明function和赋值=），函数表达式只会将变量var提升。

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

#### 块级作用域的函数声明

##### 示例代码

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

函数声明中，整个函数体也都会被提升，而函数表达式中只会提升名称。

#### This 例子

##### 示例代码

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

### 继承
通过子类的原型prototype对象实例化来实现
缺点是：子类之间相互影响。所有子对象共享同一个原型对象，对原型对象的修改会影响到所有子对象

#### 构造函数式继承
构造函数继承是通过在子构造函数中调用父构造函数来实现继承。在构造函数继承中，通过在子构造函数中使用**call()或apply()**方法，将父构造函数的上下文设置为子对象的上下文，从而实现继承。
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

使通过this创建的属性和方法在子类中复制一份，因为是单独复制的，所以各个实例化的子类互不影响。Parent.call(this ,name),所以父类的原型方法自然不会被子类继承，而如果要想被子类继承就必须要放在构造函数中。

#### 组合式继承
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

#### 寄生组合继承
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
#### ES6中的继承 extends
在 ES2015 中有了 class 语法糖，有了 extends、super、static 这样的关键字，更像强类型语言中的“类”了。


### 字符相加

在JavaScript中，不同类型的值在进行相加操作时，会根据类型的不同进行转换和处理。

#### 示例代码

```javascript
// !可将变量转换成boolean类型，null、undefined、NaN以及空字符串('')取反都为true，其余都为false。
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

#### 说明

- `[] + {}` 结果为 `"[object Object]"`，这是因为数组和对象的相加在JavaScript中被转换为字符串操作。
- `{}` + `[]` 结果为 `0`，因为会被解释为 `+ []`，而空数组转换成数字后为 `0`。
- `Number(undefined)` 和 `Number({})` 都会返回 `NaN`，因为它们不能被转换成有效的数字。
- 当数字与字符串相加时，数字会被转换为字符串。
- 与 `undefined` 相加会返回 `NaN`。
- 与 `null` 相加时，`null` 被转换为 `0`。
- 与数组相加时，数组会被转换为字符串。
- 与对象相加时，对象会被转换为字符串 `[object Object]`。
- `1 + true` 结果为 `2`，因为 `true` 被转换为 `1`。
- 逗号运算符返回最后一个表达式的值。
- `Math.max` 不能直接处理数组，需要使用 `Math.max.apply(null, [2,3,4,5])`。
- 浮点数计算可能会出现精度问题，如 `0.1 + 0.2` 结果为 `0.30000000000000004`。

[更多详细内容](https://2ality.com/2012/01/object-plus-object.html)
[参考博客](https://blog.csdn.net/jian_zi/article/details/105137258)


