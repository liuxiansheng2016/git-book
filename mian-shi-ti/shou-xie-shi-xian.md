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
function myind(obj){
```
