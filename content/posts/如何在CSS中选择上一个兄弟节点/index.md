---
title: 如何在CSS中选择上一个兄弟节点
date: 2024-10-22T09:59:44+08:00
lastmod: 2024-10-22T09:59:44+08:00
# avatar: /img/author.jpg
# authorlink: https://author.site
# images:
#   - /img/cover.jpg
cover: images.jpeg
categories:
  - CSS
tags:
  - CSS选择器
# nolastmod: true
draft: false
summary: CSS中没有提供选择上一节点（即前一个兄弟元素）的选择器，那么如何实现该需求呢？
---

在CSS中，没有直接的伪类或选择器可以用于选择上一个节点（即前一个兄弟元素）。 可以选择某个元素的后代或者后面的兄弟节点，但不能反向选择上一个兄弟节点。但是可以用下面变通方式来实现需求。


## 利用JavaScript操作DOM
尽管CSS本身不能选择上一个元素，你可以使用JavaScript来动态操作DOM，并为上一个兄弟元素添加样式类（class）。下面是一个示例：

```html
<div class="item">Item 1</div>
<div class="item">Item 2</div>
<div class="item">Item 3</div>
```
```css
.previous {
    background-color: yellow;
}
```
```javascript
const current = document.querySelector('.item:nth-child(3)');
const previous = current.previousElementSibling;
if (previous) {
    previous.classList.add('previous');
}
```
在这个例子中，JavaScript选择了第三个.item，然后使用previousElementSibling获取它的前一个兄弟元素，并为其添加一个特定的样式类previous。


## 利用伪类选择下一个元素并逆向思考

`CSS :has()`选择器允许你根据一个元素的子元素或兄弟元素的状态来选择父级或其他相关元素。你可以利用 :has() 实现反向选择。
假设我们要根据节点`A`选择上个节点`B`，这个逻辑线上是无法实现的，我们可以这样实现：选择下一个节点为`A`节点的`B`节点

```css
.B:has(+ .A) {
    background-color: yellow;
}
```

## 总结
- `CSS`本身没有选择上一个兄弟节点的直接选择器。
- 可以通过 `JavaScript` 来操作DOM，添加相应的样式类以模拟这种行为。
- 在某些情况下，可以通过选择下一个元素并逆向思考，或通过`CSS :has()` 选择器（目前支持度有限）进行变通。






