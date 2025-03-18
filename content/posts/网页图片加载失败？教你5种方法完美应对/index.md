---
title: 网页图片加载失败？教你5种方法完美应对！
date: 2025-03-18T16:28:58+08:00
lastmod: 2025-03-18T16:28:58+08:00
author: Jebben
cover: cover.png
# images:
#   - /img/cover.jpg
tags:
  - 图片加载失败
# nolastmod: true
draft: false
summary: 在网页开发过程中，图片加载失败（俗称“裂图”）是常见的问题，可能由网络错误、服务器问题或图片地址错误导致。为了优化用户体验，我们可以使用多种方式提供备用图片。本文将介绍几种常见的处理方案。
---

在网页开发过程中，图片加载失败（俗称“裂图”）是常见的问题，可能由网络错误、服务器问题或图片地址错误导致。为了优化用户体验，我们可以使用多种方式提供备用图片。本文将介绍几种常见的处理方案。

---

## 1：借助onerror事件
在 HTML 直接使用`onerror` 事件，当图片加载失败时自动替换为备用图片：

```html
<img src="broken-image.jpg" onerror="this.onerror=null; this.src='fallback-image.jpg';" alt="图片">
```

**解释：**

* `this.onerror = null;` 避免`fallback-image.jpg` 也加载失败时进入死循环。
* `this.src = 'fallback-image.jpg';` 替换为备用图片。

---

## 2：使用背景图覆盖

如果`img` 标签加载失败，可以使用 CSS 提供背景图：

```html
<img src="broken-image.jpg" class="fallback-img" alt="图片">
```

```css
.fallback-img {
  background: url('fallback-image.jpg') no-repeat center center;
  background-size: cover;
}
```

**注意：**

* 该方法不会真正替换`src`，**图片裂开的效果依然存在**，但可以确保图片区域仍有内容。
* 适用于装饰性图片。

---


## 3：使用 JavaScript 监听所有图片

如果网页中有多个图片，需要全局处理加载失败的情况：

```javascript
document.addEventListener("DOMContentLoaded", function () {
  document.querySelectorAll("img").forEach((img) => {
    img.onerror = function () {
      this.onerror = null; // 避免死循环
      this.src = "fallback-image.jpg";
    };
  });
});
```

**适用场景：**

* 适用于动态加载的图片，如单页应用（SPA）。
* 一次性修复整个网页的图片加载失败问题。

---

## 4：预检查图片是否可用

如果你想在加载图片前检测其有效性，可以使用`fetch()` 进行预加载检查：

```javascript
function checkImage(src, callback) {
  fetch(src, { method: "HEAD" })
    .then((res) => {
      if (res.ok) callback(src);
      else callback("fallback-image.jpg");
    })
    .catch(() => callback("fallback-image.jpg"));
}

checkImage("broken-image.jpg", (validSrc) => {
  document.getElementById("myImage").src = validSrc;
});
```

**特点：**

* 适用于动态生成的图片链接。
* 额外的`HEAD` 请求可能会增加服务器负担。

---

## **最佳实践总结**

| 方法                              | 适用场景         | 适用性                              |
| ----------------------------------- | ------------------ | ------------------------------------- |
| `onerror` 事件   | 适用于少量图片   | 简单直接                            |
| CSS 背景图                        | 适用于装饰性图片 | 不能真正替换 `src` |                         |
| JS 批量处理                       | 适用于大量图片   | 适合 SPA                            |
| `fetch()` 预检查 | 适用于动态内容   | 额外请求成本                        |

---

