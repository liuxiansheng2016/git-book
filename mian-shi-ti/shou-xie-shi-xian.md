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

call

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
