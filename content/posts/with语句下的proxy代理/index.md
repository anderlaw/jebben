---
title: with 语句下的 Proxy 代理行为分析
date: 2025-06-16T19:33:07+08:00
lastmod: 2025-06-16T19:33:07+08:00
author: Author Name
# avatar: /img/author.jpg
# authorlink: https://author.site
cover: image.png
# images:
#   - /img/cover.jpg
categories:
  - Proxy
tags:
  - with语句
draft: false
summary: with 语句可将对象临时引入作用域链，结合 Proxy 实现灵活变量访问。ES6 的 Symbol.unscopables 用于排除 with 中特定属性。Vue 3 通过 Proxy 和全局白名单控制模板变量访问，未直接使用 Symbol.unscopables。理解这些机制有助于掌握模板渲染的作用域管理。
---


# with 语句下的 Proxy 代理行为分析

## 一、背景简介：with 语句的作用

`with` 语句用于将一个对象临时引入当前作用域链，使得对象的属性可以像局部变量一样直接访问：

```js
with (obj) {
  console.log(foo); // 等价于访问 obj.foo
}
```

这种语法能简化代码书写，但因其带来的作用域链模糊问题，严格模式下禁止使用且日常开发中不推荐。但在某些模板编译或上下文模拟场景下，如 Vue 3 模板演练，with 仍然被利用。

  

## 二、变量作用域控制补充：Symbol.unscopables

  

ES6 引入了内置 Symbol 字段 Symbol.unscopables，用于告知引擎在 with 语句中排除某些属性，避免它们被引入作用域：

```javascript
const obj = {
  secret: 123,
  [Symbol.unscopables]: { secret: true }
};

with (obj) {
  console.log(secret); // ReferenceError: secret is not defined
}
```

需要注意，Symbol.unscopables 只影响 with 语句的作用域链变量引入，不干扰普通属性的读写访问，也不构成访问权限控制。

  

## 三、with 语句中 Proxy 的行为流程

  

当 Proxy 代理对象被用作 with (proxy) 时，变量访问遵循更复杂的流程：

1. 引擎先调用 Proxy 的 has(target, key) 判断变量是否存在；
    
2. 若 has 返回 true，引擎尝试读取目标对象上的 Symbol.unscopables 属性；
    
3. 如果 Symbol.unscopables 对象中该 key 标记为 true，该属性会被排除，不触发后续访问；
    
4. 否则，调用 Proxy 的 get(target, key) 获取属性值；
    
5. 如果 has 返回 false，继续查找外层作用域。
    

  

这一机制结合了 Proxy 的灵活拦截能力与语言层面 Symbol.unscopables 的作用域过滤，形成多层次变量访问控制。

  

## 四、Vue 中的实际应用情况

  

Vue 3 使用 Proxy 代理组件上下文 _ctx，配合 with 实现模板上下文模拟。源码中：

```javascript
get(target, key) {
  if (key === Symbol.unscopables) {
    return; // Vue 不启用该机制，返回 undefined
  }
  return PublicInstanceProxyHandlers.get(target, key, target);
}
```

可见 Vue 并未利用 Symbol.unscopables 来控制变量访问，真正的访问控制依赖 has trap，通过字段是否以下划线开头及全局白名单判断来屏蔽敏感属性，保证模板作用域的安全性和简洁性。


## 五、注意点汇总

| 项目               | 说明                                       |
|--------------------|--------------------------------------------|
| with 语句          | 不支持严格模式，不推荐日常业务代码中使用       |
| Proxy              | 通过 `has` 和 `get` 拦截实现灵活的变量访问控制 |
| Symbol.unscopables | 语言层面排除 `with` 作用域内的字段，不是权限控制 |
| Vue 模板作用域     | 依赖 `has` trap 与全局白名单实现变量访问控制  |

## 六、结语

  

with 语句虽然被限制使用，但结合 Proxy 能有效构建模板上下文环境。

理解 with + Proxy + Symbol.unscopables 的协同机制，有助于深入掌握 Vue 模板变量解析与作用域控制原理。
