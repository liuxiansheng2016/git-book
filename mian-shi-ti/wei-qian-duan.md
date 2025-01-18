---
description: 微前端（Micro Frontend）是一种架构风格，它将单个前端应用由多个小型前端应用组合而成。每个小型应用可以独立开发、部署
---

# 微前端

## 1. 基于模块联邦的解决方案

· Webpack Module Federation：这是Webpack 5引入的一个新特性，允许不同的Web应用共享模块而无需打包到同一个构建中。通过Module Federation，不同的微前端应用可以动态地加载彼此的代码和资源，实现按需加载。

## 2. 路由代理方案

· Single-SPA：Single-SPA 是一个用于在客户端协调多个JavaScript微前端应用的框架。它提供了一套标准来定义如何注册、启动、卸载子应用，并处理不同子应用间的路由切换。

· qiankun：qiankun 是阿里巴巴开源的一个微前端框架，它支持将多个子应用无缝集成到一个主应用中，且这些子应用可以是使用不同技术栈构建的。

## 3. 其他

· Piral：Piral 是一个基于React的微前端框架，专注于创建可组合的应用，支持服务端渲染。它允许开发者以插件的形式开发独立的功能模块，这些模块可以在不同的上下文中复用。

· Bit：虽然Bit本身是一个组件管理平台，但它也可以用来构建微前端应用。通过Bit，开发者可以轻松地分享、重用和管理跨项目的组件

&#x20;

## Webpack federation

1. Host Application (宿主应用): 这是主应用，负责加载其他微前端应用中的模块。
2. Remote Application (远程应用): 这些是被宿主应用引用的微前端应用，它们暴露自己的模块供宿主应用使用。
3. Shared Modules (共享模块): 这些是在多个应用之间共享的模块，比如 React、Vue 或者自定义的业务逻辑模块。



### **宿主应用配置**

```javascript
const { ModuleFederationPlugin } = require('webpack').container;
const path = require('path');

module.exports = {
  //...
  plugins: [
    new ModuleFederationPlugin({
      name: 'host', // 宿主应用的名字
      filename: 'remoteEntry.js',
      remotes: {
        app1: 'app1@http://localhost:3001/remoteEntry.js', // 指定远程应用及其URL
      },
      shared: ['react', 'react-dom'], // 指定要共享的模块
    }),
  ],
  output: {
    publicPath: 'auto',
  },
};

```

### 远程应用**配置**

```javascript
const { ModuleFederationPlugin } = require('webpack').container;
const path = require('path');

module.exports = {
  //...
  plugins: [
    new ModuleFederationPlugin({
      name: 'app1', // 远程应用的名字
      filename: 'remoteEntry.js',
      exposes: {
        './Button': './src/components/Button', // 暴露组件
      },
      shared: ['react', 'react-dom'], // 共享模块
    }),
  ],
  output: {
    publicPath: 'auto',
  },
};

```

### 使用

```javascript
import React from 'react';
import Button from 'app1/Button'; // 导入远程应用中的组件

function App() {
  return (
    <div>
      <h1>Hello, Micro Frontends!</h1>
      <Button />
    </div>
  );
}

export default App;

```
