---
title: linux配置代理
abbrlink: 42347bce
date: 2024-03-01 17:29:34
tags:
---

#设置wget


编辑文件为：/etc/wgetrc[全局配置文件]  ~/.wgetrc[用户配置文件] 添加下面两行：

http_proxy=http://111.112.113.114:8080
https_proxy=http://111.112.113.114:8080

更新一下环境文件：
source /etc/wgetrc



