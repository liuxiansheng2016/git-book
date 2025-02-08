# Dom diff

计算出Virtual DOM中真正变化的部分，并只针对该部分进行原生DOM操作，而非重新渲染整个页面。

### React 渲染过程

#### 1. Render (渲染) 阶段
这个阶段主要是生成新的虚拟DOM树，并与旧的虚拟DOM树进行比较（diff）。它包括以下子阶段：
- **Tree 构建**：从根组件开始递归地构建出整个应用的虚拟DOM树。
- **Diffing**：React 使用 Fiber 架构来遍历新旧两棵树，通过比较节点来确定哪些部分需要更新。这一过程可以被打断，以便给其他更高优先级的任务让路。
  - **Tree 层级对比**：仅在同一层级上进行比较，不会跨层级比较。
  - **Component 层级对比**：如果组件类型相同，则继续向下深入比较；如果不同，则直接替换整个组件。
  - **Element 层级对比**：对于同一层级的元素，React 会检查它们的 key 和 type 来决定是否可以复用现有的 DOM 节点。

#### 2. Pre-commit (预提交) 阶段
在这个阶段，React 已经决定了哪些更新需要应用到实际的DOM上。但是，在真正执行这些更新之前，React 可能会做一些准备工作，比如计算布局测量(layout measurements)，这在某些情况下是必要的，例如当组件依赖于其尺寸或位置时。

#### 3. Commit (提交) 阶段
一旦所有的准备工作完成，React 就会进入commit阶段，此时真正的DOM更新会被应用。这个阶段涉及以下几个方面：
- **DOM 更新**：根据前面阶段所做的决定，React 会添加、删除或修改DOM节点。
- **生命周期方法调用**：在 commit 阶段，React 还会触发相应的生命周期方法，如 componentDidMount, componentDidUpdate, 或者 componentWillUnmount（对于即将被移除的组件）。
- **Ref 处理**：如果任何组件使用了 refs，那么这些引用也会在这个阶段被更新。

#### React Diff 的主要步骤

1. **生成虚拟 DOM 树**：当组件重新渲染时，React 会生成新的虚拟 DOM 树。
2. **比较新旧虚拟 DOM 树**：React 将新的虚拟 DOM 树与旧的虚拟 DOM 树进行比较，找出差异。
3. **应用差异**：React 将找出的差异应用到实际的 DOM 上，只更新需要更改的部分。

React Diff 的比较规则主要包括以下几点：

* **相同类型的节点**：如果新旧树中的节点类型相同（例如都是 `<div>` 或 `<span>`），那么只会比较它们的属性和子节点。
* **不同类型的节点**：如果新旧树中的节点类型不同，则旧节点将被完全替换。
* **键（key）**：如果节点带有 `key` 属性，React 会使用 `key` 来唯一标识节点。这有助于 React 更精确地识别节点，从而减少不必要的重新渲染。
* **属性和状态**：比较节点的属性（如 `className`、`style` 等）和状态（如 `state`），如果发现不同，则更新相应的属性或状态。
* **子节点**：递归比较子节点，找出子节点之间的差异。

React 将 diff 分为三个阶段：tree diff、component diff 和 element diff。

#### Tree Diff
Tree diff 涉及的是对整个组件树的不同层级之间的比较。由于 Web UI 中 DOM 节点跨层级的移动操作较少，React 选择只在同一层次内的节点间进行比较。

#### Component Diff
Component diff 主要是针对同类型的组件实例之间的比较。如果两个组件属于同一类别，那么 React 会继续深入比较它们各自的子树；但如果组件类型不同，则直接替换整个组件。

#### Element Diff
Element diff 是指在同一层级的所有节点之间进行比较。当节点处于同一层级时，diff 提供了三种操作：INSERT_MARKUP（插入）、MOVE_EXISTING（移动）和 REMOVE_NODE（删除）。

