---
title: "Hugo 静态网站增加搜索功能"
date: 2024-01-08T11:45:38+08:00
draft: false
featuredImage: banner.png
tags: ["Hugo","文章检索"]
summary: 之前没有给博客增加文章检索功能，今天看到别人的博客都有这个功能，并且用来检索之前发布的文章蛮好用的，今天给博客增加上去，遇到了一些坑，今天记录一下。
---

## hugo网站文章网检索原理
其实就是在编译打包时生成一个`index.json`文件，里面包含了你写的所有文章：标题、内容等。
然后在页面中请求该`json`文件，在输入时进行文章匹配。

## 在hugo配置文件里设置输出字段

在hugo.yaml中或其他的配置文件里增加`outputs`配置
```yaml
outputs:
  home:
    - "HTML"
    - "RSS"
    - "JSON"
  page:
    - "HTML"
    - "RSS"
```
重新运行本地服务器，尝试打开`http://localhost:1313/index.json`,如果能看到json数据则证明配置成功。

## 完善检索功能
有了数据，有能力的人（对于前端工程师轻而易举）可以自己实现增加界面元素`<input type="text"/>`，然后监听输入事件，之后进行数据匹配，将匹配到的数绘制到界面中，让用户得以选择阅读就行了。

当然，也可以选择一些现成的插件：[Search tools](https://gohugo.io/tools/search/)里提供了好多现成可用的插件方案。