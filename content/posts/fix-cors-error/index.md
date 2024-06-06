---
title: "如何修复CORS错误\"No 'Access-Control-Allow-Origin' header is present on the requested resource\"？"
date: 2023-11-16T09:00:40+08:00
featuredImage: banner.png
draft: false
summary: CORS,即跨域资源共享,是前端工程师经常面临的一中情况，文中将介绍CORS的感念和工作原理。我们将了解什么是预检请求以及 CORS 如何依赖它们。此外，我们将介绍如何使用 CORS 并解决应用程序中由此产生的问题。
---
如今有多种在线应​​用程序可用。现在大多数系统都提供某种形式的在线用户界面。这些接口处理客户端呈现或如何将其显示给用户。用户可以与网络应用程序交互并从服务器请求信息或更新。数据保存在服务器可以访问的数据库中。客户端 Web 应用程序向 Web 服务器请求信息，服务器通过从数据库检索信息来响应该信息。由于数据或信息可能敏感，因此必须采取安全保护措施以确保其完整性。 CORS（即跨域资源共享）是一种提供数据完整性的安全程序。

我们将在本文中研究 CORS 是什么以及它的一般工作原理。我们将了解什么是预检请求以及 CORS 如何依赖它们。此外，我们将介绍如何使用 CORS 并解决应用程序中由此产生的问题。

## 什么是同源策略？
在互联网安全中，同源策略限制从一个源加载的文档或脚本与从另一源加载的资源的交互。

## 什么是 CORS？
CORS 代表跨源资源共享。当一个域向另一个域请求资源时，称为跨域请求。出于安全考虑，我们可能只希望少数域（除了我们自己的域）能够访问服务器的资源。这就是 CORS 的用武之地。CORS 技术允许服务器指定将从 HTTP 标头以外的其他来源（域、方案或端口）加载的资源。

在 CORS 之前，出于安全考虑，无法在单独的域中调用 API 端点。同源策略可以防止这种情况发生。

## 为什么我们需要 CORS？
此方法可以阻止黑客在不同网站上安装恶意脚本。例如，黑客可能通过 AJAX 调用 example.com 并代表登录用户进行修改。

不过，跨域访问也是有益的，甚至在其他一些真实情况下也是必需的。例如，如果我们的 React Web 应用程序调用在单独域上设置的 API 后端。如果没有 CORS，这是不可能的。


## CORS 是如何工作的？
CORS 使服务器能够显式允许特定源，从而使其能够覆盖同源限制。如果我们设置 CORS 服务器，每个响应将包含一个带有“Access-Control-Allow-Origin”键的附加标头。
## What are simple requests?
简单请求是在发送实际请求之前不开始预检请求的请求。一个简单的请求满足以下所有要求：

该请求使用允许的方法之一，例如“GET”、“HEAD”或“POST”。
除了“用户代理”生成的标头之外，唯一可以手动设置的标头是：
```
Accept
Accept-Language
Content-Language
Content-Type
The Content-Type header can only include one of the following values:
application/x-www-form-urlencoded
multipart/form-data
text/plain
```

没有与“XMLHttpRequest.upload”关联的事件侦听器。
该请求不使用“ReadableStream”对象。
## 什么是预检请求？
CORS 预检请求检查服务器使用特定方法和标头的能力以及服务器对“CORS”协议的了解。

浏览器自动生成预检请求。因此，前端开发人员通常不需要编写它们。

使用“Access-Control-Max-Age”标头，可以有选择地缓存在同一 URL 发出的请求的预检响应。浏览器对预检响应采用独特的缓存，与浏览器的标准 HTTP 缓存不同。


## 凭证请求
CORS 还能够发出“经过认证的”请求。在这些请求中，服务器和客户端可以通过 cookie（可能保存必要的凭据）进行通信。

默认情况下，CORS 不包含跨域请求的 cookie。在跨域请求中包含 cookie 可能会导致称为跨站点请求伪造 (CSRF) 的漏洞。 CORS 需要服务器和客户端确认是否可以在请求中包含 cookie，以减少 CSRF 漏洞的可能性。


## CORS 中使用的 HTTP 响应标头
我们通过在响应中包含额外的标头来说明 CORS 的工作原理，指示源是否在服务器的白名单上。让我们看一下 CORS 为此使用的一些标头。

### Access-Control-Allow-Origin
Access-Control-Allow-Origin 标头定义源并指示浏览器允许该源在没有凭据的情况下访问服务器资源。它还可能包含一个通配符“*”，它指示浏览器任何来源都可以在没有凭据的情况下访问服务器的资源。
```
Access-Control-Allow-Origin: *
```
但是，对于包含凭据或 cookie 的请求，我们通常不能在 Access-Control-Allow-Origin 标头中使用通配符。在这种情况下，只应提供一个来源。
```
Access-Control-Allow-Origin: www.example.com
```
### Access-Control-Max-Age
浏览器可以使用“Access-Control-Max-Age”标头将预检请求存储给定的时间长度。
```
Access-Control-Max-Age: 1800
```
### Access-Control-Allow-Methods
它用于响应预检请求，以指定允许访问资源的方法。
```
Access-Control-Allow-Methods: GET, POST, PUT
```

### Access-Control-Allow-Headers
作为预检请求的一部分，Access-Control-Allow-Headers 标头指定客户端在实际请求期间可以使用哪些 HTTP 标头。
```
Access-Control-Allow-Headers: Content-Type
```
## 如何修复 CORS 错误

在构建 Web 应用程序时，您可能遇到过 CORS 错误“请求的站点上不存在‘access-control-allow-origin’标头”。发生这种情况是因为预检请求中没有向浏览器发送任何标头来通知浏览器是否允许源访问该资源。

这个问题有多种解决方案。
### 在后端处理（nodejs）
为了解决 CORS 问题，我们可以手动为每个请求添加必要的标头。每当我们的服务器收到资源请求时，我们将使用中间件来设置这些标头。使用以下代码创建一个中间件来设置所需的标头以解决 CORS 错误。
```js
app.use((req, res, next) => {
  res.setHeader("Access-Control-Allow-Origin", "*");
  res.setHeader("Access-Control-Allow-Methods", "POST, GET, PUT");
  res.setHeader("Access-Control-Allow-Headers", "Content-Type");
  next();
})
```
这里我们将原点设置为*。这意味着简单的请求，如 GET、HEAD 或 POST；服务器允许所有源访问服务器的资源。

如果客户端的浏览器发送预检请求，则可能会出现问题。用于处理预检请求的来源不应是通配符或 *。因此，我们可以稍微更新一下代码来解决预检请求。
```js
app.use((req, res, next) => {
  res.setHeader("Access-Control-Allow-Origin", "https://example.com");
  res.setHeader("Access-Control-Allow-Methods", "POST, GET, PUT");
  res.setHeader("Access-Control-Allow-Headers", "Content-Type");
  next();
})
```
作为中间件的替代方案，我们可以在特定端点上使用 app.options 方法来侦听预检请求。预检请求是“OPTIONS”请求（而不是“GET”、“POST”或“PUT”）。
```js
app.options("/", (req, res) => {
  res.setHeader("Access-Control-Allow-Origin", "https://example.com");
  res.setHeader("Access-Control-Allow-Methods", "POST, GET, PUT");
  res.setHeader("Access-Control-Allow-Headers", "Content-Type");
  res.sendStatus(204);
});
```
此外，您可以使用“cors”NPM 包。

安装：
```bash
npm install cors
```
使用：
```js
const cors = require("cors");

app.use(cors({
  origin: 'https://example.com'
}));
```

### 在前端处理
如果您不控制前端代码向其发送请求的服务器，并且您正在使用像`React`、`Vue`、`Angular` 等这样的框架，您可以通过 CORS 代理插件发出请求，轻松让事情正常工作。

这是用于代理的vue Cli里的配置:
```js
module.exports = {
  devServer: {
    proxy: {
      '^/api': {
        target: '<url>',
        ws: true,
        changeOrigin: true
      },
      '^/foo': {
        target: '<other_url>'
      }
    }
  }
}
```
## 结论
本文向我们介绍了 CORS 是什么以及它的一般工作原理。此外，我们还研究了预检请求是什么以及 CORS 如何依赖它们。最后，我们学习了如何使用 CORS 并解决应用程序中由此产生的问题。