---
title: openEuler搭建lnmp
abbrlink: b991678b
categories:
  - 服务器相关
date: 2023-07-09 14:04:46
tags:
---

#openEuler搭建lnmp

##安装php
wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

rpm -ivh --nodeps epel-release-latest-7.noarch.rp

wget https://rpms.remirepo.net/enterprise/remi-release-7.rpm

rpm -ivh --nodeps remi-release-7.rpm 

yum-config-manager --enable remi-php73

yum install php73 php73-php-fpm php73-php-opcache php73-php-gd php73-php-mbstring php73-php-xml php73-php-pdo php73-php-mysqlnd php73-php-pecl-mysql php73-php-bcmath



##rpm包安装mysql

    Error: 
     Problem: cannot install the best candidate for the job
      - nothing provides libcrypto.so.10()(64bit) needed by php73-php-fpm-7.3.33-10.el7.remi.x86_64
      - nothing provides libssl.so.10()(64bit) needed by php73-php-fpm-7.3.33-10.el7.remi.x86_64
      - nothing provides libcrypto.so.10(libcrypto.so.10)(64bit) needed by php73-php-fpm-7.3.33-10.el7.remi.x86_64
      - nothing provides libssl.so.10(libssl.so.10)(64bit) needed by php73-php-fpm-7.3.33-10.el7.remi.x86_64
      - nothing provides libcrypto.so.10(OPENSSL_1.0.1_EC)(64bit) needed by php73-php-fpm-7.3.33-10.el7.remi.x86_64
      - nothing provides libcrypto.so.10(OPENSSL_1.0.2)(64bit) needed by php73-php-fpm-7.3.33-10.el7.remi.x86_64
      
此报错处理办法      

    yum install compat-openssl10
    yum install http://mirror.centos.org/centos/8-stream/AppStream/x86_64/os/Packages/compat-openssl10-1.0.2o-3.el8.x86_64.rpm