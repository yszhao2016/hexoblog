---
title: linux-安装配置php环境
abbrlink: 35d2b340
categories:
  - 服务器相关
date: 2022-04-17 22:53:21
tags:
  - linux
---
# 一、安装Remi源：

    # CentOS 6 / RHEL 6
    yum install http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
    # CentOS 7 / RHEL 7
    yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
 
 
# 二、配置Remi源(可以不操作)：
 
     编辑remi源repo文件 /etc/yum.repos.d/remi.repo
     启用 Remi Repository修改 enabled=0 为 enabled=1。
     为 Remi Repository 设置合适的优先级在 [remi] 那一节的结尾另起一行添加下面的代码：
     priority=3    
    
    
# 三、安装 php

     yum install php73 php73-php-fpm php73-php-opcache php73-php-gd php73-php-mbstring php73-php-xml php73-php-pdo php73-php-mysqlnd php73-php-pecl-mysql php73-php-bcmath
    php73-php-mcrypt   
    
    
# 四.开机启动   

    systemctl enable  php73-php-fpm
    
# 五.服务相关命令

    systemctl start  php73-php-fpm
    systemctl stop  php73-php-fpm
    systemctl status  php73-php-fpm
    
    
# 六.加入系统命令

    ln -s  /opt/remi/php73/root/usr/bin/php /usr/bin/php
    
# 七.添加用户
    
    groupadd www
    useradd -g www -s /sbin/nologin www
        
# 八.PHP-FPM配置文件www.conf
```
[www]

user = www
group = www

;listen = 127.0.0.1:9000
listen = /dev/shm/php71-fpm.socket

listen.owner = www
listen.group = www
listen.mode = 0660


listen.allowed_clients = 127.0.0.1

#如果选择static，则进程数就数固定的，由pm.max_children指定固定的子进程数。
pm = static
pm.max_children = 256
pm.start_servers = 5
#随着php-fpm一起启动时创建的子进程数目。默认值：min_spare_servers + (max_spare_servers - min_spare_servers) / 2。
#这里表示，一起启动会有20个子进程
pm.min_spare_servers = 5
#设置服务器空闲时最小php-fpm进程数量。必须设置。如果空闲的时候，会检查如果少于10个，就会启动几个来补上。
pm.max_spare_servers = 35
#设置服务器空闲时最大php-fpm进程数量。必须设置。如果空闲时，会检查进程数，多于30个了，就会关闭几个，达到30个的状态。
pm.max_requests = 5000
#设置每个子进程重生之前服务的请求数. 对于可能存在内存泄漏的第三方模块来说是非常有用的. 
#如果设置为 '0' 则一直接受请求. 等同于 PHP_FCGI_MAX_REQUESTS 环境变量. 默认值: 0.
pm.status_path = /php7-php-fpm-status

#当request_slowlog_timeout 设为一个具体秒时request_slowlog_timeout =5，表
#示如果哪个脚本执行时间大于5秒，会记录这个脚本到慢日志文件中
request_slowlog_timeout =10
slowlog = /var/opt/remi/php71/log/php-fpm/www-slow.log

php_admin_value[error_log] = /var/opt/remi/php71/log/php-fpm/www-error.log
php_admin_flag[log_errors] = on



php_value[session.save_handler] = files
php_value[session.save_path]    = /var/opt/remi/php71/lib/php/session
php_value[soap.wsdl_cache_dir]  = /var/opt/remi/php71/lib/php/wsdlcache

```

# 九、php.ini配置文件
```
#关闭PHP版本信息
expose_php = On

#.单个脚本等待输入的最长时间
max_input_time = 60

memory_limit = 128M
error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT

#每个脚本最大允许执行时间(秒)，0表示没有限制
max_execution_time = 300

#上传文件的最大许可大小
upload_max_filesize = 20M

#post上传的大小，要>=upload_max_filesize
post_max_size = 20M


#修改成如下设置：
session.save_handler = memcache
session.save_path = "tcp://10.0.0.18:11211"
```
    