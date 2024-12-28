---
title: js中host与hostname区别
date: 2024-12-28T16:02:58+08:00
lastmod: 2024-12-28T16:02:58+08:00
author: Author Name
# avatar: /img/author.jpg
# authorlink: https://author.site
cover: image.png
# images:
#   - /img/cover.jpg
categories:
  - category1
tags:
  - tag1
  - tag2
# nolastmod: true
summary: 在开发一个项目的过程中，需要判断页面的域名时，发现 DOM 的 location 对象中有两个相关属性：host 和 hostname。经过对比后发现，这两者存在一些差异。特此记录，方便日后参考。
draft: false
---


`location` 对象的 `host` 和 `hostname` 属性在用途和内容上有所不同，以下是它们的区别：

### 1. **`host`**

* **定义** ：返回页面完整的主机地址，包括域名和端口号（如果有）。
* **格式** ：`hostname:port`（例如 `example.com:8080`）。
* **特点** ：
  * 如果 URL 中指定了端口号，则 `host` 会包含端口号。
  * 如果没有指定端口号，返回的内容仅为域名或 IP 地址。

**示例** ：
```javascript
console.log(location.host); 
// 假设当前页面 URL 是 http://example.com:8080/path 
// 输出：example.com:8080
```
---

### 2. **`hostname`**

* **定义** ：只返回页面的主机名部分，不包含端口号。
* **格式** ：仅域名或 IP 地址（例如 `example.com`）。
* **特点** ：
  * 始终不包含端口号，即使 URL 中包含端口号。

**示例** ：
```javascript
 console.log(location.hostname);
// 假设当前页面 URL 是 http://example.com:8080/path
// 输出：example.com
```
---

### **总结对比**

| 属性           | 内容                         | 示例（[http://example.com:8080/path）]() |
| ---------------- | ------------------------------ | --------------------------------------- |
| `host`     | 域名 + 端口号（如果有）      | example.com:8080                      |
| `hostname` | 仅域名或 IP 地址，不含端口号 | example.com                           |

因此，当需要判断域名时，通常使用 `hostname`；而如果需要包括端口号的信息，则应使用 `host`
