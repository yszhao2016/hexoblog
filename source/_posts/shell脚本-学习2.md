---
title: shell脚本学习2
abbrlink: 8a98ccfe
date: 2023-07-29 14:58:19
categories:
  - 服务器相关
  - shell
tags:
---


搜索出当前目录下包含某个字符串的文件  并替换文件中的xx字符串为bb字符串
    sed -i "s/xx/bb/g" `grep -rl "xx ./`
    sed -i "s/http:\/\/121.229.56.251:8082/https:\/\/zkzp.jyrcfzjt.com:8082/g" `grep -rl "http://121.229.56.251:8082" ./`
