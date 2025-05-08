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
# 二、具体加密操作
## 、2.1加密方案1
项目【https://github.com/liexusong/php-beast】目录下有/tools/configure.ini
```phpregexp
; source path
src_path = ""

; destination path
dst_path = ""

; expire time
expire = ""

; encrypt type (selection: DES, AES, BASE64)
encrypt_type = "DES"
```

修改完 configure.ini 文件后就可以使用命令 php encode_files.php 开始加密项目。

## 、2.2 加密方案2

制定自己的php-beast

1. 使用 header.c 文件可以修改 php-beast 加密后的文件头结构，
     这样网上的解密软件就不能认识我们的加密文件，就不能进行解密，增加加密的安全性。

2. php-beast 提供只能在指定的机器上运行的功能
   要使用此功能可以在 networkcards.c 文件添加能够运行机器的网卡号，例如：

```phpregexp
char *allow_networkcards[] = {
	"fa:16:3e:08:88:01",
    NULL,
};
```

# 三、beast 加密部署相关问题

## 3.1 laravel项目加密部署问题

加密laravel 模板文件是不无法使用的  会显示`{{}}`

建议加密 app文件夹下 所有文件就可以了


## 3.2 php配置库问题

使用remi库安装的php 

fpm cli 配置扩展分2个文件夹 建议软链接解决，不然需要分别配置

/etc/php/7.1/fpm/conf.d/

/etc/php/7.1/cli/conf.d/


## 3.3 日志报错

```
PHP Fatal error:  PHP Startup: Unable open log file for beast in Unknown on line 0
PHP Fatal error:  Unable to start beast module in Unknown on line 0
```


使用相应权限去操作
或者配置项为空

beast.log_file =""

sudo -u www  /bin/php  【具体命令】








