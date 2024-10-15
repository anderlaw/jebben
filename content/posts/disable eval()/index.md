---
title: "通过配置“内容安全策略”禁用eval"
date: 2023-12-24T10:56:15+08:00
draft: false
cover: banner.png
summary: eval()是JavaScript中的一个强大函数，它可以从文字字符串运行javascript代码，但出于一些不安全的原因，我们不建议使用eval(),本文介绍如何在项目中禁用该函数。
---
> 众所周知，eval()是JavaScript中的一个强大函数，它可以从文字字符串运行javascript代码，但出于一些不安全的原因，我们不建议使用eval()。 


有很多不同的方法可以在应用程序中禁用“eval”，例如重写`eval`函数，或使用 `eslint` 检查它在开发环境中的使用情况，并给出警告提醒。

今天我将通过实施“CSP”（内容安全策略）来禁用“eval()”。

## 什么是“CSP”

内容安全策略 (CSP) 是附加的安全层，有助于检测和缓解某些类型的攻击，包括跨站点脚本 (XSS) 和数据注入攻击。

您可以在服务器端或前端启用“CSP”。

在服务器端，您需要配置 Web 服务器以返回 `Content-Security-Policy` HTTP 标头。

在前端，您需要配置一个“mata”元素：
```html

<meta http-equiv="Content-Security-Policy" content="default-src 'self'" />

```

上面的“CSP”限制可用资源仅来自站点的来源。


您可以声明多个语句，并用分号分隔。 您可以在 [MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) 上看到其他声明


## 它与 `eval()` 有什么关系

`CSP` 限制了所有可用资源的来源，而`JavaScript 代码字符串`也是来源的一种，因此当您限制 `CSP` 策略时，您同时禁止使用 `eval()` 执行该字符串。

当您配置“CSP”策略时，如果调用了“eval()”，您的浏览器将抛出错误并忽略它：

![评估错误](eval-error.png)


## 注意事项

不要忘记将 `script` 放在 *CSP* 语句的 **后面** 或使用 `async` 属性。
否则你不会看到配置内容安全策略起作用