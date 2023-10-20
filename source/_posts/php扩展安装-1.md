---
title: php扩展安装
date: 2023-10-16 18:54:48
tags:
---


#下载相应的扩展并phpize

    到扩展目录下
    执行phpize
    
    1.phpize 不是系统命令时候
    
    find / -name phpize 
    
    后找到相应版本的phpize
    
    2.报错can‘t find PHP headers in path/php
    
    yum install -y php-devel
    
 # configure
 
 项目目录下
 执行 ./configure
 
 1.报错：configure: error: Cannot find php-config. Please use --with-php-config=PATH
 
 执行 find / -name php-config
 
 找到相应版本php的 php-config
 
 ./configure --with-php-config=【php-config路径】
 

 
 # make && make install
 
    执行成功后会提示.so文件的路径
 
 #配置php.ini
 
 
 