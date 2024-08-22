---
title: linux-logrotate日志切割
abbrlink: a61dc71f
categories:
  - 服务器相关
date: 2023-07-19 09:14:27
tags:
---
# 一、logrotate介绍
    
logrotate是centos自带命令，其他linux操作系统可能需要自行安装，用来进行日志切割和定期删除，

简单来说就是将某个日志文件按照时间或大小分割成多份，删除时间久远的日志。

# 一、安装

    yum install -y logrotate     #centos 系统
    
    apt install -y logrotate    #ubuntu 系统
    
    
# 二、配置  

/etc/logrotate.conf是全局配置

    #指定日志文件保留几个副本
    rotate 4
    
    # 指定当发生滚动后，创建一个新的空日志文件（权限不变）
    create
    
    # 指定滚动文件的后缀是当前的日期
    dateext  
    
    # 指定是否对滚动日志进行压缩
    #compress
    
    # 加载子配置
    include /etc/logrotate.d
    
    # 指定对特定文件的滚动规则
    /var/log/wtmp {
        monthly
        create 0664 root utmp
        minsize 1M  #指定文件小于1m就不滚动
        rotate 1
    }
    
    /var/log/btmp {
        missingok
        monthly
        create 0600 root utmp
        rotate 1
    }

/etc/logrotate.d/*（子配置）



# 三、执行

方案一
    通过 /etc/cron.daily/logrotate 配置
方案二
    crontab -e写入，内容为“  59 23 * * * /usr/sbin/logrotate -f /home/zmq/daily_logrotate  


# 四、具体的配置文件

    /var/log/nginx/*.log {
              daily
              missingok
              rotate 52
              compress
              delaycompress
              notifempty
              create 640 nginx adm
              sharedscripts
              postrotate
                     if [ -f /var/run/nginx.pid ]; then
                             kill -USR1 `cat /var/run/nginx.pid`
                     fi
              endscript
     }
     
     
     
     /var/opt/remi/php71/log/php-fpm/*log {
            missingok
            notifempty
            sharedscripts
            delaycompress
            postrotate
                /bin/kill -SIGUSR1 `cat /var/opt/remi/php71/run/php-fpm/php-fpm.pid 2>/dev/null` 2>/dev/null ||     true
            endscript
      }
