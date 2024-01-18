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
    
    
# 安装 yum install nginx 报错

    Error: 
     Problem: 软件包 nginx-1:1.24.0-1.el7.ngx.x86_64 需要 libcrypto.so.10()(64bit)，但没有提供者可以被安装
      - 软件包 nginx-1:1.24.0-1.el7.ngx.x86_64 需要 libcrypto.so.10(libcrypto.so.10)(64bit)，但没有提供者可以被安装
      - 软件包 nginx-1:1.24.0-1.el7.ngx.x86_64 需要 libssl.so.10()(64bit)，但没有提供者可以被安装
      - 软件包 nginx-1:1.24.0-1.el7.ngx.x86_64 需要 libssl.so.10(libssl.so.10)(64bit)，但没有提供者可以被安装
      - 软件包 nginx-1:1.24.0-1.el7.ngx.x86_64 需要 libcrypto.so.10(OPENSSL_1.0.2)(64bit)，但没有提供者可以被安装
      - 无法同时安装 openssl-libs-1:1.0.2k-19.el7.x86_64 和 openssl-libs-1:1.1.1m-22.oe2203.x86_64
      - 无法同时安装 openssl-libs-1:1.0.2k-21.el7_9.x86_64 和 openssl-libs-1:1.1.1m-22.oe2203.x86_64
      - 无法同时安装 openssl-libs-1:1.0.2k-22.el7_9.x86_64 和 openssl-libs-1:1.1.1m-22.oe2203.x86_64
      - 无法同时安装 openssl-libs-1:1.0.2k-24.el7_9.x86_64 和 openssl-libs-1:1.1.1m-22.oe2203.x86_64
      - 无法同时安装 openssl-libs-1:1.0.2k-25.el7_9.x86_64 和 openssl-libs-1:1.1.1m-22.oe2203.x86_64
      - 无法同时安装 openssl-libs-1:1.0.2k-26.el7_9.x86_64 和 openssl-libs-1:1.1.1m-22.oe2203.x86_64
      - 软件包 ima-evm-utils-libs-1.3.2-8.oe2203sp1.x86_64 需要 libcrypto.so.1.1()(64bit)，但没有提供者可以被安装
      - 软件包 ima-evm-utils-libs-1.3.2-8.oe2203sp1.x86_64 需要 libcrypto.so.1.1(OPENSSL_1_1_0)(64bit)，但没有提供者可以被安装
      - 软件包 ima-evm-utils-libs-1.3.2-8.oe2203sp1.x86_64 需要 libcrypto.so.1.1(OPENSSL_1_1_1)(64bit)，但没有提供者可以被安装
      - 无法为该任务安装最佳候选
      - 安装的软件包的问题 ima-evm-utils-libs-1.3.2-8.oe2203sp1.x86_64
    (try to add '--allowerasing' to command line to replace conflicting packages or '--skip-broken' to skip uninstallable packages or '--nobest' to use not only best candidate packages)
    
    
    解决方案
    yum install compat-openssl10
    yum install http://mirror.centos.org/centos/8-stream/AppStream/x86_64/os/Packages/compat-openssl10-1.0.2o-3.el8.x86_64.rpm