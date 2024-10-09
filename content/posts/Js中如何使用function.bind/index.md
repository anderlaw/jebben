---
title: "Js中如何使用function.bind"
date: 2024-10-09T15:14:00+08:00
draft: false
summary: "js中bind的方法一直在用，今天看Vue的源码发现里面还有一些其他的东西，记录一下巩固，不然每次都查MDN很不方便，希望这次能记到脑筋里"
---

### Function.prototype.bind的定义
Function 实例的 bind() 方法创建一个新函数，当调用该新函数时，它会调用原始函数并将其 this 关键字设置为给定的值，同时，还可以传入一系列指定的参数，这些参数会插入到调用新函数时传入的参数的前面。
```javascript
func.bind(thisArg[, arg1[, arg2[, ...]]])
```

- `thisArg`在调用绑定函数时，作为 this 参数传入目标函数 func 的值。如果函数不在严格模式下，null 和 undefined 会被替换为全局对象，并且原始值会被转换为对象。如果使用 new 运算符构造绑定函数，则忽略该值。

- arg1, …, argN **可选**。在调用 `func` 时，插入到传入绑定函数的参数前的参数。

绑定的函数可以再次绑定创建新的绑定函数，参数会被接受，后续绑定的this则会被忽略：
```javascript
"use strict"; // 防止 `this` 被封装到到包装对象中

function log(...args) {
  console.log(this, ...args);
}
const boundLog = log.bind("this value", 1, 2);
const boundLog2 = boundLog.bind("new this value", 3, 4);
boundLog2(5, 6); // "this value", 1, 2, 3, 4, 5, 6
```

当调用 `boundLog2` 时，它会调用 `boundLog`， `boundLog` 又会调用 `log`。`log` 最终接收到的参数按顺序为：
- `boundLog` 绑定的参数
- `boundLog2` 绑定的参数
- 以及 `boundLog2` 接收到的参数。
### 使用场景
#### 绑定 this 指向

bind 常用于确保某个函数在调用时的 this 始终指向特定对象。例如，事件处理器中 this 通常会被重新绑定到触发事件的元素上，这可能会导致问题。通过 bind 可以确保 this 指向我们期望的对象。
#### 为函数预设部分参数
使用 bind 你可以为函数预设部分参数，创建一个新的函数，在调用新函数时，只需要提供剩余的参数。
```javascript
function list(...args) {
  return args;
}

function addArguments(arg1, arg2) {
  return arg1 + arg2;
}

console.log(list(1, 2, 3)); // [1, 2, 3]

console.log(addArguments(1, 2)); // 3

// 创建一个带有预设前导参数的函数
const leadingThirtySevenList = list.bind(null, 37);

// 创建一个带有预设第一个参数的函数。
const addThirtySeven = addArguments.bind(null, 37);

console.log(leadingThirtySevenList()); // [37]
console.log(leadingThirtySevenList(1, 2, 3)); // [37, 1, 2, 3]
console.log(addThirtySeven(5)); // 42
console.log(addThirtySeven(5, 10)); // 42
//（最后一个参数 10 被忽略）
```

