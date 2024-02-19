---
title: Linux服务之crond
abbrlink: ceed82ea
date: 2023-07-19 14:20:19
tags:
---


# 一、Cron介绍

Cron被用来进行Linux系统的计划任务执行


# 二、相关目录

cron.d/*用来存放一些软件的Cron设置

cron.daily/每天执行一次的任务

cron.hourly/每小时执行一次的任务

cron.monthly/每个月执行一次的任务

cron.weekly/，每周执行一次的任务

每分钟执行一次脚本

    crontab -e
    
# 三、crontab使用

    *  *  *   *  *  命令/脚本
    分 时 日 月 周

    1  *  *   *  *  命令/脚本   指的是每个小时的第一分钟
    */1 * * * *     命令/脚本   每分钟执行脚本
    

    crontab -l
    查看计划任务列表   
# 四、/etc/cron.daily 与crontab -e 定义的区别
    
   
    /etc/cron.daily 和 crontab -e 都是用来设置定时任务的方式，但它们有一些不同之处。
    
    /etc/cron.daily 是一个目录，里面存放着以 "cron" 格式命名的脚本文件。这些脚本会每天执行一次，通常在系统闲置时执行。这个目录中的脚本是全局的，适用于整个系统的所有用户。
    
    而 crontab -e 则是一个命令，用于编辑和管理用户个人的 crontab 文件。crontab 文件中定义了用户的定时任务计划。通过编辑 crontab 文件，用户可以设置自己的定时任务，包括每天、每周、每月或其他时间间隔的执行。
    
    因此，主要区别在于：
    
    /etc/cron.daily 适用于全局系统级别的定时任务，而 crontab -e 是用户级别的定时任务。
    /etc/cron.daily 中的脚本在每天执行一次，而 crontab -e 可以更精确地设置时间间隔。
    /etc/cron.daily 中的脚本对所有用户有效，而 crontab -e 只对当前用户有效。
    需要注意的是，修改 /etc/cron.daily 目录中的文件需要 root 权限，而 crontab -e 只需要用户自己的权限
    
 # 五、Linux anacron
 
 执行由于关机  忘记执行的任务
 
 配置好 /etc/anacrontab 文件
 
 更具体学习参考http://c.biancheng.net/view/1095.html
 
        