---
title: "如何使用Css变量"
date: 2024-01-10T09:21:51+08:00
draft: false
featuredImage: banner.jpeg
summary: 借助css变量，可以让我们更佳轻松地配置、修改页面的样式。早期组织css需借助预编译语言如less、sass等，而如今css变量则赋予了css原生的能力，本文介绍如何使用css变量.
---

CSS 变量（也称为自定义属性）允许您在 CSS 中定义并重复使用值。

下面是如何使用 CSS 变量的基本方法：

## 定义 CSS 变量
您可以使用 `--` 前缀来定义 CSS 变量，并为其赋值。

```css
:root {
  --main-color: #ff0000;
  --secondary-color: #00ff00;
}
```
上述代码在根节点上声明了两个CSS变量，主要和次要颜色。

>选择器来声明变量的使用范围。变量名可以是任何有效的 CSS 标识符。变量值可以任意有效的CSS属性值。
## 使用 CSS 变量
在您的 CSS 规则中使用 CSS 变量时，使用 `var()` 函数，将变量名称作为参数传递给它。

```css
.element {
  color: var(--main-color);
  background-color: var(--secondary-color);
}
```
*注意：`.article`元素必须为定义变量选择器的后代元素*
## 动态更改 CSS 变量
您可以使用 JavaScript 动态更改 CSS 变量的值。通过 JavaScript，您可以在运行时更改 CSS 变量的值，从而实现**动态主题**、**样式切换**等效果。

```javascript
// 修改一个 Dom 节点上的 CSS 变量
element.style.setProperty("--my-var", 'blue');

// 获取一个 Dom 节点上的 CSS 变量
element.style.getPropertyValue("--my-var");

// 获取任意 Dom 节点上的 CSS 变量
getComputedStyle(element).getPropertyValue("--my-var");

```

## 变量的继承与覆盖
CSS 变量具有继承性，即它们可以在 CSS 层级结构中继承和覆盖。变量在定义时可以被重新定义。

```css
.element {
  --main-color: #0000ff; /* 重新定义 --main-color */
}
```
这是一个简单的例子，展示了如何定义、使用和动态更改 CSS 变量。使用 CSS 变量可以使您的 CSS 更加灵活和可维护，同时提供更好的样式重用性。

## CSS变量的备用值

所谓的备用值，就是设置了**回退方案**。

虽然开发者很小心，仍然存在各种变数导致变量值没有被顺利应用，这样样式的设置是无效的，结果就是元素会使用默认的（或继承的）样式，会产生预料之外的结果。

于是，设置合适的**备用值**就很重要：

```css
.article {
  color: var(--my-var, red); /* Red if --my-var is not defined */
}
```
当`--my-var`不生效时会使用`red`这个值。



