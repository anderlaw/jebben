---
title: a标签下载文件 download 属性无效？原来问题出在这里
date: 2025-09-28T17:18:25+08:00
lastmod: 2025-09-28T17:18:25+08:00
author: Jebben
# avatar: /img/author.jpg
# authorlink: https://author.site
summary: 最近在开发中遇到一个小坑：我想用a标签下载文件，并通过 download 属性来自定义文件名。代码写好后，却发现文件名始终是默认的，根本没有按照我设置的来。一番调查后才发现，这里面还真有点门道。
# images:
#   - /img/cover.jpg
tags:
  - 文件下载
# nolastmod: true
draft: false
---


最近在开发中遇到一个小坑：我想用 `<a>` 标签下载文件，并通过 download 属性来自定义文件名。代码写好后，却发现文件名始终是默认的，根本没有按照我设置的来。一番调查后才发现，这里面还真有点门道。

---

## 1. download 的正常使用方式

在同源环境下，给 `<a>` 标签设置 `download` 属性，确实能生效。比如：

```javascript
const downloadFile = (url, fileName) => {
  const a = document.createElement('a');
  a.href = url;
  a.download = fileName;
  a.click();
};
```

这段代码会触发浏览器下载，并且文件名会按我们设置的 fileName 来保存。

## 2. 为何文件名没有生效？

关键点在于：**跨域下载时，浏览器出于安全策略，会忽略 download 设置的文件名**。

这么设计是有原因的：

- 假设某个网站偷偷嵌入了一段恶意代码，让用户下载一个木马文件。
- 如果它能随意改文件名（比如改成 resume.pdf），用户就可能在不知情的情况下打开恶意程序。

为了避免这种“文件欺骗”，浏览器在 **跨域资源** 上直接禁用了 download 属性的文件重命名能力。

## 3. 怎么解决？

既然浏览器对跨域有限制，那解决思路就是：**想办法让文件下载看起来是同源的**。常见有两种方法。

### 方法一：前端先拉取文件，再触发下载

思路是：

1. 通过 fetch / XHR 把文件以 blob 的形式拉到本地（前提是目标服务允许跨域访问，需正确配置 CORS）。
2. 用 URL.createObjectURL 生成临时链接，再用 a.download 触发下载。

示例代码：

```javascript
const fetchFile = (url, callback) => {
  const xhr = new XMLHttpRequest();
  xhr.open('GET', url);
  xhr.responseType = 'blob'; // 以二进制形式拿到数据
  xhr.onload = () => {
    if (xhr.status === 200) {
      const blob = new Blob([xhr.response], {
        type: 'application/octet-stream'
      });
      callback(blob);
    }
  };
  xhr.send();
};

const downloadFile = (url, fileName) => {
  fetchFile(url, (blob) => {
    const objectURL = URL.createObjectURL(blob);
    const link = document.createElement('a');
    link.href = objectURL;
    link.download = fileName; // ✅ 现在可以自定义文件名了
    link.click();
    URL.revokeObjectURL(objectURL); // 释放内存
  });
};
```

这种方法的前提是：**服务端必须配置了允许跨域的 CORS 响应头**，否则浏览器会拦截请求。

### 方法二：服务端做代理，转发请求

如果目标服务不支持 CORS，或者你不想暴露原始文件地址，可以在自己的后端加一层代理。

流程：

- 前端请求自己的服务 /server-proxy?originalURL=xxx
- 后端去目标服务下载文件，再流式返回给前端
- 由于下载来源变成了“同源”，download 属性就能生效

#### 前端代码

```javascript
const downloadFile = (url, fileName) => {
  const a = document.createElement('a');
  a.href = `http://localhost:3000/server-proxy?originalURL=${encodeURIComponent(url)}`;
  a.download = fileName;
  a.click();
};
```

#### Node.js 服务端（纯 http/https 实现）

```javascript
import http from 'http';
import https from 'https';
import { URL } from 'url';

const server = http.createServer((req, res) => {
  const parsedUrl = url.parse(req.url, true);
  if (parsedUrl.pathname === '/server-proxy') {
    const originalURL = parsedUrl.query?.originalURL || '';
    if (!originalURL) {
      res.writeHead(400, { 'Content-Type': 'application/json' });
      return res.end(JSON.stringify({ error: 'Missing originalURL' }));
    }
    //待转发的原始URL
    console.log('originalURL', originalURL);
    // 发起请求到目标服务
    const urlOptions = new URL(originalURL);
    const client = urlOptions.protocol === 'https:' ? https : http;
    const proxyReq = client.request(urlOptions, (proxyRes) => {
      res.writeHead(proxyRes.statusCode || 200, proxyRes.headers);
      proxyRes.pipe(res);
    });

    proxyReq.on('error', (err) => {
      console.error('Proxy error:', err);
      res.writeHead(500);
      res.end('Proxy error');
    });

    proxyReq.end();
  } else {
    res.writeHead(404, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify({ error: 'Not found' }));
  }
});

server.listen(3000, () => {
  console.log('Server running at http://localhost:3000');
});
```
## 4. 小结

- a.download 生效条件：
  - 资源必须是同源，或者 CORS 允许访问
  - 否则浏览器会忽略自定义文件名

- 解决方案：

  1. 前端 fetch + blob 下载，再触发保存
  2. 后端做代理，转发文件

这样就能既保证安全，又能灵活设置下载文件名。

---

📌 补充：

- 某些情况下，服务端返回的 Content-Disposition: attachment; filename="xxx" 头也会影响最终文件名，如果设置了，它会覆盖前端的 download。
- 如果文件非常大，前端 fetch + blob 可能会占用较多内存，建议使用服务端代理方案。
- fetch + blob 可能会被打断，文件尚未下载完毕时刷新浏览器会导致下载中断，如果不希望被打断可以考虑服务端代理方案。


