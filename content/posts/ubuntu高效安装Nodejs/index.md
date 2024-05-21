---
title: "Ubuntu高效安装Nodejs"
date: 2024-05-21T22:38:15+08:00
draft: false
categories: ["dev-notes"]
summary: "Ubuntu上安装nodejs方法非常多，下面这个方式既可以指定安装新版本，又不用配置任何的环境变量，轻松三步即可搞定"
---


## 选择版本：
```bash
# 这里安装的是16版本，你可以将16改成其他的版本数字
curl -sL https://deb.nodesource.com/setup_16.x -o nodesource_setup.sh
```

## 运行配置脚本
```bash
sudo bash nodesource_setup.sh
```

## 安装

```bash
sudo apt install nodejs
```

然后运行 `node -v`，搞定。