---
title: 搭建nps内网穿透
abbrlink: a04b492d
date: 2023-08-29 15:19:19
tags:
 - linux
categories:
  - 服务器相关  
---
# 前言
内网穿透的方式有很多种，什么Frp、Ngrok、NPS、蒲公英等等，


# NPS简介

nps是一款轻量级、高性能、功能强大的内网穿透代理服务器。目前支持tcp、udp流量转发，
可支持任何tcp、udp上层协议（访问内网网站、本地支付接口调试、ssh访问、远程桌面，内网dns解析等等……），
此外还支持内网http代理、内网socks5代理、p2p等，并带有功能强大的web管理端

# 安装docker

    配置docker阿里云 镜像
    yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
        
    yum install docker-ce docker-ce-cli containerd.io

# 安装nps

    拉取镜像
    docker pull ffdfgdfg/nps

    添加配置文件
    创建nps挂载文件目录(自己随意设置)
    mkdir -p /docker/nps/conf
    
    配置下载地址 https://github.com/ehang-io/nps/blob/master/conf
    修改刚下载的conf文件夹里的/docker/nps/conf/nps.conf文件：
    
    web_host=a.o.com   #修改为你的域名admin.nps.youdomain.com
    web_username=admin 
    web_password=123   #修改为一个强密码，毕竟是放在公网上的服务
    web_port = 8090    访问的端口号
    web_ip=0.0.0.0

    
    运行nps
    docker run -d   --restart=always --name nps --net=host -v /docker/nps/conf:/conf ffdfgdfg/nps


# 客户端安装npc
  
    拉取npc镜像
    docker pull ffdfgdfg/npc
    
    
    docker run -d --restart=always --name npc-227 --net=host ffdfgdfg/npc -server=111.231.69.227:8024 -vkey=dle31e3ry1yl616p
    【参数 -server 服务端获取  -vkey 服务端获取】 
    
    
# 访问
    
    http://ip/8090  账号密码 配置文件中   