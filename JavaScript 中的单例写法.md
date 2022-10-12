---
title: JavaScript ES6 中的单例写法
categories: JavaScript
tags: [JavaScript]
date: 2021-11-9 9:00:00
updated: 2022-10-12 9:00:00
---

Singleton Pattern

### 单例类

该类负责创建自己的对象，同时确保只有单个对象被创建。这个类提供了一种访问其唯一的对象的方式，可以直接访问，不需要实例化该类的对象。

- 单例类只能有一个实例。
- 单例类必须自己创建自己的唯一实例。
- 单例类必须给所有其他对象提供这一实例。

### ES6中创建单例模式

```js
class SingletonApple {
  constructor(name, creator, products) {
      this.name = name;
      this.creator = creator;
      this.products = products;
  }
  //静态方法
  static getInstance(name, creator, products) {
    if(!this.instance) {
      this.instance = new SingletonApple(name, creator, products);
    }
    return this.instance;
  }
}
```

> [从ES6重新认识JavaScript设计模式(一): 单例模式 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/34754447)

