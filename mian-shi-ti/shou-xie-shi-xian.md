# 手写实现

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

Copy

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
