---
title: __proto__、prototype与constructor
categories: JavaScript
tags: [JavaScript]
date: 2021-11-9 9:00:00
updated: 2022-10-12 9:00:00
---

`__proto__`、`prototype`与`constructor`<!--more-->

### \_\_proto\_\_

`__proto__`属性在ES标准定义中的名字是`[[Prototype]]`

- `__proto__`和`constructor`属性是**对象**所独有的
- `prototype`属性是**函数**所独有的
  - 由于JavaScript中函数也是对象，所以函数也拥有`__proto__`和`constructor`属性

原型链通过`__proto__`一级一级向上寻找，直到`null`

### prototype

函数的`prototype`属性指向该函数的原型对象（从一个函数指向一个对象），即当这个函数作为构造函数时所创建的实例的原型对象

作用：让该函数所实例化的对象们可以找到公用的属性和方法

### constructor

只有prototype对象自身拥有`constructor`属性

对象的`constructor`属性指向该对象的构造函数（从一个对象指向一个函数），“每个对象都有构造函数”（意思是每个对象都可以找到其对应的`constructor`属性）

由`Function`创建的函数对象较为特殊，该函数对象的`constructor`属性指向其构造函数`Function`，此时该函数对象相当于`Function`的一个实例

```JavaScript
function fun() {}
console.log(fun.constructor === fun.__proto__.constructor); //true
console.log(fun.constructor === fun.prototype.constructor); //false
console.log(fun.__proto__ === Function.prototype); //true
console.log(fun.constructor === Function.constructor); //true
console.log(fun.constructor === Function.prototype.constructor); //true
```

该函数对象的原型对象，即`.prototype`的`constructor`属性指向该函数对象

对一个对象，有

`对象.__proto__ === 其构造函数.prototype`

`其构造函数.prototype.constructor === 其构造函数本身`

> 使用对象字面量 `= {}` 创建新对象相当于 `= new Object()`，其对应的`construcor`属性即指向`Object`

