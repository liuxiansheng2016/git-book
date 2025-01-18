# Template engine

结合 Node.js 的 Express 框架与模板引擎（如 Nunjucks、EJS 或 Pug），你可以轻松地创建一个简单的 SSR 应用程序

## **EJS**

npm install express ejs

```javascript
const express = require('express');
const app = express();

// 设置视图引擎为 EJS
app.set('view engine', 'ejs');

// 设置视图文件夹的位置（默认是 'views'）
app.set('views', './views');

// 示例路由
app.get('/', (req, res) => {
  const data = { title: 'Home Page', message: 'Welcome to the home page!' };
  res.render('index', data);
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

创建ejs模板

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title><%= title %></title>
</head>
<body>
  <h1><%= message %></h1>
</body>
</html>
```

## **Nunjucks**

我们配置了 Nunjucks 来查找位于 views 文件夹中的模板文件，并将 .njk 扩展名设置为默认的视图引擎。然后，我们在路由处理函数中传递数据给模板进行渲染。

```javascript
const nunjucks = require('nunjucks');
const express = require('express');
const app = express();

// 设置 Nunjucks
const env = nunjucks.configure('views', {
    autoescape: true,
    express: app, // 集成 Express
});

app.set('view engine', 'njk'); // 设置视图引擎为 nunjucks

// 示例路由
app.get('/', (req, res) => {
    const data = {
        title: 'Hello Nunjucks!',
        items: ['Item 1', 'Item 2', 'Item 3']
    };
    res.render('index', data);
});

// 启动服务器
app.listen(3000, () => {
    console.log('Server is running on port 3000');
});

```

创建模板文件

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{{ title }}</title>
</head>
<body>
    <h1>{{ title }}</h1>
    <ul>
        {% raw %}
{% for item in items %}
            <li>{{ item }}</li>
        {% endfor %}
{% endraw %}
    </ul>
</body>
</html>

```

## **使用 Pug (以前称为 Jade)**

```javascript
const express = require('express');
const app = express();

// 设置视图引擎为 Pug
app.set('view engine', 'pug');

// 设置视图文件的存放位置 (默认为 'views')
app.set('views', './views');

// 示例路由
app.get('/', (req, res) => {
    const data = { title: 'Home Page', message: 'Welcome to the home page!' };
    res.render('index', data);
});

app.listen(3000, () => console.log('Server running on port 3000'));

```

Creating a Pug Template

```html
doctype html
html(lang="en")
  head
    meta(charset="UTF-8")
    title= title
  body
    h1= message

```

**对比**

1\. Nunjucks

特点

语法：基于 Jinja2 模板语言，支持简洁的控制结构和表达式。

继承和包含：强大的模板继承和包含功能，有助于创建模块化的模板系统。

过滤器和宏：内置了许多有用的过滤器，并且可以定义宏来重用代码片段。

异步加载：支持异步加载模板，非常适合现代 Web 应用程序。

自动转义：默认启用自动转逃，以防止 XSS 攻击。

社区支持：由 Mozilla 维护，拥有活跃的社区和支持。

2\. EJS (Embedded JavaScript Templates)

特点

语法：基于 HTML，允许直接嵌入 JavaScript 表达式和控制结构。

学习曲线：对于已经有 HTML 和 JavaScript 基础的开发者来说非常容易上手。

灵活性：可以直接在模板中编写复杂的逻辑，适合需要高度定制的应用场景。

输出直观：生成的 HTML 更接近于原始模板，便于调试。

3\. Pug (以前称为 Jade)

特点

语法：采用缩进语法，语法更加紧凑，减少了冗余的标签和括号。

高效：编译速度快，生成的 HTML 文件通常较小。

简洁：模板看起来更整洁，适合喜欢简洁风格的开发者。

混合内容：可以在同一文件中混合使用文本和其他语言（如 Markdown）。

学习曲线：初学者可能觉得不直观，但对于熟悉其语法的人来说，编写和维护模板会更加高效。
