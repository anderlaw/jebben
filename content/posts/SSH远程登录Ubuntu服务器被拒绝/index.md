---
title: "SSH远程登录Ubuntu服务器被拒绝"
date: 2024-05-23T20:14:43+08:00
categories: ["dev-notes"]
summary: "手头有一个服务器，想通过vscode编辑器提供的插件Remote-SSH远程登录，实现在线远程开发，调试啥的直接在服务器上搞定了，想想都美好，然后遇到了一些麻烦，最终放弃了这种方式，在这里记录一下遇到的问题以及如何应对的过程"
draft: false
---

## 登录被拒绝
这里我用的是Root用户账户密码认证的方式，提示：`Permission denied, please try again` .服务器状态没问题，只是权限被限制了。原因是：**SSH 配置了禁止 root 用户登录策略** ，修改配置策略：

```bash
sudo vim /etc/ssh/sshd_config
```
- 将参数 `PermitRootLogin` 的值修改为 `yes`。

- 重启 SSH 服务：`service sshd restart`


如果还是无法登录，重启服务器试试。

## 服务器死机

root用户远程登录问题解决了，能正常登录进去查看、编辑代码了，灰常丝滑，正舒服着呢，突然连接中断了，并且无论如何死活登不进去了，服务器失去了响应，并且服务器控制台的监控数据也一并丢失出现了灰色地带。

只好重启机器，然后再次登录，过一会服务器再次死机。

开始我以为是腾讯云的故障，但是故障每次都在远程登录后出现，让我怀疑是否是vscode的    `Remote-SSH`插件搞的鬼：难道它把服务器的内存（机器内存只有2GB）占满导致机器宕机？

马上搜索一下，果不其然。搜索了解决方案，都不太令人满意，于是，放弃了远程开发的模式，还是本地开发调试吧。速度等个个方面还是完胜远程开发的。

