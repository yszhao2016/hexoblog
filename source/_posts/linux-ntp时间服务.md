---
title: linux-ntp时间服务
categories:
  - 服务器相关
tags:
  - linux
abbrlink: dd9c94d0
date: 2025-06-11 09:02:52
---
# 相关常用命令
    
    timedatectl  #查看 当前 系统时间 时区 
    
    timedatectl set-time "2025-06-11 09:02:52" #设置时间

    timedatectl set-timezone "Asia/Shanghai" 		##设定系统时区


# chrony与ntp

## 简介
    chrony
    chrony 是一个开源的 NTP 时间同步服务，
    ntp 是一个开源的 NTP 时间同步服务，
    ntp 服务是 chrony 的一个 fork 项目。
    chrony 网络环境差，chrony 更适合


## 安装chrony服务

    yum install -y chrony       #redhat 系统
    
    apt-get install -y chrony   #ubuntu 系统

    systemctl enable chronyd
    systemctl status chronyd

    配置文件  /etc/chrony.conf

    配置文件添加ntp服务器地址
```text
    server ntp1.aliyun.com iburst
    server ntp2.aliyun.com iburst
```

    chronyc sources   #查看同步源

    chronyc -a makestep #立即手动同步




##  安装ntp服务

    yum -y install ntp  ntpdate

    apt-get install -y chronyd

    systemctl status ntp

    sudo ntpdate -u ntp1.aliyun.com   #手动同步

    配置文件
    /etc/ntp.conf

    显示 NTP 服务器和客户端的同步状态
    ntpq -p 