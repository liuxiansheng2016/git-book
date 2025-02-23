# 性能优化

Https://zhuanlan.zhihu.com/p/559922432

一、避免不必要的渲染

二、优化条件渲染

三、列表中正确的使用 key

四、组件销毁后清理引用数据

五、正确的处理数据

六、缓存优化

七、批量更新，减少 Render 次数

八、懒渲染/虚拟列表

九、debounce、throttle 优化频繁触发的回调

在React中，优化组件的渲染性能可以从以下几个方面入手：

#### 减少不必要的渲染

* **使用`React.memo()`**：对函数组件使用`React.memo()`，通过浅比较props来避免不必要的渲染。可以传递一个自定义的比较函数作为第二个参数，实现更深层次的对比。
* **使用`shouldComponentUpdate()`或`PureComponent`**：在类组件中，重写`shouldComponentUpdate()`方法或继承`React.PureComponent`，通过比较新旧props和state来决定是否重新渲染。

#### 优化渲染过程

* **避免在`render()`中创建新对象**：在`render()`方法内部创建新对象（如数组、函数等）会导致每次渲染时引用变化，触发子组件的重新渲染。应将这些对象移到组件外部或利用`useMemo()`进行缓存。
* **使用`useCallback()`**：对回调函数使用`useCallback()`，仅在依赖项变化时才重新创建函数，避免不必要的子组件渲染。

#### 优化数据传递

* **合理使用props和state**：尽量将数据传递限制在必要的组件范围内，避免不相关的组件因接收数据而触发渲染。
* **使用`context`**：对于需要跨多层级传递的数据，使用`context`可以避免逐层传递props，减少不必要的渲染。

#### 代码拆分和懒加载

* **代码拆分**：利用React的代码拆分功能，将应用拆分为多个代码块，按需加载，减少初始加载时间。
* **懒加载组件**：使用`React.lazy()`和`Suspense`组件，实现组件的按需加载，提升应用性能。

#### 使用生产环境构建

* **启用生产环境模式**：在生产环境中构建应用，React会启用各种优化，如减少不必要的检查、优化渲染算法等，提升运行性能。

#### 性能分析和监控

* **使用React Profiler**：利用React Profiler工具分析组件的渲染性能，找出性能瓶颈并进行优化。
* **监控性能指标**：使用性能监控工具，实时监控应用的性能指标，及时发现并解决性能问题。

通过综合运用这些策略，可以有效提升React应用的渲染性能，改善用户体验。

### 虚拟列表

相关库： react-window/react-virtualized

https://juejin.cn/post/7132277540806213645

<mark style="color:red;">虚拟列表（Virtual List）是一种优化长列表渲染性能的技术。在React中，虚拟列表通过只渲染当前可见区域的列表项</mark>，而不是渲染所有列表项，从而显著减少DOM节点的数量和渲染工作量，提高页面的滚动性能和响应速度。

#### 实现原理

1. **确定可见区域**：计算当前可视区域内应该显示的列表项范围。
2. **动态渲染**：仅渲染可见区域内的列表项，当用户滚动时，根据滚动位置重新计算可见区域，并更新渲染的列表项。
3. **模拟滚动高度**：为了保持滚动的连贯性，需要设置一个容器元素的高度，使其等于所有列表项的总高度，即使实际渲染的列表项只有一小部分。

```
import React, { useRef, useState, useEffect } from 'react';

const VirtualList = ({ items, itemHeight }) => {
  const containerRef = useRef(null);
  const [startIndex, setStartIndex] = useState(0);
  const [endIndex, setEndIndex] = useState(0);

  useEffect(() => {
    const handleScroll = () => {
      const scrollTop = containerRef.current.scrollTop;
      const newStartIndex = Math.floor(scrollTop / itemHeight);
      const newEndIndex = Math.min(items.length - 1, newStartIndex + Math.ceil(containerRef.current.clientHeight / itemHeight));
      setStartIndex(newStartIndex);
      setEndIndex(newEndIndex);
    };

    const container = containerRef.current;
    container.addEventListener('scroll', handleScroll);

    // Initial calculation
    handleScroll();

    return () => {
      container.removeEventListener('scroll', handleScroll);
    };
  }, [items, itemHeight]);

  return (
    <div
      ref={containerRef}
      style={{
        height: '100vh', // 设置容器高度为视口高度
        overflowY: 'auto', // 启用滚动条
      }}
    >
      <div style={{ height: `${items.length * itemHeight}px`, position: 'relative' }}>
        {items.slice(startIndex, endIndex + 1).map((item, index) => (
          <div
            key={item.id}
            style={{
              position: 'absolute',
              top: `${(startIndex + index) * itemHeight}px`,
              height: `${itemHeight}px`,
              width: '100%', // Ensure the item takes full width of the container
              boxSizing: 'border-box', // Include padding and border in the element's total width and height
            }}
          >
            {item.name}
          </div>
        ))}
      </div>
    </div>
  );
};

export default VirtualList;


// src/App.js
import React from 'react';
import VirtualList from './VirtualList';

function generateItems(count) {
  const items = [];
  for (let i = 1; i <= count; i++) {
    items.push({ id: i, name: `Item ${i}` });
  }
  return items;
}

const ITEMS_COUNT = 1000; // 模拟1000条数据
const ITEM_HEIGHT = 50; // 每个列表项的高度为50px

function App() {
  const items = generateItems(ITEMS_COUNT);

  return (
    <div>
      <h1>Virtual List Demo</h1>
      <VirtualList items={items} itemHeight={ITEM_HEIGHT} />
    </div>
  );
}

export default App;
```

### 在 React 中，实现代码拆分主要有以下几种方式：

1.  **动态 import() 与 React.lazy**\
    使用动态 import() 语法可以实现按需加载模块。React.lazy 配合 Suspense 组件，能很方便地实现组件级别的代码拆分。例如：

    ```jsx
    import React, { Suspense } from 'react';

    const OtherComponent = React.lazy(() => import('./OtherComponent'));

    function App() {
      return (
        <div>
          <Suspense fallback={<div>Loading...</div>}>
            <OtherComponent />
          </Suspense>
        </div>
      );
    }

    export default App;
    ```

    在这个例子中，当 App 组件渲染时，OtherComponent 会被延迟加载，并在加载过程中显示 fallback 指定的内容。
2. **第三方库（如 Loadable Components）**\
   除了 React.lazy 外，还有一些第三方库（例如 loadable-components 或 react-loadable）可以提供更灵活的代码拆分方案，比如支持服务端渲染、预加载等功能。
3. **打包工具（如 Webpack）的支持**\
   Webpack 内置了对动态 import 的支持，通过配置也可以实现代码拆分。Webpack 会根据动态 import 自动将代码拆分成多个 chunk，并在需要时加载相应的代码文件。
