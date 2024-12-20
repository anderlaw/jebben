---
title: 聊聊循环依赖那些事
date: 2024-12-20T10:15:30+08:00
lastmod: 2024-12-20T10:15:30+08:00
author: Jebben
# avatar: /img/author.jpg
# authorlink: https://author.site
cover: cover.png
categories:
  - 模块
tags:
  - 模块循环依赖
summary: "在软件开发过程中，模块化和组件化是提升代码质量和可维护性的重要手段。然而，当模块之间形成循环依赖时，这不仅会破坏代码的结构，还可能导致一系列编译、运行和可维护性问题。本文探讨循环依赖可能造成的后果以及如何规避，Javascript语言中循环依赖的不同表现"
draft: false
---
在软件开发中，**模块化**和**组件化**是提升代码质量与可维护性的核心手段。然而，当模块之间形成**循环依赖**时，这不仅会破坏代码结构，还可能引发编译、运行和维护上的一系列问题。

## 什么是模块循环依赖？

模块循环依赖是指两个或多个模块之间相互引用，形成一个闭环的依赖关系。例如，模块 A 依赖于模块 B，而模块 B 又依赖于模块 A。这种关系会导致代码无法正常执行，因为每个模块都在等待另一个模块的初始化或加载完成。

## 模块循环依赖的影响

1. **编译问题**
   在某些编程语言和编译器中，循环依赖可能导致编译错误或依赖解析失败。编译器无法确定应该优先编译哪个模块，因为它们相互依赖。
2. **运行时问题**
   循环依赖在运行时可能导致无限循环或栈溢出错误。例如，当两个模块不断地引用对方，系统可能无法完成初始化，最终导致程序崩溃。
3. **可维护性问题**
   循环依赖显著增加了代码的复杂性，降低了代码的可读性和可维护性。开发者需要花费更多精力去理解和修改这种交织复杂的依赖关系。

## 如何解决模块循环依赖？

1. **代码重构**
   重新组织代码结构是解决循环依赖的根本方法。这通常需要重新划分模块的职责，甚至引入新的模块来拆分现有的依赖链。虽然此方法有效，但可能带来额外的复杂性，需权衡利弊。
2. **延迟加载**
   通过延迟加载技术，可以在真正需要某个模块时才加载它，从而避免循环依赖。例如，在 JavaScript 中，可以使用动态导入 (`import()`) 或异步加载来实现延迟加载。
3. **使用接口或抽象类**
   在面向对象编程中，可以通过定义接口或抽象类来解耦模块之间的直接依赖关系。模块之间的依赖会转移到接口或抽象类上，从而可能打破循环依赖。
4. **依赖注入**
   依赖注入是一种设计模式，它通过动态注入依赖关系，避免了模块之间的直接引用，核心思想是通过将依赖的管理和创建责任从模块内部移到外部容器或调用方，避免模块直接依赖彼此的具体实例，从而打破循环。

## 循环依赖在Java和Javascript中的不同表现
### Java
```java
// ModuleA.java
public class ModuleA {
    static String valueA = ModuleB.valueB; // Depends on ModuleB's valueB
    static {
        System.out.println("Module A initialized");
    }
}

// ModuleB.java
public class ModuleB {
    static String valueB = ModuleA.valueA; // Depends on ModuleA's valueA
    static {
        System.out.println("Module B initialized");
    }
}
```
**运行结果：**

* 在某些 JVM 实现中，会抛出 `ExceptionInInitializerError` 或 `NullPointerException`。
* **原因** ：
  * `ModuleA` 在初始化时依赖 `ModuleB` 的 `valueB`。
  * `ModuleB` 又反过来依赖 `ModuleA` 的 `valueA`，导致初始化无法完成。

### Js
```js
// moduleA.js
import { valueB } from './moduleB.js';
console.log('Module A sees valueB:', valueB);
export const valueA = 'Export from A';

// moduleB.js
import { valueA } from './moduleA.js';
console.log('Module B sees valueA:', valueA);
export const valueB = 'Export from B';

//运行 node moduleA.js 输出：
// Module B sees valueA: undefined
// Module A sees valueB: Export from B
```
1. **部分完成的模块**
   如果在模块 A 和模块 B 的循环依赖中，模块 A 尚未完全加载完成时模块 B 开始依赖模块 A，则模块 B 只能访问到模块 A 中已经初始化的部分。
2. **运行时无编译错误**
   JavaScript 不会在编译时报错，而是在运行时表现出异常行为，例如未定义或不完整的值。

### 两者的对比与总结

| **特性** | **JavaScript** | **Java** |
| ---------------- | ---------------------- | ---------------- |
| **加载机制** | 动态运行时加载 | 静态编译时加载 |
| **编译时错误** | 无 | 有（静态依赖时可能编译失败） |
| **运行时行为** | 输出部分完成的值或`undefined` | 可能抛出运行时异常，如`NullPointerException` |
| **解决方法** | - 使用延迟加载- 避免直接依赖 | - 使用依赖注入- 明确静态初始化顺序 |


## 如何避免模块循环依赖？

1. **明确模块职责**
   在模块设计时，应清晰定义每个模块的职责和边界，避免职责重叠或混淆。
2. **遵循单一职责原则**
   每个模块应仅有一个导致其变化的原因。承担过多职责的模块往往会增加与其他模块的耦合风险，从而导致循环依赖。
3. **提前规划依赖关系**
   在开发初期，务必规划好模块之间的依赖关系，避免开发后期因循环依赖问题而进行大规模重构。
