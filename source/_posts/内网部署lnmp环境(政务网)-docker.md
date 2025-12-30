---
title: 内网部署lnmp环境-docker
abbrlink: 4535910d
date: 2025-09-02 09:08:37
tags:
---

# 一、通过docker方式安装

1.安装docker运行环境（基于openEnler系统）

1.1 处理yum源

系统源配置文件

    wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo

    vim /etc/yum.repos.d/CentOS-Base.repo
    
    进去后将所有$release替换为7  %s/$release/7/


     yum install -y wget && wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo -O /etc/yum.repos.d/docker-ce.repo
     
     vim /etc/yum.repos.d/docker-ce.repo
     
     进去后将所有$release替换为7  %s/$release/7/


安装相关工具    

    yum install -y yum-utils 


下载docke rpm包
    
最新版本

     yum install docker-ce  --downloadonly --downloaddir=/home/soft/docker

指定版本

     yum install docker-ce-24*  --downloadonly --downloaddir=/home/soft/docker

安装docker

    rpm -ivh *.rpm

# 二、打包镜像

## 2.1 配置镜像源
    
    vim /etc/docker/daemon.json
```json
  {
        "registry-mirrors": [
            "https://docker.1ms.run",
            "https://proxy.1panel.live",
            "https://docker.xuanyuan.me",
            "https://docker.m.daocloud.io",
            "https://docker.rainbond.cc",
            "https://docker.1panel.live"
        ]
    }
```
    # 查看下是否成功
    docker info  

    docker pull nginx:latest

    docker pull mysql:5.7

    docker pull php:7.4-fpm
## 2.2 打包镜像
    
    docker save -o nginx.tar  nginx:1.26

    docker save -o mysql.tar  mysql:5.7

    docker save -o php74.tar  php:7.4-fpm


# 三、导入镜像启动容器

## 3.1 导入镜像

    docker load -i nginx.tar
    
    docker load -i mysql.tar
    
    docker load  -i php74.tar

## 3.2 启动容器

    

# 四、配置容器



# docker-compose脚本