---
abbrlink: 3820734e
---
---
title: 搭建git服务（适用小团队开发）
date: 2022-04-13 22:14:23
categories: # 分类
- server
tags:
--- #标签
- git
#添加账号
    useradd git 

    useradd -g git test 
    passwd test

    cd /home/git/
#创建项目
    git init --bare 项目名称.git

#配置权限
    cd /home/git/项目名称.git

    git config core.sharedRepository 0770


第二种方法 公钥 方法
