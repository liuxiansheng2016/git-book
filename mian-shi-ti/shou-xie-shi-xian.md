# 手写实现

### **将多层嵌套数组平铺成一层数组的方法**

`flat()` 可以将嵌套数组展开到指定的层级，`flat(Infinity)` 可以完全展开多层嵌套数组。

`reduce()` 遍历数组，递归检查是否为数组，如果是则继续展开

```
function flattenArray(arr) {
  return arr.reduce(
    (acc, val) => acc.concat(Array.isArray(val) ? flattenArray(val) : val),
    []
  );
}

const arr = [1, [2, [3, [4, 5]]], 6];
console.log(flattenArray(arr));  // [1, 2, 3, 4, 5, 6]
```

`toString()` + `split()`

```
const arr = [1, [2, [3, [4, 5]]], 6];
const flattened = arr.toString().split(',').map(Number);
console.log(flattened);  // [1, 2, 3, 4, 5, 6]
```

使用 `JSON.stringify()` + `replace()`

```
const arr = [1, [2, [3, [4, 5]]], 6];
const flattened = JSON.stringify(arr)
  .replace(/\[|\]/g, '')   // 去掉方括号
  .split(',')              // 转换为字符串数组
  .map(Number);            // 转换回数字
console.log(flattened);  // [1, 2, 3, 4, 5, 6]
```

### 模拟let

```
(function() {
    var c = 3;
    console.log(c); // 1
})();

console.log(c); // c is not defined
```

### 模拟const

```
function _const(key, value) {
    window[key] = value;
    Object.defineProperty(window, key, {
        enumerable: false,
        configurable: false,
        get: function() {
            return value;
        },
        set: function(newValue) {
            if (newValue !== value) {
                throw TypeError("这是只读变量，不可修改");
            } else {
                return value;
            }
        },
    });
}
```

### new

```
function myNew(fn,...arg){
    var obj = {};//创建一个空对象
    obj.__proto__ = fn.prototype;//继承该对象的原型
    //obj  = Object.create(fn.prototype)
    var result = fn.apply(obj, ...arg); //this指向该对象，并执行构造函数的代码
    return result instanceof Object ? result : obj; //如果构造函数返回的是对象，则返回该对象，否则返回 obj
}

function Person(name, age) {
  this.name = name;
  this.age = age;
}

const p1 = myNew(Person, "Alice", 25);
console.log(p1.name); // Alice
console.log(p1.age);  // 25
console.log(p1 instanceof Person); // true
}
```

### Object.create

```
function myCreate(obj){
    function Fn(){} // 创建一个空的构造函数
    F.prototype = obj; //继承对象的原型
    return new Fn(); // 创建一个新对象，并将其返回
}
const person = {
  greet: function() {
    console.log("Hello, I am " + this.name);
  }
};

const obj = myCreate(person);
obj.name = "Alice";
obj.greet(); // Hello, I am Alice

console.log(obj.__proto__ === person); // true
```

### bind

```
Function.prototype.myind(obj,...args){
  var self = this;
  return function(...innerArgs){
    //在这里，直接使用 'this' 将不会指向原来的函数，而是取决于这个匿名函数是如何被调用的。
    return self.apply(obj, args.concat(innerArgs)) 
  }
}


const obj = { num: 40 };
function add(a, b) {
    return this.num + a + b;
}
const boundAdd = add.mybind(obj, 1);
var res = boundAdd(2);

console.log(res)

```

### call

```
Function.prototype.myCall = function (context, ...args) {
  context = context || window; // 若 context 为空，则默认为全局对象
  const fnKey = Symbol(); // 生成唯一键，避免属性覆盖
  context[fnKey] = this; // 将当前函数赋值到 context 的属性中
  const result = context[fnKey](...args); // 执行函数
  delete context[fnKey]; // 清理属性
  return result;
};

const obj = { num: 40 };
function add(a, b) {
    return this.num + a + b;
}
const res = add.myCall(obj, 1,2);


console.log(res)
```

### instanceof

```
function myInstanceOf(obj, constructor) {
  let prototype = Object.getPrototypeOf(obj);
  while (prototype) {
    if (prototype === constructor.prototype) return true;
    prototype = Object.getPrototypeOf(prototype);
  }
  return false;
}

```

### 节流

````
```html
function throttle(fn, delay) {
   let canRun = true;
   return function () {
     if (!canRun) return;
     canRun = false;

     setTimeout(() => {
       fn.apply(this, arguments);
       canRun = true;
     }, delay);
   }

}

function handleScroll() {
  console.log('scrolling');
}

window.addEventListener('scroll',throttle(handleScroll,1000));
```
````

### 防抖

```html
function  debounce(fn, time) {
    let timer;
    return function() {
        clearTimeout(timer);
        timer = setTimeout(() => {
          fn.apply(this, arguments);
        }, time);
    }
}
```

### 深拷贝

```
// 在 Object 类的原型中添加一个深克隆方法
Object.prototype.clone = function () {
    // 根据情况创建一个数组或者普通 object 对象
    let obj = this instanceof Array ? [] : {};
    // 遍历当前对象的成员，赋给新对象
    for (let prop in this) {
        // 如果当前成员是对象类型
        if (typeof this[prop] === 'object' && this[prop]!== null) {
            obj[prop] = this[prop].clone();
        } else {
            obj[prop] = this[prop];
        }
    }
    return obj;
};

// 使用自定义深克隆方法
let obj1 = { a: 1, b: { c: 2 } };
let obj2 = obj1.clone();

// 验证深克隆，修改 obj2 中 b 属性的 c 值
obj2.b.c = 3;
console.log(obj1.b.c); // 输出 2，说明修改 obj2 不影响 obj1
```

### 去重

```
[...new Set(array)];
Array.from(new Set(array));
const array = [1, 2, 2, 3, 4, 4, 5];

const uniqueArray = array.filter((value, index, self) => {
  return self.indexof(value) == index;
});
console.log(uniqueArray); // 输出 [1, 2, 3, 4, 5]
```

### **实现promise all**

```
function promiseAll(promises) {
    return new Promise((resolve, reject) => {
        if (!Array.isArray(promises)) {
            return reject(new TypeError('Argument is not an array'));
        }

        let resolvedCounter = 0;
        const result = new Array(promises.length);
        for (let i = 0; i < promises.length; i++) {
            Promise.resolve(promises[i])
                .then(value => {
                    resolvedCounter++;
                    // 按照原始顺序保存结果
                    result[i] = value;
                    if (resolvedCounter === promises.length) {
                        resolve(result);
                    }
                })
                .catch(error => {
                    // 如果任意一个 Promise 被拒绝，则整个 Promise.all 被拒绝
                    reject(error);
                });
        }
    });
}

// 测试代码
const p1 = Promise.resolve(3);
const p2 = 42;
const p3 = new Promise((resolve, reject) => {
    setTimeout(resolve, 100, 'foo');
});

promiseAll([p1, p2, p3]).then(values => {
    console.log(values); // [3, 42, "foo"]
}).catch(error => {
    console.error(error);
});
```

### 输入一个promise和一个时间

在规定的时间内如果promise的状态为非pending，则返回状态，如果为pending则返回一个新的promise，内容为new Error()

```
function timeoutPromise(promise, timeout) {
    // 创建一个在指定时间后拒绝的 Promise
    const timeoutPromise = new Promise((_, reject) => {
        setTimeout(() => {
            reject(new Error('Timeout'));
        }, timeout);
    });

    // 使用 Promise.race 来竞争原 Promise 和超时 Promise
    return Promise.race([promise, timeoutPromise]);
}
```

### 每几秒打印

```
setInterval

function print(){
       console.log(111);
       setTimeOut(print, 5000)
}
Print()

function delay(ms) {
    return new Promise(resolve => {
        setTimeout(() => {
            console.log("delay");
            resolve();
        },ms)
    })
}
async function excute(){
    while (true) {
        await delay(2000);
    }
}
excute();
```

### 虚拟dom  转成真实dom

```
function createDOMFromVirtualDOM(virtualDOM) {
    // 创建一个新的 DOM 元素
    const domElement = document.createElement(virtualDOM.type);

    // 设置属性
    if (virtualDOM.props) {
        Object.keys(virtualDOM.props).forEach(key => {
            if (key === 'children') {
                // 递归处理子节点
                virtualDOM.props.children.forEach(child => {
                    if (typeof child === 'string') {
                        // 文本节点
                        domElement.appendChild(document.createTextNode(child));
                    } else {
                        // 子虚拟 DOM 节点
                        domElement.appendChild(createDOMFromVirtualDOM(child));
                    }
                });
            } else {
                // 其他属性
                domElement.setAttribute(key, virtualDOM.props[key]);
            }
        });
    }

    return domElement;
}
```

### 排序

```
```

### eventbus

// eventBus.js

<pre><code><strong>```javascript
</strong>// eventBus.js
class EventBus {
    constructor() {
      this.events = {};
    }
  
    on(eventName, callback) {
      if (!this.events[eventName]) {
        this.events[eventName] = [];
      }
      this.events[eventName].push(callback);
    }
  
    off(eventName, callback) {
      if (this.events[eventName]) {
        this.events[eventName] = this.events[eventName].filter(
          (cb) => cb !== callback
        );
      }
    }
  
    emit(eventName, data) {
      if (this.events[eventName]) {
        this.events[eventName].forEach((callback) => callback(data));
      }
    }
  }
  
  const eventBus = new EventBus();
  export default eventBus;
```
}  
```
</code></pre>

//Subscriber.js

````
```javascript
import React, { useEffect, useState } from 'react';
import eventBus from './eventBus';

const Subscriber = () => {
    const [message, setMessage] = useState('');

    useEffect(() => {
        const handler = (data) => {
            setMessage(data.message);
        };
        
        eventBus.on('customEvent', handler); // 订阅事件

        return () => {
            eventBus.off('customEvent', handler); // 组件卸载时取消订阅
        };
    }, []);

    return (
        <div>
            Received Message: {message}
        </div>
    );
};

export default Subscriber;
```
````

// Publisher.js

````
```javascript
import React from 'react';
import eventBus from './eventBus';

const Publisher = () => {
    const handleClick = () => {
        console.log('Publishing an event');
        eventBus.emit('customEvent', { message: 'Hello from Publisher!' });
    };

    return (
        <div>
            <button onClick={handleClick}>Trigger Event</button>
        </div>
    );
};

export default Publisher;
```
````

### 实现动态表单

```
// Some codeimport React from 'react';
import DynamicMergeTable from './DynamicMergeTable';

const columns = [
  { Header: '姓名', accessor: 'name' },
  { Header: '年龄', accessor: 'age' },
  { Header: '城市', accessor: 'city' },
];

const data = [
  { name: 'Alice', age: 25, city: 'New York' },
  { name: 'Alice', age: 26, city: 'Los Angeles' },
  { name: 'Bob', age: 30, city: 'Chicago' },
  { name: 'Charlie', age: 22, city: 'San Francisco' },
  { name: 'Charlie', age: 22, city: 'San Francisco' },
  { name: 'Charlie', age: 23, city: 'San Francisco' },
  { name: 'David', age: 35, city: 'Boston' },
];

const App = () => (
  <div>
    <h1>动态合并表格示例</h1>
    {/* 同时在 mergeColumns 中传入 'name' 和 'city' */}
    <DynamicMergeTable columns={columns} data={data} mergeColumns={['name', 'city']} />
  </div>
);

export default App;

import React from 'react';

const DynamicMergeTable = ({ columns, data, mergeColumns = [] }) => {
  // mergeColumns: 数组，指定哪些列需要进行动态合并（例如 ['name']）

  // 预处理：为需要合并的列计算每个单元格的 rowSpan
  const rowSpanMatrix = data.map(() => {
    const obj = {};
    mergeColumns.forEach(col => obj[col] = 0);
    return obj;
  });

  mergeColumns.forEach(col => {
    let i = 0;
    while (i < data.length) {
      let count = 1;
      // 当当前行与下一行该列的值相同时，增加计数
      while (i + count < data.length && data[i][col] === data[i + count][col]) {
        count++;
      }
      // 在第一行设置 rowSpan
      rowSpanMatrix[i][col] = count;
      // 后续合并的行设置为 0（表示不再渲染该单元格）
      for (let j = i + 1; j < i + count; j++) {
        rowSpanMatrix[j][col] = 0;
      }
      i += count;
    }
  });

  return (
    <table border="1" cellPadding="5" style={{ borderCollapse: 'collapse' }}>
      <thead>
        <tr>
          {columns.map(col => (
            <th key={col.accessor}>{col.Header}</th>
          ))}
        </tr>
      </thead>
      <tbody>
        {data.map((row, rowIndex) => (
          <tr key={rowIndex}>
            {columns.map(col => {
              // 如果该列需要合并
              if (mergeColumns.includes(col.accessor)) {
                const span = rowSpanMatrix[rowIndex][col.accessor];
                // 当 rowSpan 为 0 时，说明该单元格已被上面的单元格合并，不再渲染
                if (span === 0) return null;
                return (
                  <td key={col.accessor} rowSpan={span}>
                    {row[col.accessor]}
                  </td>
                );
              }
              // 非合并列直接渲染
              return <td key={col.accessor}>{row[col.accessor]}</td>;
            })}
          </tr>
        ))}
      </tbody>
    </table>
  );
};

export default DynamicMergeTable;

```
