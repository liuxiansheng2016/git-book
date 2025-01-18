# 兼容性实现

1. &#x20;html 设置正确的doctype
2. &#x20;语义化标签
3. css reset 基础样式
4. css 样式前缀
5. &#x20;js plyfill

## js plyfill

1. **手动实现**

以 ES6 的 object#assign 为例 ，即使在 IE 11上，仍会报错

所以我们需要打上相应的补丁。可以用第三方成熟的package ，也可以使用

MDN 提供的模板进行打补丁

&#x20;

2. **通过使用webpack或者（.babelrc）打补丁**

相关依赖 有@babel/preset-env,transform-runtime,core-js(来提供常见的 polyfills)

&#x20;

**.babelrc配置**

```
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "useBuiltIns": "usage",
        "corejs": 3,
        "targets": {
          "browsers": ["> 1%", "last 2 versions", "not dead"]
        }
      }
    ]
  ],
  "env": {
    "development": {
      "plugins": ["transform-runtime"]
    },
    "production": {
      "plugins": ["transform-runtime"],
      "compact": true
    }
  }
}

```



**Webpack 配置文件中配置**

```
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: [
              [
                '@babel/preset-env',
                {
                  useBuiltIns: 'usage',
                  corejs: 3,
                  targets: {
                    browsers: ['> 1%', 'last 2 versions', 'not dead']
                  }
                }
              ]
            ]
          }
        }
      }
    ]
  }
};

```

Core.js 是JavaScript 标准运行库之一，它提供了从ES3～ES7+ 以及还处在提案阶段的 JavaScript 的实现。

&#x20;

**useBuiltIns**

usage：只引入实际用到的 polyfills。

entry：在入口文件中引入所有的 polyfills。

false：不引入任何 polyfills。

**transform-runtime:**

1\. 减少代码体积：默认情况下，Babel 会在每次编译时生成必要的 polyfill 代码，这会导致代码体积增大。transform-runtime 通过重用已存在的 polyfill 实例，减少了重复代码的生成。

2\. 提高性能：由于减少了重复代码，最终生成的代码体积更小，加载和执行速度更快。

3\. 避免污染全局作用域：transform-runtime 不会将 polyfill 污染到全局作用域，而是通过 require 动态加载所需的 polyfill。

compact: 这个选项用于控制输出代码的格式。

true: 生成最小化的代码，减少文件大小，适合生产环境。

false: 生成更易读的代码，适合开发环境。

auto: 默认值，根据环境自动决定是否生成最小化的代码。

&#x20;

**3 根据浏览器特性，动态打补丁**

```
<script src="https://polyfill.io/v3/polyfill.js?features=Promise"></script>
```

&#x20;这种按需加载 polyfill 的方式有助于减小最终打包后的代码大小
