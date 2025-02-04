# React

主要特性：

1. **组件化**：
   * React 基于组件构建，组件是独立的、可复用的 UI 模块。每个组件可以包含自己的状态和逻辑，并且可以组合成更复杂的 UI。
2. **虚拟 DOM**：
   * React 使用虚拟 DOM 来提高性能。虚拟 DOM 是 real DOM 的轻量级副本，它在内存中进行更新，然后将变化应用到 real DOM 中。这样可以减少直接操作 DOM 的次数，从而提高应用的性能。
3. **单向数据流**：
   * React 遵循单向数据流的设计思想，数据从父组件流向子组件。这使得数据的管理和调试更加简单和可预测。
4. **声明式编程**：
   * React 采用声明式编程范式，开发者只需描述 UI 的最终状态，React 会负责更新和渲染 UI。当应用的状态变化时，React 会高效地更新视图。
5. **JSX 语法**：
   * React 使用 JSX（JavaScript XML）语法，这是一种在 JavaScript 中编写类似 HTML 的语法。JSX 使得代码更加直观和可读，并且支持强大的 JavaScript 表达式。
6. **组件生命周期**：
   * React 组件具有一套完整的生命周期方法，如 `componentDidMount`、`componentDidUpdate` 和 `componentWillUnmount`，允许开发者在组件的不同阶段执行特定的逻辑。
7. **Hooks**：
   * React 16.8 引入了 Hooks，允许在函数组件中使用状态和其他 React 特性，如 `useState` 和 `useEffect`。Hooks 简化了代码并提高了代码的复用性。
8. **高效的更新**：
   * React 通过协调算法（也称为调和）来高效地更新组件。当状态或属性变化时，React 会比较新的虚拟 DOM 和旧的虚拟 DOM，并仅应用实际更改的部分到 real DOM。
9. **生态系统**：
   * React 拥有一个庞大且活跃的生态系统，提供了丰富的第三方库和工具，如 React Router、Redux 和 Next.js，以扩展和增强 React 应用的功能。
10. **服务器端渲染（SSR）**：
    * React 支持服务器端渲染（SSR），即在服务器端生成 HTML 并发送到客户端，这有助于提高首屏渲染速度和搜索引擎优化（SEO）。



### 搭建步骤
腾讯云 NPM 镜像：
npm config set registry https://mirrors.cloud.tencent.com/npm/

1 npm install -g create-react-app （npm uninstall -g create-react-app before install）

2 npx create-react-app my-app (if use ts, npx create-react-app my-app --template typescript)

3 cd my-app
