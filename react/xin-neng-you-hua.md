# 常见性能优化指南

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

- **使用`React.memo()`**：对函数组件使用`React.memo()`，通过浅比较props来避免不必要的渲染。可以传递一个自定义的比较函数作为第二个参数，实现更深层次的对比。
- **使用`shouldComponentUpdate()`或`PureComponent`**：在类组件中，重写`shouldComponentUpdate()`方法或继承`React.PureComponent`，通过比较新旧props和state来决定是否重新渲染。

#### 优化渲染过程

- **避免在`render()`中创建新对象**：在`render()`方法内部创建新对象（如数组、函数等）会导致每次渲染时引用变化，触发子组件的重新渲染。应将这些对象移到组件外部或利用`useMemo()`进行缓存。
- **使用`useCallback()`**：对回调函数使用`useCallback()`，仅在依赖项变化时才重新创建函数，避免不必要的子组件渲染。

#### 优化数据传递

- **合理使用props和state**：尽量将数据传递限制在必要的组件范围内，避免不相关的组件因接收数据而触发渲染。
- **使用`context`**：对于需要跨多层级传递的数据，使用`context`可以避免逐层传递props，减少不必要的渲染。

#### 代码拆分和懒加载

- **代码拆分**：利用React的代码拆分功能，将应用拆分为多个代码块，按需加载，减少初始加载时间。
- **懒加载组件**：使用`React.lazy()`和`Suspense`组件，实现组件的按需加载，提升应用性能。

#### 使用生产环境构建

- **启用生产环境模式**：在生产环境中构建应用，React会启用各种优化，如减少不必要的检查、优化渲染算法等，提升运行性能。

#### 性能分析和监控

- **使用React Profiler**：利用React Profiler工具分析组件的渲染性能，找出性能瓶颈并进行优化。
- **监控性能指标**：使用性能监控工具，实时监控应用的性能指标，及时发现并解决性能问题。

通过综合运用这些策略，可以有效提升React应用的渲染性能，改善用户体验。

### 虚拟列表
```
/**
 * 绝对定位方案
 */
import { useState } from 'react';
import { flushSync } from 'react-dom';

function FixedSizeList({ containerHeight, itemHeight, itemCount, children }) {
  // children 语义不好，赋值给 Component
  const Component = children;

  const contentHeight = itemHeight * itemCount; // 内容高度
  const [scrollTop, setScrollTop] = useState(0); // 滚动高度

  // 继续需要渲染的 item 索引有哪些
  let startIdx = Math.floor(scrollTop / itemHeight);
  let endIdx = Math.floor((scrollTop + containerHeight) / itemHeight);

  // // 上下额外多渲染几个 item，解决滚动时来不及加载元素出现短暂的空白区域的问题
  // const paddingCount = 2;
  // startIdx = Math.max(startIdx - paddingCount, 0); // 处理越界情况
  // endIdx = Math.min(endIdx + paddingCount, itemCount - 1);

  const top = itemHeight * startIdx; // 第一个渲染 item 到顶部距离

  // 需要渲染的 items
  const items = [];
  for (let i = startIdx; i <= endIdx; i++) {
    items.push(
      <Component
        key={i}
        index={i}
        style={{
          position: 'absolute',
          left: 0,
          top: i * itemHeight,
          width: '100%',
          height: itemHeight
        }}
      />
    );
  }

  return (
    <div
      style={{
        height: containerHeight,
        overflow: 'auto',
        position: 'relative'
      }}
      onScroll={(e) => {
        flushSync(() => {
          setScrollTop(e.target.scrollTop);
        });
      }}
    >
      <div style={{ height: contentHeight }}>{items}</div>
    </div>
  );
}

export default FixedSizeList;

import FixedSizeList from './FixedSizeList';

function Item({ style, index }) {
  return (
    <div
      className="item"
      style={{
        ...style,
        backgroundColor: index % 2 === 0 ? 'burlywood' : 'cadetblue'
      }}
    >
      {index}
    </div>
  );
}

export default function App() {
  const list = new Array(10000).fill(0).map((item, i) => i);

  return (
    <>
      列表项高度固定 - 虚拟列表实现
      <FixedSizeList
        containerHeight={300}
        itemCount={list.length}
        itemHeight={50}
      >
        {Item}
      </FixedSizeList>
    </>
  );
}

```

