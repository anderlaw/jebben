---
title: Index
date: 2024-12-11T11:47:03+08:00
lastmod: 2024-12-11T11:47:03+08:00
author: Author Name
cover: image.png
tags:
  - 国际化
draft: false
summary: 项目中接入了多种语言，做国际站，代码中适配语言时要将中文改为对应的key，随着项目不同模块的key越来越多，key很难记忆，提供一种友好的提示就变的极为重要，本文介绍如何定义key的类型来实现代码提示
---

## 增加语言包和类型文件

```js
//语言包字典和接口定义
interface LanguagePackage {
  user: {
    username: string;
    password: string;
  };
}
const ZH_Language: LanguagePackage = {
  user: {
    username: '用户名',
    password: '密码'
  }
};

//泛型类型定义
type NestedLangKey<T> = T extends object
  ? {
      [K in keyof T]: T[K] extends object ? `${K & string}.${NestedLangKey<T[K]>}` : K & string;
    }[keyof T]
  : never;
//语言key类型定义
export type LanguageKey = NestedLangKey<LanguagePackage>;

```

## 使用模拟

引入语言的key类型定义

```js
import { LanguageKey } from '.....'
//使用，这里模拟,其他的i8n包提供了类似的方法，引入即可
const $message = (key: LanguageKey) => {
  //省略
};
$message('user.password');
```

## Vue里如何使用
React项目一般已经集成或者很好集成类似的功能，但是Vue文件的模版中想要使用类型提示，需要在`@vue/runtime-core`模块下定义全局变量，比如我们想要在模版里使用 `$message`这个预定义好的方法：
```js
declare module '@vue/runtime-core' {
  interface ComponentCustomProperties {
    $message: (key: LanguageKey, options?: any) => string;
  }
}
```
