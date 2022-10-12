---
title: 《JavaScript高级程序设计（第4版）》读书笔记-继承
categories: 读书笔记
tags: [JavaScript, 读书笔记]
date: 2021-11-9 9:00:00
updated: 2022-10-12 9:00:00
---

实现继承是 ECMAScript 唯一支持的继承方式，主要是通过原型链实现的

### 原型链

每个构造函数都有一个原型对象，原型有一个属性指回构造函数，而实例有一个内部指针指向原型

如果原型是另一个类型的实例，这个原型本身有一个内部指针指向另一个原型，相应地另一个原型也有一个指针指向另一个构造函数

> 该原型自身不再存在constructor属性，而是继承自另一个原型

#### 默认原型

任何函数的默认原型都是一个 Object 的实例，这意味着这个实例有一个内部指针指向 `Object.prototype`

```Java
function fun() {};
console.log(fun.prototype.__proto__ === Object.prototype); //true
```

#### 原型与继承关系

`instanceof` 如果一个实例的原型链中出现过相应的构造函数，则返回true

`isPrototypeOf()` `构造函数.prototype.isPrototypeOf(实例)`只要原型链中包含这个原型，这个方法就返回true

#### 关于方法

以对象字面量方式创建原型方法会破坏之前的原型链，相当于重写了原型链，覆盖后的原型为默认原型

#### 原型链的问题

原型中包含的引用值会在所有实例间共享

在使用原型实现继承时，原型实际上变成了另一个类型的实例，意味着原先的实例属性变成了原型属性

### 盗用构造函数

解决原型包含引用值导致的继承问题

基本思路：在子类构造函数中调用父类构造函数

```javascript
function SuperType() { 
 this.colors = ["red", "blue", "green"]; 
} 
function SubType() { 
 // 继承 SuperType 
 SuperType.call(this); 
} 
let instance1 = new SubType(); 
instance1.colors.push("black"); 
console.log(instance1.colors); // "red,blue,green,black" 
let instance2 = new SubType(); 
console.log(instance2.colors); // "red,blue,green" 
```

新的 `SubType` 对象上运行了 `SuperType()` 函数中的所有初始化代码

#### 传递参数

#### 盗用构造函数的问题

必须在构造函数中定义方法，因此函数不能重用

子类不能访问父类原型上定义的方法

### 组合继承

综合了原型链和盗用构造函数

**基本思路：使用原型链继承原型上的属性和方法，而通过盗用构造函数继承实例属性**

```JavaScript
function SuperType(name){ 
 this.name = name; 
 this.colors = ["red", "blue", "green"]; 
} 

SuperType.prototype.sayName = function() { 
 console.log(this.name); 
}; 

function SubType(name, age){ 
 // 继承属性
 SuperType.call(this, name); 
 this.age = age; 
} 

// 继承方法
SubType.prototype = new SuperType(); 
SubType.prototype.sayAge = function() { 
 console.log(this.age); 
}; 
```

### 原型式继承

```javascript
function object(o) { 
 function F() {} 
 F.prototype = o; 
 return new F(); 
} 
```

这个 `object()` 函数会创建一个临时构造函数，将传入的对象赋值给这个构造函数的原型，然后返回这个临时类型的一个实例

ECMAScript5 增加 `Object.create()` 方法将原型式继承的概念规范化

```javascript
let person = { 
 name: "Nicholas", 
 friends: ["Shelby", "Court", "Van"] 
}; 

let anotherPerson = Object.create(person, { 
 name: { 
 value: "Greg" 
 } 
}); 

let yetAnotherPerson = Object.create(person); 
yetAnotherPerson.name = "Linda"; 
yetAnotherPerson.friends.push("Barbie"); 

console.log(person.friends); // "Shelby,Court,Van,Rob,Barbie"

console.log(anotherPerson.name); // "Greg"
console.log(anotherPerson.friends); // "Shelby,Court,Van,Rob,Barbie"
```

`anotherPerson` 和 `yetAnotherPerson` 的 `[[prototype]]` 都是 `person` （无对象标识）

适用不需要单独创建构造函数，但仍然需要在对象间共享信息的场合

### 寄生式继承

**基本思路：创建一个实现继承的函数，以某种方式增强对象，然后返回这个对象**

```JavaScript
function createAnother(original){ 
 let clone = object(original); // 通过调用函数创建一个新对象
 clone.sayHi = function() { // 以某种方式增强这个对象
 console.log("hi"); 
 }; 
 return clone; // 返回这个对象
} 
```

> `object()` 函数不是寄生式继承所必需的，任何返回新对象的函数都可以在这里使用

```javascript
let person = { 
 name: "Nicholas", 
 friends: ["Shelby", "Court", "Van"] 
}; 

let anotherPerson = createAnother(person); 
anotherPerson.sayHi(); // "hi" 
```

上例基于 `person` 对象返回了一个新对象。新返回的 `anotherPerson` 对象具有 `person` 的所有属性和方法，还有一个新方法 `sayHi()`

适用主要关注对象，而不在乎类型和构造函数的场合

#### 问题

通过寄生式继承给对象添加函数会导致函数难以重用，与构造函数模式类似

### 寄生式组合继承

组合继承最主要的效率问题就是父类构造函数始终会被调用两次：一次在是创建子类原型时调用，另一次是在子类构造函数中调用

```JavaScript
function SuperType(name) { 
 this.name = name; 
 this.colors = ["red", "blue", "green"]; 
} 

SuperType.prototype.sayName = function() { 
 console.log(this.name); 
}; 

function SubType(name, age){ 
 SuperType.call(this, name); // 第二次调用 SuperType() 
 this.age = age; 
} 

SubType.prototype = new SuperType(); // 第一次调用 SuperType() 
SubType.prototype.constructor = SubType; 
SubType.prototype.sayAge = function() { 
 console.log(this.age); 
}; 
```

有两组 name 和 colors 属性：一组在实例上，另一组在 `SubType` 的原型上

**基本思路：不通过调用父类构造函数给子类原型赋值，而是取得父类原型的一个副本**

即使用寄生式继承来继承父类原型，然后将返回的新对象赋值给子类原型

寄生式组合继承的基本模式

```JavaScript
function inheritPrototype(subType, superType) { 
 let prototype = object(superType.prototype); // 创建对象 
 prototype.constructor = subType; // 增强对象 
 subType.prototype = prototype; // 赋值对象 
} 
```

这个函数接收两个参数：子类构造函数和父类构造函数

1. 创建父类原型的一个副本
2. 给返回的 `prototype` 对象设置 `constructor` 属性，解决由于重写原型导致默认 `constructor` 丢失的问题
3. 将新创建的对象赋值给子类型的原型

```JavaScript
function SuperType(name) { 
 this.name = name; 
 this.colors = ["red", "blue", "green"]; 
}

SuperType.prototype.sayName = function() { 
 console.log(this.name); 
};

function SubType(name, age) { 
 SuperType.call(this, name);
 this.age = age; 
}

inheritPrototype(SubType, SuperType); 

SubType.prototype.sayAge = function() { 
 console.log(this.age); 
}; 
```



