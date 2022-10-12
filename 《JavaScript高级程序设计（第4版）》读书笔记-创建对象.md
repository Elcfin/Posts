---
title: 《JavaScript高级程序设计（第4版）》读书笔记-创建对象
categories: 读书笔记
tags: [JavaScript, 读书笔记]
date: 2021-11-9 9:00:00
updated: 2022-10-12 9:00:00
---

创建对象

#### 工厂模式

#### 构造函数模式

#### 原型模式

可以通过 `Object.create()` 来创建一个对象，同时为其指定原型

`hasOwnProperty()` 只有属性存在于实例上时才返回 true

`in` 操作符会在可以通过对象访问指定属性时返回 true，无论该属性是在实例上还是在原型上

