---
title: "服务器查看端口占用并杀死对应进程"
date: 2024-06-05T07:34:06+08:00
draft: false
tags: ["杀死进程","端口占用"]
categories: ["dev-notes"]
summary: "经常跟服务器打交道的开发人员避免不了端口被占用的情况，这时候查询哪些程序占用了进程，并杀死那些僵尸进程就很重要了。"
---

## lsof 查看占用端口的进程
lsof全称是`list open files`表示列出当前系统打开文件的工具。在linux环境下，任何事物都以文件的形式存在，通过文件不仅仅可以访问常规数据，还可以访问网络连接和硬件。

因此我们也可以打开占用某个端口的“文件”，并展示其详情

```bash
lsof -i :22
```
下面是查看5500端口的占用情况，可以看到进程ID（PID）为89135。接下来我们可以根据PID来杀死该进程
![图片](lsof.png)

## 根据PID杀死进程
```bash
kill -9 pid
```
比如`kill -9 89135`会杀死刚刚查询到的进程

