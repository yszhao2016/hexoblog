---
title: openEuler系统安装遇到问题
date: 2023-11-10 11:59:43
tags:
---


#yum 安装报依赖库找不到问题

    openEuler release 22.03版本
    
    替换centos 7的源就可以
    
    具体命令：wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
    
    然后 将文件中$releasever替换7
    
    具体命令：vim 后  %s/$releasever/7/g