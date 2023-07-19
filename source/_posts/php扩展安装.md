---
title: php扩展编译安装
date: 2023-07-19 09:14:51
tags:
---


# 一、找点phpize 和 php-config路径

    find / -name phpize
    
    find / -name php-config
    
    
# 二、找到 

项目目录下执行 查找到的phpize 

    /opt/remi/php74/root/usr/bin/phpize
    
    如报错
    Can't find PHP headers in /usr/include/php The php-devel package is required for use of this command...
    
    安装php-devel即可
   
    yum install -y php-devel
    
    remi安装如下安装
    yum install -y php73-php-devel

    
# 三、编译
    
     1.进入项目目录下
     ./configure --with-php-config=【第一步查询到的路径】
     
     示例
        ./configure --with-php-config=/www/server/php/71/bin/php-config

     2. 执行
     
     make && make install

# 四、添加配置
    
    vi /具体路径/php.ini
     
    extension=swoole.so
    
    如是remi 安装的php 
    在/etc/opt/remi/php73/php.d下新建
    新建ini文件  参考其他设置下
    
    万变方案 phpinfo 看下是如何加载扩展的 具体的目录 多有
    
# 五、重启服务

    systemctl restart php-fpm   
