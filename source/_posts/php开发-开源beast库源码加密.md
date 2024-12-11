---
title: php开发-开源beast库源码加密
abbrlink: 71e4e85e
date: 2024-12-11 10:20:30
tags:
---

# 一、php-beast编译安装

项目地址 【https://github.com/liexusong/php-beast】

参考文档操作就可以了

Ubuntu 22.04.2 LTS
gcc 11.4.0
成功


centos 7.9
gcc 4.8.5
编译能成功

运行报错

```
PHP Warning:  PHP Startup: Unable to load dynamic library '/opt/remi/php71/root/usr/lib64/php/modules/beast.so'
 - /opt/remi/php71/root/usr/lib64/php/modules/beast.so: undefined symbol: rc_dtor_func in Unknown on line 0
 ```


待解决


# 二、beast 加密部署相关问题

## 2.1 laravel项目加密部署问题

加密laravel 模板文件是不无法使用的  会显示`{{}}`

建议加密 app文件夹下 所有文件就可以了


## 2.2 php配置库问题

使用remi库安装的php 

fpm cli 配置扩展分2个文件夹 建议软链接解决，不然需要分别配置

/etc/php/7.1/fpm/conf.d/

/etc/php/7.1/cli/conf.d/


## 2.3 日志报错

```
PHP Fatal error:  PHP Startup: Unable open log file for beast in Unknown on line 0
PHP Fatal error:  Unable to start beast module in Unknown on line 0
```


使用相应权限去操作
或者配置项为空

beast.log_file =""

sudo -u www  /bin/php  【具体命令】








