---
title: ubuntu搭建lnmp环境
abbrlink: a7094d11
categories:
  - 服务器相关
date: 2023-07-09 14:01:25
tags:
---

# 安装php环境

 安装remi仓库
 sudo add-apt-repository ppa:ondrej/php
 
 sudo apt update
 
 
 sudo apt install php7.1 php7.1-common php7.1-cli php7.1-fpm php7.1-mysql php7.1-curl php7.1-json
 php7.1-mbstring php7.1-xml php7.1-gd php7.1-bcmath php7.1-bz2  php7.1-gmp php7.1-zip
 
 
 syse

 #安装nginx
 
 
 # 更新索引
 sudo apt update
 # 查看当前可用的版本
 sudo apt-cache show nginx
 
 # 等于号后面给出来指定版本号即可
 sudo apt install nginx=1.18.0-6ubuntu14.4