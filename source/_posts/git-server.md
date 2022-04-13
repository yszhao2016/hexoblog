---
title: 搭建git服务（适用小团队开发）
date: 2022-04-13 22:14:23
tags:
---

useradd git -s /sbin/nologin

useradd -M -g git test -s /sbin/nologin

cd /home/git/

git init --bare 项目名称.git

cd /home/git/项目名称.git

git config core.sharedRepository 0770


第二种方法 公钥 方法
