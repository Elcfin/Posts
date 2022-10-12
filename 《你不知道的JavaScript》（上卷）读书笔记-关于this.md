---
title: 《你不知道的JavaScript》（上卷）读书笔记-关于this
categories: 读书笔记
tags: [读书笔记, JavaScript]
date: 2021-11-9 9:00:00
updated: 2022-10-12 9:00:00
---

`this` 的绑定和函数声明的位置没有任何关系，只取决于函数的调用方式

当一个函数被调用时，会创建一个活动记录（有时候也称为执行上下文）。这个记录会包含函数在哪里被调用（调用栈）、函数的调用方法、传入的参数等信息。`this` 就是记录的其中一个属性，会在函数执行的过程中用到<!--more-->

`this` 既不指向函数自身也不指向函数的词法作用域

**调用位置**：函数在代码中被调用的位置（而不是声明的位置）

**调用栈**：为了到达当前执行位置所调用的所有函数

> 使用开发者工具得到调用栈

### 绑定规则

找到调用位置，判断应用四条规则中的哪一条

#### 默认绑定

默认绑定时，`this` 指向全局对象

适用：独立函数调用

如果使用严格模式（strict mode），那么全局对象将无法使用默认绑定，因此 `this` 会绑定到 `undefined`

#### 隐式绑定

当函数引用有上下文对象时，隐式绑定规则会把函数调用中的 this 绑定到这个上下文对象

适用：调用位置是否有上下文对象，或者说是否被某个对象拥有或者包含

> 无论是直接在 obj 中定义还是先定义再添加为引用属性，这个函数严格来说都不属于 obj 对象。 然而，调用位置会使用 obj 上下文来引用函数，因此你可以说函数被调用时 obj 对象“拥有”或者“包含”它

对象属性引用链中只有最顶层或者说最后一层会影响调用位置

例：`obj1.obj2.foo(); ` 调用位置为对象 `obj2`

##### 隐式丢失

被隐式绑定的函数会丢失绑定对象，也就是说它会应用默认绑定

```java
const bar = obj.foo;
bar();
```

`bar` 引用的是 `foo` 函数本身，此时的 `bar()` 其实是一个不带任何修饰的函数调用，因此应用默认绑定

```java
function foo(){
  console.log( this.a );
}

function doFoo(fn){
  fn();
}

var obj = {
  a:2,
  foo:foo
};

var a = "opps, global";

doFoo( obj.foo ); //"opps, global"
```

传入回调函数时，参数传递其实就是一种隐式赋值

#### 显示绑定

通过`call(..)` 和 `apply(..)` 方法指定 `this` 的绑定对象

无法解决丢失绑定的问题

##### 1.硬绑定

```javascript
function foo() {
  console.log( this.a );
}

var obj = {
  a:2
};

var bar = function() {
  foo.call( obj );
};

bar(); // 2
setTimeout( bar, 100 ); // 2

// 硬绑定的 bar 不可能再修改它的 this
bar.call( window ); // 2
```

我们创建了函数 `bar()`，并在它的内部手动调用 了 `foo.call(obj)`，因此强制把 `foo` 的 `this` 绑定到了 `obj`。无论之后如何调用函数 `bar`，它总会手动在 `obj` 上调用 `foo`。这种绑定是一种显式的强制绑定，因此我们称之为**硬绑定**

**应用场景一（典型）：**

创建一个包裹函数，传入所有的参数并返回接收到的所有值

```JavaScript
function foo(something) {
  console.log( this.a, something );
  return this.a + something;
}

var obj = {
  a:2
};

var bar = function() {
  return foo.apply( obj, arguments );
};

var b = bar( 3 ); // 2 3
console.log( b ); // 5
```

包裹函数：`bar`

**应用场景二：**

创建一个 i 可以重复使用的辅助函数

```javascript
function foo(something) {
  console.log( this.a, something );
  return this.a + something;
}

// 简单的辅助绑定函数
function bind(fn, obj) {
  return function() {
    return fn.apply( obj, arguments );
  };
}

var obj = {
  a:2
};

var bar = bind( foo, obj );

var b = bar( 3 ); // 2 3
console.log( b ); // 5
```

**内置方法 `Function.prototype.bind` （ ES5 ）**

```JavaScript
function foo(something) {
console.log( this.a, something );
  return this.a + something;
}

var obj = {
  a:2
};

var bar = foo.bind( obj );

var b = bar( 3 ); // 2 3
console.log( b ); // 5
```

`bind(..)` 会返回一个硬编码的新函数，它会把参数设置为 this 的上下文并调用原始函数

##### 2. API调用的“上下文”

第三方库的许多函数，以及 JavaScript 语言和宿主环境中许多新的内置函数，都提供了一个可选的参数，通常被称为“上下文”（context），其作用和 `bind(..)` 一样，确保你的回调函数使用指定的 `this`

#### new绑定

JavaScript 中 new 的机制实际上和面向类的语言完全不同

**构造函数**：在 JavaScript 中，构造函数只是一些使用 new 操作符时被调用的函数。它们并不会属于某个类，也不会实例化一个类。实际上， 它们甚至都不能说是一种特殊的函数类型，它们只是被 new 操作符调用的普通函数而已

实际上并不存在所谓的“构造函数”，只有对于函数的“构造调用”

使用 new 来调用函数，或者说发生构造函数调用时，会自动执行下面的操作：

1. 创建（或者说构造）一个全新的对象 
2. 这个新对象会被执行 [[ 原型 ]] 连接 
3. 这个新对象会绑定到函数调用的 this 
4. 如果函数没有返回其他对象，那么 new 表达式中的函数调用会自动返回这个新对象

```JavaScript
function foo(a) {
  this.a = a;
}

var bar = new foo(2);
console.log( bar.a ); // 2
```

使用 new 来调用 `foo(..)` 时，我们会构造一个新对象并把它绑定到 `foo(..)` 调用中的 this 上

### 优先级

显示绑定（判断时优先考虑） > 隐式绑定 > 默认绑定

判断硬绑定函数是否是被 new 调用，如果是的话就会使用新创建的 this 替换硬绑定的 this

#### 判断this

1. 函数是否在 new 中调用（new 绑定）？

   如果是的话 this 绑定的是新创建的对象。 `var bar = new foo()`

2. 函数是否通过 call、apply（显式绑定）或者硬绑定调用？

   如果是的话，this 绑定的是指定的对象。 `var bar = foo.call(obj2)`

3. 函数是否在某个上下文对象中调用（隐式绑定）？

   如果是的话，this 绑定的是那个上下文对象。 `var bar = obj1.foo()`

4. 如果都不是的话，使用默认绑定。

   如果在严格模式下，就绑定到 undefined，否则绑定到全局对象。 `var bar = foo()`

### 绑定例外

如果把 null 或者 undefined 作为 this 的绑定对象传入 call、apply 或者 bind，这些值在调用时会被忽略，实际应用的是默认绑定规则

#### 更安全的this

一种“更安全”的做法是传入一个特殊的对象，把 this 绑定到这个对象不会对你的程序 产生任何副作用

我们可以创建一个“DMZ”（demilitarized zone，非军事区）对象——一个空的非委托的对象

**在忽略 this 绑定时总是传入一个 DMZ 对象**

在 JavaScript 中创建一个空对象最简单的方法： `Object.create(null)`

> `Object.create(null)` 和 `{}` 很像， 但是并不会创建 Object. prototype 这个委托，所以它比 {}“更空”

```javascript
function foo(a,b) {
  console.log( "a:" + a + ", b:" + b );
}

// 我们的 DMZ 空对象
var ø = Object.create( null );

// 把数组展开成参数
foo.apply( ø, [2, 3] ); // a:2, b:3

// 使用 bind(..) 进行柯里化
var bar = foo.bind( ø, 2 );
bar( 3 ); // a:2, b:3
```

### 间接引用

创建一个函数的“间接引用”时，调用这个函数会应用默认绑定规则

联系：隐式丢失

间接引用最容易在赋值时发生

```javascript
function foo() {
  console.log( this.a );
}

var a = 2;
var o = { a: 3, foo: foo };
var p = { a: 4 };

o.foo(); // 3
(p.foo = o.foo)(); // 2
```

赋值表达式 `p.foo = o.foo` 的返回值是目标函数的引用，因此调用位置是 `foo()` 而不是 `p.foo()` 或者 `o.foo()`

### 软绑定

给默认绑定指定一个全局对象和 undefined 以外的值，实现和硬绑定相同的效果，同时保留隐式绑定或者显示绑定修改this的能力

### 箭头函数

无法使用这些规则的特殊函数类型

根据外层（函数或者全局）作用域来决定 this

```javascript
function foo() {
  // 返回一个箭头函数
  return (a) => {
    //this 继承自 foo()
    console.log( this.a );
  };
}

var obj1 = {
  a:2
};

var obj2 = {
  a:3
};

var bar = foo.call( obj1 );
bar.call( obj2 ); // 2, 不是 3 ！
```

`foo()` 内部创建的箭头函数会捕获调用时 `foo()` 的 this。由于 `foo()` 的 this 绑定到 `obj1`， `bar`（引用箭头函数）的 this 也会绑定到 `obj1`，**箭头函数的绑定无法被修改**（new也不可以哟）

箭头函数可以像 `bind(..)` 一样确保函数的 this 被绑定到指定对象，会继承外层函数调用的 this 绑定（无论 this 绑定到什么），和 ES6 之前代码中的 self = this 机制一样

它用更常见的词法作用域取代了传统的 this 机制

**2种this代码风格**：

1. 只使用词法作用域并完全抛弃错误 this 风格
2. 完全采用 this 风格，在必要时使用 bind(..)，尽量避免使用 self = this 和箭头函数

