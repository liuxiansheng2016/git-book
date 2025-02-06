## CSS

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
