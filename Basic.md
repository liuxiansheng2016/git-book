## CSS

<https://codepen.io/jh3y/full/WNxBLbB>

<https://dev.to/fabiogiolito/create-a-color-theme-with-these-upcoming-css-features-4o83>

<https://segmentfault.com/a/1190000041826742>

### 盒子模型
一个标准的盒子模型是由四部分组成，内容 padding（内边距）border（边框）margin（外边距）

#### 盒子模型的怪异模式
在标准模式的盒子模型中，padding和border都是向外扩展空间，并不会占据内容本身的空闲。
在怪异模式的盒子模型中，padding和border是占据内容本身的空闲，并不会向外扩展
**怪异模式的情况：**
1. 不是doctype文档类型并且在低版本的IE浏览器下
2. 在CSS3中用box-sizing属性设置值为border-box

### 浏览器的私有前缀：
谷歌浏览器 `-webkit`
火狐浏览器 `-moz`
欧朋浏览器 `-o`
正向浏览器 `-ms`

### 选择器的权重
元素选择器 `1`
完全选择器 `10`
id选择器 `100`
行内样式 `1000`
!important 权重最高！

### 选择器优先级：
id选择器>类选择器>元素选择器 行内样式>内嵌样式>外联样式

### CSS 加载和渲染阻塞
**解释阶段：**
浏览器会解析HTML文档时，遇到 `<link rel="stylesheet">` 标签时会暂停解析HTML，直到CSS文件完全加载并解析完毕，这是因为浏览器需要确保在渲染页面之前，所有的样式都已经准备好，以避免出现“闪烁”（FOUC, Flash of Unstyled Content）的现象。
**渲染阶段：**
即使HTML解析继续进行，浏览器也不会开始渲染页面，直到所有引用的样式表（即 `<link rel="stylesheet">` 标签引用的样式表）都已加载完毕，这是因为浏览器需要确保页面的视觉效果与预期一致。
**JavaScript 执行阻塞：**
由于CSS和脚本都会阻塞HTML的解析，因此也会间接阻塞JavaScript的执行。当浏览器遇到 `<script>` 标签时，它会暂停HTML解析并执行JavaScript代码，如果在此之前有未加载完毕的CSS文件，HTML解析会被阻塞，进而导致JavaScript代码的执行也被阻塞。

### BFC(块级格式化上下文)
**什么是BFC**
Block Formatting Context，解释为块级格式化上下文。一旦HTML元素触发BFC，则满足下列的任意一个或多个条件即可：
1. 设置了`display:none`, `display:flex`, `display:inline-flex`.
2. position的值不是static或者relative（position:absolute或者position:fixed）.
3. display的值是inline-block、table-cell、flex、table-caption除了inline-flex.
4. overflow的值是visible以外（overflow:hidden、overflow:scroll）.

**BFC 特性**
- 用于决定盒子布局以及浮动互相影响范围的一个区域。
- BFC 是一个独立的渲染单元，容器里面的元素不会影响到外面的元素。
- 使用BFC属性的元素可以包含浮动元素，会自动包围浮动，可以阻止元素被浮动元素覆盖。

### 垂直margin失效问题的解决办法
1. 父元素Overflow: hidden.
2. 父元素Border.
3. 父元素position设置absolute或者fixed，或者float浮动，脱离文档输出流.
4. 父元素Display: inline-block.

### 浮动元素会导致父元素高度塌陷，解决的方法
**浮动元素中**
父元素的高度默认是以子元素高度撑开的，但子元素浮动后，便脱离了文档流，因此无法撑起父元素，从而导致父元素高度丢失。
1. **父元素设置溢出隐藏**：给一个浮动元素的后面，加上一个任意块级元素应用clear清除浮动样式.
2. **父元素添加clearfix类**：使用`:after`伪元素清除浮动.
3. **父元素启用overflow:hidden的方式**，在IE6下需要要用zoom:1样式做兼容.
4. **使用浮动元素的祖先元素的最简单输出一个空内容**，在空内容上应用清除浮动样式.

### 选择器的分类
1. **元素选择器**，标签名称作为选择器 例如 p h1 table
2. **类选择器**，用法选择定义类名，然后在元素中用class属性应用
3. **id选择器**，用#定义id，指定id的元素会自动应用样式
4. **通用选择器**，用 * 定义，选中所有元素
5. **后代选择器**，语法：E F，E不是F，类、id选择器任意情况 例如 ul li #div1 a .p1 span1
6. **相邻兄弟选择器**，语法：E+F，针对ul-li，#div1+p1
7. **普通兄弟选择器**，语法：E[F] 如p h1 h2
8. **属性选择器**，语法：[E|属性] [E|属性|=值] 例如input[size] input[type=text]

#### 伪元素选择器：语法E:after E:before 通过css添加页面内容，必须配合content属性
CSS 伪元素用来选择和添加非内容产生的元素

#### **伪类选择器**
##### 动态伪类
- 动态伪类与用户交互相关，描述了元素处于某种状态时的样式。
  - `hover`：当鼠标悬停在元素上时触发。
  - `active`：当元素被激活（如点击）时触发。
  - `focus`：当元素获得焦点时触发，通常是通过Tab键导航到该元素时发生。
  - `visited`, `link`：针对超链接的不同状态设置样式

##### 元素状态伪类
- 这些伪类适用于用户界面元素的状态，通常用于表单控件。
  - `enabled`, `disabled`：分别匹配启用或禁用的表单控件。
  - `checked`, `unchecked`：匹配选中的复选框或单选按钮。
  - `valid`, `invalid`：基于输入是否符合其验证要求来应用样式。
  - `required`, `optional`：分别匹配设置了required属性和没有设置该属性的表单元素。
  - `read-only`, `read-write`：分别匹配只读或可编辑的元素

##### 结构伪类
- 结构伪类允许你基于文档树中的位置来选择元素。
  - `first-child`, `last-child`：分别匹配作为父元素的第一个或最后一个子元素的元素。
  - `nth-child(n)`, `nth-last-child(n)`：基于元素在其父元素中的位置选择元素。
  - `only-child`：选择那些是其父元素唯一子元素的元素。
  - `empty`：选择没有任何子元素（包括文本节点）的元素。
  - `:not(selector)`：否定伪类，选择不匹配给定选择器的所有元素。
  - `:root`：匹配文档的根元素，在HTML文档中通常是`<html>`标签

## less和sass

Less 和 Sass 都是 CSS 预处理器，它们扩展了 CSS 的功能，允许开发者使用变量、嵌套规则、混合（Mixins）、继承等功能来编写更易维护和扩展的样式代码。以下是 Less 和 Sass 对比 CSS 的一些关键特性：

### 变量

-   CSS: 在原生 CSS 中没有变量的概念，直到 CSS 变量（也称为自定义属性）被引入。
-   Less/Sass: 两者都支持变量，可以存储颜色、字体和其他重复使用的值。例如，在 Less 中使用 @ 符号定义变量，而在 Sass 中则使用 $ 符号。
  ```
 / Less\
@primary-color: #4D926F;\
body { color: @primary-color; }

// Sass\
$primary-color: #4D926F;\
body { color: $primary-color; }
```
### 嵌套

-   CSS: 不支持选择器的嵌套，所有的选择器都需要独立声明。
-   Less/Sass: 允许选择器嵌套，从而减少重复书写父级选择器的工作量，并且使代码结构更加清晰。
```
// Less\
nav {\
  ul {\
    margin: 0;\
    padding: 0;\
    list-style: none;\
  }\
}

// Sass\
nav {\
  ul {\
    margin: 0;\
    padding: 0;\
    list-style: none;\
  }\
}
```
### 混合（Mixins）

-   CSS: 没有直接等同于 Mixins 的概念。
-   Less/Sass: Mixins 可以重用一组属性或样式规则，使得代码复用变得更加简单。
```
// Less\
.border-radius(@radius) {\
  -webkit-border-radius: @radius;\
     -moz-border-radius: @radius;\
          border-radius: @radius;\
}\
.box { .border-radius(10px); }

// Sass\
@mixin border-radius($radius) {\
  -webkit-border-radius: $radius;\
     -moz-border-radius: $radius;\
          border-radius: $radius;\
}\
.box { @include border-radius(10px); }
```
### 继承

-   CSS: 不支持类的继承。
-   Sass: 提供了 @extend 指令，可以让一个选择器继承另一个选择器的所有样式。
```
// Sass\
.error { border: 1px solid red; }\
.badError { @extend .error; font-weight: bold; }
```
### 运算

-   CSS: 不支持数学运算。
-   Less/Sass: 支持基本的数学运算，如加法、减法、乘法和除法，可以直接在样式中进行计算。
```
// Less\
@the-border: 1px;\
@base-size: 10px;\
@full-size: @base-size + @the-border * 2;

// Sass\
$the-border: 1px;\
$base-size: 10px;\
$full-size: $base-size + $the-border * 2;
```
### 控制指令（仅限 Sass）

-   Sass: 提供条件语句（如 @if, @else）和循环结构（如 @for, @each, @while），使得样式表可以根据不同的条件动态生成。

scss
```
// Sass\
@if $type == warning {\
  background-color: yellow;\
} @else if $type == error {\
  background-color: red;\
}
```


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

### HTTP网络通信协议

TIP/IP：传输控制协议/网际协议 有很多子协议 http ftp https

HTTP：超文本传输协议 简单说就是传输html数据的，HTTP协议是无连接，无状态的，客户端发出请求，连接服务器，得到响应，立即断开。

请求request-响应response：必须由客户端主动发出请求，服务器才能产生响应，一般来说，请求是客户端向服务器端发送数据，而响应是服务器端向客户端发送数据

如果请求的是一个静态资源，服务端会直接响应数据到客户端，如果请求的是一个动态资源，比如java,php,asp，服务器端会先在服务器端执行完毕这些动态内容，然后把执行的结果响应到客户端。

#### HTTP请求的三部分组成：

请求行：请求方法（GET POST PUT DELETE）请求的URI 协议和版本 例如http1.1

消息报头：客户端的一些相关信息，例如语言，浏览器，操作系统，压缩方式......

请求正文：GET请求没有请求正文，POST的请求正文存储了发送的数据

#### 响应状态码：

200 请求响应

304 从浏览器本地缓存读取数据 (Last Modified)

404 资源找不到

405 无法请求对应的get和post处理程序

429 limit

401 unthrization

403 forbidden

500 服务器程序运行错误

301 永久重定向

302 暂时重定向

#### 增删改查的四大操作（CRUD）：restful风格

GET 查询

POST 增加

PUT 修改

DELETE 删除

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

每个类型（例如Date,Error,Array,Number......包括自定义类型例如Person）都有一个属性，这个属性的名字叫做prototype，这个prototype指向一个对象，这个对象就是这个类的原型对象，里面默认有两个属性，constructor，_proto_，如果把属性或者方法定义到类的原型对象中，不管创建多少个对象，这些属性和方法仅存在一次，比较节省内存，一般来说，只 会把方法定义到原型对象中。不放属性，因为属性值一样，无法实现面向对象。

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

propertyIsEnumerable方法：判断对象的某个成员是否允许被遍历，默认的成员都是可以被遍历的，如果不想被遍历，需要对成员单独做特殊设置。
