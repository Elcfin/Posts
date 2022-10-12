---
title: 《CSS新世界》读书笔记
categories: 读书笔记
tags: [读书笔记, CSS]
date: 2021-11-9 9:00:00
updated: 2022-10-12 9:00:00
---

#### 了解 `CSS` 全局关键字属性值

`inherit` 关键字，用过都说好

使用 `inherit` 关键字重置输入框的内置字体：

```css
input, textarea {
  font-family: inherit;
}
```

`initial` 关键字

适合用在需要重置某些 `CSS` 样式，但又不记得初始值的场景。

可以通过设置 `initial` 了解某个 `CSS` 属性的初始值。

`revert` 关键字

让当前元素的样式还原成浏览器内置的样式。

**没有任何理由对 `<li>` 元素进行任何样式重置。**

