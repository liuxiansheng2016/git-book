# 性能优化

## 加载优化

1. 按需加载（预加载，懒加载，滚动加载，分页加载）：根据用户的需求动态加载资源，避免一次性加载所有资源，从而减少初始加载时间。
2. 缓存策略：利用浏览器缓存和服务器端缓存策略，减少资源的重复加载。 Service Worker
3. 请求合并：通过合并多个请求为一个请求，减少HTTP请求的数量，从而加快加载速度。
   1. API端点聚合可以通过创建一个中间层（通常称为API网关或代理服务）来实现，
   2. 合并CSS和JavaScript文件

## JS优化

1. 异步加载：通过异步加载JavaScript文件，避免阻塞页面的渲染。
2. &#x20;减少回流和重绘：优化JavaScript代码，减少不必要的DOM操作，从而减少回流（reflow）和重绘（repaint）。
3. &#x20;合并DOM操作：将多个DOM操作合并为一次操作，减少浏览器的处理负担。
4. webwoker 做复杂的运算

## 资源优化

&#x20;webpack 打包和压缩

## 渲染优化

1. 减少DOM数量和层级：简化DOM结构，减少DOM节点的数量和层级，从而减少浏览器的处理负担。
2. 避免JS渲染动画：使用CSS动画代替JavaScript动画，减少JavaScript的执行时间。
3. 高频操作节流防抖：对高频操作（如滚动事件）使用节流（throttle）和防抖（debounce）技术，减少不必要的处理\


优化服务器响应

* **使用HTTP/2**：HTTP/2支持多路复用，可以同时处理多个请求，减少延迟。
* **优化数据库查询**：优化数据库索引和查询语句，减少数据库响应时间。
* **使用缓存层**：使用Redis、Memcached等缓存层，减少对数据库的直接访问



webpack

splitchunk提取公共代码

确保在部署应用时使用生产模式构建，使用如Tree Shaking

terser配置

懒加载 通过路由配置实现按需加载模块，减少初始加载时间。

滚动加载，page + 1

预加载 prefetch 和preload。 异步加载JavaScript 使用async和defer属性

使用缓存

数据库优化

## 如何处理大数据量的渲染，比如 10 万条数据的列表？

### 1. 虚拟列表

* 计算可见区域的起始和结束索引。
* 根据起始和结束索引，从大数据集合中提取可见的数据项。
* 渲染可见的数据项，并根据滚动位置动态更新可见区域。

```


import React, { useState, useRef, useEffect } from 'react';

const VirtualList = ({ data, itemHeight, containerHeight }) => {
    const [scrollTop, setScrollTop] = useState(0);
    const containerRef = useRef(null);

    useEffect(() => {
        const handleScroll = () => {
            setScrollTop(containerRef.current.scrollTop);
        };
        containerRef.current.addEventListener('scroll', handleScroll);
        return () => {
            containerRef.current.removeEventListener('scroll', handleScroll);
        };
    }, []);

    const startIndex = Math.floor(scrollTop / itemHeight);
    const endIndex = startIndex + Math.ceil(containerHeight / itemHeight);
    const visibleData = data.slice(startIndex, endIndex);

    const paddingTop = startIndex * itemHeight;
    const paddingBottom = (data.length - endIndex) * itemHeight;

    return (
        <div
            ref={containerRef}
            style={{
                height: containerHeight,
                overflowY: 'auto',
                position: 'relative'
            }}
        >
            <div style={{ paddingTop, paddingBottom }}>
                {visibleData.map((item, index) => (
                    <div
                        key={index}
                        style={{ height: itemHeight, border: '1px solid #ccc' }}
                    >
                        {item}
                    </div>
                ))}
            </div>
        </div>
    );
};

// 使用示例
const data = Array.from({ length: 100000 }, (_, i) => `Item ${i}`);
const App = () => {
    return (
        <div>
            <VirtualList
                data={data}
                itemHeight={30}
                containerHeight={400}
            />
        </div>
    );
};

export default App;
```

### 2. 分页加载

\
分页加载是将大数据集分成多个小的页面，每次只加载和渲染当前页面的数据。用户可以通过翻页按钮切换不同的页面。

### 3. 懒加载（Lazy Loading）

* **图像懒加载**：可以通过 HTML5 的 `loading="lazy"` 属性实现，也可以利用 JavaScript 和 `IntersectionObserver` API 进行更精细的控制。
* **模块懒加载**：利用 ES6 的动态导入特性，可以有效地延迟加载不必要的 JavaScript 代码，直到真正需要的时候再加载。
* **基于用户行为的内容加载**：可以根据用户的操作（例如滚动、点击按钮等）来动态加载额外的数据或内容，进一步优化首屏加载速度和整体性能。

**内容加载实现思路**

* 监听滚动事件，当滚动到接近列表底部时，触发加载新数据的操作。
* 加载新数据后，将其添加到已渲染的数据列表中。

## 如果有 100 万条数据，如何在页面中高效搜索特定内容？

当面对 100 万条数据并需要在页面中高效搜索特定内容时，可采用以下多种策略和技术来实现：

#### 前端层面

**1. 数据预处理与索引**

* **构建索引**：在前端加载数据后，为数据构建索引结构，例如哈希表（JavaScript 中的对象）。以每条数据的某个关键属性（如姓名、ID 等）作为键，数据的索引或引用作为值。这样在搜索时可以通过键快速定位到对应的数据，时间复杂度可达到 O(1)。

```javascript
// 假设 data 是包含 100 万条数据的数组
const data = [...]; 
const index = {};
data.forEach((item, i) => {
    const key = item.keyProperty; // keyProperty 是用于搜索的关键属性
    if (!index[key]) {
        index[key] = [];
    }
    index[key].push(i);
});

// 搜索函数
function search(key) {
    const resultIndices = index[key] || [];
    return resultIndices.map(i => data[i]);
}
```

* **分词索引**：对于文本内容的搜索，可以对文本进行分词处理，为每个分词构建索引。例如使用自然语言处理库（如 `natural`）进行分词，然后为每个分词建立倒排索引。

**2. 虚拟列表结合搜索**

* **虚拟列表**：使用虚拟列表技术只渲染当前可见区域的数据，减少 DOM 操作和内存占用。在搜索时，先在全部数据中进行筛选，然后将筛选结果通过虚拟列表展示。

```jsx
import React, { useState, useRef, useEffect } from 'react';

const VirtualList = ({ data, itemHeight, containerHeight, searchTerm }) => {
    const [scrollTop, setScrollTop] = useState(0);
    const containerRef = useRef(null);

    const filteredData = data.filter(item => item.text.includes(searchTerm));

    useEffect(() => {
        const handleScroll = () => {
            setScrollTop(containerRef.current.scrollTop);
        };
        containerRef.current.addEventListener('scroll', handleScroll);
        return () => {
            containerRef.current.removeEventListener('scroll', handleScroll);
        };
    }, []);

    const startIndex = Math.floor(scrollTop / itemHeight);
    const endIndex = startIndex + Math.ceil(containerHeight / itemHeight);
    const visibleData = filteredData.slice(startIndex, endIndex);

    const paddingTop = startIndex * itemHeight;
    const paddingBottom = (filteredData.length - endIndex) * itemHeight;

    return (
        <div
            ref={containerRef}
            style={{
                height: containerHeight,
                overflowY: 'auto',
                position: 'relative'
            }}
        >
            <div style={{ paddingTop, paddingBottom }}>
                {visibleData.map((item, index) => (
                    <div
                        key={index}
                        style={{ height: itemHeight, border: '1px solid #ccc' }}
                    >
                        {item.text}
                    </div>
                ))}
            </div>
        </div>
    );
};

// 使用示例
const largeData = Array.from({ length: 1000000 }, (_, i) => ({ text: `Item ${i}` }));
const App = () => {
    const [searchTerm, setSearchTerm] = useState('');
    return (
        <div>
            <input
                type="text"
                placeholder="Search..."
                value={searchTerm}
                onChange={(e) => setSearchTerm(e.target.value)}
            />
            <VirtualList
                data={largeData}
                itemHeight={30}
                containerHeight={400}
                searchTerm={searchTerm}
            />
        </div>
    );
};

export default App;
```

**3. 分页与搜索结合**

* **分页加载**：将数据分成多个页面，每次只加载当前页面的数据。在搜索时，根据搜索条件筛选出符合条件的数据，然后重新计算分页信息并加载相应页面的数据。

```javascript
// 假设 data 是包含 100 万条数据的数组
const data = [...];
const itemsPerPage = 10;
let currentPage = 1;

function searchAndPaginate(searchTerm, page) {
    const filteredData = data.filter(item => item.text.includes(searchTerm));
    const startIndex = (page - 1) * itemsPerPage;
    const endIndex = startIndex + itemsPerPage;
    const currentPageData = filteredData.slice(startIndex, endIndex);
    return currentPageData;
}

// 使用示例
const searchResult = searchAndPaginate('keyword', 1);
```

#### 后端层面

**1. 数据库索引**

* **数据库索引优化**：如果数据存储在数据库中，为搜索字段创建合适的索引。例如，在 MySQL 中，可以使用 `CREATE INDEX` 语句为需要搜索的字段创建索引，这样可以加快数据库查询速度。

```sql
-- 为 users 表的 name 字段创建索引
CREATE INDEX idx_name ON users (name);
```

* **全文索引**：对于文本搜索，使用数据库的全文索引功能。例如，在 MySQL 中可以使用 `FULLTEXT` 索引，支持更高效的文本搜索。

```sql
-- 为 articles 表的 content 字段创建全文索引
ALTER TABLE articles ADD FULLTEXT(content);
-- 使用全文索引进行搜索
SELECT * FROM articles WHERE MATCH(content) AGAINST('search keyword');
```

**2. 后端分页与搜索 API**

* **分页 API**：后端提供分页查询的 API，前端根据用户输入的搜索条件和当前页码向后端发送请求，后端根据条件筛选数据并返回相应页的数据。

```javascript
// 后端 Node.js + Express 示例
const express = require('express');
const app = express();
const data = [...]; // 100 万条数据

app.get('/search', (req, res) => {
    const { searchTerm, page, itemsPerPage = 10 } = req.query;
    const filteredData = data.filter(item => item.text.includes(searchTerm));
    const startIndex = (page - 1) * itemsPerPage;
    const endIndex = startIndex + itemsPerPage;
    const currentPageData = filteredData.slice(startIndex, endIndex);
    res.json(currentPageData);
});

const port = 3000;
app.listen(port, () => {
    console.log(`Server running on port ${port}`);
});
```

#### 综合策略

* **前端缓存**：对于一些经常搜索的关键词和结果进行前端缓存，避免重复请求后端。
* **异步搜索**：使用异步编程技术（如 `async/await`、`Promise`）进行搜索操作，避免阻塞主线程，保证页面的流畅性。

通过以上前端和后端的综合优化策略，可以在处理 100 万条数据时实现高效的搜索功能。
