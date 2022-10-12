---
title: 阮一峰ES6 笔记
categories: 读书笔记
tags: [读书笔记, JavaScript]
date: 2021-11-9 9:00:00
updated: 2022-10-12 9:00:00
---

ECMAScript 6

## Class的基本语法

### 类的实例

可以通过实例的`__proto__`属性为“类”添加方法，改写原型（不推荐使用，因为这会改变“类”的原始定义，影响到所有实例）

> `__proto__` 并不是语言本身的特性，这是各大厂商具体实现时添加的私有属性，虽然目前很多现代浏览器的 JS 引擎中都提供了这个私有属性，但依旧不建议在生产中使用该属性，避免对环境产生依赖。生产环境中，我们可以使用 `Object.getPrototypeOf` 方法来获取实例对象的原型，然后再来为原型添加方法/属性。

### Class表达式

与函数一样，类也可以使用表达式的形式定义

```javascript
const MyClass = class Me {
  getClassName() {
    return Me.name;
  }
};
```

上面代码使用表达式定义了一个类。需要注意的是，这个类的名字是`Me`，但是`Me`只在 Class 的内部可用，指代当前类。在 Class 外部，这个类只能用`MyClass`引用。

```javascript
let inst = new MyClass();
inst.getClassName() // Me
Me.name // ReferenceError: Me is not defined
```

上面代码表示，`Me`只在 Class 内部有定义。

### 注意点

#### 不存在提升

 ES6 不会把类的声明提升到代码头部

```javascript
{
  let Foo = class {};
  class Bar extends Foo {
  }
}
```

上面的代码不会报错，因为`Bar`继承`Foo`的时候，`Foo`已经有定义了。但是，如果存在`class`的提升，上面代码就会报错，因为`class`会被提升到代码头部，而`let`命令是不提升的，所以导致`Bar`继承`Foo`的时候，`Foo`还没有定义。

### 私有属性的提案

#### in 运算符

`in`运算符对于`Object.create()`、`Object.setPrototypeOf`形成的继承，是无效的，因为这种继承不会传递私有属性

### `new.target` 属性

Class 内部调用`new.target`，返回当前 Class

子类继承父类时，`new.target`会返回子类

利用这个特点，可以写出不能独立使用、必须继承后才能使用的类

```javascript
class Shape {
  constructor() {
    if (new.target === Shape) {
      throw new Error('本类不能实例化');
    }
  }
}

class Rectangle extends Shape {
  constructor(length, width) {
    super();
    // ...
  }
}

var x = new Shape();  // 报错
var y = new Rectangle(3, 4);  // 正确
```

上面代码中，`Shape`类不能被实例化，只能用于继承

## class的继承

### super关键字

#### 第一种情况

`super`作为函数调用时，代表父类的构造函数，`super()`只能用在子类的构造函数之中

```javascript
class A {}

class B extends A {
  constructor() {
    super();
  }
}
```

上面代码中，子类`B`的构造函数之中的`super()`，代表调用父类的构造函数

注意，`super`虽然代表了父类`A`的构造函数，但是返回的是子类`B`的实例，即`super`内部的`this`指的是`B`的实例，因此`super()`在这里相当于`A.prototype.constructor.call(this)`

#### 第二种情况

`super`作为对象时，在普通方法中，指向父类的原型对象；在静态方法中，指向父类

## 变量的解构赋值

### 对象的解构赋值

对象的解构赋值可以取到继承的属性

#### 注意点

1. 将一个已经声明的变量用于解构赋值时

   错误写法：

   ```javascript
   let x;
   {x} = {x: 1};
   // SyntaxError: syntax error
   ```

   JavaScript 引擎会将`{x}`理解成一个代码块

   解决方法：不将大括号写在行首，避免 JavaScript 将其解释为代码块

   ```javascript
   let x;
   ({x} = {x: 1});
   ```

2. 数组本质是特殊的对象，因此可以对数组进行对象属性的解构

   数组键值作为匹配模式

   ```JavaScript
   let arr = [1, 2, 3];
   let {0 : first, [arr.length - 1] : last} = arr;
   first // 1
   last // 3
   ```

#### 用途

1. 交换变量的值

   ```javascript
   let x = 1;
   let y = 2;
   
   [x, y] = [y, x];
   ```

2. 从函数返回多个值

   ```JavaScript
   // 返回一个数组
   
   function example() {
     return [1, 2, 3];
   }
   let [a, b, c] = example();
   
   // 返回一个对象
   
   function example() {
     return {
       foo: 1,
       bar: 2
     };
   }
   let { foo, bar } = example();
   ```

3. 函数参数的定义

   ```javascript
   // 参数是一组有次序的值
   function f([x, y, z]) { ... }
   f([1, 2, 3]);
   
   // 参数是一组无次序的值
   function f({x, y, z}) { ... }
   f({z: 3, y: 2, x: 1});
   ```

4. 提取`JSON`数据

   ```JavaScript
   let jsonData = {
     id: 42,
     status: "OK",
     data: [867, 5309]
   };
   
   let { id, status, data: number } = jsonData;
   
   console.log(id, status, number);
   // 42, "OK", [867, 5309]
   ```

5. 函数参数的默认值

   ```JavaScript
   jQuery.ajax = function (url, {
     async = true,
     beforeSend = function () {},
     cache = true,
     complete = function () {},
     crossDomain = false,
     global = true,
     // ... more config
   } = {}) {
     // ... do stuff
   };
   ```

6. 遍历`Map`结构

   ```JavaScript
   const map = new Map();
   map.set('first', 'hello');
   map.set('second', 'world');
   
   for (let [key, value] of map) {
     console.log(key + " is " + value);
   }
   // first is hello
   // second is world
   
   //只想获取键名，或只想获取键值
   
   // 获取键名
   for (let [key] of map) {
     // ...
   }
   
   // 获取键值
   for (let [,value] of map) {
     // ...
   }
   ```

7. 输入模块指定方法

   ```javascript
   const { SourceMapConsumer, SourceNode } = require("source-map");
   ```



