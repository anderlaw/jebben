---
title: "如何用ts定义固定类型数组"
date: 2024-08-27T12:03:19+08:00
draft: false
tags: ["ts","元组"]
---

在 `TypeScript` 中，你可以使用元组类型（Tuple Type）来定义一个数组，其中**每个元素**的类型都是固定的。
比如，你可以定义一个有两个元素的数组，第一个元素类型是布尔值，第二个是数字：

```typescript
let arr: [boolean, string] = [false, 1];
```
其中：`[boolean, string]` 是一个**元组**类型，表示该数组的第一个元素必须是 `boolean` 类型，第二个元素必须是 `number` 类型。